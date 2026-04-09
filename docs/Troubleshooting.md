# Troubleshooting / 排障手册

### 1. Permission Denied (Rootless Mapping) / 权限拒绝（无根映射）
**Issue**: Containers cannot write to mounted volumes despite `755` permissions.

**问题**: 即使权限设为 `755`，容器仍无法写入挂载卷。

<img width="934" height="502" alt="Screenshot 2026-04-09 110433" src="https://github.com/user-attachments/assets/aaeb058b-2437-4c99-b818-2e2fa6758082" />

**Root Cause / 根因**: 
In Rootless Podman, the User Namespace maps the container's internal `root` (UID 0) to the external unprivileged user (e.g., `danny`, UID 1000). If the container process runs as a non-zero UID inside, it may map to a completely different "sub-UID" range on the host, leading to permission mismatch.

**根因分析**: 在 Rootless Podman 中，用户命名空间（User Namespace）将容器内的 `root` (UID 0) 映射为外部的普通用户（如 `danny`, UID 1000）。如果容器内进程以非 0 UID 运行，它在宿主机上会映射到一个完全不同的“子 UID”区间，导致权限无法匹配。

**Solution**: Set `User=0` within the Quadlet file. In rootless Podman, UID 0 inside the container maps to the regular user (e.g., `danny`) outside.

**方案**: 在 Quadlet 文件中设置 `User=0`。在无根模式下，容器内的 UID 0 会映射为外部的普通用户。

<img width="640" height="302" alt="image" src="https://github.com/user-attachments/assets/6cb3cbb0-27ed-4507-a5e2-412c3fecc086" />


### 2. SELinux "Permission Denied" / SELinux 导致的报错
**Issue**: Services fail to start on RHEL 9 due to security context mismatch.

**问题**: 由于安全上下文不匹配，服务在 RHEL 9 上无法启动。
```bash
/home/danny/monitoring/grafana/data:/var/lib/grafana:Z \
--publish 3000:3000 \
--env GF_SECURITY_ADMIN_PASSWORD=admin \
docker.io/grafana/grafana-oss:latest
Error: lsetxattr(label=system_u:object_r:container_file_t:s0:c111,c871) /home/danny/monitoring/grafana/data: operation not permitted
```

**Root Cause / 根因**: 
SELinux enforces Mandatory Access Control (MAC). By default, container processes (`container_t`) are forbidden from accessing files labeled as user home data (`user_home_t`). Standard `:Z` flags often fail in rootless mode due to restricted permissions to modify extended attributes (xattr).

**根因分析**: SELinux 执行强制访问控制（MAC）。默认情况下，容器进程（`container_t`）被禁止访问标签为用户家目录数据（`user_home_t`）的文件。在无根模式下，由于修改扩展属性（xattr）的权限受限，标准的 `:Z` 挂载标志经常失效。

**Solution**: Use Ansible to apply `setype: container_file_t` to data directories instead of relying on `:Z` flags which may fail on certain filesystems.

**方案**: 使用 Ansible 为目录应用 `container_file_t` 标签，而不是依赖可能报错的 `:Z` 挂载标志。

<img width="687" height="302" alt="image" src="https://github.com/user-attachments/assets/a6d69be8-a0eb-4f2a-913a-15310bcf9ec5" />


### 3. Systemd Service "Inactive (Dead)" / 服务显示已死
**Issue**: After running the playbook, services are dead but show no errors.

**问题**: 跑完脚本后，服务处于 dead 状态但无明显错误。

```bash
[danny@rhel DRCOO-Project]$ systemctl --user status prometheus grafana

○ prometheus.service - Prometheus Service

     Loaded: loaded (/home/danny/.config/containers/systemd/prometheus.container; generated)

     Active: inactive (dead)
```

**Root Cause / 根因**: 
This is typically a **Race Condition** during `systemctl --user daemon-reload`. If the unit files are overwritten while the service is being managed, systemd may lose track of the process or fail to trigger the `restart` command correctly before the unit is fully re-parsed.

**根因分析**: 这通常是 `daemon-reload` 过程中的**时序竞争**问题。如果在服务运行期间重写了 Unit 文件，systemd 可能会失去对进程的跟踪，或者在 Unit 文件尚未完全解析完成前无法正确触发 `restart` 指令。

**Solution**: This is often a race condition during `daemon-reload`. Manually run `systemctl --user start <service>` or optimize Ansible Handlers to include `started` state.

**方案**: 这通常是 `daemon-reload` 时的时序竞争。需手动启动或优化 Ansible Handler 确保 state 为 `started`。在 Systemd User 模式下，直接使用 restarted 有时不如 started 稳健

<img width="540" height="268" alt="image" src="https://github.com/user-attachments/assets/d8328330-54a0-4294-b6d8-c8cfedd97c18" />

