# MobileWorld 容器端口说明文档

## 端口概览

每个 MobileWorld 容器映射 **4 个外部端口** 到容器内部的 4 个服务端口。

| 外部端口 (Host) | 内部端口 (Container) | 服务名称 | 协议 | 用途 |
|-----------------|---------------------|---------|------|------|
| `6800` | `6800` | **Backend API** | HTTP | 控制模拟器的 REST API |
| `7860` | `7860` | **Device Viewer** | HTTP/WebSocket | 设备屏幕实时查看器 (Gradio) |
| `5800` | `5800` | **noVNC** | HTTP/WebSocket | 完整的 Android GUI 网页访问 |
| `5556` | `5555` | **ADB Relay** | TCP | ADB 远程连接端口 |

> **注意**: 多容器运行时，端口以 **100 为间隔** 递增。例如 5 个容器：
> - 容器 0: 6800, 7860, 5800, 5556
> - 容器 1: 6900, 7960, 5900, 5557 (ADB 递增为 +1)
> - 容器 2: 7000, 8060, 6000, 5558
> - ...

---

## 端口 1: Backend API (默认 `6800`)

### 服务说明
这是 MobileWorld 的 **核心控制接口**，基于 FastAPI 构建的 REST API 服务器。所有自动化操作、任务执行、评估流程都通过此端口与 Android 模拟器交互。

### 关键 API 端点

```
POST http://localhost:6800/init          # 初始化 Agent/任务
POST http://localhost:6800/step          # 执行一步操作
POST http://localhost:6800/health        # 健康检查
GET  http://localhost:6800/screenshot    # 获取当前截图
POST http://localhost:6800/click         # 点击坐标
POST http://localhost:6800/input_text    # 输入文本
POST http://localhost:6800/key           # 按键事件
POST http://localhost:6800/scroll        # 滚动操作
POST http://localhost:6800/navigate_home # 返回主页
POST http://localhost:6800/open_app      # 打开指定应用
POST http://localhost:6800/done          # 标记任务完成
```

### 如何使用

**1. 通过 CLI (推荐)**
```bash
# CLI 自动使用此端口
sudo uv run mw eval --agent_type general_e2e ...
```

**2. 直接 API 调用**
```bash
# 健康检查
curl http://localhost:6800/health

# 获取截图
curl http://localhost:6800/screenshot -o screen.png

# 点击坐标 (x=500, y=1000)
curl -X POST http://localhost:6800/click \
  -H "Content-Type: application/json" \
  -d '{"x": 500, "y": 1000}'

# 输入文本
curl -X POST http://localhost:6800/input_text \
  -H "Content-Type: application/json" \
  -d '{"text": "Hello World"}'

# 返回主页
curl -X POST http://localhost:6800/navigate_home
```

**3. 通过 Python SDK**
```python
import requests

base_url = "http://localhost:6800"

# 获取截图
resp = requests.get(f"{base_url}/screenshot")
with open("screen.png", "wb") as f:
    f.write(resp.content)

# 执行点击
requests.post(f"{base_url}/click", json={"x": 500, "y": 1000})

# 初始化任务
requests.post(f"{base_url}/init", json={"instruction": "Open Gmail and send an email"})
```

---

## 端口 2: Device Viewer (默认 `7860`)

### 服务说明
基于 **Gradio** 构建的实时设备屏幕查看器。提供 Web 界面实时查看 Android 模拟器屏幕，支持手动交互。

### 访问方式
```
浏览器打开: http://localhost:7860
```

### 功能
- **实时屏幕显示**: 查看当前模拟器屏幕状态
- **手动交互**: 通过网页直接点击、输入文本
- **调试用途**: 开发时快速验证自动化操作是否正确
- **非开发模式启动**: 通过 `mobile-world viewer` 命令启动

### 如何使用
1. 打开浏览器访问 `http://localhost:7860`
2. 看到实时 Android 屏幕
3. 可以直接在网页上与模拟器交互

> **注意**: 在 `--dev` 模式下，此端口会被 noVNC 替代 (见端口 3)。

---

## 端口 3: noVNC (默认 `5800`)

