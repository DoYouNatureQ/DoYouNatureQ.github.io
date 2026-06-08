---
layout: post
title: "JMComic-PDF-GUI项目发布"
date: 2026-06-02
last_modified_at: 2026-06-08
---

[JMComic-PDF-GUI](https://github.com/DoYouNatureQ/JMComic-PDF-GUI) - 禁漫天堂漫画下载 & PDF 合成图形化工具，
基于 [JMComic-Crawler-Python](https://github.com/hect0x7/JMComic-Crawler-Python) 二次开发，在原有下载功能基础上，增加了**图形化界面 (GUI)**、**搜索ID下载**、**PDF 自动合成**、**账号登录下载**等实用功能。

## 主要功能

- **图形化界面 (GUI)**：点点鼠标键盘即可完成登录、搜索、下载、PDF 合成
- **PDF 自动合成**：下载完成后自动将图片合并为 PDF，支持压缩质量调节
- **账号登录**：支持登录禁漫账号，同步收藏夹数据，批量下载收藏漫画
- **单章 / 多章下载**：智能识别本子章节数，单章合并为单个 PDF，多章分章节生成 PDF
- **漫画信息保存**：自动提取并保存本子的标题、作者、标签、简介为 TXT 文件
- **命令行模式**：也支持通过命令行快速下载（继承自原项目）
- **独立的EXE文件**：双击EXE文件即可使用

## 安装教程,或者[直接下载JM漫画下载器.exe](https://github.com/DoYouNatureQ/JMComic-PDF-GUI/releases)可跳过此步骤

> ⚠ 需要先安装 Python，版本 >= 3.9

```shell
pip install -r requirements.txt
```

本项目的 GUI 模式额外依赖：

```shell
pip install img2pdf pillow
```

## 快速上手

### 方式一：GUI 图形界面（推荐）

[下载JM漫画下载器.exe](https://github.com/DoYouNatureQ/JMComic-PDF-GUI/releases)`,双击即可运行`

GUI 界面支持以下操作流程：

1. **登录账号**（可选）—— 登录后可同步收藏夹
2. **搜索漫画** —— 输入关键词或本子 ID 搜索
3. **选择章节** —— 勾选要下载的章节
4. **一键下载** —— 自动下载图片并合成 PDF

### 方式二：命令行交互模式

双击运行 `AAA执行脚本.bat`，或执行：

```shell
python jm.py
```

按提示输入本子 ID，脚本会自动下载图片 → 保存漫画信息 → 合成 PDF。

## 配置文件

项目根目录下的两个配置文件：

| 文件 | 用途 |
|------|------|
| `option.yml` | 单章节本子下载配置 |
| `option2.yml` | 多章节本子下载配置（每个章节生成独立子文件夹） |

可根据需要修改下载路径、图片格式等参数。

## 项目结构

```
JMPdf/
├── jm.py                 # 命令行交互下载脚本
├── AAA执行脚本.bat        # Windows 一键启动脚本
├── option.yml             # 单章节配置
├── option2.yml            # 多章节配置
├── GUI/                   # 图形化界面
│   ├── main.py            # GUI 入口
│   ├── 启动.bat           # GUI 一键启动
│   ├── gui/               # 界面模块
│   ├── core/              # 核心逻辑（下载、PDF合成、登录）
│   └── config.py          # GUI 配置
├── src/                   # jmcomic 模块源码
├── tests/                 # 测试代码
└── usage/                 # 使用示例
```

## 感谢

本项目基于 [hect0x7/JMComic-Crawler-Python](https://github.com/hect0x7/JMComic-Crawler-Python) 开发，感谢原作者的杰出工作。
