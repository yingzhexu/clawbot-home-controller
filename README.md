# Clawbot-Home-Controller (Mlotbot) 🏠🤖

> **Clawbot (aka Mlotbot)**: A bridge connecting local AI Agents to Xiaomi/Mijia Smart Home.
> **Clawbot (又名 Mlotbot)**：连接本地 AI Agent 与米家智能家居的桥梁。

## 📖 Introduction / 简介

**English:**
This project provides a solution to use a local AI Agent (Clawbot / Mlotbot) running on **macOS (Apple Silicon)** to control Xiaomi/Mijia smart devices via Home Assistant. By sending simple cURL commands, your AI can turn lights on/off, control switches, and execute smart scenes.

**中文:**
本项目提供了一套解决方案，利用 **Mac mini (Apple Silicon)** 强大的本地算力，部署 **Home Assistant**，让本地 AI Agent (Clawbot / Mlotbot) 能够通过执行 cURL 命令来控制家里的灯光、开关等米家设备。

**Core Logic / 核心原理:**
`User` -> `Clawbot / Mlotbot` -> `Local cURL Command` -> `Home Assistant (Docker)` -> `Xiaomi Miot Auto` -> `Smart Devices`

---

## 🛠 Prerequisites / 前置要求

* **Hardware / 硬件**: Mac (M1/M2/M3/M4 chip recommended / 推荐 Apple Silicon 芯片)
* **Software / 软件**:
    * [Docker Desktop for Mac](https://www.docker.com/products/docker-desktop/)
    * Clawbot / Mlotbot (or any AI Agent capable of executing local Shell commands / 或任何能执行本地 Shell 命令的 AI)
* **Account / 账号**: Mi Home Account / 米家账号

---

## 🚀 Installation / 部署步骤

### Step 1: Deploy Home Assistant via Docker
### 第一步：在 Docker 中部署 Home Assistant

Run the following command in your Mac Terminal:
在 Mac 终端 (Terminal) 中运行以下命令：

docker run -d \
  --name homeassistant \
  --privileged \
  --restart=unless-stopped \
  -e TZ=Asia/Shanghai \
  -v ~/homeassistant:/config \
  -p 8123:8123 \
  homeassistant/home-assistant:stable# clawbot-home-controller
Control Xiaomi smart home with Clawbot AI on Mac


After startup, visit http://localhost:8123 to create an account. 启动后，访问 http://localhost:8123 创建账号并登录。

### Step 2: Install HACS & Xiaomi Miot Auto
### 第二步：安装 HACS 和米家插件

**1. Install HACS (Plugin Store) / 安装 HACS:**
Enter the container and run the installation script:
进入容器并运行安装脚本：


docker exec -it homeassistant bash
wget -O - [https://get.hacs.xyz](https://get.hacs.xyz) | bash
exit

Note: Restart Home Assistant after installation. / 注意：安装完成后必须重启 Home Assistant。

2. Install Xiaomi Miot Auto / 安装米家插件:

Go to HACS -> Integrations -> Search Xiaomi Miot Auto -> Download.

Restart HA. Go to Settings -> Devices & Services -> Add Integration -> Search Xiaomi Miot Auto.

Select Account Integrated and log in with your Mi Home account.

Filter Devices: Use Include mode to select only the devices you need (e.g., Lights, Switches).

在 HACS 中下载 Xiaomi Miot Auto。

重启 HA，在“集成”中添加它，选择 账号集成 (Account Integrated) 并登录。

建议使用 Include (包含) 模式，只勾选需要控制的设备

Step 3: Get API Token

第三步：获取控制权限 (Token)

To allow Clawbot/Mlotbot to control HA, you need a Long-Lived Access Token. 为了让 AI 能控制 HA，需要生成长期访问令牌。

Click your User Profile (bottom left) in HA. / 点击 HA 左下角用户头像。

Scroll down to Long-Lived Access Tokens. / 滚动到底部“长期访问令牌”。

Click Create Token. / 点击“创建令牌”。

Save this Token! / 保存好这串 Token！

Step 4: Get Entity ID

第四步：获取设备实体 ID

Go to Overview -> Click Device Icon -> Settings (Gear Icon) -> Copy Entity ID. 在 HA 面板 -> 概览 -> 点击设备图标 -> 设置 (齿轮) -> 复制 实体 ID。

Example / 例如: light.bedroom_lamp, switch.kitchen_switch

🤖 AI Configuration / AI 配置
Teach Clawbot/Mlotbot to control devices using the following prompt template. 将以下 Prompt 和指令模板发送给 Clawbot/Mlotbot，让它学习如何控制设备。

Universal cURL Template / 通用控制模板:
curl -X POST \
  -H "Authorization: Bearer <YOUR_LONG_LIVED_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"entity_id": "<DEVICE_ENTITY_ID>"}' \
  [http://127.0.0.1:8123/api/services/](http://127.0.0.1:8123/api/services/)<domain>/<service>

<domain>: switch or light (switches or lights / 开关或灯)

<service>: turn_on, turn_off, toggle (On/Off/Toggle / 开/关/切换)

💡 Example Commands / 指令示例

1. Turn on a Light / 打开灯光:

"When I say 'Turn on the lamp', execute this:" "当我说'打开台灯'时，请执行：
"curl -X POST \
  -H "Authorization: Bearer YOUR_TOKEN_HERE" \
  -H "Content-Type: application/json" \
  -d '{"entity_id": "light.yeelink_lamp2"}' \
  [http://127.0.0.1:8123/api/services/light/turn_on](http://127.0.0.1:8123/api/services/light/turn_on)

2. Turn off Kitchen Switches (Batch) / 关闭厨房开关 (批量):

"When I say 'Turn off kitchen lights', execute this:" "当我说'关闭厨房灯'时，请执行："
curl -X POST \
  -H "Authorization: Bearer YOUR_TOKEN_HERE" \
  -H "Content-Type: application/json" \
  -d '{"entity_id": ["switch.kitchen_left", "switch.kitchen_right"]}' \
  [http://127.0.0.1:8123/api/services/switch/turn_off](http://127.0.0.1:8123/api/services/switch/turn_off)

⚠️ Notes / 注意事项
Network / 网络: If Docker fails to pull images, check your proxy settings. / 如果镜像拉取失败，请检查 Docker 代理设置。

Localhost: Use 127.0.0.1 instead of localhost in cURL commands to ensure stability. / 建议在 cURL 命令中使用 127.0.0.1 以避免解析问题。

Security / 安全: Never share your Token publicly. / 请勿公开分享你的 Token。

Created by Ying