### 服务说明
完整的 **Android GUI 网页访问**。当启用 VNC 时 (`--dev` 或 `--vnc` 标志)，提供完整的 Android 桌面环境访问。

### 启动条件
- 使用 `--dev` 模式: `mw env run --dev`
- 或显式启用 VNC: `mw env run --vnc`

### 内部架构
```
Xvfb (虚拟显示器 :0)
    ↓
openbox (窗口管理器)
    ↓
x11vnc (VNC 服务器, 端口 5900)
    ↓
noVNC (Web VNC 客户端, 端口 5800)
    ↓
浏览器访问 http://localhost:5800/vnc.html
```

### 访问方式
```
浏览器打开: http://localhost:5800/vnc.html
```

### 功能
- **完整 Android GUI**: 看到真实的 Android 界面 (非截图)
- **实时交互**: 直接在浏览器中操作 Android 系统
- **开发调试**: 配合 `--dev` 模式，实时查看代码修改效果
- **手动执行任务**: 可以手动完成任务来验证任务设计

### 如何使用

**1. 启动带 VNC 的容器**
```bash
# 开发模式 (自动启用 VNC)
sudo uv run mw env run --dev

# 或仅启用 VNC
sudo uv run mw env run --vnc
```

**2. 访问 noVNC**
- 浏览器打开: `http://localhost:5800/vnc.html`
- 点击 "Connect" 即可看到 Android 界面

**3. 在容器内手动执行任务**
```bash
# 进入容器
mw env exec mobile_world_env_0

# 手动运行任务
uv run python src/mobile_world/tasks/test_task.py \
  --task CheckGithubInfoTask \
  --question "What is today?"
```
然后通过 noVNC 界面手动完成任务。

---

## 端口 4: ADB Relay (默认 `5556`)

### 服务说明
通过 **socat** 将容器内的 ADB 服务 (端口 5555) 中继到宿主机，允许宿主机直接通过 ADB 连接模拟器。

### 内部架构
```
容器内: adb server (localhost:5555)
    ↓
socat TCP-LISTEN:5556 → TCP:127.0.0.1:5555
    ↓
宿主机: localhost:5556
```

### 如何使用

**1. 连接 ADB**
```bash
# 查看设备
adb -s localhost:5556 devices

# 或直接连接
adb connect localhost:5556
adb devices
```

**2. 常用 ADB 操作**
```bash
# 截图
adb -s localhost:5556 shell screencap -p /sdcard/screen.png
adb -s localhost:5556 pull /sdcard/screen.png

# 安装 APK
adb -s localhost:5556 install my_app.apk

# 查看界面层次 (UI Hierarchy)
adb -s localhost:5556 shell uiautomator dump /sdcard/ui.xml
adb -s localhost:5556 pull /sdcard/ui.xml

# 输入文本
adb -s localhost:5556 shell input text "Hello"

# 点击坐标
adb -s localhost:5556 shell input tap 500 1000

# 返回键
adb -s localhost:5556 shell input keyevent 4

# 主页键
adb -s localhost:5556 shell input keyevent 3

# 查看应用列表
adb -s localhost:5556 shell pm list packages

# 查看日志 (logcat)
adb -s localhost:5556 logcat

# 进入 shell
adb -s localhost:5556 shell
```

**3. 查看模拟器状态**
```bash
# 检查启动状态
adb -s localhost:5556 shell getprop sys.boot_completed

# 查看网络状态
adb -s localhost:5556 shell dumpsys wifi

# 查看电池状态
adb -s localhost:5556 shell dumpsys battery
```

---

## SSH 端口转发：从远程服务器访问 MobileWorld

当 MobileWorld 容器运行在远程服务器 (如 GPU 服务器) 上时，可以通过 SSH 隧道将端口转发到本地机器，实现远程操作模拟器。

### 基本原理

```
本地机器 ──SSH──→ 远程服务器 ──端口映射──→ Docker 容器
                                              ↓
                                         Android 模拟器
```

### SSH 转发指令

**基础命令格式：**
```bash
ssh -L <本地端口>:localhost:<远程端口> -N <用户名>@<远程服务器IP>
```

