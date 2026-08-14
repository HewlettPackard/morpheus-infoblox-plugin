# Morpheus Infoblox Plugin

The Morpheus Infoblox Plugin integrates Morpheus with Infoblox to provide IP address management (IPAM) and DNS record automation. The plugin communicates with the Infoblox WAPI (Web API) to allocate and release IP addresses and manage DNS records.

## Table of Contents

- [Features](#features)
- [Requirements](#requirements)
- [Repository structure](#repository-structure)
- [Building the plugin](#building-the-plugin)
- [License](#license)
- [Installing](#installing)
- [Detailed Usage Steps](#detailed-usage-steps)
- [API Endpoints](#api-endpoints)

---

## Features

### IP Address Management

Allocate and release IP addresses from Infoblox network pools within Morpheus. Supports automatic next-available IP selection, manual IP entry, and existing inventory import.

### DNS Record Management

Create and delete A, AAAA, CNAME, TXT, and MX DNS records in Infoblox zones when instances are provisioned or decommissioned.

### Cloud Sync

Morpheus synchronises the following Infoblox resources for inventory:

- Network pools (subnets and IP ranges)
- DNS zones and records

---

## Requirements

| Requirement | Version |
|-------------|---------|
| Morpheus | 9.0.0 or later |
| Java | 25 or later |
| Gradle | Use the included Gradle wrapper (`./gradlew`) |

Additional prerequisites:

- A running Infoblox Grid Manager accessible over HTTP or HTTPS from the Morpheus appliance
- An Infoblox user account with read/write API access (WAPI access enabled)
- Network access from the Morpheus appliance to the Infoblox host on the configured port

---

## Repository structure

```
src/main/groovy/com/morpheusdata/infoblox/
├── InfobloxPlugin.groovy              - Plugin entry point; registers InfobloxProvider and InfobloxOptionSourceProvider
├── InfobloxProvider.groovy            - IPAMProvider implementation; IPAM and DNS operations, sync, OptionTypes
└── InfobloxOptionSourceProvider.groovy - UI option source data
src/main/groovy/com/morpheusdata/util/ - Shared utility classes
build.gradle, gradle.properties        - Build configuration and plugin metadata
```

---

## Building the plugin

Run the following command to compile and package the plugin jar:

```bash
./gradlew clean build
```

The packaged jar will be written to `build/libs/`.

To execute tests, use the following command:

```bash
./gradlew test
```

---

## License

This project is licensed under the Apache License 2.0.

See the [LICENSE](LICENSE) file for details.

---

## Installing

1. Build the plugin (see [Building the plugin](#building-the-plugin)) or download a released jar.
2. In Morpheus, navigate to **Administration > Integrations > Plugins**.
3. Click **Add** and upload the `morpheus-infoblox-plugin-<version>.jar` from `build/libs/`.
4. Navigate to **Infrastructure > Networks > IP Pools > Add** and select **Infoblox** to configure the integration.

---

## Detailed Usage Steps

### Adding an Infoblox IPAM Integration

1. Go to **Infrastructure > Networks > IP Pools > Add**.
2. Select **Infoblox** as the pool server type.
3. Enter the **API Url** (e.g. `https://infoblox.example.com`), **Username**, and **Password** (or select a stored credential).
4. Optionally configure **Throttle Rate**, **Disable SSL SNI Verification**, and **Inventory Existing**.
5. Save. Morpheus connects to Infoblox and syncs available network pools.

### Allocating an IP Address

When provisioning an instance on a network backed by an Infoblox pool, Morpheus automatically calls the WAPI to reserve the next available IP. DNS records are created if DNS is configured on the network.

### Releasing an IP Address

When an instance is decommissioned, Morpheus calls the Infoblox WAPI to release the IP and delete the associated DNS records.

---

## API Endpoints

This plugin communicates with the **Infoblox WAPI** at the configured service URL. Authentication uses HTTP Basic credentials. All calls use HTTP or HTTPS as configured.

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `{serviceUrl}/wapi/v2.1/network` | GET | List networks/pools |
| `{serviceUrl}/wapi/v2.1/fixedaddress` | GET | List IP allocations |
| `{serviceUrl}/wapi/v2.1/fixedaddress` | POST | Allocate an IP address |
| `{serviceUrl}/wapi/v2.1/fixedaddress/{ref}` | DELETE | Release an IP address |
| `{serviceUrl}/wapi/v2.1/record:a` | POST | Create an A record |
| `{serviceUrl}/wapi/v2.1/record:aaaa` | POST | Create an AAAA record |
| `{serviceUrl}/wapi/v2.1/record:cname` | POST | Create a CNAME record |
| `{serviceUrl}/wapi/v2.1/record:txt` | POST | Create a TXT record |
| `{serviceUrl}/wapi/v2.1/record:mx` | POST | Create an MX record |
| `{serviceUrl}/wapi/v2.1/{recordRef}` | DELETE | Delete a DNS record |
