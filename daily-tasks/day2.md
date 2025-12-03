# Day 2: 量化交易基础概念

## 今日目标
理解量化交易的核心概念和常用技术指标

## 任务清单

### 1. 加密市场量化交易基础概念
- [ ] 学习什么是Alpha策略
- [ ] 了解Beta和Alpha的区别
- [ ] 学习加密市场的特点（24小时交易、高波动、全球市场）
- [ ] 了解量化交易在加密市场的优势和局限性
- [ ] 学习加密市场特有概念：资金费率、永续合约、链上数据

### 2. 技术指标学习（加密市场适用）
- [ ] 移动平均线(MA) - 在加密市场中的应用
- [ ] MACD指标 - 适合高波动市场
- [ ] RSI指标 - 识别超买超卖
- [ ] 布林带(Bollinger Bands) - 波动率分析
- [ ] 成交量指标 - 加密市场成交量分析

### 3. 指标实现
- [ ] 使用Python实现MA计算
- [ ] 实现MACD指标
- [ ] 实现RSI指标
- [ ] 实现布林带计算

### 4. 指标可视化
- [ ] 使用matplotlib绘制技术指标
- [ ] 分析指标的有效性
- [ ] 记录指标使用心得

## 今日成果物
1. 技术指标计算代码
2. 指标可视化图表
3. 指标分析报告

## 示例代码

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import ccxt

# 获取加密货币数据
def get_crypto_data(symbol='BTC/USDT', timeframe='1h', limit=200):
    exchange = ccxt.binance()
    ohlcv = exchange.fetch_ohlcv(symbol, timeframe, limit=limit)
    df = pd.DataFrame(ohlcv, columns=['timestamp', 'open', 'high', 'low', 'close', 'volume'])
    df['datetime'] = pd.to_datetime(df['timestamp'], unit='ms')
    df.set_index('datetime', inplace=True)
    return df

def calculate_ma(data, window=20):
    return data['close'].rolling(window=window).mean()

def calculate_macd(data, fast=12, slow=26, signal=9):
    exp1 = data['close'].ewm(span=fast, adjust=False).mean()
    exp2 = data['close'].ewm(span=slow, adjust=False).mean()
    macd = exp1 - exp2
    signal_line = macd.ewm(span=signal, adjust=False).mean()
    return macd, signal_line

def calculate_rsi(data, period=14):
    delta = data['close'].diff()
    gain = (delta.where(delta > 0, 0)).rolling(window=period).mean()
    loss = (-delta.where(delta < 0, 0)).rolling(window=period).mean()
    rs = gain / loss
    return 100 - (100 / (1 + rs))

def calculate_bollinger_bands(data, window=20, num_std=2):
    """计算布林带
    
    Args:
        data: 包含'close'列的DataFrame
        window: 移动平均窗口大小
        num_std: 标准差倍数
        
    Returns:
        (中轨, 上轨, 下轨)
    """
    # 计算中轨（20日移动平均线）
    middle_band = data['close'].rolling(window=window).mean()
    
    # 计算标准差
    std = data['close'].rolling(window=window).std()
    
    # 计算上轨和下轨
    upper_band = middle_band + (std * num_std)
    lower_band = middle_band - (std * num_std)
    
    return middle_band, upper_band, lower_band

def plot_indicators(data):
    """绘制加密货币技术指标图表"""
    fig, (ax1, ax2, ax3, ax4) = plt.subplots(4, 1, figsize=(12, 10))
    
    # 价格和MA
    ax1.plot(data.index, data['close'], label='Close Price', color='blue')
    ax1.plot(data.index, calculate_ma(data), label='MA20', color='orange')
    ax1.legend()
    ax1.set_title('BTC/USDT Price and Moving Average')
    ax1.set_ylabel('Price (USDT)')
    
    # MACD
    macd, signal = calculate_macd(data)
    ax2.plot(data.index, macd, label='MACD', color='blue')
    ax2.plot(data.index, signal, label='Signal Line', color='orange')
    ax2.axhline(y=0, color='gray', linestyle='--', alpha=0.5)
    ax2.legend()
    ax2.set_title('MACD Indicator')
    ax2.set_ylabel('MACD')
    
    # RSI
    rsi = calculate_rsi(data)
    ax3.plot(data.index, rsi, label='RSI', color='purple')
    ax3.axhline(y=70, color='r', linestyle='--', label='Overbought')
    ax3.axhline(y=30, color='g', linestyle='--', label='Oversold')
    ax3.set_ylim(0, 100)
    ax3.legend()
    ax3.set_title('RSI Indicator')
    ax3.set_ylabel('RSI')
    
    # 布林带
    middle, upper, lower = calculate_bollinger_bands(data)
    ax4.plot(data.index, data['close'], label='Close Price', color='blue')
    ax4.plot(data.index, middle, label='Middle Band', color='red')
    ax4.plot(data.index, upper, label='Upper Band', color='green')
    ax4.plot(data.index, lower, label='Lower Band', color='green')
    ax4.fill_between(data.index, upper, lower, alpha=0.1, color='green')
    ax4.legend()
    ax4.set_title('Bollinger Bands')
    ax4.set_ylabel('Price (USDT)')
    
    plt.tight_layout()
    plt.show()

# 使用示例
data = get_crypto_data('BTC/USDT', '1h', 200)
plot_indicators(data)
```