**转发全部 4 个端口 (单条命令)：**
```bash
ssh -L 6800:localhost:6800 \
    -L 7860:localhost:7860 \
    -L 5800:localhost:5800 \
    -L 5556:localhost:5556 \
    -N user@remote-server-ip
```

**参数说明：**
| 参数 | 含义 |
|------|------|
| `-L` | 本地端口转发，格式 `本地端口:目标主机:目标端口` |
| `localhost` | 目标主机 (从远程服务器视角，容器端口映射到 localhost) |
| `-N` | 不执行远程命令，仅做端口转发 |
| `-f` | (可选) 后台运行，追加此参数可将 SSH 放到后台 |
| `-o ExitOnForwardFailure=yes` | (可选) 端口转发失败时退出 |

### 使用场景

#### 场景 1: 从本地浏览器查看远程模拟器屏幕

```bash
# 建立 SSH 隧道
ssh -L 7860:localhost:7860 -L 5800:localhost:5800 -N user@remote-server

# 本地浏览器访问
http://localhost:7860        # Device Viewer
http://localhost:5800/vnc.html  # noVNC (需 --dev 或 --vnc 启动容器)
```

#### 场景 2: 从本地 CLI 调用远程 API

```bash
# 建立隧道后，本地直接调用
curl http://localhost:6800/health
curl http://localhost:6800/screenshot -o screen.png
```

#### 场景 3: 通过 ADB 连接远程模拟器

```bash
# 建立隧道后
adb connect localhost:5556
adb devices
# 输出: localhost:5556    device

# 操作模拟器
adb -s localhost:5556 shell input tap 500 1000
adb -s localhost:5556 shell screencap -p /sdcard/screen.png
```

#### 场景 4: 后台持久化隧道

```bash
# 后台运行，不阻塞终端
ssh -f -L 6800:localhost:6800 \
       -L 7860:localhost:7860 \
       -L 5800:localhost:5800 \
       -L 5556:localhost:5556 \
       -N -o ExitOnForwardFailure=yes \
       user@remote-server

# 查看隧道进程
ps aux | grep "ssh.*-L"

# 关闭隧道
pkill -f "ssh.*-L.*6800"
```

#### 场景 5: 自定义本地端口 (避免冲突)

如果本地端口已被占用，可使用不同端口：

```bash
ssh -L 16800:localhost:6800 \
    -L 17860:localhost:7860 \
    -L 15800:localhost:5800 \
    -L 15556:localhost:5556 \
    -N user@remote-server

# 访问时使用自定义端口
curl http://localhost:16800/health
浏览器: http://localhost:17860
adb connect localhost:15556
```

#### 场景 6: 多容器转发

远程运行了多个容器时，全部转发：

```bash
ssh -L 6800:localhost:6800 -L 7860:localhost:7860 -L 5800:localhost:5800 -L 5556:localhost:5556 \
    -L 6900:localhost:6900 -L 7960:localhost:7960 -L 5900:localhost:5900 -L 5557:localhost:5557 \
    -L 7000:localhost:7000 -L 8060:localhost:8060 -L 6000:localhost:6000 -L 5558:localhost:5558 \
    -N user@remote-server
```

### SSH 配置优化

在 `~/.ssh/config` 中添加配置简化命令：

```
Host mobileworld-server
    HostName 192.168.1.100
    User your-username
    LocalForward 6800 localhost:6800
    LocalForward 7860 localhost:7860
    LocalForward 5800 localhost:5800
    LocalForward 5556 localhost:5556
    ServerAliveInterval 60
    ServerAliveCountMax 3
```

配置后只需执行：
```bash
ssh -N mobileworld-server
```

### 注意事项

1. **容器必须先启动**: 远程服务器上需要先运行 `mw env run` 启动容器
2. **防火墙规则**: 确保远程服务器防火墙允许 SSH 连接 (端口 22)
3. **保持连接**: SSH 隧道断开后端口转发失效，可使用 `autossh` 保持连接：
   ```bash
   autossh -M 0 -N -o "ServerAliveInterval 30" -o "ServerAliveCountMax 3" \
       -L 6800:localhost:6800 -L 7860:localhost:7860 \
       -L 5800:localhost:5800 -L 5556:localhost:5556 \
       user@remote-server
   ```
