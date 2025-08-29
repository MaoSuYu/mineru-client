# Autodl 启动 Mineru 指南

简短说明：本文档说明在 Autodl 场景下启动 Mineru 的推荐步骤，包括激活 conda 环境、设置模型来源环境变量、以及启动 `sglang-server` 的命令。文中同时给出 Linux (bash) 与 Windows (cmd) 的命令示例。

## 需求清单 (从用户请求提取)
- 激活 conda 环境：`conda deactivate`，`conda activate mineru_py312`。
- 设置环境变量：`export MINERU_MODEL_SOURCE=local`（在 Windows 上给出等效命令）。
- 启动 sglang-server：`mineru-sglang-server --port 6006 --mem-fraction-static 0.75`。

> 假设：Autodl 实际运行环境可能是 Linux（常见于 GPU 服务器）——我在下面同时给出 Linux(bash) 与 Windows(cmd) 的写法；在 Windows 上你可能需要在 Anaconda Prompt 或启用了 conda 的 cmd 环境下执行。

## 步骤（Linux / macOS，bash 示例）
1. 退出当前 conda 环境（如果有）：

```bash
conda deactivate
```

2. 激活目标 conda 环境：

```bash
conda activate mineru_py312
```

3. 设置模型来源为本地（仅影响当前 shell 会话）：

```bash
export MINERU_MODEL_SOURCE=local
```

如果你希望将环境变量写入系统级配置以便对所有用户生效（需要 root 权限），可以追加到 `/etc/profile`：

```bash
sudo bash -c "echo 'export MINERU_MODEL_SOURCE=local' >> /etc/profile"
# 或者
echo 'export MINERU_MODEL_SOURCE=local' | sudo tee -a /etc/profile
```

4. 启动 sglang-server：

```bash
mineru-sglang-server --port 6006 --mem-fraction-static 0.75
```

说明：`--mem-fraction-static 0.75` 控制静态内存分配比率，按需调整（例如 0.5、0.8）。

## 步骤（Windows，cmd 示例）
> 在 Windows 下，请在 Anaconda Prompt 或已经初始化 conda 的 cmd 会话中执行以下命令。

1. 退出当前 conda 环境：

```bat
conda deactivate
```

2. 激活目标环境：

```bat
conda activate mineru_py312
```

3. 设置会话级环境变量（仅当前 cmd 窗口有效）：

```bat
set MINERU_MODEL_SOURCE=local
```

如需永久设置（对后续新开的窗口生效），可以使用 `setx`：

```bat
setx MINERU_MODEL_SOURCE local
```

注意：`setx` 不会修改当前打开的命令行会话，只影响后续新开窗口。

4. 启动 sglang-server：

```bat
mineru-sglang-server --port 6006 --mem-fraction-static 0.75
```

## 启动校验
- 控制台输出：检查 `mineru-sglang-server` 启动日志，确认 `listening on port 6006` 或类似信息。
- 端口检查（Linux 示例）：

```bash
ss -tlnp | grep 6006
# 或
netstat -tlnp | grep 6006
```

- Windows 检查端口：

```bat
netstat -ano | findstr 6006
```

- 简单连接测试（本机）：

```bash
curl http://127.0.0.1:6006/health
# 或者浏览器打开 http://127.0.0.1:6006
```

（具体健康检查路径视 `sglang-server` 实现而定，如果没有 `/health`，用浏览器或 curl 访问根路径查看响应。）

## 可选：在 Linux 上以 systemd 服务方式管理（示例）
创建 `/etc/systemd/system/mineru-sglang.service`（需要 root）：

```ini
[Unit]
Description=Mineru sglang server
After=network.target

[Service]
Type=simple
User=youruser
Environment=MINERU_MODEL_SOURCE=local
ExecStart=/bin/bash -lc 'source /home/youruser/miniconda3/bin/activate mineru_py312 && mineru-sglang-server --port 6006 --mem-fraction-static 0.75'
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

然后：

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now mineru-sglang.service
sudo systemctl status mineru-sglang.service
```

将 `youruser` 与 conda 路径替换为实际值。

## 小贴士与注意事项
- 确认 `mineru-sglang-server` 在你的 PATH 中可执行，或使用全路径调用。
- 若使用 GPU，请确保相应驱动和 CUDA/cuDNN 与环境兼容。
- 如 Autodl 在容器中运行，请在容器启动脚本中写入上述命令或在 Dockerfile/entrypoint 中设置环境变量。

## 完成情况
本文档提供了所需的命令、Windows 与 Linux 的等效写法、验证方式与可选的 systemd 管理示例。如需我把该文件保存到特定路径或添加到 README，请告知目标文件名与位置。
