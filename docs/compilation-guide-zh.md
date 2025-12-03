---
permalink: /trustzone-sdk-docs/compilation-guide-zh.md
---

# 编译指南 (Compilation Guide)

## 关于 QConnectManager

**重要提示**: 本项目中不存在名为 "QConnectManager" 的组件。

如果您在寻找如何编译本项目的指南,请参阅下面的说明。本文档介绍了如何编译 Apache Teaclave™ TrustZone SDK 及其示例应用程序。

**Important Note**: There is no component named "QConnectManager" in this project.

If you are looking for a guide on how to compile this project, please refer to the instructions below. This document explains how to compile the Apache Teaclave™ TrustZone SDK and its example applications.

## 可用的组件 (Available Components)

本项目包含以下可编译的组件:

### 1. 核心库 (Core Libraries)
- `optee-teec`: OP-TEE 客户端 API
- `optee-utee`: OP-TEE 可信执行环境 API

### 2. 示例应用程序 (Example Applications)
- `hello_world-rs`: Hello World 示例
- `acipher-rs`: 非对称加密示例
- `aes-rs`: AES 加密示例
- `authentication-rs`: 认证示例
- `big_int-rs`: 大整数运算示例
- `build_with_optee_utee_sys-rs`: 使用 optee-utee-sys 的示例
- `client_pool-rs`: 客户端连接池示例 (包含连接管理器)
- `diffie_hellman-rs`: Diffie-Hellman 密钥交换示例
- `digest-rs`: 哈希摘要示例
- `error_handling-rs`: 错误处理示例
- `hotp-rs`: HOTP 认证示例
- `inter_ta-rs`: TA 间通信示例
- `message_passing_interface-rs`: MPI 示例
- `mnist-rs`: 机器学习推理示例
- `property-rs`: 属性处理示例
- `random-rs`: 随机数生成示例
- `secure_db_abstraction-rs`: 安全数据库抽象示例
- `secure_storage-rs`: 安全存储示例
- `serde-rs`: 序列化/反序列化示例
- `signature_verification-rs`: 数字签名验证示例
- `supp_plugin-rs`: Supplicant 插件示例
- `tcp_client-rs`: TCP 客户端示例
- `time-rs`: 时间操作示例
- `tls_client-rs`: TLS 客户端示例
- `tls_server-rs`: TLS 服务器示例
- `udp_socket-rs`: UDP 套接字示例

### 3. 项目 (Projects)
- `web3`: Web3 相关项目

## 快速开始: 在 QEMU 中编译和运行

### 方式一: 使用 Docker (推荐)

这是最简单的开始方式,适合想要快速体验的开发者。

#### 1. 拉取开发环境 Docker 镜像

```bash
# 拉取预构建的开发环境
docker pull teaclave/teaclave-trustzone-emulator-nostd-expand-memory:latest

# 克隆仓库
git clone https://github.com/apache/teaclave-trustzone-sdk.git
cd teaclave-trustzone-sdk

# 启动开发容器
docker run -it --rm \
  --name teaclave_dev_env \
  -v $(pwd):/root/teaclave_sdk_src \
  -w /root/teaclave_sdk_src \
  teaclave/teaclave-trustzone-emulator-nostd-expand-memory:latest
```

#### 2. 编译 Hello World 示例

在 Docker 容器内执行:

```bash
# 编译 Hello World 示例 (包括 CA 和 TA)
make -C examples/hello_world-rs/
```

编译成功后,您将在 `hello_world-rs` 目录下找到以下文件:
```bash
TA=ta/target/aarch64-unknown-linux-gnu/release/133af0ca-bdab-11eb-9130-43bf7873bf67.ta
HOST_APP=host/target/aarch64-unknown-linux-gnu/release/hello_world-rs
```

#### 3. 同步到模拟器

使用提供的帮助命令将编译产物同步到模拟环境:

```bash
sync_to_emulator --ta $TA
sync_to_emulator --host $HOST_APP
```