4. **ADB 版本兼容**: 本地 ADB 版本应与容器内兼容，建议保持版本一致
5. **延迟**: 网络延迟会影响截图和操作响应速度，局域网内通常无明显影响

---

## 模拟器管理：启动、重置与快照

### 1. 启动模拟器

模拟器在容器创建时自动启动。如需手动启动或重启，有以下几种方式：

#### 方式 A: 通过 CLI 重启容器内服务器 (推荐)

```bash
# 重启单个容器的服务器
mw env restart mobile_world_env_0

# 重启所有容器的服务器
mw env restart
```

此命令会：
1. 找到容器内的 MobileWorld server 进程
2. 终止旧进程
3. 重新启动服务器 (`uv run mobile-world server --port 6800`)

#### 方式 B: 进入容器手动启动

```bash
# 进入容器
mw env exec mobile_world_env_0

# 在容器内执行
# 终止旧服务器
pkill -f "mobile-world server"

# 启动新服务器
uv run mobile-world server --port 6800 &
```

#### 方式 C: 完全重启模拟器 (Kill + Relaunch)

```bash
# 进入容器
mw env exec mobile_world_env_0

# 在容器内执行
# 终止现有模拟器
adb -s emulator-5554 emu kill

# 重新启动模拟器
export AVD_NAME=Pixel_8_API_34_x86_64
/app/docker/start_emulator.sh
```

#### 方式 D: 销毁并重建容器

```bash
# 销毁现有容器
mw env rm mobile_world_env_0

# 重新启动
sudo uv run mw env run --count 1
```

---

### 2. 重置模拟器状态

MobileWorld 使用 **AVD 快照** 实现确定性的状态重置，这是评估可重复性的核心机制。

#### 方式 A: 通过快照管理脚本 (推荐)

使用 `scripts/snapshot_manager.py` 脚本，通过 Backend API 统一管理快照和任务状态：

```bash
# 列出所有任务及其对应快照
uv run python scripts/snapshot_manager.py list

# 加载指定任务的快照 (推荐方式)
uv run python scripts/snapshot_manager.py load --task MattermostCreateChannel

# 加载多个指定任务
uv run python scripts/snapshot_manager.py load --task MattermostCreateChannel GmailSendEmail

# 加载所有任务的快照 (自动按顺序加载并验证)
uv run python scripts/snapshot_manager.py load --all

# 仅加载 GUI-Only 任务 (不含 MCP 和 User Interaction)
uv run python scripts/snapshot_manager.py load --all --gui-only

# 仅加载 MCP 任务
uv run python scripts/snapshot_manager.py load --all --mcp-only

# 加载含 User Interaction 的任务
uv run python scripts/snapshot_manager.py load --all --with-user-interaction

# 验证任务与快照的对应关系
uv run python scripts/snapshot_manager.py verify

# 通过 SSH 隧道操作远程服务器上的快照
# 先建立隧道: ssh -L 6800:localhost:6800 user@remote
uv run python scripts/snapshot_manager.py load --all --base-url http://localhost:6800
```

**脚本工作原理:**
1. 通过 `/task/list` 获取服务器端 TaskRegistry 中的全部任务
2. 通过 `/task/metadata` 查询每个任务的 `snapshot_tag`
3. 通过 `/task/init` 初始化任务 (内部自动调用 `controller.load_snapshot(task.snapshot_tag)`)
4. 通过 `/task/tear_down` 卸载任务，清理残留状态
5. 自动验证任务列表与快照的对应关系

#### 方式 B: 通过 API 直接加载快照

每个任务定义了一个 `snapshot_tag`，任务开始时自动加载对应快照：

```python
# 在任务定义中
class MyTask(BaseTask):
    snapshot_tag = "init_state"  # 任务开始时自动加载此快照
```

```bash
# 通过 Backend API 初始化任务 (自动加载快照)
curl -X POST http://localhost:6800/task/init \
  -H "Content-Type: application/json" \
  -d '{"task_name": "MattermostCreateChannel", "req_device": "emulator-5554"}'
```

#### 方式 C: 手动通过 ADB 加载快照

