# 运维手册(RUNBOOK)

## 日常操作

### 启动守护进程

```bash
uv run kama-core
```

默认监听`127.0.0.1:7437`，按`Ctrl+C`优雅退出

### 验证通道
```bash
un run kama ping
# -> pong server=0.0.1 uptime=12ms latency=2ms
```

### 停止守护进程
```bash
kill $(pgrep -f kama-core)
```

---
## 配置
优先级（低-> 高）：**内建默认值-> `~/.Kama/config.toml` -> `.env` -> 系统环境变量**

```toml
[core]
host = "127.0.0.1"
port = 7437

[logging]
level = "INFO"
file = "~/.Kama/logs/core.log"
format = "text" # "text" | "json"
```

### `.env`
从 `.env.example` 复制后修改，存放本机配置与密钥（不提交git）：

```bash
cp .env .example .env
```

### 系统环境变量
| 变量 | 默认值                     | 说明                    |
|------|-------------------------|-----------------------|
|`KAMA_CONFIG | `~/.kama/config.toml`   | 覆盖配置文件路径              |
|`KAMA_HOST | `127.0.0.1`             | TCP监听地址               |
|`KAMA_PORT | `7437`                  | TCP监听端口               |
|`KAMA_LOG_LEVEL` | `INFO`                  | 日志级别                  |
|`KAMA_LOG_FILE` | `~/.kama/logs/core.log` | 日志文件路径（留空则仅输出stderr）  |
|`KAMA_LOG_FORMAT` | `text`                  | 日志格式（`text` 或 `json`） 

---
## 开发
```bash
uv run ruff check src tests script # lint
uv run mypy src                    # 类型检查
uv run pytest tests/ -v            # 全量测试
uv run pytest tests/unit -v        # 仅单元测试（无需启动 daemon）

make docs                         # 重新生成 WIRE_PROTOCOL.md
make verify-s0                    # 完整性验证 （lint + 类型 + 测试 + 协议同源检查）
```

## 日志
```bash
tail -f ~/.kama/logs/core.log
```

---
## 常见错误
| 报错                                          | 原因                  | 处理                            |
|---------------------------------------------|---------------------|-------------------------------|
| `core already running at 127.0.0.0.1:7434`  | 已有守护线程运行            | `kill $(pgrep -f kama-core)`  |
| `core not running`                          | 未启动守护线程             | `uv run kama-core`            |
| `Address already in use`                    | 端口已被其他进程占用          | `KAMA_PORT=8000 uv run kama-core` |
| `Config error: KAMA_PORT must be an integer` | `.env` 或环境变量中端口值非整数 | `KAMA_PORT` 的值                |
