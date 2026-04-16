---
linkTitle: 代码框架
title: 代码框架
---

- 交易品种：A 股起步，后续扩展港股 / 美股，预留期货接口
- 行情周期：1 分钟 K 线为基础，自动合成多周期（5/15/60min）
- 行情数据：默认 Level-1，预留 Level-2 扩展
- 持仓模式：策略自由决定，支持日内 / 隔夜 / 多日
- 交易模式：全自动 + 半自动可切换
- 回测：完整 C++ 回测引擎 + 参数优化 + 绩效报表
- 运行环境：Linux 服务器
- 账户：先单账户，预留多账户扩展
- 风控：先基础风控，模块化预留进阶扩展
- 日志告警：基础日志，预留告警扩展
- 交互界面：先命令行后台，预留 UI/Web 面板扩展
- 数据存储：时序数据库（TimescaleDB/QuestDB）
- 策略加载：Linux .so 动态库热加载
- 模拟环境：本地虚拟模拟盘 + 官方接口全仿真模拟盘
- 策略类型：开放式插件框架，支持趋势 / 回归 / 套利 / 混合
- 订单类型：市价、限价、止盈止损条件单，预留高级订单
- 性能要求：订单处理延迟≤20ms，预留低延迟优化空间

QuantTrader/
├── CMakeLists.txt              # 顶层编译配置
├── include/
│   └── QuantTrader.h           # 全局公共头文件（宏、命名空间、基础类型）
├── src/
│   ├── main.cpp                # 程序入口
│   ├── common/                 # 公共模块
│   │   ├── Common.h            # 公共枚举、结构体
│   │   ├── Utils.h             # 工具函数（时间、字符串、动态库加载）
│   │   └── Utils.cpp           # 工具函数实现
│   ├── market/                 # 行情模块
│   │   ├── MarketData.h        # 行情数据定义
│   │   ├── KlineEngine.h       # K线合成引擎
│   │   └── KlineEngine.cpp     # 1分钟→多周期K线合成
│   ├── order/                  # 订单模块
│   │   ├── OrderDef.h          # 订单枚举、结构体
│   │   ├── OrderManager.h      # 订单管理
│   │   └── OrderManager.cpp    # 订单管理实现
│   ├── risk/                   # 风控模块
│   │   ├── RiskManager.h       # 基础风控
│   │   └── RiskManager.cpp     # 风控实现
│   └── engine/                 # 策略引擎
│       ├── StrategyEngine.h    # 策略调度、热加载
│       └── StrategyEngine.cpp  # 引擎实现
├── strategy/
│   ├── Strategy.h              # 策略抽象基类（核心接口）
│   ├── trend_strategy/
│   │   ├── TrendStrategy.h
│   │   └── TrendStrategy.cpp
│   ├── mean_strategy/
│   │   ├── MeanStrategy.h
│   │   └── MeanStrategy.cpp
│   └── arbitrage/
│       ├── ArbitrageStrategy.h
│       └── ArbitrageStrategy.cpp
├── gateway/                    # 交易网关
│   ├── Gateway.h               # 网关抽象基类
│   ├── a_share/
│   │   └── ASharesGateway.h/.cpp
│   ├── hk_us/
│   │   └── HKUSGateway.h/.cpp
│   ├── ctp/
│   │   └── CTPGateway.h/.cpp
│   └── simulate/
│       ├── SimGateway.h        # 模拟网关基类
│       ├── LocalSim.h          # 本地虚拟模拟盘
│       ├── LocalSim.cpp
│       └── OfficialSim.h/.cpp  # 官方仿真模拟盘（预留）
├── store/                      # 数据存储
│   ├── TSDBBase.h              # 时序数据库基类
│   ├── TSDBImpl.cpp            # TimescaleDB/QuestDB 实现
│   ├── KlineStore.h            # K线存储接口
│   ├── KlineStore.cpp
│   ├── OrderStore.h            # 订单/持仓存储
│   └── OrderStore.cpp
├── backtest/                   # 回测引擎
│   ├── BacktestEngine.h
│   ├── BacktestEngine.cpp
│   ├── Optimizer.h
│   ├── Optimizer.cpp
│   ├── Report.h
│   └── Report.cpp
├── config/
│   └── Config.h/.cpp           # 配置文件解析
├── log/
│   └── Logger.h/.cpp           # 日志模块
├── build/
├── bin/
└── lib/