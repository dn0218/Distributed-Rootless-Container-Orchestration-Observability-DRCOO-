# Distributed Rootless Container Orchestration & Observability (DRCOO)
## 分布式无根容器编排与观测系统

DRCOO is an Infrastructure as Code (IaC) project designed for enterprise-level container management. It leverages Ansible, Podman Quadlet, and the Prometheus/Grafana stack to deploy a secure, rootless, and highly observable environment across RHEL 9 and Rocky Linux 10 nodes.

DRCOO 是一个为企业级容器管理设计的“基础架构即代码 (IaC)”项目。它利用 Ansible、Podman Quadlet 以及 Prometheus/Grafana 技术栈，在 RHEL 9 和 Rocky Linux 10 节点上部署安全、无根（Rootless）且具备高度可观测性的环境。

### 🚀 Key Features / 核心特性
- **Rootless Security**: All containers run in rootless mode, minimizing the attack surface.
- **无根安全**: 所有容器均以 rootless 模式运行，最大限度减少攻击面。
- **Podman Quadlet**: Native systemd integration for container management.
- **Podman Quadlet**: 原生 systemd 集成，实现容器即服务。
- **Automated Observability**: One-click deployment of Prometheus, Grafana, and Node Exporter.
- **自动化观测**: 一键部署 Prometheus、Grafana 和 Node Exporter 监控全家桶。
- **SELinux Hardening**: Automated labeling (`container_file_t`) for seamless RHEL integration.
- **SELinux 加固**: 自动执行标签化，确保与红帽系系统完美兼容。

### 🛠️ Quick Start / 快速开始
```bash
# Clone the repository / 克隆仓库
git clone [https://github.com/dn0218/Distributed-Rootless-Container-Orchestration-Observability-DRCOO-.git](https://github.com/dn0218/Distributed-Rootless-Container-Orchestration-Observability-DRCOO-.git)
cd Distributed-Rootless-Container-Orchestration-Observability-DRCOO-

# Run the playbook / 执行脚本
ansible-playbook -i inventory.ini playbook.yml
