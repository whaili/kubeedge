# KubeEdge 项目概览

## 项目简介

KubeEdge 是一个基于 Kubernetes 的云原生边缘计算框架，将容器化应用编排和设备管理能力扩展到边缘节点。该项目由云端和边缘端组件组成，提供网络、应用部署和云边元数据同步的核心基础设施支持。

## 目录结构与职责

| 目录 | 主要职责 | 关键文件 |
|------|----------|----------|
| `cloud/` | 云端组件和控制器 | `cmd/cloudcore/`, `pkg/cloudhub/`, `pkg/edgecontroller/` |
| `cloud/cmd/cloudcore/` | 云端核心二进制文件 | `main.go`, `cloudcore.go` |
| `cloud/cmd/admission/` | 准入控制器 | `main.go` |
| `cloud/cmd/controllermanager/` | 控制器管理器 | `main.go` |
| `cloud/pkg/cloudhub/` | WebSocket 服务器，云边通信 | `cloudhub.go`, `server.go` |
| `cloud/pkg/edgecontroller/` | 扩展的 Kubernetes 控制器，管理边缘节点和 Pod | `controller.go` |
| `cloud/pkg/devicecontroller/` | 设备控制器，通过 CRD 管理边缘设备 | `devicecontroller.go` |
| `edge/` | 边缘端运行时和代理 | `cmd/edgecore/`, `pkg/edged/`, `pkg/edgehub/` |
| `edge/cmd/edgecore/` | 边缘端核心二进制文件 | `main.go`, `edgecore.go` |
| `edge/pkg/edgehub/` | WebSocket 客户端，云边通信 | `edgehub.go`, `client.go` |
| `edge/pkg/edged/` | 容器运行时代理（类似 kubelet） | `edged.go` |
| `edge/pkg/eventbus/` | MQTT 客户端，设备通信 | `eventbus.go` |
| `edge/pkg/servicebus/` | HTTP 客户端，REST 服务 | `servicebus.go` |
| `edge/pkg/devicetwin/` | 设备状态管理和同步 | `devicetwin.go` |
| `edge/pkg/metamanager/` | 消息处理和本地 SQLite 存储 | `metamanager.go` |
| `keadm/` | 集群启动和管理工具 | `cmd/keadm/` |
| `pkg/` | 共享库和工具 | `util/`, `version/`, `stream/` |
| `hack/` | 构建脚本和工具 | `make-rules/`, `lib/`, `generate-*.sh` |
| `manifests/` | Kubernetes 部署清单 | `charts/`, `profiles/` |
| `build/` | 构建配置和 Dockerfile | `cloud/`, `edge/`, `tools/` |
| `tests/` | E2E 测试 | `e2e/`, `performance/` |
| `staging/` | 阶段性代码，API 和 beehive 框架 | `src/github.com/kubeedge/` |

## 构建和运行方式

### 构建命令

| 命令 | 用途 | 说明 |
|------|------|------|
| `make all` | 构建所有组件 | 默认使用容器构建 |
| `make all WHAT=cloudcore` | 构建特定组件 | 构建 cloudcore |
| `make all WHAT=edgecore` | 构建边缘组件 | 构建 edgecore |
| `make all BUILD_WITH_CONTAINER=false` | 本地环境构建 | 需要本地 Go 环境 |
| `make crossbuild WHAT=edgecore ARM_VERSION=GOARM7` | 交叉编译 | 为 ARM 平台构建 |
| `make smallbuild WHAT=edgecore` | 小体积构建 | 优化的边缘构建 |
| `make image WHAT=cloudcore` | 构建容器镜像 | 构建 Docker 镜像 |

### 测试命令

| 命令 | 用途 | 说明 |
|------|------|------|
| `make test` | 运行所有测试 | 单元测试 |
| `make test WHAT=cloud` | 测试特定组件 | 测试云端组件 |
| `make test PROFILE=y` | 测试覆盖率 | 生成覆盖率报告 |
| `make integrationtest` | 集成测试 | 组件间集成测试 |
| `make e2e` | E2E 测试 | 端到端测试 |

### 代码质量检查

| 命令 | 用途 | 说明 |
|------|------|------|
| `make lint` | Go 代码检查 | 使用 golangci-lint |
| `make verify` | 验证代码生成 | 检查 vendor、CRD 等 |
| `make verify-golang` | 验证 Go 代码 | Go 语法检查 |
| `make verify-codegen` | 验证代码生成 | 检查生成的代码 |
| `make clean` | 清理构建产物 | 清理 _output 目录 |

### 运行方式

#### 容器运行（推荐）
```bash
# 使用 Docker 容器构建和运行（默认方式）
make all
docker run kubeedge/cloudcore:latest
```

#### 本地运行
```bash
# 构建后直接运行
make all BUILD_WITH_CONTAINER=false
./_output/local/bin/cloudcore
./_output/local/bin/edgecore
```

