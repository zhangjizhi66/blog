---
linkTitle: 性能分析
title: 程序性能分析
---

1. 编译时加入 -g 调试选项（保存调用栈信息）
2. 运行 perf list 查看当前系统支持的性能事件
3. 运行 perf record -g -e [性能事件名称] [程序运行命令] （-g保存调用图信息，perf数据保存至perf.data）
4. 运行 perf report 解析 perf.data 文件，显示性能事件报告（可定位特定函数的性能事件）