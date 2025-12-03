# Day 1: 环境搭建与加密市场数据获取

## 今日目标
完成开发环境设置，学习获取加密货币市场数据

## 任务清单

### 1. 环境搭建
- [ ] 安装 Python (推荐 3.8+)
- [ ] 安装必要的包：
  ```bash
  pip install pandas numpy ccxt python-binance backtrader matplotlib websocket
  ```
- [ ] 验证安装是否成功

### 2. 交易所API配置
- [ ] 注册币安(Binance)账号（或选择其他交易所）
- [ ] 创建API密钥（仅读取权限，不要给交易权限）
- [ ] 测试API连接
- [ ] 了解API限制（请求频率、数据限制等）

### 3. 数据获取基础
- [ ] 学习使用CCXT获取加密货币数据（统一接口）
- [ ] 学习使用python-binance获取币安数据
- [ ] 了解K线数据格式（OHLCV）
- [ ] 实现数据存储功能（CSV/数据库）

### 4. 加密市场数据处理
- [ ] 获取BTC/USDT历史K线数据
- [ ] 获取ETH/USDT历史K线数据
- [ ] 计算基本的技术指标（收益率、波动率）
- [ ] 数据清洗和预处理
- [ ] 数据可视化

## 今日成果物
1. 完整的开发环境
2. 能够获取任意加密货币交易对的历史数据
3. 一个基础的数据处理脚本
4. 了解加密市场24小时交易的特点

## 示例代码

```python
import ccxt
import pandas as pd
from datetime import datetime
import os

# 使用CCXT（支持多个交易所）
# 配置代理（clash代理端口7890）- 必须配置以解决451错误
exchange = ccxt.binance({
    'apiKey': os.getenv('BINANCE_API_KEY', ''),  # 可选，公共数据不需要
    'secret': os.getenv('BINANCE_SECRET_KEY', ''),  # 可选，公共数据不需要
    'enableRateLimit': True,
    'timeout': 30000,
    # 配置代理（必须，解决451地理位置限制错误）
    'proxies': {
        'http': 'http://127.0.0.1:7890',
        'https': 'http://127.0.0.1:7890',
    },
    # 禁用需要API密钥的功能（如果只是获取公共数据）
    'options': {
        'defaultType': 'spot',
        'fetchCurrencies': False,  # 禁用获取货币配置，避免451错误
    }
})

# 获取BTC/USDT历史K线数据
symbol = 'BTC/USDT'
timeframe = '1d'  # 1分钟: '1m', 1小时: '1h', 1天: '1d'
limit = 100

try:
    # 获取K线数据
    ohlcv = exchange.fetch_ohlcv(symbol, timeframe, limit=limit)
except ccxt.ExchangeNotAvailable as e:
    if '451' in str(e) or 'restricted location' in str(e).lower():
        print("❌ 451错误：地理位置限制")
        print("💡 解决方案：")
        print("   1. 确保v2rayN已启动并连接到服务器")
        print("   2. 检查代理端口是否正确（当前使用7890）")
        print("   3. 尝试使用python-binance库（见下方替代方案）")
        raise
    else:
        raise

# 转换为DataFrame
df = pd.DataFrame(ohlcv, columns=['timestamp', 'open', 'high', 'low', 'close', 'volume'])
df['datetime'] = pd.to_datetime(df['timestamp'], unit='ms')
df.set_index('datetime', inplace=True)

# 基础数据处理
df['returns'] = df['close'].pct_change()
df['volatility'] = df['returns'].rolling(20).std()

# 保存数据
os.makedirs('data', exist_ok=True)  # 确保目录存在
df.to_csv('data/btc_usdt_data.csv')

print(f"获取了 {len(df)} 条数据")
print(df.head())
```

## 学习资源
1. [CCXT官方文档](https://docs.ccxt.com/)
2. [Binance API文档](https://binance-docs.github.io/apidocs/)
3. [Pandas官方文档](https://pandas.pydata.org/docs/)
4. [加密货币数据获取教程](https://github.com/ccxt/ccxt)

## 注意事项
- **安全第一**：API密钥只给读取权限，不要给交易权限
- 注意API请求频率限制（避免被封）
- 加密市场24小时交易，数据更新频繁
- 做好数据备份，建议使用数据库存储
- 注意时区问题（UTC时间）
- **451错误解决**：如果遇到451地理位置限制错误：
  - 确保v2rayN已启动并连接
  - 检查代理配置是否正确
  - 推荐使用python-binance库（更稳定）
  - 或使用币安测试网进行练习

## 常见错误解决

### 错误451：地理位置限制

**错误信息：**
```
451 Client Error: Service unavailable from a restricted location
```

**解决方案：**

1. **使用python-binance（推荐）**：
   ```python
   from binance.client import Client
   
   client = Client(
       requests_params={
           'proxies': {
               'http': 'http://127.0.0.1:7890',
               'https': 'http://127.0.0.1:7890',
           }
       }
   )
   klines = client.get_klines(symbol='BTCUSDT', interval=Client.KLINE_INTERVAL_1DAY, limit=100)
   ```

2. **使用测试网**：
   ```python
   exchange = ccxt.binance({
       'urls': {
           'api': {
               'public': 'https://testnet.binance.vision/api',
               'private': 'https://testnet.binance.vision/api',
           }
       }
   })
   ```

3. **检查代理**：
   - 确保v2rayN已启动
   - 确认代理端口正确
   - 运行 `examples/day1_alternative_solutions.py` 测试 