# 快照管理脚本使用指南

> **脚本路径**: `scripts/snapshot_manager.py`
>
> **功能**: 通过 Backend API 管理 MobileWorld 模拟器快照和任务状态重置。

## 目录

1. [快速开始](#快速开始)
2. [核心概念](#核心概念)
3. [命令详解](#命令详解)
   - [`list` - 列出任务](#1-list---列出任务)
   - [`load` - 加载快照](#2-load---加载快照)
   - [`verify` - 验证对应关系](#3-verify---验证对应关系)
4. [远端任务采集方案](#远端任务采集方案)
5. [快照机制原理](#快照机制原理)
6. [常见问题](#常见问题)

---

## 快速开始

```bash
# 列出所有可用任务
python scripts/snapshot_manager.py list \
  --base-url http://<服务器IP>:<端口> --device emulator-5554

# 加载单个任务快照 (恢复到该任务的确定初始状态)
python scripts/snapshot_manager.py load --task GmailSendEmail \
  --base-url http://<服务器IP>:<端口> --device emulator-5554

# 加载所有任务快照
python scripts/snapshot_manager.py load --all \
  --base-url http://<服务器IP>:<端口> --device emulator-5554
```

**常用参数速查**:

| 参数 | 简写 | 说明 | 默认值 |
|------|------|------|--------|
| `--base-url` | - | Backend API 地址 | `http://123.60.91.241:9000` |
| `--device` | - | ADB 设备标识符 | `emulator-5554` |
| `--task` | `-t` | 指定任务名称 (可多个) | 无 |
| `--all` | `-a` | 加载所有任务快照 | 否 |
| `--gui-only` | - | 仅 GUI-Only 任务 | 否 |
| `--mcp-only` | - | 仅 MCP 任务 | 否 |

---

## 核心概念

### 快照是什么？

MobileWorld 使用 Android AVD (Android Virtual Device) 快照机制来实现**确定性状态重置**。每个任务关联一个快照标签 (snapshot tag)，加载快照后模拟器会完全恢复到保存时的状态：

- 应用已打开并处于指定页面
- 登录状态已保存
- 数据已预填充
- 系统时间已同步 (如任务需要)

### 任务 vs 快照

- **任务** (Task): 定义了一个完整的评估场景，包括目标描述、初始状态、评估逻辑
- **快照** (Snapshot): 是模拟器在某个时间点的完整状态镜像

**一对多关系**: 一个快照可以被多个任务共享。201 个任务共享 ~50 个不同的快照。

### 快照加载的两种方式

| 方式 | 实现 | 适用场景 |
|------|------|----------|
| **任务初始化** (推荐) | `POST /task/init` → `task.initialize_task()` → `controller.load_snapshot()` | 任务执行、数据采集 |
| **直接 ADB 加载** | `adb emu avd snapshot load <tag>` | 调试、手动探索 |

---

## 命令详解

### 1. `list` - 列出任务

列出所有可用任务及其对应的快照信息。

```bash
# 列出所有任务 (201 个)
python scripts/snapshot_manager.py list

# 仅列出 GUI-Only 任务 (不含 MCP 和 User Interaction)
python scripts/snapshot_manager.py list --gui-only

# 仅列出 MCP 任务
python scripts/snapshot_manager.py list --mcp-only

# 列出含 User Interaction 的任务
python scripts/snapshot_manager.py list --with-user-interaction

# 指定远程服务器
python scripts/snapshot_manager.py list \
  --base-url http://192.168.1.100:6800 --device emulator-5556
```

**输出示例**:

```
┌─────────────────────────────────────────────────────────────┐
│ 可用任务 (201 个)                                           │
├──────┬──────────────────────────┬───────────────┬───────────┤
│ #    │ 任务名称                 │ 快照标签      │ 应用      │
├──────┼──────────────────────────┼───────────────┼───────────┤
│ 1    │ GmailSendEmail           │ gmail_base    │ gmail     │
│ 2    │ MattermostCreateChannel  │ mattermost_base │ mattermost│
│ ...  │ ...                      │ ...           │ ...       │
└──────┴──────────────────────────┴───────────────┴───────────┘

📊 统计
总计 201 个任务
使用 50 个不同快照
```

### 2. `load` - 加载快照

加载指定任务的快照，使模拟器恢复到该任务的确定初始状态。

```bash
# 加载单个任务快照
python scripts/snapshot_manager.py load --task GmailSendEmail

# 加载多个指定任务快照
python scripts/snapshot_manager.py load --task GmailSendEmail SlackSendMessage

# 加载所有任务快照 (按顺序，每个任务加载后自动卸载)
python scripts/snapshot_manager.py load --all

# 仅加载 GUI-Only 任务 (排除 MCP 和 User Interaction)
python scripts/snapshot_manager.py load --all --gui-only

# 仅加载 MCP 任务
python scripts/snapshot_manager.py load --all --mcp-only

# 加载含 User Interaction 的任务
python scripts/snapshot_manager.py load --all --with-user-interaction
```

**加载过程**:

1. 检查服务器健康状态 (`/health`)
2. 确保设备已初始化 (`/init`)
3. 获取任务列表并过滤
4. 对每个任务:
   - 卸载前一个任务残留 (`/task/tear_down`)
   - 初始化任务 (`/task/init`) → 自动加载快照
   - 等待 3 秒确保快照加载完成
   - 卸载任务 (为下一个任务准备)

**注意**: `load` 命令是**批量预热**模式，加载完所有任务后会回到初始状态。如果你需要在任务间保持加载后的状态，应使用直接 API 调用 (见 [远端采集方案](#远端任务采集方案))。

### 3. `verify` - 验证对应关系

验证任务与快照的对应关系，检查是否有缺失的快照。

```bash
# 验证所有任务
python scripts/snapshot_manager.py verify

# 仅验证 GUI-Only 任务
python scripts/snapshot_manager.py verify --gui-only

# 仅验证 MCP 任务
python scripts/snapshot_manager.py verify --mcp-only
```

**输出示例**:

```
📋 快照-任务对应关系
┌──────────────────┬──────────┬──────────────────────────────┐
│ 快照标签         │ 任务数量 │ 任务列表                     │
├──────────────────┼──────────┼──────────────────────────────┤
│ gmail_base       │ 5        │ GmailSendEmail, ...          │
│ slack_base       │ 3        │ SlackSendMessage, ...        │
└──────────────────┴──────────┴──────────────────────────────┘

✅ 验证完成
50 个不同快照 对应 201 个任务
```

---

## 远端任务采集方案

### 场景描述

在远端服务器上运行 MobileWorld，需要通过 ADB 或 API 对 15 个核心应用进行数据采集。**每一轮操作都必须恢复到相同的初始状态**，以保证实验的可重复性。

### 方案 A: 使用脚本自动加载 (推荐)

```bash
#!/bin/bash
# collect_data.sh - 批量采集所有任务数据

API="http://123.60.91.241:9000"
DEVICE="emulator-5554"

# Step 0: 初始化设备
curl -s -X POST "$API/init" -d "{\"device\": \"$DEVICE\"}"

# Step 1: 获取所有任务列表
TASKS=$(curl -s "$API/task/list" | jq -r '.[].name')

# Step 2: 遍历每个任务
for TASK in $TASKS; do
  echo "=== 开始采集: $TASK ==="

  # 2a: 加载任务快照 (恢复到确定初始状态)
  curl -s -X POST "$API/task/init" \
    -d "{\"task_name\": \"$TASK\", \"req_device\": \"$DEVICE\"}"
  sleep 5  # 等待快照加载完成

  # 2b: 执行你的采集逻辑
  # 方式1: 通过 ADB 截图
  # adb -s $DEVICE shell screencap -p /sdcard/screenshot.png
  # adb -s $DEVICE pull /sdcard/screenshot.png ./screenshots/${TASK}.png

  # 方式2: 通过 FastAPI /step 接口与 Agent 交互
  # curl -X POST "$API/step" -d '{"device": "...", "action": "..."}'

  # 2c: 卸载任务
  curl -s -X POST "$API/task/tear_down" \
    -d "{\"task_name\": \"$TASK\", \"req_device\": \"$DEVICE\"}"

  echo "=== 完成: $TASK ==="
  sleep 2
done

echo "🎉 所有任务采集完成!"
```

**运行**:

```bash
chmod +x collect_data.sh
./collect_data.sh
```

### 方案 B: 单任务多次循环采集

适用于需要对同一任务进行多次重复采集 (例如测试 Agent 稳定性)：

```bash
#!/bin/bash
# collect_single_task.sh - 对同一任务进行 N 轮采集

API="http://123.60.91.241:9000"
DEVICE="emulator-5554"
TASK="GmailSendEmail"
ROUNDS=10

# 初始化设备
curl -s -X POST "$API/init" -d "{\"device\": \"$DEVICE\"}"

for ROUND in $(seq 1 $ROUNDS); do
  echo "=== Round $ROUND: $TASK ==="

  # 加载快照
  curl -s -X POST "$API/task/init" \
    -d "{\"task_name\": \"$TASK\", \"req_device\": \"$DEVICE\"}"
  sleep 5

  # 执行采集...

  # 卸载
  curl -s -X POST "$API/task/tear_down" \
    -d "{\"task_name\": \"$TASK\", \"req_device\": \"$DEVICE\"}"

  echo "=== Round $ROUND 完成 ==="
  sleep 2
done
```

### 方案 C: 直接使用 Python API 客户端

```python
#!/usr/bin/env python3
"""高级采集脚本 - 支持重试、超时控制、结果记录"""

import time
import json
from datetime import datetime
from scripts.snapshot_manager import SnapshotManagerClient

API = "http://123.60.91.241:9000"
DEVICE = "emulator-5554"
OUTPUT_DIR = "collection_results"
MAX_RETRIES = 3

client = SnapshotManagerClient(base_url=API, device=DEVICE)

# 确保设备初始化
client.health_check()
client.ensure_initialized()

# 获取所有任务
task_list = client.get_task_list()

results = []

for task_meta in task_list:
    task_name = task_meta["name"]
    print(f"采集: {task_name}")

    for attempt in range(1, MAX_RETRIES + 1):
        try:
            # 加载快照
            success = client.init_task(task_name)
            if not success:
                raise RuntimeError("快照加载失败")

            time.sleep(5)  # 等待模拟器稳定

            # === 在这里执行你的采集逻辑 ===
            # 例如: adb screenshot, /step API, 等等
            collection_result = {
                "status": "success",
                "timestamp": datetime.now().isoformat(),
            }

            print(f"  ✓ Round {attempt}: {collection_result}")
            results.append({
                "task": task_name,
                "attempt": attempt,
                **collection_result,
            })
            break  # 成功，跳出重试循环

        except Exception as e:
            print(f"  ✗ Round {attempt}: {e}")
            if attempt == MAX_RETRIES:
                results.append({
                    "task": task_name,
                    "attempt": attempt,
                    "status": "failed",
                    "error": str(e),
                })
        finally:
            client.tear_down_task(task_name)
            time.sleep(2)

# 保存结果
import os
os.makedirs(OUTPUT_DIR, exist_ok=True)
with open(f"{OUTPUT_DIR}/results_{datetime.now():%Y%m%d_%H%M%S}.json", "w") as f:
    json.dump(results, f, indent=2)

print(f"\n📊 结果已保存到 {OUTPUT_DIR}/")
```

### 方案 D: 纯 cURL 命令行 (快速测试)

```bash
# 1. 初始化设备
curl -X POST http://123.60.91.241:9000/init \
  -H "Content-Type: application/json" \
  -d '{"device": "emulator-5554"}'

# 2. 加载任务快照
curl -X POST http://123.60.91.241:9000/task/init \
  -H "Content-Type: application/json" \
  -d '{"task_name": "GmailSendEmail", "req_device": "emulator-5554"}'

# 3. 等待快照加载
sleep 5

# 4. 执行采集 (示例: 截图)
adb -s localhost:5556 shell screencap -p /sdcard/screenshot.png
adb -s localhost:5556 pull /sdcard/screenshot.png ./gmail_initial.png

# 5. 执行 Agent 操作 (示例)
curl -X POST http://123.60.91.241:9000/step \
  -H "Content-Type: application/json" \
  -d '{"device": "emulator-5554", "action": {"type": "tap", "x": 500, "y": 300}}'

# 6. 卸载任务 (清理状态)
curl -X POST http://123.60.91.241:9000/task/tear_down \
  -H "Content-Type: application/json" \
  -d '{"task_name": "GmailSendEmail", "req_device": "emulator-5554"}'

# 7. 下一轮采集 → 回到 Step 2
```

### SSH 隧道配置

如果你的远端服务器没有直接暴露 API 端口，使用 SSH 隧道：

```bash
# 建立隧道 (本地端口 9000 → 远端 9000)
ssh -L 9000:localhost:9000 -N user@123.60.91.241 &

# 使用本地端口访问
python scripts/snapshot_manager.py list --base-url http://localhost:9000

# 采集时也使用本地端口
curl -X POST http://localhost:9000/task/init \
  -d '{"task_name": "GmailSendEmail", "req_device": "emulator-5554"}'

# 完成后关闭隧道
kill %1
```

---

## 快照机制原理

### 快照加载流程

```
┌─────────────┐
│ POST /task/init │
└──────┬──────┘
       │
       ▼
┌─────────────────────────────────┐
│ task.initialize_task(controller) │
│  1. controller.load_snapshot(tag)│
│  2. controller.app_switch(app)  │
│  3. controller.press_home()     │
│  4. sync_task_time(task)        │
│  5. wait_stable()               │
└─────────────────────────────────┘
       │
       ▼
┌──────────────────────────────────┐
│ ADB: avd snapshot load <tag>     │
│ (通过容器内 socat → emulator)    │
└──────────────────────────────────┘
       │
       ▼
┌──────────────────────────────────┐
│ 模拟器完全恢复到保存时的状态      │
│ (应用、数据、UI、系统设置)       │
└──────────────────────────────────┘
```

### 快照保存位置

快照存储在宿主机上，由 Android Emulator 管理：

```
~/.android/avd/Pixel_8_API_34_x86_64.avd/
├── snapshots/
│   ├── gmail_base/
│   ├── mattermost_base/
│   ├── slack_base/
│   └── ...
└── ...
```

### 快照生命周期

1. **创建**: 在开发模式容器中手动保存
   ```bash
   mw env run --dev
   adb emu avd snapshot save my_snapshot
   ```

2. **加载**: 通过任务初始化或直接 ADB 命令
   ```bash
   # 方式1 (推荐)
   curl -X POST $API/task/init -d '{"task_name": "GmailSendEmail", ...}'

   # 方式2 (直接)
   adb -s localhost:5556 emu avd snapshot load gmail_base
   ```

3. **删除**: 通过 ADB (谨慎操作!)
   ```bash
   adb emu avd snapshot delete old_snapshot
   ```

---

## 常见问题

### Q1: `load` 命令执行后模拟器处于什么状态？

`load` 命令是批量预热模式。它会遍历所有指定任务，对每个任务执行：
1. 加载快照 → 2. 等待稳定 → 3. 卸载任务

最终模拟器会回到**最后一个加载的任务状态**。如果你需要保持在特定任务状态，应使用直接 API 调用而非 `load` 命令。

### Q2: 快照加载失败怎么办？

```bash
# 检查服务器健康状态
python scripts/snapshot_manager.py list

# 检查设备是否在线
adb -s localhost:5556 devices

# 检查容器日志
docker logs mobile_world_env_0 2>&1 | tail -50

# 尝试手动重置
curl -X POST $API/init -d '{"device": "emulator-5554"}'
curl -X POST $API/task/init -d '{"task_name": "GmailSendEmail", "req_device": "emulator-5554"}'
```

### Q3: 如何查看某个任务对应哪个快照？

```bash
# 方法1: 通过脚本
python scripts/snapshot_manager.py list | grep GmailSendEmail

# 方法2: 直接查询 API
curl -s "$API/task/metadata?task_name=GmailSendEmail" | jq '.snapshot_tag'

# 方法3: 查看任务定义源码
grep -r "GmailSendEmail" src/mobile_world/tasks/
```

### Q4: 快照加载后需要等待多久？

建议等待 **5 秒**。快照加载本身很快 (1-2 秒)，但模拟器需要额外时间：
- 恢复 UI 渲染
- 应用重新启动
- 系统服务稳定

`load` 命令内部已包含 3 秒等待 (`SNAPSHOT_LOAD_WAIT`)。

### Q5: 多个容器如何切换？

```bash
# 容器 0 (端口 5556)
python scripts/snapshot_manager.py list --device emulator-5554 \
  --base-url http://localhost:6800

# 容器 1 (端口 5557)
python scripts/snapshot_manager.py list --device emulator-5554 \
  --base-url http://localhost:6801
```