```bash
# 通过 Backend API 加载快照
curl -X POST http://localhost:6800/init \
  -H "Content-Type: application/json" \
  -d '{"instruction": "your task instruction"}'

# 任务初始化时会自动调用 load_snapshot
```

#### 方式 C: 手动通过 ADB 加载快照

```bash
# 查看可用快照
adb -s localhost:5556 emu avd snapshot list

# 加载指定快照
adb -s localhost:5556 emu avd snapshot load init_state

# 等待几秒让快照完全加载
sleep 3
```

#### 方式 D: 通过 Python Controller

---

### 3. 快照管理

#### 创建快照

```bash
# 通过 ADB
adb -s localhost:5556 emu avd snapshot save my_checkpoint

# 通过 Python
controller.create_snapshot("my_checkpoint")

# 带时间戳的快照名
controller.create_snapshot()  # 自动生成: snapshot_20260414_153000
```

#### 查看快照列表

```bash
# 通过 ADB
adb -s localhost:5556 emu avd snapshot list

# 通过 Python
snapshots = controller.list_snapshots()
for s in snapshots:
    print(s)
```

#### 删除快照

```bash
# 通过 ADB
adb -s localhost:5556 emu avd snapshot delete my_checkpoint

# 通过 Python
controller.delete_snapshot("my_checkpoint")
```

---

### 4. 健康检查与故障排查

#### 检查模拟器健康状态

```bash
# 通过 API
curl http://localhost:6800/health

# 通过 ADB
adb -s localhost:5556 shell getprop sys.boot_completed
# 返回 "1" 表示已启动

# 通过 Python
controller.check_health()
```

#### 模拟器常见问题

**问题 1: 模拟器卡住或无响应**
```bash
# 进入容器
mw env exec mobile_world_env_0

# 检查模拟器进程
ps aux | grep emulator

# 强制终止并重启
pkill -9 emulator
export AVD_NAME=Pixel_8_API_34_x86_64
/app/docker/start_emulator.sh
```

**问题 2: 服务器无响应但模拟器正常**
```bash
# 重启服务器 (无需重启模拟器)
mw env restart mobile_world_env_0
```

**问题 3: 容器完全异常**
```bash
# 销毁并重建
mw env rm mobile_world_env_0
sudo uv run mw env run --count 1
```

**问题 4: ADB 连接断开**
```bash
# 重新连接
adb connect localhost:5556

# 重启 ADB 服务
adb kill-server
adb start-server
adb connect localhost:5556
```

---

## 完整操作速查表

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    MobileWorld 模拟器操作速查表                           │
├──────────────────────┬──────────────────────────────────────────────────┤
│ 操作                  │ 命令                                              │
├──────────────────────┼──────────────────────────────────────────────────┤
│ 启动容器              │ sudo uv run mw env run --dev                     │
│ 启动多容器            │ sudo uv run mw env run --count 5                 │
│ 重启服务器            │ mw env restart mobile_world_env_0                │
│ 进入容器              │ mw env exec mobile_world_env_0                   │
│ 销毁容器              │ mw env rm mobile_world_env_0                     │
│ 查看容器状态          │ mw env list                                      │
│ 健康检查              │ curl http://localhost:6800/health                │
│ 获取截图              │ curl http://localhost:6800/screenshot -o s.png   │
│ 点击坐标              │ curl -X POST :6800/click -d '{"x":500,"y":1000}' │
│ 输入文本              │ curl -X POST :6800/input_text -d '{"text":"hi"}' │
│ 返回主页              │ curl -X POST :6800/navigate_home                 │
│ ADB 连接              │ adb connect localhost:5556                       │
│ ADB 点击              │ adb -s localhost:5556 shell input tap 500 1000   │
│ ADB 截图              │ adb -s localhost:5556 shell screencap -p /sd.png │
│ 查看快照              │ adb -s localhost:5556 emu avd snapshot list      │
│ 加载快照              │ adb -s localhost:5556 emu avd snapshot load xxx  │
│ 创建快照              │ adb -s localhost:5556 emu avd snapshot save xxx  │
│ 列出任务 (脚本)       │ uv run python scripts/snapshot_manager.py list   │
│ 加载任务快照 (脚本)   │ uv run python scripts/snapshot_manager.py load   │
│                       │     --task TaskName / --all                      │
│ 验证任务快照 (脚本)   │ uv run python scripts/snapshot_manager.py verify │
│ noVNC 访问            │ http://localhost:5800/vnc.html                   │
│ Device Viewer         │ http://localhost:7860                            │
│ SSH 隧道 (全部端口)   │ ssh -L 6800:localhost:6800 -L 7860:localhost:... │
│                      │     -L 5800:localhost:5800 -L 5556:localhost:... │
│                      │     -N user@remote-server                        │
└──────────────────────┴──────────────────────────────────────────────────┘
```

---

## 端口组合使用场景

### 场景 1: 运行自动评估
```bash
# 只需要 Backend API 端口
sudo uv run mw eval --agent_type general_e2e --task ALL ...
```
**使用端口**: `6800` (Backend API)

### 场景 2: 开发调试代码
```bash
# 启动开发模式 (所有 4 个端口)
sudo uv run mw env run --dev

