CHANGELOG

## [1.2.7] - 2026-10-06
### Added
- **Comprehensive Backend Unit Testing & High Coverage (>=85% across 11 packages)**:
  - Added structured internal unit tests across core subsystems: `auth`, `backup`, `bufferpool`, `charger`, `config`, `filemanager`, `hotspot`, `logger`, `nas`, `partition`, `power`, `qos`, `samsung`, `speedtest`, `ssh`, `tlsgen`, `vnstat`, and `worker`.
  - Reached 100.0% coverage on `bufferpool` and `worker`, 91.4% on `logger`, 89.8% on `auth`, 88.5% on `hotspot`, 88.3% on `vnstat`, 88.1% on `power`, 87.5% on `qos`, 85.8% on `filemanager`, 85.7% on `charger`, and 85.1% on `backup`.
  - `partition` coverage elevated to 78.8% via mock block device and slot suffix isolation.
- **Modular Parser Decoupling & Testability**:
  - `internal/hotspot`: Extracted `parseIPNeigh` and `parseArp` with `io.Reader` support for deterministic ARP table testing.
  - `internal/filemanager`: Decoupled `parseDfOutput` from filesystem execution for isolated storage calculation tests.
  - `internal/charger`: Introduced testable mock sysfs hooks (`BFR_MOCK_SYSFS_POWER_SUPPLY`, `BFR_MOCK_SYSFS_POWER_SUPPLY_ROOT`).
  - `internal/vnstat`: Added `BFR_MOCK_PROC_NET_DEV` support for zero-side-effect network interface parsing.
- **Module Upgrade Preservation (`customize.sh`)**:
  - Expanded configuration preservation list to maintain `network_config.json`, `cell_lock.json`, `kernel_config.json`, `modem_config.json`, `hotspot_mac_filter.json`, `nas.json`, `tunnel.json`, `auth.json`, `cloud_config.json`, and `power_schedule.json` across Magisk module updates.
### Added
- **Box for Magisk (BFM) Proxy Suite Overhaul (`internal/proxy/`, `TabProxy.svelte`)**:
  - **Modular Architecture (<800 lines/file)**: Decomposed proxy logic into `settings.go`, `packages.go`, `hotspot.go`, `subscription.go`, and `clash_api.go`. Frontend decomposed into `ProxyHero`, `ProxyNodes`, `ProxyRouting`, `ProxyApps`, `ProxySubscription`, and `ProxyLogs`.
  - **Bi-directional BFM Config Sync**: Direct synchronization with `/data/adb/box/settings.ini`, `package.list.cfg`, and `ap.list.cfg`.
  - **Kernel Routing Modes**: Transparent switching between `TPROXY`, `REDIRECT`, `TUN`, and `MIXED` with IPv6 and QUIC block toggles.
  - **Per-App Routing Matrix**: Interactive Blacklist vs Whitelist manager for installed Android apps with search and user app filtering.
  - **Tethering / Hotspot Proxying**: Route Wi-Fi and USB tethered client traffic directly through the proxy core.
  - **Subscription & GeoX Synchronizer**: Remote subscription download/reload and automated GeoIP (`Country.mmdb`) and GeoSite database updates with direct links to upstream MetaCubeX repositories.
  - **File Manager Integration**: 1-click shortcut from Proxy tab directly into `/data/adb/box` in BFR File Manager.
- **Multi-Tier SDK-Aware RAT Locking (Android 7–15)**:
  - **Strict 4G LTE Only**: Enforced native Android binary bitmask (`01000001000000000000`) on Android 11+ to completely eliminate 3G/GSM fallback leaks.
  - **5G & 4G Dual Lock**: Added dedicated `5g_4g` mode with Qualcomm QMI bitmask 80 (`0x50`) and Android bitmask (`11000001000000000000`).
  - **Legacy Fallback**: Automatic graceful fallback to `set-preferred-network-type` and Settings database on Android 7–10 (SDK < 30).
- **Persistent Network & Cell Lock Subsystem**:
  - Persistent storage in `/data/adb/bfr_webui_go/data/network_config.json` and `cell_lock.json` surviving full module ZIP updates and reboots.
  - Auto-apply boot hooks registered in `main.go --apply-tweaks` with baseband retry tolerances.
  - Added instant **Reset Default** button for DNS to cleanly purge custom iptables NAT redirections.
  - Added persistent **Reset to Auto** button and comprehensive Carrier Aggregation (CA) PCC/SCC architectural warnings in Cell Lock UI.
- **Interactive About QRIS Card**:
  - Pokemon TCG-style idle floating vertical animation with continuous holographic foil sheen.
  - Inspection zoom modal featuring 3D pop-in reveal, ambient golden aura burst, and high-contrast scannable canvas with 1-click download.

### Changed & Refactored
- **Cleaned Deprecated Features**: Removed obsolete *Dynamic Optimizations & Sysctl Tweaks* and *Receive Packet Steering (RPS)* cards, simplifying Network tab into pure TTL, DNS, and Ping diagnostics.
- **Hardened Ping Diagnostics**: Built multi-platform support, 7-second timeout context, packet loss tolerance, and root ICMP fallback.
- **Public Repository Migration**: Updated all repository links to `https://github.com/latifangren/BFR-WEBUI-GO-release` and Telegram community to `https://t.me/skrprojects`.
- **Strict UI Language Standard**: Standardized all UI labels, form controls, notices, and toasts across Proxy, Power, Network, and About tabs to 100% formal English in compliance with `AGENTS.md`.
