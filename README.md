# gre-manager
Simple GRE Tunnel Manager for Linux VPS servers. Create, check, manage, delete, and monitor multiple GRE tunnels with systemd, MSS clamp, rp_filter control, watchdog support, and existing tunnel discovery. Tested on Ubuntu VPS servers.


# GRE Tunnel Manager

A simple Bash menu script for creating and managing GRE tunnels between Linux VPS servers.

This tool helps you set up GRE tunnels between Iran and foreign servers, manage multiple tunnels, enable MSS clamp, control `rp_filter`, check tunnel health, and detect GRE tunnels that were created before.

## Features

* Create GRE tunnels with a simple menu
* Support for multiple GRE tunnels on one server
* Set tunnel name, GRE key, MTU, local IP, remote IP, and private tunnel IP
* Make GRE tunnels permanent with `systemd`
* Delete existing GRE tunnels
* Edit tunnel settings
* Show tunnel status and logs
* Check GRE tunnel health
* Enable or remove MSS clamp rules
* Enable or disable `rp_filter`
* Discover existing GRE tunnels already configured on the server
* Detect old GRE services created manually with `systemd`
* Optional watchdog for automatic tunnel checks

## Tested On

Tested on Ubuntu VPS servers.

Recommended systems:

* Ubuntu 20.04
* Ubuntu 22.04
* Ubuntu 24.04
* Debian-based servers with `systemd`

## Requirements

The script should be run as root.

Required tools:

* bash
* iproute2
* iptables
* systemd
* ping
* curl

Install basic requirements:

```bash
apt update
apt install -y iproute2 iptables curl
```

## Installation

Download and run:

```bash
bash <(curl -fsSL https://raw.githubusercontent.com/Hasan-Torabi/gre-manager/main/gre-manager)
```

Or install manually:

```bash
wget https://raw.githubusercontent.com/Hasan-Torabi/gre-manager/main/gre-manager -O /root/gre-manager.sh
chmod +x /root/gre-manager.sh
bash /root/gre-manager.sh
```

After installation, you can run the manager from anywhere:

```bash
gre-manager
```

## Important Note for Servers Without GitHub Access

If your server cannot access GitHub, download the script on another system and upload it to the server with `scp`, WinSCP, or FileZilla.

Example:

```bash
scp gre-manager.sh root@YOUR_SERVER_IP:/root/
```

Then run:

```bash
chmod +x /root/gre-manager.sh
bash /root/gre-manager.sh
```

## Menu Options

```text
0) GRE pre-check between two servers
1) Create/setup a GRE tunnel
2) Delete a tunnel
3) Edit a tunnel
4) View tunnel logs/status
5) Check tunnel health
6) Show tunnel details
7) Manage MSS clamp
8) Manage rp_filter
9) Manage watchdog
10) Install/update manager
11) Discover/import existing GRE tunnels
```

## Existing GRE Tunnel Support

If you already created GRE tunnels manually before, this script can still help you.

It can detect:

* active GRE interfaces from `ip tunnel show`
* old `systemd` GRE services
* setup scripts like `/root/setup-gre-*.sh`
* tunnels created manually before using this manager

So you do not have to recreate everything from zero.

Use:

```bash
gre-manager --discover
```

Or open the menu and choose:

```text
11) Discover/import existing GRE tunnels
```

## Basic Usage

Run the script on both servers:

```bash
gre-manager
```

First, use option `0` on both servers to check if GRE can work between them.

Then use option `1` on both servers to create the real GRE tunnel.

On the Iran server, choose the Iran-side role.

On the foreign server, choose the Foreign-side role.

The script will ask for:

* local public IP
* remote public IP
* tunnel name
* GRE key
* MTU
* private tunnel IP
* MSS clamp setting
* rp_filter setting
* watchdog setting

## Example Tunnel Plan

Example:

```text
Iran public IP:      YOUR_IRAN_SERVER_PUBLIC_IP
Foreign public IP:   YOUR_FOREIGN_SERVER_PUBLIC_IP
Tunnel name:         gre2
GRE key:             8020
MTU:                 1300
Iran tunnel IP:      10.80.0.2/30
Foreign tunnel IP:   10.80.0.1/30
```

Test from Iran:

```bash
ping 10.80.0.1
```

Test from foreign server:

```bash
ping 10.80.0.2
```

## MSS Clamp

MSS clamp is recommended for GRE tunnels, especially when using TCP traffic, Xray, 3x-ui, web traffic, or proxy traffic.

The script can add MSS clamp rules for:

* `FORWARD -o greX`
* `FORWARD -i greX`
* `OUTPUT -o greX`

You can enable or remove MSS clamp from the menu.

## rp_filter

For GRE tunnels, it is usually better to disable `rp_filter` on the GRE interface.

This helps avoid packet drops when you have multiple tunnels, custom routes, Xray, or 3x-ui routing.

The script can show and change `rp_filter` settings for GRE interfaces.

## Systemd Service

Each tunnel can be saved as a separate `systemd` service.

This means the GRE tunnel can come back automatically after reboot.

Example service name:

```text
gre-manager-gre2.service
```

Useful commands:

```bash
systemctl status gre-manager-gre2.service --no-pager
systemctl restart gre-manager-gre2.service
systemctl stop gre-manager-gre2.service
```

## Health Check

The script can check if a GRE tunnel is active by checking:

* GRE interface exists
* interface is UP
* private tunnel IP is reachable by ping
* optional TCP port checks

## Uninstall

Remove the manager:

```bash
rm -f /usr/local/bin/gre-manager
rm -f /root/gre-manager.sh
```

Remove a tunnel from the menu:

```text
2) Delete a tunnel
```

## Notes

Use a different tunnel name for each GRE tunnel.

Good examples:

```text
gre1
gre2
gre-armenia
gre-turkey
```

Use a different private subnet for each tunnel.

Good examples:

```text
10.60.0.0/30
10.80.0.0/30
10.90.0.0/30
```

Use a different GRE key for each tunnel.

Good examples:

```text
6010
8020
9030
```

Recommended default MTU:

```text
1300
```

## Disclaimer

This script is made for simple GRE tunnel management on Linux VPS servers.

Use it carefully on production servers. Always check your routes, firewall rules, and existing network configuration before making changes.