或者使用集成的 Makefile 目标一步完成编译和同步:

```bash
make -C examples/hello_world-rs/ emulate
```

#### 4. 运行示例

详细的运行步骤请参阅 [QEMU 快速开始文档](emulate-and-dev-in-docker.md)。

### 方式二: 手动设置构建环境

如果您想在没有 Docker 的情况下构建,或者需要针对特定硬件平台进行构建:

#### 1. 安装依赖

```bash
# 安装必要的依赖包
sudo apt-get install android-tools-adb android-tools-fastboot autoconf \
automake bc bison build-essential ccache cscope curl device-tree-compiler \
expect flex ftp-upload gdisk iasl libattr1-dev libc6:i386 libcap-dev \
libfdt-dev libftdi-dev libglib2.0-dev libhidapi-dev libncurses5-dev \
libpixman-1-dev libssl-dev libstdc++6:i386 libtool libz1:i386 make \
mtools netcat python-crypto python3-crypto python-pyelftools \
python3-pycryptodome python3-pyelftools python-serial python3-serial \
rsync unzip uuid-dev xdg-utils xterm xz-utils zlib1g-dev
```

#### 2. 克隆项目

```bash
git clone https://github.com/apache/teaclave-trustzone-sdk.git
cd teaclave-trustzone-sdk
```

#### 3. 设置 Rust 环境

```bash
./setup.sh
```

#### 4. 构建 OP-TEE 库

```bash
./build_optee_libraries.sh optee/
```

#### 5. 配置目标平台

默认目标平台是 `aarch64`。如果需要构建 `arm` 目标:

```bash
export ARCH_HOST=arm
export ARCH_TA=arm
```

#### 6. 编译示例

```bash
# 编译所有 no-std 示例
make examples

# 或者编译特定示例
make -C examples/hello_world-rs/

# 编译 std 示例
make std-examples
```

## 编译客户端连接池示例 (Client Pool Manager)

如果您在寻找连接管理器相关的功能,可以查看 `client_pool-rs` 示例:

```bash
# 编译客户端连接池示例
make -C examples/client_pool-rs/
```

这个示例包含了连接管理器的实现,支持 `r2d2` 和 `mobc` 两种连接池。

## 常见问题 (FAQ)

### Q: 如何编译所有示例?

```bash
# 编译所有 no-std 示例
make examples

# 编译所有 std 示例  
make std-examples
```

### Q: 编译失败,提示找不到 TA_DEV_KIT_DIR?

A: 请确保已经正确设置了构建环境。运行:

```bash
./build_optee_libraries.sh optee/
```

然后设置环境变量:

```bash
source environment
```

### Q: 如何清理编译产物?

```bash
make clean
```

### Q: 支持哪些目标平台?

A: 支持以下平台:
- `aarch64-unknown-linux-gnu` (默认)
- `arm-unknown-linux-gnu`
- QEMU virt (QEMUv8)
- 以及 [OP-TEE 支持的其他平台](https://optee.readthedocs.io/en/latest/general/platforms.html)

### Q: 如何在特定硬件上运行?

A: 请参阅 [高级设置文档](advanced-setup.md) 了解如何针对特定硬件平台进行配置和编译。

## 更多文档

- [QEMU 快速开始](emulate-and-dev-in-docker.md)
- [使用 Rust 标准库开发 TA](emulate-and-dev-in-docker-std.md)
- [高级设置](advanced-setup.md)
- [TA 开发模式](ta-development-modes.md)
- [示例概览](overview-of-optee-rust-examples.md)

## 获取帮助

如果您在编译过程中遇到问题:

1. 查看 [文档目录](README.md) 获取更多信息
2. 访问 [Apache Teaclave 邮件列表](https://lists.apache.org/list.html?dev@teaclave.apache.org)
3. 在 [GitHub Issues](https://github.com/apache/teaclave-trustzone-sdk/issues) 提交问题

## 贡献

欢迎贡献! 请参阅项目的 [README.md](../README.md) 了解如何参与贡献。
