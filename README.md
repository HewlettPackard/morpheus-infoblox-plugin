# Morpheus Infoblox Plugin

This plugin provides an IPAM and DNS integration between [Infoblox](https://www.infoblox.com/) and [Morpheus](https://morpheusdata.com). It enables IPv4 and IPv6 network pool sync, DNS zone and record inventory, host record management, IP allocation, DHCP reservation support, and IP release automation from within the Morpheus platform.

## Requirements

| Component | Minimum Version |
|-----------|----------------|
| Morpheus | 9.0.0 |

## Installation

1. Download the latest `.jar` from the [Releases](https://github.com/HewlettPackard/morpheus-infoblox-plugin/releases) page, or [build it yourself](#building).
2. In Morpheus, navigate to **Administration → Integrations → Plugins**.
3. Click **Browse** and upload the `.jar` file.
4. The **Infoblox** IPAM/DNS network service integration will appear after the plugin loads.

## Configuration

When adding an Infoblox network service in Morpheus (**Infrastructure → Network → Services**), provide the following:

| Field | Description |
|-------|-------------|
| **API Url** | Infoblox WAPI endpoint, e.g. `https://x.x.x.x/wapi/v2.2.1`. |
| **Credentials** | Morpheus credential containing the Infoblox username and password. |
| **Username** | Infoblox username used when local credentials are selected. |
| **Password** | Infoblox password used when local credentials are selected. |
| **Throttle Rate** | Optional API throttle rate for Infoblox requests. |
| **Disable SSL SNI Verification** | Disables SSL SNI verification when connecting to Infoblox. |
| **Inventory Existing** | Syncs existing IP address records and DNS resource records from Infoblox into Morpheus. |
| **Host Only** | Creates only the Infoblox host record during allocation. |
| **Alternate DNS Method** | Uses the Infoblox `configure_for_dns` host flag instead of creating separate DNS records. |
| **DNS view** | DNS view where the zone exists. |
| **Network Filter** | Optional Infoblox network query filter. |
| **Zone Filter** | Optional Infoblox zone query filter. |
| **Tenant Match Attribute** | Optional extra attribute used to match tenant-specific inventory. |
| **IP Mode** | Allocation mode: static IPs or DHCP reservations. |
| **Extra Attributes** | JSON template for Infoblox extensible attributes saved on host records. Supports `userId`, `username`, and `dateCreated` values. |

Credentials can also be stored as a Morpheus [Credential](https://docs.morpheusdata.com/en/latest/administration/credentials/credentials.html) and selected at network service setup time.

## Features

### IPAM Sync

The plugin implements `IPAMProvider` and keeps Morpheus network pools aligned with Infoblox.

- **IPv4 networks** — synced as Infoblox network pools with CIDR and range data
- **IPv6 networks** — synced as Infoblox IPv6 network pools
- **Network views** — included in synced pool display names and used during allocation
- **Filtered sync** — optionally limits inventory with Infoblox network query filters

Any additions, updates, and removals in Infoblox are automatically reflected in Morpheus on the next network service refresh.

### IP Address Inventory

When existing inventory sync is enabled, the plugin caches Infoblox address records for synced pools.

- **IPv4 addresses** — synced from Infoblox `ipv4address` records
- **IPv6 addresses** — synced from Infoblox `ipv6address` records
- **Address state** — assigned, used, and unmanaged states are reflected in Morpheus
- **Hostnames** — synced from Infoblox address names when available

### IP Allocation and Release

Morpheus can allocate and release addresses from synced Infoblox networks during workload lifecycle operations. Supported operations include:

- Allocate the next available IPv4 or IPv6 address from a network
- Assign requested IPv4 or IPv6 addresses through host records
- Create DHCP reservations when IP mode is set to DHCP and a MAC address is present
- Update host record names
- Release host records and associated DNS records when workloads are removed
- Apply Infoblox extensible attributes to created host and DNS records

### DNS Zone Sync

The plugin implements `DNSProvider` and discovers authoritative DNS zones from Infoblox.

- **Authoritative zones** — synced into Morpheus as network domains
- **Zone filters** — optionally limit DNS zone inventory with Infoblox query filters
- **Existing record inventory** — optional sync of existing DNS resource records when enabled in configuration

### DNS Record Management

DNS records can be managed from Morpheus through the Infoblox WAPI. Supported operations include:

- Create A records
- Create AAAA records
- Create CNAME records
- Create TXT records
- Create MX records
- Create PTR records during host allocation when requested
- Delete DNS records
- Sync existing A, AAAA, PTR, TXT, CNAME, and MX records

## Building

```bash
./gradlew shadowJar
```

The plugin JAR will be written to `build/libs/`.

## License

Copyright 2026 Morpheus Data, LLC. Licensed under the [Apache License, Version 2.0](LICENSE).