# 修改代码 → 重启服务器
mw env restart mobile_world_env_0

# 通过 noVNC 查看效果
# 浏览器: http://localhost:5800/vnc.html
```
**使用端口**: 全部 4 个

### 场景 3: 手动验证任务
```bash
# 启动带 VNC 的容器
sudo uv run mw env run --vnc

# 进入容器
mw env exec mobile_world_env_0

# 初始化任务环境
uv run python src/mobile_world/tasks/test_task.py --task YourTaskName

# 通过 noVNC 手动完成任务
# 浏览器: http://localhost:5800/vnc.html
```

### 场景 4: 仅查看屏幕
```bash
# 通过 Device Viewer
# 浏览器: http://localhost:7860

# 或通过 noVNC (更完整)
# 浏览器: http://localhost:5800/vnc.html
```

### 场景 5: 使用 ADB 调试
```bash
# 通过 ADB 直接操作
adb connect localhost:5556
adb shell input tap 500 1000

# 或通过 API
curl -X POST http://localhost:6800/click -d '{"x": 500, "y": 1000}'
```

---

## 多容器端口分配

当启动多个容器时，端口按规则递增:

```bash
sudo uv run mw env run --count 5
```

| 容器名称 | Backend API | Device Viewer | noVNC | ADB |
|----------|------------|---------------|-------|-----|
| `mobile_world_env_0` | 6800 | 7860 | 5800 | 5556 |
| `mobile_world_env_1` | 6900 | 7960 | 5900 | 5557 |
| `mobile_world_env_2` | 7000 | 8060 | 6000 | 5558 |
| `mobile_world_env_3` | 7100 | 8160 | 6100 | 5559 |
| `mobile_world_env_4` | 7200 | 8260 | 6200 | 5560 |

**递增规则**:
- Backend API: +100 per container
- Device Viewer: +100 per container (始终 = Backend + 1060)
- noVNC: +100 per container (始终 = Backend - 1000)
- ADB: +1 per container

---

## 端口冲突排查

```bash
# 检查端口是否被占用
lsof -i :6800
lsof -i :7860
lsof -i :5800
lsof -i :5556

# 或使用 netstat
netstat -tulpn | grep 6800

# 杀死占用进程
kill -9 <PID>
```

---

## 快速参考卡片

```
┌─────────────────────────────────────────────────────────────┐
│                 MobileWorld 端口快速参考                      │
├─────────────────┬──────────┬────────────────────────────────┤
│ 服务             │ 默认端口  │ 访问方式                        │
├─────────────────┼──────────┼────────────────────────────────┤
│ Backend API     │ 6800     │ curl / Python SDK / CLI        │
│ Device Viewer   │ 7860     │ http://localhost:7860          │
│ noVNC           │ 5800     │ http://localhost:5800/vnc.html │
│ ADB Relay       │ 5556     │ adb connect localhost:5556     │
└─────────────────┴──────────┴────────────────────────────────┘

一键启动所有服务:
  sudo uv run mw env run --dev

访问 GUI:
  http://localhost:5800/vnc.html

访问 API:
  http://localhost:6800/health
```
