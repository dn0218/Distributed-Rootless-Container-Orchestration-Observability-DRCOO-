# Project Architecture / 项目架构

### System Topology / 系统拓扑
The project splits responsibilities between a Control Node and Managed Nodes.
该项目将职责划分为控制节点和被控节点。

1. **Control Node (RHEL 9)**: 
   - Acts as the Monitoring Server (Prometheus & Grafana).
   - Manages the entire fleet via Ansible.
   - **控制节点 (RHEL 9)**: 作为监控服务端，通过 Ansible 管理全局。

2. **Managed Nodes (Rocky Linux 10)**:
   - Hosts microservices (WordPress Stack).
   - Runs Node Exporter for telemetry.
   - **被控节点 (Rocky Linux 10)**: 托管微服务（WordPress 栈），运行 Node Exporter 采集数据。

### Networking Logic / 网络逻辑
We utilize **Host Networking (`--net=host`)** for the monitoring stack to:
1. Simplify container-to-host communication.
2. Bypass complex user-mode networking (slirp4netns) overhead.
3. Ensure Prometheus can reach agents without NAT issues.

监控栈采用 **主机网络模式 (`--net=host`)** 以：
1. 简化容器与宿主机的通信。
2. 绕过复杂的宿主用户态网络 (slirp4netns) 开销。
3. 确保 Prometheus 可以在无 NAT 干扰的情况下访问采集插件。
