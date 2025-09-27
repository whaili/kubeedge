# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

KubeEdge is a Kubernetes-native edge computing framework that extends native containerized application orchestration to edge nodes. It consists of cloud-side and edge-side components that work together to manage workloads and devices at the edge.

## Common Development Commands

### Building
```bash
# Build all components (uses containers by default)
make all

# Build specific component
make all WHAT=cloudcore
make all WHAT=edgecore

# Build without containers (requires local Go environment)
make all BUILD_WITH_CONTAINER=false

# Cross-platform build
make crossbuild WHAT=edgecore ARM_VERSION=GOARM7
```

### Testing
```bash
# Run all tests
make test

# Test specific component
make test WHAT=cloud
make test WHAT=edge

# Run tests with coverage
make test PROFILE=y

# Integration tests
make integrationtest

# E2E tests
make e2e
```

### Linting and Verification
```bash
# Run Go linting (uses golangci-lint)
make lint

# Verify code generation, vendor, and CRDs
make verify

# Individual verification commands
make verify-golang
make verify-vendor
make verify-codegen
make verify-crds
```

### Development Workflow
```bash
# Clean build artifacts
make clean

# Generate CRDs
make generate

# Small build for edgecore (optimized)
make smallbuild WHAT=edgecore

# Build container images
make image WHAT=cloudcore
```

## Architecture Overview

### Cloud Components
- **CloudCore**: Main cloud-side binary (`cloud/cmd/cloudcore/`)
  - **CloudHub**: WebSocket server for cloud-edge communication
  - **EdgeController**: Extended Kubernetes controller managing edge nodes/pods
  - **DeviceController**: Manages edge devices via CRDs
- **Admission**: Admission webhook controller (`cloud/cmd/admission/`)
- **ControllerManager**: Additional controllers (`cloud/cmd/controllermanager/`)

### Edge Components
- **EdgeCore**: Main edge-side binary (`edge/cmd/edgecore/`)
  - **EdgeHub**: WebSocket client for cloud communication
  - **Edged**: Container runtime agent (kubelet-like)
  - **EventBus**: MQTT client for device communication
  - **ServiceBus**: HTTP client for REST services
  - **DeviceTwin**: Device status management and sync
  - **MetaManager**: Message processing and local SQLite storage

### Management Tools
- **keadm**: Cluster bootstrap and management tool (`keadm/cmd/keadm/`)

### Key Directories
- `cloud/`: Cloud-side components and controllers
- `edge/`: Edge-side runtime and agents
- `pkg/`: Shared libraries and utilities
- `hack/`: Build scripts and tooling
- `manifests/`: Kubernetes deployment manifests
- `build/`: Build configurations and Dockerfiles

## Go Module Structure

This is a Go 1.22+ project using Go modules. The main module is `github.com/kubeedge/kubeedge` with key dependencies including:
- Kubernetes client-go and APIs
- MQTT libraries (Eclipse Paho, GoMQTT)
- gRPC and protobuf
- Container runtime interfaces

## Code Generation

The project uses code generation for:
- Kubernetes CRDs and clients (`hack/generate-crds.sh`)
- Device Management Interface protobuf (`hack/generate-dmi-proto.sh`)
- Run `make verify-codegen` to check if generated code is up to date

## Testing Strategy

- Unit tests throughout `*_test.go` files
- Integration tests in `cloud/test/integration/` and `edge/test/integration/`
- E2E tests in `tests/` directory
- Mock generation using `golang/mock`

## Container Build

By default, builds use containers for reproducibility. Build scripts are in `hack/make-rules/` and use `hack/make-rules/build_with_container.sh` wrapper.

## Key Configuration Files

- `.golangci.yml`: Go linting configuration
- `Makefile`: Primary build interface with comprehensive help (`make HELP=y`)
- `go.work`: Go workspace configuration
- `external-dependency.md`: Dependency documentation

## Reply Guidelines
- Always reference **file path + function name** when explaining code.
- Use **Mermaid diagrams** for flows, call chains, and module dependencies.
- If context is missing, ask explicitly which files to `/add`.
- Never hallucinate non-existing functions or files.
- Always reply in **Chinese**

## Excluded Paths
- vendor/
- build/
- dist/
- .git/
- third_party/

## Glossary


## Run Instructions
