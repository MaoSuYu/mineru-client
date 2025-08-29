# Autodl 启动 Mineru 指南

简短说明：本文档说明在 Autodl 场景下启动 Mineru 的推荐步骤，包括激活 conda 环境、设置模型来源环境变量、以及启动 `sglang-server` 的命令。文中给出 Linux (bash) 的命令示例。

## 需求清单 (从用户请求提取)
- 激活 conda 环境：`conda deactivate`，`conda activate mineru_py312`。
- 设置环境变量：`export MINERU_MODEL_SOURCE=local`。
- 启动 sglang-server：`mineru-sglang-server --port 6006 --mem-fraction-static 0.75 --enable-torch-compile`。
/- 另一种可选的启动方式（使用 Gradio 前端与 sglang 引擎）：`mineru-gradio --server-name 0.0.0.0 --server-port 6008 --enable-sglang-engine true --enable-torch-compile --mem-fraction-static 0.75`。

> 假设：Autodl 实际运行环境通常是 Linux（常见于 GPU 服务器），下面给出 Linux (bash) 的写法。

## 步骤（Linux，bash 示例）
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

使用 `source /etc/profile` 使其生效，或重新登录。

4. 启动 sglang-server（启用 Torch 编译以提高性能）：

```bash
mineru-sglang-server --port 6006 --mem-fraction-static 0.75 --enable-torch-compile
```

另一种可选的启动方式（使用 Gradio 前端并启用 sglang 引擎与 Torch 编译）：

```bash
mineru-gradio --server-name 0.0.0.0 --server-port 6008 --enable-sglang-engine true --enable-torch-compile --mem-fraction-static 0.75
```

说明：`--mem-fraction-static 0.75` 控制静态内存分配比率，按需调整（例如 0.5、0.8）。