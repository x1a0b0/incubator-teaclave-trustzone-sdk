---
permalink: /trustzone-sdk-docs/faq.md
---

# Frequently Asked Questions (FAQ)

## General Questions

### What is Apache Teaclave™ TrustZone SDK?

Apache Teaclave™ TrustZone SDK (Rust OP-TEE TrustZone SDK) provides the ability to build safe TrustZone applications in Rust. The SDK is based on the OP-TEE project and provides ergonomic APIs for developing Trusted Applications (TAs).

### What components are available in this project?

This project does **NOT** include a component called "QConnectManager". 

The project includes:

**Core Libraries:**
- `optee-teec`: OP-TEE Client API
- `optee-utee`: OP-TEE Trusted Execution Environment API
- `optee-utee-build`: Build support for OP-TEE TAs

**Example Applications:** (in the `examples/` directory)
- `hello_world-rs`: Basic Hello World example
- `aes-rs`: AES encryption example
- `acipher-rs`: Asymmetric cipher example
- `authentication-rs`: Authentication example
- `big_int-rs`: Big integer operations
- `build_with_optee_utee_sys-rs`: Example using optee-utee-sys
- `client_pool-rs`: Client connection pool (includes connection managers)
- `diffie_hellman-rs`: Diffie-Hellman key exchange
- `digest-rs`: Hash digest example
- `error_handling-rs`: Error handling patterns
- `hotp-rs`: HOTP authentication
- `inter_ta-rs`: Inter-TA communication
- `message_passing_interface-rs`: MPI example
- `mnist-rs`: Machine learning inference
- `property-rs`: Property handling
- `random-rs`: Random number generation
- `secure_db_abstraction-rs`: Secure database abstraction
- `secure_storage-rs`: Secure storage
- `serde-rs`: Serialization/deserialization
- `signature_verification-rs`: Digital signature verification
- `supp_plugin-rs`: Supplicant plugin
- `tcp_client-rs`: TCP client
- `tls_client-rs`: TLS client
- `tls_server-rs`: TLS server
- `time-rs`: Time operations
- `udp_socket-rs`: UDP socket

**Projects:**
- `web3`: Web3 related projects

## Building and Compilation

### How do I compile this project?

See the [Quick Start guides](emulate-and-dev-in-docker.md) or the [Chinese Compilation Guide](compilation-guide-zh.md) for detailed instructions.

Quick summary:
```bash
# Using Docker (recommended)
docker pull teaclave/teaclave-trustzone-emulator-nostd-expand-memory:latest
docker run -it --rm --name teaclave_dev_env \
  -v $(pwd):/root/teaclave_sdk_src \
  -w /root/teaclave_sdk_src \
  teaclave/teaclave-trustzone-emulator-nostd-expand-memory:latest

# Build an example
make -C examples/hello_world-rs/
```

### How do I build all examples?

```bash
# Build all no-std examples
make examples

# Build all std examples
make std-examples
```

### How do I build a specific example?

```bash
make -C examples/<example-name>/
```

For example:
```bash
make -C examples/hello_world-rs/
make -C examples/aes-rs/
make -C examples/client_pool-rs/
```

### What is the difference between `no-std` and `std` modes?

- **no-std mode**: TAs are built without Rust's standard library, resulting in smaller binaries
- **std mode**: TAs can use Rust's standard library and many third-party crates

For detailed comparison, see [TA Development Modes](ta-development-modes.md).

### How do I enable `std` mode for a TA?

Set the `STD` environment variable:
```bash
export STD=y
```

Or use the `std-examples` target:
```bash
make std-examples
```

### Build fails with "TA_DEV_KIT_DIR is not correctly defined"

You need to build the OP-TEE libraries first:

```bash
./build_optee_libraries.sh optee/
source environment
```

### How do I clean build artifacts?

```bash
# Clean all examples
make clean

# Clean a specific example
make -C examples/hello_world-rs/ clean
```

## Platform and Hardware

### What platforms are supported?

- **QEMU virt** (QEMUv8) - recommended for development and testing
- **ARM Juno Board**
- **Raspberry Pi 3**
- **HiKey 620/960**
- And other [platforms supported by OP-TEE](https://optee.readthedocs.io/en/latest/general/platforms.html)

### How do I build for ARM instead of AArch64?

Set the architecture environment variables:
```bash
export ARCH_HOST=arm
export ARCH_TA=arm
```

### Can I run TAs on real hardware?

Yes! While QEMU is convenient for development, TAs can be deployed to real ARM TrustZone-enabled hardware. See the [Advanced Setup](advanced-setup.md) documentation for platform-specific instructions.

## Development

### How do I debug a TA?

See the [Debugging OP-TEE TA](debugging-optee-ta.md) guide.

### How do I add a new TA?

1. Look at existing examples in the `examples/` directory
2. Create a new directory with the structure: `my-ta/host/`, `my-ta/ta/`
3. Follow the patterns in `hello_world-rs` as a template
4. See [Writing Rust TAs using optee-utee-build](writing-rust-tas-using-optee-utee-build.md)

### Where can I find the connection pool manager code?

The `client_pool-rs` example includes connection pool implementations:
- `r2d2` pool manager: `examples/client_pool-rs/host/src/pool/r2d2_pool.rs`
- `mobc` pool manager: `examples/client_pool-rs/host/src/pool/mobc_pool.rs`

### How do I use third-party Rust crates in my TA?

In `std` mode, you can use many third-party crates. In `no-std` mode, you need `no_std`-compatible crates. Add them to your `Cargo.toml` as usual.

## Running and Testing

### How do I run a TA in QEMU?

See the [Quick Emulation And Development in Docker](emulate-and-dev-in-docker.md) guide for detailed steps.

Summary:
1. Build the example
2. Sync artifacts to emulator: `sync_to_emulator --ta $TA --host $HOST_APP`
3. Start QEMU and run the host application

### How do I run tests?

```bash
# Run tests in QEMU
./tests/optee-qemuv8.sh
```

## Troubleshooting

### I get "error: failed to run custom build command" when building

Make sure you have:
1. Run `./setup.sh` to install Rust toolchains
2. Built OP-TEE libraries: `./build_optee_libraries.sh optee/`
3. Sourced the environment: `source environment`

### Rust compiler version issues

This project uses a specific Rust toolchain defined in `rust-toolchain.toml`. The toolchain should be automatically installed when you run `./setup.sh`.

### TA fails to load in QEMU

Check that:
1. The TA `.ta` file is in the correct location: `/lib/optee_armtz/`
2. The TA UUID matches between the TA and the host application
3. The OP-TEE environment is properly initialized

### Where can I get help?

1. Check the [documentation directory](README.md)
2. Join the [Apache Teaclave mailing list](https://lists.apache.org/list.html?dev@teaclave.apache.org)
3. Submit an issue on [GitHub](https://github.com/apache/teaclave-trustzone-sdk/issues)

## Documentation

### Where can I find more documentation?

- [Documentation Index](README.md)
- [Quick Start in Docker](emulate-and-dev-in-docker.md)
- [Advanced Setup](advanced-setup.md)
- [Examples Overview](overview-of-optee-rust-examples.md)
- [TA Development Modes](ta-development-modes.md)
- [Chinese Compilation Guide](compilation-guide-zh.md)

## Contributing

### How can I contribute?

Apache Teaclave™ is open source and welcomes contributions! See the main [README.md](../README.md) for contribution guidelines.

### Do I need to sign a CLA?

Yes, major contributions and all committers must have a signed CLA on file, as required by the Apache Software Foundation.
