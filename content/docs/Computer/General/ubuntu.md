---
linkTitle: Ubuntu 安装
title: Ubuntu 安装
---

#### 准备工作

- 一个 8G 以上的 u 盘或移动硬盘
- 修改 BIOS 模式为 UEFI
- 修改磁盘的分区形式为 GPT
- 挑选一个磁盘压缩出大于 128 GB 的未分配分区

#### 制作安装盘

1. 下载 ventoy 等工具，将 u 盘或移动硬盘制作为安装盘
2. 下载 Ubuntu 映像文件并拷贝到安装盘内
  - https://launchpad.net/ubuntu/+mirror/mirrors.tuna.tsinghua.edu.cn-release

#### 修改 BIOS 设置

1. 重启电脑并进入 BIOS 设置
2. 关闭安全启动 (Secure Boot)
3. 在 Boot 启动顺序中将 u 盘放置到最前
4. 保存设置并再次重启

#### 安装 Ubuntu

- 忽略连接网络
- 勾选“为图形或无线硬件，以及其他媒体格式安装第三方软件”
- 手动分配分区，选择之前压缩出来的未分配分区