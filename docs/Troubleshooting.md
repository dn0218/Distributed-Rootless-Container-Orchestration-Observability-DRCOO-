# Troubleshooting / 排障手册

### 1. Permission Denied (Rootless Mapping) / 权限拒绝（无根映射）
**Issue**: Containers cannot write to mounted volumes despite `755` permissions.
**Solution**: Set `User=0` within the Quadlet file. In rootless Podman, UID 0 inside the container maps to the regular user (e.g., `danny`) outside.
**问题**: 即使权限设为 `755`，容器仍无法写入挂载卷。
**方案**: 在 Quadlet 文件中设置 `User=0`。在无根模式下，容器内的 UID 0 会映射为外部的普通用户。

### 2. SELinux "Permission Denied" / SELinux 导致的报错
**Issue**: Services fail to start on RHEL 9 due to security context mismatch.
**Solution**: Use Ansible to apply `setype: container_file_t` to data directories instead of relying on `:Z` flags which may fail on certain filesystems.
**问题**: 由于安全上下文不匹配，服务在 RHEL 9 上无法启动。
**方案**: 使用 Ansible 为目录应用 `container_file_t` 标签，而不是依赖可能报错的 `:Z` 挂载标志。

### 3. Systemd Service "Inactive (Dead)" / 服务显示已死
**Issue**: After running the playbook, services are dead but show no errors.
**Solution**: This is often a race condition during `daemon-reload`. Manually run `systemctl --user start <service>` or optimize Ansible Handlers to include `started` state.
**问题**: 跑完脚本后，服务处于 dead 状态但无明显错误。
**方案**: 这通常是 `daemon-reload` 时的时序竞争。需手动启动或优化 Ansible Handler 确保 state 为 `started`。
