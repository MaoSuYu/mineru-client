# Autodl 启动 Mineru 指南

简短说明：本文档说明在 Autodl 场景下启动 Mineru 的推荐步骤，包括激活 conda 环境、设置模型来源环境变量、以及启动 `sglang-server` 的命令。文中给出 Linux (bash) 的命令示例。

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

4. 启动服务（请选择以下三种方式之一）：

- 方式 A:

```bash
mineru-sglang-server --port 6006 --mem-fraction-static 0.75 --enable-torch-compile
```
(sglangserver)

- 方式 B:

```bash
mineru-gradio --server-name 0.0.0.0 --server-port 6008 --enable-sglang-engine true --enable-torch-compile --mem-fraction-static 0.75
```
(gradio)

- 方式 C:

```bash
mineru-api --host 0.0.0.0 --port 6006
```
(fastapi)
文档地址: http://127.0.0.1:8000/docs