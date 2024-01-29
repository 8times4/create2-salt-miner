# ⛏️ Salty – CREATE2 Salt Miner

Nickname: `salty`

An _extremely_ fast miner for finding salts that create gas-efficient Ethereum addresses via `CREATE2`.

Salty only searches for results better than what is already found. For example, if a salt is found that results in an address with 3 leading zero bytes, the next salt will only be displayed if it results in an address with 4 leading zero bytes. This improves performance by reducing the number of times the kernel needs to communicate with the host.

Salty can run for a really long time and will keep finding better salts. It is recommended to leave it running for a few hours if you're looking to find a salt that results in an efficient address.

Salty is written in [Rust](https://www.rust-lang.org/), uses [Alloy](https://github.com/alloy-rs/core) for Ethereum primitives and uses [OpenCL](https://www.khronos.org/opencl/) as the processing backend.

Salty always uses OpenCL, which means it can utilize a wide variety of systems including CPUs, GPUs and supported accelerators. It is highly recommended to use GPUs for mining as they are significantly faster than CPUs. If you'd like to use CPUs, you'll need to install the OpenCL Driver for your platform. Typically, for all `x86-64` systems, [Intel's OpenCL Driver](https://software.intel.com/content/www/us/en/develop/articles/opencl-drivers.html) works best.

Using OpenCL Platform as the backend also means you can run it across multiple GPUs as long as they are grouped in the same OpenCL Platform. By default, if your GPUs are from the same vendor, the drivers will automatically group them. Additional configuration may be required if you have GPUs from different vendors.

## Installation

Binaries are available on GitHub Releases, built via GitHub Actions to ensure they aren't tampered with.

Salty is currently tested on Linux, macOS and Windows.

If you'd like to build from source, you'll need Rust and OpenCL SDK installed and available in `PATH`.

```bash
git clone git@github.com:akshatmittal/create2-salt-miner.git
cargo build
```

Generic `OpenCL.lib` from Khronos's OpenCL SDK is included in this repository. However, if you'd like to use a specific vendor's OpenCL library, you can do so by deleting the file in root.

## Features

- [x] Multiple Config Sources (CLI, Config File)
- [x] OpenCL Backend (CPU, GPU, Accelerators)
- [x] Ranking Mode (Zero Bytes)
- [x] CREATE2 Support
- [ ] CREATE3 Support
- [ ] Pattern Matching Mode
- [ ] Hardhat Plugin
- [ ] Foundry Plugin
- [ ] WASM Build (is that even possible?)

## Usage

The salt miner can be used via the CLI by providing each option as an argument.

```bash
./salty mine --factory 0x0000000000FFe8B47B3e2130213B802212439497
             --caller 0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045
             --codehash 0x64e604787cbf194841e7b68d7cd28786f6c9a0a3ab9f8b0a0e87cb4387ab0107
```

Alternatively, you can create a file named `salty.toml` in the current directory with the same parameter names and simply run the miner.

```bash
./salty mine
```

CLI arguments take precedence so any arguments you provide via the CLI will override the arguments provided via the config file.

Additionally, Salty also includes a `list` command to display all available OpenCL platforms on the device.

```bash
./salty list
```

## Parameters

The following parameters are available when using the `mine` command.

| Option     | Description                                                          | Default                                      |
| ---------- | -------------------------------------------------------------------- | -------------------------------------------- |
| `factory`  | Factory address that will be used to deploy the contract via CREATE2 | `0x0000000000FFe8B47B3e2130213B802212439497` |
| `caller`   | Caller for the deployment                                            | (none - required parameter)                  |
| `codehash` | Keccak-256 hash of the contract initialization code                  | (none - required parameter)                  |
| `worksize` | Work size per batch                                                  | `0x4400000`                                  |
| `zeros`    | Minimum zero bytes to look for in the created contract (no stop)     | `1`                                          |

All of the available parameters can be supplied to the command line, or be set using the config file (`salty.toml`). CLI arguments will override any options in the configuration file.

## Acknowledgements

This project is heavily inspired by 0age's `create2crunch`. The code for the OpenCL Kernel is taken from there and modified to work in this context.

- [0age](https://github.com/0age)
- [Khronos OpenCL SDK](https://github.com/KhronosGroup/OpenCL-SDK)
