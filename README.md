# rdmaPerfMon - RDMA Performance Monitor

A real-time monitoring tool for RDMA (Remote Direct Memory Access) network interfaces on Linux systems.

![RDMA Performance Monitor](https://github.com/lordachoo/rdmaPerfMon/raw/main/screenshots/rdmaPerfMon-exampleView.png)

## Overview

rdmaPerfMon is a lightweight, terminal-based utility that provides real-time statistics and performance metrics for RDMA-capable network interfaces. It's designed for system administrators, network engineers, and HPC professionals who need to monitor RDMA traffic patterns, bandwidth utilization, and error conditions on high-performance computing clusters and data centers.

## Features

- **Real-time monitoring** of RDMA interfaces with configurable refresh rates
- **Multi-interface support** - monitor all RDMA interfaces or select specific ones
- **Comprehensive metrics** including:
  - Transmit/Receive bandwidth (bytes/sec and Gbps)
  - Packet rates (packets per second)
  - Error counters (receive errors, transmit discards)
  - Hardware-specific counters (out-of-sequence, timeouts)
  - Priority queue statistics (per-queue TX/RX rates)
- **Human-readable formatting** of network statistics
- **Color-coded output** for better readability
- **Zero dependencies** beyond standard Linux utilities

## Requirements

- Linux system with RDMA-capable network interfaces (Mellanox/NVIDIA ConnectX, Intel Omni-Path, etc.)
- Properly configured RDMA subsystem with drivers loaded
- Access to `/sys/class/infiniband/` filesystem (typically requires root privileges)
- `ethtool` and `ibdev2netdev` utilities installed (for priority queue statistics)

## Installation

1. Clone the repository or download the script:

```bash
git clone https://github.com/lordachoo/rdmaPerfMon
cd rdmaPerfMon
```

2. Make the script executable:

```bash
chmod +x rdmaPerfMon
```

3. Run the script (may require sudo for full access to counters):

```bash
sudo ./rdmaPerfMon
```

## Usage

```
./rdmaPerfMon [interface|all] [refresh_interval]
```

### Parameters

- `interface`: Specific RDMA interface to monitor (e.g., `mlx5_0`), or `all` to monitor all interfaces (default: `all`)
- `refresh_interval`: Update interval in seconds (default: `1`)

### Examples

Monitor all RDMA interfaces with 1-second refresh rate:
```bash
./rdmaPerfMon
```

Monitor all interfaces with 2-second refresh rate:
```bash
./rdmaPerfMon all 2
```

Monitor only a specific interface:
```bash
./rdmaPerfMon mlx5_2 1
```

Display help and list available interfaces:
```bash
./rdmaPerfMon --help
```

## Understanding the Output

The monitor displays the following information for each RDMA interface and port:

- **TX**: Transmit rate in human-readable format (KB/s, MB/s, GB/s) and Gbps
- **RX**: Receive rate in human-readable format and Gbps
- **Packets**: Transmit and receive packet rates (packets per second)
- **Errors**: Any receive errors or transmit discards (shown only when errors exist)
- **HW**: Hardware-specific counters like out-of-sequence packets and timeouts (shown only when non-zero)
- **Priority Queue Statistics**: Per-queue TX/RX rates for active priority queues (0-7)

## How It Works

rdmaPerfMon reads RDMA performance counters directly from the Linux `/sys/class/infiniband/` filesystem, which exposes hardware counters from the RDMA subsystem. For priority queue statistics, it uses `ethtool -S` to access network interface statistics. The script:

1. Identifies available RDMA interfaces and ports
2. Maps RDMA devices to network interface names using `ibdev2netdev`
3. Reads counter values at regular intervals from both sysfs and ethtool
4. Calculates rates based on the difference between readings
5. Converts raw counter values to human-readable formats
6. Displays formatted output with color coding

## Troubleshooting

### No interfaces found

If the script reports "No RDMA interfaces found", check:
- RDMA drivers are loaded (`lsmod | grep mlx` or similar)
- You have proper permissions to access `/sys/class/infiniband/`
- Run the script with `sudo` to ensure full access

### Counters showing zero

Some counters may show zero if:
- The interface is not actively transmitting/receiving data
- The specific counter is not supported by your hardware
- The driver doesn't expose that specific counter

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- Inspired by the need for simple, real-time RDMA monitoring tools
- Thanks to the Linux RDMA community for documenting the sysfs interface
