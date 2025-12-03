# Day 6: 加密市场风险管理

## 今日目标
学习加密市场风险管理方法，实现风险控制功能（高波动、杠杆、24小时交易）

## 任务清单

### 1. 风险指标学习
- [ ] 学习波动率计算
- [ ] 了解VaR(Value at Risk)
- [ ] 学习最大回撤控制
- [ ] 了解风险平价策略

### 2. 波动率管理（加密市场高波动）
- [ ] 实现历史波动率计算（加密市场波动率通常50-100%+）
- [ ] 实现波动率预测
- [ ] 添加波动率控制（动态调整仓位）
- [ ] 测试波动率管理效果
- [ ] 考虑加密市场的极端波动事件（闪崩、暴涨）

### 3. 资金管理（加密市场杠杆）
- [ ] 实现资金分配
- [ ] 添加杠杆控制（建议不超过3-5倍，避免爆仓）
- [ ] 实现风险预算
- [ ] 测试资金管理效果
- [ ] 实现爆仓风险预警（永续合约）
- [ ] 考虑资金费率对收益的影响

### 4. 分散投资
- [ ] 实现相关性分析
- [ ] 添加分散度控制
- [ ] 实现资产配置
- [ ] 测试分散投资效果

## 今日成果物
1. 风险管理模块代码
2. 风险分析报告
3. 资金管理方案
4. 分散投资策略

## 示例代码

```python
import numpy as np
import pandas as pd
from scipy.stats import norm

class RiskManager:
    def __init__(self, returns, confidence_level=0.95):
        self.returns = returns
        self.confidence_level = confidence_level

    def calculate_volatility(self, window=20):
        """计算历史波动率"""
        return self.returns.rolling(window=window).std() * np.sqrt(252)

    def calculate_var(self, method='historical'):
        """计算VaR"""
        if method == 'historical':
            return np.percentile(self.returns, (1 - self.confidence_level) * 100)
        elif method == 'parametric':
            mean = self.returns.mean()
            std = self.returns.std()
            return norm.ppf(1 - self.confidence_level, mean, std)

    def calculate_correlation(self, other_returns):
        """计算相关性"""
        return self.returns.corr(other_returns)

    def risk_parity_weights(self, cov_matrix):
        """风险平价权重"""
        inv_vol = 1 / np.sqrt(np.diag(cov_matrix))
        weights = inv_vol / inv_vol.sum()
        return weights

class CryptoRiskAwareStrategy(bt.Strategy):
    params = (
        ('max_volatility', 0.5),  # 最大波动率（加密市场波动大）
        ('max_drawdown', 0.2),    # 最大回撤（20%）
        ('position_limit', 0.3),  # 单个头寸限制（30%）
        ('max_leverage', 3.0),    # 最大杠杆（3倍）
    )

    def __init__(self):
        self.risk_manager = RiskManager(self.returns)
        self.volatility = self.risk_manager.calculate_volatility()
        self.var = self.risk_manager.calculate_var()

    def next(self):
        # 检查波动率限制
        if self.volatility[0] > self.p.max_volatility:
            self.reduce_position()
            return

        # 检查回撤限制
        if self.drawdown > self.p.max_drawdown:
            self.close_all_positions()
            return

        # 执行交易逻辑
        self.execute_trades()

    def reduce_position(self):
        """降低仓位"""
        for data in self.datas:
            if self.getposition(data).size > 0:
                self.sell(data, size=self.getposition(data).size * 0.5)

    def close_all_positions(self):
        """关闭所有头寸"""
        for data in self.datas:
            if self.getposition(data).size > 0:
                self.close(data)

def calculate_portfolio_risk(weights, cov_matrix):
    """计算投资组合风险"""
    return np.sqrt(np.dot(weights.T, np.dot(cov_matrix, weights)))

def optimize_risk_parity(cov_matrix, target_risk=0.1):
    """优化风险平价"""
    n_assets = cov_matrix.shape[0]
    initial_weights = np.ones(n_assets) / n_assets
    
    def objective(weights):
        portfolio_risk = calculate_portfolio_risk(weights, cov_matrix)
        return (portfolio_risk - target_risk) ** 2
    
    # 使用优化器求解
    from scipy.optimize import minimize
    constraints = ({'type': 'eq', 'fun': lambda x: np.sum(x) - 1})
    bounds = [(0, 1) for _ in range(n_assets)]
    result = minimize(objective, initial_weights, 
                     method='SLSQP', bounds=bounds,
                     constraints=constraints)
    return result.x
```

## 学习资源
1. [风险管理基础](https://www.investopedia.com/terms/r/riskmanagement.asp)
2. [VaR计算方法](https://www.investopedia.com/terms/v/var.asp)
3. [风险平价策略](https://www.investopedia.com/terms/r/risk-parity.asp)

## 注意事项
- 注意风险指标的局限性
- 理解不同风险控制方法的适用场景
- 记录风险控制对策略表现的影响 