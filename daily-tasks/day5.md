# Day 5: 策略优化

## 今日目标
学习策略优化方法，实现参数优化和仓位管理

## 任务清单

### 1. 参数优化方法
- [ ] 学习网格搜索优化
- [ ] 了解遗传算法优化
- [ ] 实现参数优化框架
- [ ] 分析优化结果

### 2. 仓位管理
- [ ] 实现固定仓位管理
- [ ] 实现动态仓位管理
- [ ] 实现凯利公式仓位
- [ ] 测试不同仓位管理方法

### 3. 多币种策略（加密市场）
- [ ] 实现币种池管理（BTC, ETH, BNB等主流币）
- [ ] 添加选币逻辑（基于市值、流动性、相关性）
- [ ] 实现资金分配（多币种组合）
- [ ] 测试多币种策略
- [ ] 考虑币种间的相关性（加密市场相关性高）

### 4. 优化结果分析
- [ ] 分析参数敏感性
- [ ] 评估优化效果
- [ ] 生成优化报告
- [ ] 确定最优参数

## 今日成果物
1. 参数优化框架代码
2. 仓位管理模块
3. 多股票策略代码
4. 优化分析报告

## 示例代码

```python
import backtrader as bt
import numpy as np
from itertools import product

class OptimizedStrategy(bt.Strategy):
    params = (
        ('fast_period', 10),
        ('slow_period', 30),
        ('position_size', 0.1),  # 仓位大小
    )

    def __init__(self):
        self.fast_ma = bt.indicators.SMA(period=self.p.fast_period)
        self.slow_ma = bt.indicators.SMA(period=self.p.slow_period)
        self.crossover = bt.indicators.CrossOver(self.fast_ma, self.slow_ma)

    def next(self):
        if not self.position:
            if self.crossover > 0:
                size = int(self.broker.getcash() * self.p.position_size / self.data.close[0])
                self.buy(size=size)
        elif self.crossover < 0:
            self.close()

def grid_search_optimization(data, strategy_class, param_grid):
    """
    网格搜索优化
    """
    best_params = None
    best_sharpe = -np.inf
    
    # 生成所有参数组合
    param_names = list(param_grid.keys())
    param_values = list(param_grid.values())
    param_combinations = list(product(*param_values))
    
    for params in param_combinations:
        param_dict = dict(zip(param_names, params))
        
        # 运行回测
        cerebro = bt.Cerebro()
        cerebro.addstrategy(strategy_class, **param_dict)
        cerebro.adddata(bt.feeds.PandasData(dataname=data))
        cerebro.broker.setcash(100000.0)
        cerebro.broker.setcommission(commission=0.001)
        
        # 运行策略
        results = cerebro.run()
        strategy = results[0]
        
        # 计算夏普比率
        returns = pd.Series(strategy.returns)
        sharpe = calculate_sharpe_ratio(returns)
        
        # 更新最优参数
        if sharpe > best_sharpe:
            best_sharpe = sharpe
            best_params = param_dict
    
    return best_params, best_sharpe

def kelly_position_size(win_rate, win_loss_ratio):
    """
    凯利公式计算仓位
    """
    return win_rate - (1 - win_rate) / win_loss_ratio

# 参数网格
param_grid = {
    'fast_period': range(5, 21, 5),
    'slow_period': range(20, 61, 10),
    'position_size': [0.1, 0.2, 0.3]
}

# 运行优化
best_params, best_sharpe = grid_search_optimization(data, OptimizedStrategy, param_grid)
print(f"最优参数: {best_params}")
print(f"最优夏普比率: {best_sharpe}")
```

## 学习资源
1. [参数优化方法](https://www.quantstart.com/articles/Parameter-Optimization-for-Trading-Strategies/)
2. [凯利公式](https://www.investopedia.com/articles/trading/04/091504.asp)
3. [多资产策略](https://www.quantstart.com/articles/Portfolio-Optimization-in-Python/)

## 注意事项
- 注意过拟合问题
- 理解参数优化的局限性
- 记录不同参数组合的表现 