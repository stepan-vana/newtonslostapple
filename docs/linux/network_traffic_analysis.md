{[date]Last updated June 11, 2026}
# Network Traffic Anylysis
{[author]{pp::stepan-vana}Štěpán Váňa}
{[read_time]5 min read}
{[copy_raw_file]Copy Markdown}
{[view_as_md]View as Markdown}

In this exercise, we will explore the use of `tcpdump`, a tool for capturing, displaying, and saving packets traveling across a network. It is widely used for troubleshooting network issues and diagnosing communication between devices.

## Basic Usage

```bash
tcpdump [options] [expression]
```

---

## Options

`[options]` — parameters that modify the behavior of the program:

- `-i <interface>` — select the network interface to capture traffic on (e.g. `eth0`, `wlan0`, or `any` to capture on all available interfaces)
- `-s <size>` — set the maximum capture size per packet in bytes (default is 262144 in modern versions; use `0` or `65535` to capture full packets)
- `-w <file>` — save captured traffic to a file
- `-n` — do not resolve hostnames to IP addresses (e.g. `google.com` → `8.8.8.8`)
- `-nn` — do not resolve hostnames or port names to service names (e.g. `22` → `ssh`)
- `-c <count>` — stop after capturing `<count>` packets (default: unlimited)
- `-A` — display packet contents in ASCII format
- `-X` — display packet contents in hexadecimal format
- `-vv` — display more verbose information about captured packets

---

## Expressions

`[expression]` — a filter condition that must be met for a packet to be captured:

| Expression | Description |
|---|---|
| `host <host>` | Capture packets to/from `<host>` |
| `port <port>` | Capture packets on port `<port>` |
| `src <host>` | Capture packets originating from `<host>` |
| `dst <host>` | Capture packets destined for `<host>` |
| `src port <port>` | Capture packets from source port `<port>` |
| `dst port <port>` | Capture packets destined for port `<port>` |
| `tcp` | Capture TCP packets only |
| `udp` | Capture UDP packets only |
| `icmp` | Capture ICMP packets only |
| `ip` | Capture IP packets only |
| `ether` | Capture Ethernet packets only |
| `net <network>` | Capture packets for network `<network>` |

### Logical Operators

Expressions can be combined using logical operators:

| Operator | Symbol |
|---|---|
| AND | `and` / `&&` |
| OR | `or` / `\|\|` |
| NOT | `not` / `!` |
| Grouping | `(` and `)` |

**Examples:**

```bash
# Capture TCP packets on port 22
tcp and port 22

# Capture TCP packets on port 22 or 80
tcp and (port 22 or port 80)
```

---

## Usage Examples

### Save all traffic to a file (full packet size)

```bash
tcpdump -i any -s 0 -w traffic.pcap
```

#### Download the file via `sftp`

```bash
sftp -P 2222 admin@[ip]
get traffic.pcap
```

#### Download via `scp`

```bash
scp -P <port> <user>@[ip]:traffic.pcap .
```

#### Download via `rsync`

```bash
rsync -avz -e 'ssh -p <port>' <user>@[ip]:traffic.pcap .
```

---

### Display HTTP traffic on all interfaces

```bash
tcpdump -i any -s 0 -A -vvv 'tcp and port 80'
```

---

### Complex filter expression

```bash
tcpdump -i any -s 0 -A -vvv 'tcp and (port 22 or port 80) and (src host 192.168.1.34 or dst host 1.1.1.1)'
```

This captures TCP packets that:
- are destined for port `22` **or** port `80`, **and**
- originate from `192.168.1.34` **or** are destined for `1.1.1.1`

<br><br>