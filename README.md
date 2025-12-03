# 7-Day Quantitative Trading Basics

> A 7-day quantitative trading learning journey guided by AI agent conversations to develop personalized learning strategies.

[![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)](https://www.python.org/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

## 📖 项目简介

本项目是一个为期 7 天的加密货币量化交易入门学习计划。通过 AI agent 对话的方式，制定个性化的学习策略，帮助具有计算机背景和投资经验的学习者快速掌握量化交易基础知识。

### ✨ 项目特色

- 🤖 **AI 指导学习**: 通过 AI agent 对话制定个性化学习策略
- 📚 **系统化学习**: 7 天完整学习路径，从基础到实践
- 💰 **加密货币聚焦**: 专门针对加密市场的量化交易
- 🛠️ **实战项目**: 完整的量化交易系统实现
- 📊 **回测系统**: 包含完整的回测框架和风险分析

## 🎯 学习目标

7 天后你将能够：

- ✅ 搭建完整的加密货币量化交易系统
- ✅ 实现基础的量化策略（支持现货和永续合约）
- ✅ 进行回测并评估策略表现
- ✅ 了解加密货币市场的特点和量化交易工作流程
- ✅ 掌握资金费率、链上数据、订单流等加密市场特有概念

## 📅 学习计划

| 天数 | 主题 | 文档 |
|------|------|------|
| Day 1 | 环境搭建与加密市场数据获取 | [day1.md](daily-tasks/day1.md) |
| Day 2 | 量化交易基础概念与技术指标 | [day2.md](daily-tasks/day2.md) |
| Day 3 | 加密货币策略开发基础 | [day3.md](daily-tasks/day3.md) |
| Day 4 | 回测系统（考虑24小时交易和资金费率） | [day4.md](daily-tasks/day4.md) |
| Day 5 | 策略优化与多币种策略 | [day5.md](daily-tasks/day5.md) |
| Day 6 | 加密市场风险管理 | [day6.md](daily-tasks/day6.md) |
| Day 7 | 系统集成与测试 | [day7.md](daily-tasks/day7.md) |

### 📋 每日学习内容概览

#### Day 1: 环境搭建与加密市场数据获取
- 完成开发环境设置
- 学习获取加密货币市场数据
- 了解加密市场24小时交易的特点
- [查看详细任务](daily-tasks/day1.md)

#### Day 2: 量化交易基础概念与技术指标
- 理解量化交易的核心概念
- 学习常用技术指标（MA、MACD、RSI、布林带等）
- 实现技术指标计算和可视化
- [查看详细任务](daily-tasks/day2.md)

#### Day 3: 加密货币策略开发基础
- 学习策略开发的基本框架
- 实现双均线策略
- 了解加密市场策略的特点
- [查看详细任务](daily-tasks/day3.md)

#### Day 4: 回测系统
- 搭建回测框架
- 考虑24小时交易和资金费率
- 评估策略表现
- [查看详细任务](daily-tasks/day4.md)

#### Day 5: 策略优化与多币种策略
- 策略参数优化
- 多币种策略开发
- 策略组合管理
- [查看详细任务](daily-tasks/day5.md)

#### Day 6: 加密市场风险管理
- 高波动市场的风险控制
- 杠杆控制策略
- 止损和仓位管理
- [查看详细任务](daily-tasks/day6.md)

#### Day 7: 系统集成与测试
- 整合所有组件
- 系统测试和优化
- 文档编写和项目总结
- [查看详细任务](daily-tasks/day7.md)

## 🚀 快速开始

### 环境要求

- Python 3.8+
- pip 包管理器

### 安装步骤

1. **克隆项目**
   ```bash
   git clone https://github.com/voidmomo/7-day-quantitative-trading-basics.git
   cd 7-day-quantitative-trading-basics
   ```

2. **安装依赖**
   ```bash
   pip install pandas numpy ccxt python-binance backtrader matplotlib websocket
   ```

3. **配置 API 密钥**
   - 参考 [Binance API 设置指南](docs/binance_api_setup.md)
   - 创建 `.env` 文件并添加你的 API 密钥（仅读取权限）

4. **开始学习**
   - 按照 [Day 1](daily-tasks/day1.md) 开始你的学习之旅

## 🛠️ 技术栈

### 核心库

- **数据处理**: `pandas`, `numpy`
- **交易所 API**: `ccxt`, `python-binance`
- **回测框架**: `backtrader`
- **可视化**: `matplotlib`
- **实时数据**: `websocket`

### 加密市场特点

- ⏰ **24小时交易**: 没有开盘收盘，需要适应连续数据
- 📈 **高波动率**: 日波动率可达5-10%，需要更严格的风险控制
- 🌍 **全球市场**: 不同时区都有交易活动，流动性分布不均
- ⚡ **杠杆交易**: 永续合约支持高杠杆，需要谨慎使用
- 💸 **资金费率**: 永续合约特有的费用机制，影响策略收益

## 📚 学习资源

### 推荐阅读

- [CCXT 官方文档](https://docs.ccxt.com/)
- [Binance API 文档](https://binance-docs.github.io/apidocs/)
- [Freqtrade 文档](https://www.freqtrade.io/)

### 加密市场特有概念

- **资金费率 (Funding Rate)**: 永续合约每8小时结算的费用
- **永续合约 (Perpetual Swap)**: 没有到期日的期货合约
- **链上数据 (On-chain Data)**: 区块链上的交易、地址、持仓等数据
- **订单流分析 (Order Flow)**: 分析买卖订单的流动和分布

## ⚠️ 重要提示

1. **安全第一**: API 密钥只给读取权限，不要给交易权限
2. **风险控制**: 加密市场波动大，建议使用小资金测试
3. **数据质量**: 注意数据获取频率限制，避免被封
4. **回测局限性**: 回测结果不代表实盘表现，需要充分测试
5. **合规交易**: 遵守当地法律法规，不要进行非法交易

## 🎓 学习建议

1. 每天保持 1-2 小时的学习时间，建议固定时间学习
2. 多动手实践，不要只看理论
3. 遇到问题及时在网上搜索或请教
4. 保持代码的整洁和可维护性
5. 记录学习过程中的问题和心得
6. 先在测试网或模拟环境练习，再考虑实盘

## 📊 最终成果物

7 天学习结束后，你将拥有：

1. **完整的加密货币量化交易系统代码**
   - 数据获取模块（支持多个交易所）
   - 策略实现模块（双均线、MACD等）
   - 回测模块（考虑资金费率、24小时交易）
   - 风险管理模块（高波动、杠杆控制）

2. **策略回测报告**
   - 收益率分析
   - 风险指标分析（夏普比率、最大回撤等）
   - 交易记录
   - 资金费率影响分析

3. **项目文档**
   - 系统架构说明
   - 使用说明
   - API 文档
   - 后续改进计划

## 🤝 贡献

欢迎提交 Issue 和 Pull Request 来改进这个学习计划！

## 📄 许可证

本项目采用 MIT 许可证 - 查看 [LICENSE](LICENSE) 文件了解详情

## 👤 作者

**voidmomo**

- GitHub: [@voidmomo](https://github.com/voidmomo)

## 📞 联系方式

如有问题或建议，欢迎通过以下方式联系：

- 提交 [Issue](https://github.com/voidmomo/7-day-quantitative-trading-basics/issues)
- 发送 Pull Request

---

⭐ 如果这个项目对你有帮助，请给个 Star！
