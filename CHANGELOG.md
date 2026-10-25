# Changelog

All notable changes to the **BFR-WEBUI-GO** project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [1.2.8] - 2026-10-21
### Security
- **Authentication Hardening**: Replaced new password hashes with bcrypt while preserving legacy hash verification, secured wallpaper upload and deletion routes, enabled secure session cookies over TLS, and removed query-string session token authentication from WebSocket endpoints.
- **File and Command Safety**: Added upload size limits and multipart cleanup, hardened archive extraction path containment, removed shell injection from subscription updates, and restricted default file-manager access.

### Added
- **ModemCaps-Derived Qualcomm Advanced Suite**:
  - **EFS Write Foundation (`internal/efs`)**: Robust EFS atomic item vs regular file detection, automatic pre-write backup (`.bak` with timestamp), read-back verification (byte-by-byte SHA-256 integrity check), and explicit Subsystem Restart (SSR) execution.
  - **Searcher Preferences & RAT Locks (`internal/efs/searcher.go`, `QualcommSearcher.svelte`)**: High-performance bit-packed `long[30]` decoder/encoder supporting RAT blacklist/whitelist (CDMA, HDR, GSM, WCDMA, LTE, TDSCDMA, NR5G), EARFCN/UARFCN list, and PCI/PSC locking.
  - **RRC Capabilities Editor (`internal/efs/capability_linux.go`, `QualcommCapability.svelte`)**: Interactive capability toggle matrix for 38 RRC feature tokens across `/nv/item_files/modem/rrc/cap/` and `/policyman/` (VoLTE, VoWiFi, ViLTE, CA, NR DC, UL CA, DL 256QAM, UL 64QAM, HPUE).
  - **MBN & PDC Profile Manager (`internal/efs/mbn.go`, `QualcommMBN.svelte`)**: Full carrier MBN configuration catalog supporting multi-source scanning across OS partitions, merged catalogs, and EFS fallbacks. Features 1-click PDC profile activation with rollback safety shield (`Revert to Previous`), autoselect mode toggling, and PixelParts mode switching.
  - **Custom User MBN Directory**: Dynamic scanner for custom carrier MBN files located in `/data/adb/modules/bfr_webui_go/custom_mbn`, automatically categorized with pink Neo-Brutalist badge styling.
  - **Active Carrier Resolver (`internal/handlers/mbn_handler.go`)**: Intelligent IMSI and SIM operator name fallback matching for active carrier profile reporting on SIM 1 and SIM 2.
  - **Comprehensive Reverse-Engineering Documentation (`docs/modemcaps/`, `docs/modemcaps-qualcomm-spec.md`)**: Complete teardown analysis of ModemCaps v2402-1, 3,092 decrypted strings, flow reports, and architecture specifications.
- **Hotspot Router Suite**: Added managed router controls with verified status reporting and cleanup limited to state owned by the application.
- **Release and Installation Tooling**: Added dual-variant release packaging, Magisk installer metadata, and daemon shutdown during module uninstall; ensured all local, Makefile, and CI packages include `META-INF` and the `uninstall.sh` hook.
- **SMS Message Pagination**: Added incremental message loading and improved parsing for message bodies containing commas.
### Changed & Refactored
- **Frontend Startup Performance**: Split tabs into dynamically loaded chunks, added asynchronous tab rendering and terminal keep-alive, and prevented overlapping sysinfo polling while handling expired sessions cleanly.
- **Backend Performance and Resilience**: Reused gzip writers, shared proxy log streaming, bounded worker queue submissions, and improved server startup and SSE timeout handling.
- **Build and Module Lifecycle**: Repaired Windows and Android release packaging, improved boot readiness and health checks, and preserved user configuration across module upgrades.
- **vnStat Accuracy**: Count only selected Wi-Fi and cellular interfaces, keep RX and TX accounting separate, persist counter baselines, and clarify calendar period labels.
- **Qualcomm Sub-Tabs & Styling**: Refactored Qualcomm sub-tabs (`QualcommSearcher`, `QualcommCapability`, `QualcommMBN`) with responsive badge styling, clean status indicators, and consistent modal dialogs.
- **Backend Clean Code & Optimization**: Eliminated `/bin/sh` shell invocation overhead on `setprop` calls, decoupled `DiscoverMbnConfigs` into a clean package function with nil-safe session handling.
- **SSH and NAS Defaults**: Require safer authentication defaults, keep SSH bound to localhost by default, and make NAS sharing authenticated and read-only by default.
### Fixed
- **Security and Data Handling**: Fixed archive extraction containment, subscription update command construction, wallpaper access controls, and upload resource cleanup.
- **SMS and Traffic Reporting**: Fixed SMS pagination and body parsing; excluded virtual and hotspot interfaces from vnStat accounting to reduce duplicate traffic totals.
- **Core Stability**: Fixed worker queue growth, session handling, proxy log streaming lifecycle, and several build and installation script failures.


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
