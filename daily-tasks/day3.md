# Day 3: 加密货币策略开发基础

## 今日目标
开发第一个加密货币量化策略，学习backtrader框架基础，了解加密市场策略特点

## 任务清单

### 1. Backtrader框架学习
- [ ] 了解Backtrader的基本概念
- [ ] 学习策略类(Strategy)的编写
- [ ] 了解数据加载方式
- [ ] 学习如何添加技术指标

### 2. 双均线策略实现（适配加密市场）
- [ ] 实现快速均线计算（考虑24小时交易）
- [ ] 实现慢速均线计算
- [ ] 编写交易信号生成逻辑
- [ ] 实现买卖点判断
- [ ] 考虑加密市场的高波动特性调整参数

### 3. 策略回测
- [ ] 设置回测参数
- [ ] 运行回测
- [ ] 分析回测结果
- [ ] 优化策略参数

### 4. 结果可视化
- [ ] 绘制策略收益曲线
- [ ] 绘制交易信号图
- [ ] 生成回测报告
- [ ] 分析策略表现

## 今日成果物
1. 完整的双均线策略代码
2. 策略回测报告
3. 可视化分析图表

## 示例代码

```python
import backtrader as bt
import pandas as pd
import ccxt

# 获取加密货币数据
def get_crypto_data_for_backtest(symbol='BTC/USDT', timeframe='1h', limit=500):
    exchange = ccxt.binance()
    ohlcv = exchange.fetch_ohlcv(symbol, timeframe, limit=limit)
    df = pd.DataFrame(ohlcv, columns=['timestamp', 'open', 'high', 'low', 'close', 'volume'])
    df['datetime'] = pd.to_datetime(df['timestamp'], unit='ms')
    df.set_index('datetime', inplace=True)
    return df[['open', 'high', 'low', 'close', 'volume']]

class CryptoDualMAStrategy(bt.Strategy):
    params = (
        ('fast_period', 12),   # 加密市场参数调整
        ('slow_period', 26),   # 加密市场参数调整
    )

    def __init__(self):
        self.fast_ma = bt.indicators.SMA(period=self.p.fast_period)
        self.slow_ma = bt.indicators.SMA(period=self.p.slow_period)
        self.crossover = bt.indicators.CrossOver(self.fast_ma, self.slow_ma)
        
        # 添加成交量指标（加密市场重要）
        self.volume_sma = bt.indicators.SMA(self.data.volume, period=20)

    def next(self):
        # 只有在成交量足够时才交易（避免低流动性时段）
        if self.data.volume[0] < self.volume_sma[0] * 0.5:
            return
            
        if not self.position:
            if self.crossover > 0:
                self.buy()
        elif self.crossover < 0:
            self.close()

def run_crypto_backtest(symbol='BTC/USDT', timeframe='1h', limit=500):
    cerebro = bt.Cerebro()
    cerebro.addstrategy(CryptoDualMAStrategy)
    
    # 获取并加载数据
    df = get_crypto_data_for_backtest(symbol, timeframe, limit)
    data = bt.feeds.PandasData(dataname=df)
    cerebro.adddata(data)
    
    # 设置初始资金（USDT）
    cerebro.broker.setcash(10000.0)
    
    # 设置交易手续费（币安现货手续费约0.1%）
    cerebro.broker.setcommission(commission=0.001)
    
    # 运行回测
    print(f'初始资金: {cerebro.broker.getvalue():.2f} USDT')
    cerebro.run()
    print(f'最终资金: {cerebro.broker.getvalue():.2f} USDT')
    print(f'收益率: {(cerebro.broker.getvalue() / 10000.0 - 1) * 100:.2f}%')
    
    # 绘制结果
    cerebro.plot()

# 运行回测
run_crypto_backtest('BTC/USDT', '1h', 500)
```

## 学习资源
1. [Backtrader官方文档](https://www.backtrader.com/docu/)
2. [Backtrader示例代码](https://github.com/mementum/backtrader/tree/master/samples)
3. [加密货币量化策略](https://www.binance.com/en/support/faq)
4. [量化策略开发指南](https://www.quantstart.com/articles/Backtesting-a-Moving-Average-Crossover-Strategy-in-Python-with-pandas/)

## 注意事项
- 加密市场24小时交易，没有开盘/收盘价，需要适应连续数据
- 加密市场波动率极高，止损止盈参数需要相应调整
- 注意回测中的过拟合问题，加密市场变化快
- 理解交易成本的影响（现货约0.1%，合约约0.02-0.05%）
- 记录策略参数对结果的影响，找到适合加密市场的参数
- 考虑不同时段的流动性差异（亚洲、欧洲、美洲时段） 