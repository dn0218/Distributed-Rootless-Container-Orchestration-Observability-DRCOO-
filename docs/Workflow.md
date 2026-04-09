# Deployment Workflow / 部署工作流

### Step 1: Pre-requisites / 前置条件
- SSH Key-based authentication configured.
- `loginctl enable-linger` executed for the user to ensure persistence.
- 配置基于密钥的 SSH 认证，并开启用户 Linger 状态以确保注销后容器继续运行。

### Step 2: Configuration / 配置
Users modify `.j2` templates in the `roles/monitoring/templates` directory.
用户修改 `roles/monitoring/templates` 目录下的 Jinja2 模板。

### Step 3: Execution / 执行
The Playbook executes in the following sequence:
1. **Firewall**: Opening ports 3000, 9090, 9100.
2. **Directories**: Creating paths with proper SELinux contexts.
3. **Quadlets**: Deploying `.container` files to `~/.config/containers/systemd/`.
4. **Reload**: Triggering systemd user daemon-reload and service restart.

Playbook 按以下顺序执行：
1. **防火墙**: 放行 3000, 9090, 9100 端口。
2. **目录**: 创建带正确 SELinux 上下文的路径。
3. **Quadlets**: 下发 `.container` 文件至 systemd 目录。
4. **加载**: 触发用户级 daemon-reload 并重启服务。