#### Go 直接运行
```bash
# 开发调试模式
go run cloud/cmd/cloudcore/main.go
go run edge/cmd/edgecore/main.go
```

## 外部依赖

### 核心框架依赖
| 依赖 | 版本 | 用途 |
|------|------|------|
| Kubernetes APIs | v0.31.12 | Kubernetes 客户端和 API |
| gRPC | v1.65.0 | 远程过程调用 |
| Protobuf | v1.35.2 | 协议缓冲区 |
| WebSocket (Gorilla) | v1.5.0 | 云边 WebSocket 通信 |

### 消息和通信
| 依赖 | 版本 | 用途 |
|------|------|------|
| Eclipse Paho MQTT | v1.2.0 | MQTT 协议支持 |
| GoMQTT | v0.10.4 | MQTT 客户端库 |
| Beego | v2.1.6 | Web 框架 |

### 数据存储
| 依赖 | 版本 | 用途 |
|------|------|------|
| SQLite3 | v1.14.22 | 边缘端本地数据库 |
| etcd client | v3.5.14 | 分布式键值存储 |

### 容器和运行时
| 依赖 | 版本 | 用途 |
|------|------|------|
| containerd | v1.7.25 | 容器运行时 |
| CRI-API | v0.31.12 | 容器运行时接口 |
| CSI | v1.9.0 | 容器存储接口 |

### 测试和工具
| 依赖 | 版本 | 用途 |
|------|------|------|
| Ginkgo | v2.19.0 | BDD 测试框架 |
| Gomega | v1.33.1 | 匹配器库 |
| golang/mock | v1.6.0 | Mock 生成工具 |
| Cobra | v1.8.1 | CLI 命令行框架 |

### 系统和网络
| 依赖 | 版本 | 用途 |
|------|------|------|
| netlink | v1.2.1-beta.2 | Linux 网络配置 |
| gopsutil | v3.23.2 | 系统监控 |
| Prometheus client | v1.19.1 | 监控指标 |

## 新手建议阅读顺序

### 第一阶段：基础了解
1. **项目介绍**
   - `README.md` - 项目概述和架构图
   - `CLAUDE.md` - 开发指南和构建命令
   - `docs/` - 官方文档（如果存在）

2. **核心架构**
   - `cloud/README.md` - 云端组件说明
   - `edge/README.md` - 边缘端组件说明
   - `pkg/README.md` - 共享包说明

### 第二阶段：核心组件
3. **云端入口点**
   - `cloud/cmd/cloudcore/main.go` - 云端主程序入口
   - `cloud/cmd/cloudcore/cloudcore.go` - 云端核心逻辑

4. **边缘端入口点**
   - `edge/cmd/edgecore/main.go` - 边缘端主程序入口
   - `edge/cmd/edgecore/edgecore.go` - 边缘端核心逻辑

5. **通信层**
   - `cloud/pkg/cloudhub/` - 云端 WebSocket 服务器
   - `edge/pkg/edgehub/` - 边缘端 WebSocket 客户端

### 第三阶段：深入理解
6. **控制器模式**
   - `cloud/pkg/edgecontroller/` - 边缘节点控制器
   - `cloud/pkg/devicecontroller/` - 设备控制器
   - `cloud/pkg/synccontroller/` - 同步控制器

7. **边缘运行时**
   - `edge/pkg/edged/` - 边缘容器运行时
   - `edge/pkg/metamanager/` - 元数据管理器
   - `edge/pkg/devicetwin/` - 设备孪生

8. **设备管理**
   - `edge/pkg/eventbus/` - MQTT 事件总线
   - `edge/pkg/servicebus/` - HTTP 服务总线

### 第四阶段：工具和测试
9. **管理工具**
   - `keadm/cmd/keadm/` - 集群管理命令行工具

10. **测试和构建**
    - `hack/make-rules/` - 构建脚本
    - `tests/e2e/` - 端到端测试
    - `cloud/test/integration/` - 云端集成测试
    - `edge/test/integration/` - 边缘端集成测试

### 第五阶段：高级主题
11. **扩展和插件**
    - `staging/src/github.com/kubeedge/api/` - API 定义
    - `staging/src/github.com/kubeedge/beehive/` - 微服务框架

12. **部署和配置**
    - `manifests/` - Kubernetes 部署清单
    - `build/` - 容器镜像构建配置

## 开发建议

- **开始开发前**：先运行 `make verify` 确保代码生成是最新的
- **提交代码前**：运行 `make lint` 进行代码检查
- **测试验证**：使用 `make test` 进行单元测试，`make e2e` 进行端到端测试
- **构建验证**：使用 `make all` 确保所有组件都能正确构建
- **容器构建**：默认使用容器构建，确保环境一致性

## 代码生成

项目使用代码生成来处理：
- Kubernetes CRD 和客户端代码（`hack/generate-crds.sh`）
- 设备管理接口 protobuf（`hack/generate-dmi-proto.sh`）
- 运行 `make verify-codegen` 检查生成代码是否最新