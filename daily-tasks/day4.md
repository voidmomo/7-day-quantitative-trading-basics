# Day 4: 回测系统（加密市场特化）

## 今日目标
完善回测框架，添加交易成本、资金费率、止损止盈功能，考虑24小时交易特点

## 任务清单

### 1. 回测系统完善（加密市场）
- [ ] 添加交易成本计算（现货0.1%，合约0.02-0.05%）
- [ ] 实现滑点模拟（加密市场滑点通常较小）
- [ ] 添加交易量限制（考虑流动性）
- [ ] 实现订单执行逻辑
- [ ] 添加资金费率计算（永续合约每8小时结算）
- [ ] 考虑24小时连续交易的特点

### 2. 止损止盈功能（加密市场高波动）
- [ ] 实现固定止损（建议2-5%，加密市场波动大）
- [ ] 实现跟踪止损（动态调整）
- [ ] 实现止盈功能（建议3-10%）
- [ ] 测试止损止盈效果
- [ ] 考虑加密市场的假突破问题

### 3. 策略评估指标
- [ ] 计算夏普比率
- [ ] 计算最大回撤
- [ ] 计算年化收益率
- [ ] 计算胜率

### 4. 回测报告生成
- [ ] 生成交易记录
- [ ] 生成收益曲线
- [ ] 生成风险指标报告
- [ ] 分析策略表现

## 今日成果物
1. 完整的回测框架代码
2. 策略评估报告
3. 交易记录分析

## 示例代码

```python
import backtrader as bt
import numpy as np

class CryptoStopLossStrategy(bt.Strategy):
    params = (
        ('stop_loss', 0.03),  # 3%止损（加密市场波动大）
        ('trail_stop', 0.02),  # 2%跟踪止损
        ('take_profit', 0.05),  # 5%止盈
        ('funding_rate', 0.0001),  # 资金费率（每8小时0.01%）
    )

    def __init__(self):
        self.order = None
        self.buyprice = None
        self.buycomm = None

    def notify_order(self, order):
        if order.status in [order.Submitted, order.Accepted]:
            return

        if order.status in [order.Completed]:
            if order.isbuy():
                self.buyprice = order.executed.price
                self.buycomm = order.executed.comm
            self.bar_executed = len(self)

        self.order = None

    def next(self):
        if self.order:
            return

        if not self.position:
            if self.crossover > 0:
                self.order = self.buy()
        else:
            if self.crossover < 0:
                self.order = self.sell()
            else:
                # 跟踪止损
                if self.data.close[0] < self.data.close[-1] * (1 - self.p.trail_stop):
                    self.order = self.sell()
                # 固定止损
                elif self.data.close[0] < self.buyprice * (1 - self.p.stop_loss):
                    self.order = self.sell()
                # 止盈
                elif self.data.close[0] > self.buyprice * (1 + self.p.take_profit):
                    self.order = self.sell()

def calculate_sharpe_ratio(returns, risk_free_rate=0.02):
    excess_returns = returns - risk_free_rate/252
    return np.sqrt(252) * excess_returns.mean() / excess_returns.std()

def calculate_max_drawdown(returns):
    cumulative = (1 + returns).cumprod()
    running_max = cumulative.expanding().max()
    drawdown = (cumulative - running_max) / running_max
    return drawdown.min()

def generate_report(strategy):
    returns = pd.Series(strategy.returns)
    sharpe = calculate_sharpe_ratio(returns)
    max_dd = calculate_max_drawdown(returns)
    annual_return = (1 + returns.mean()) ** 252 - 1
    
    report = {
        'Sharpe Ratio': sharpe,
        'Max Drawdown': max_dd,
        'Annual Return': annual_return,
        'Win Rate': strategy.win_rate,
        'Total Trades': strategy.total_trades
    }
    return report
```

## 学习资源
1. [回测系统设计](https://www.quantstart.com/articles/Backtesting-a-Moving-Average-Crossover-Strategy-in-Python-with-pandas/)
2. [风险指标计算](https://www.investopedia.com/terms/s/sharperatio.asp)
3. [止损策略设计](https://www.investopedia.com/articles/trading/08/trailing-stop-loss.asp)

## 注意事项
- 注意回测中的未来数据问题
- 理解不同止损策略的适用场景
- 记录策略在不同市场环境下的表现 