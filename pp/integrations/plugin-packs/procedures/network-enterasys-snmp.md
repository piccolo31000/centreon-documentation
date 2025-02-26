---
id: network-enterasys-snmp
title: Enterasys SNMP
---
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

## Pack Assets

### Templates

The Centreon Pack **Enterasys SNMP** brings a host template:

* Net-Enterasys-SNMP-custom

It brings the following service templates:

| Service Alias | Service Template              | Service Description                                  | Default | Discovery |
|:--------------|:------------------------------|:-----------------------------------------------------|:--------|:----------|
| Cpu           | Net-Enterasys-Cpu-SNMP        | Check the rate of utilization of CPU for the machine | X       |           |
| Interfaces    | Net-Enterasys-Interfaces-SNMP | Check interfaces                                     |         | X         |
| Memory        | Net-Enterasys-Memory-SNMP     | Check memory usage                                   | X       |           |
| Storage       | Net-Enterasys-Storage-SNMP    | Check storage usage                                  | X       |           |
| Uptime        | Net-Enterasys-Uptime-SNMP     | Time since the server has been working and available | X       |           |

### Discovery rules

<Tabs groupId="sync">
<TabItem value="Services" label="Services">

| Rule Name                         | Description                                                   |
|:----------------------------------|:--------------------------------------------------------------|
| Net-Enterasys-SNMP-Interface-Name | Discover network interfaces and monitor bandwidth utilization |

More information about discovering services automatically is available on the [dedicated page](/docs/monitoring/discovery/services-discovery)
and in the [following chapter](/docs/monitoring/discovery/services-discovery/#discovery-rules).

</TabItem>
</Tabs>

### Collected metrics & status

<Tabs groupId="sync">
<TabItem value="Cpu" label="Cpu">

| Metric Name                                               | Unit  |
|:----------------------------------------------------------|:------|
| cpu.utilization.1m.percentage                             | %     |
| cpu.utilization.5m.percentage                             | %     |
| cpu.utilization.5s.percentage                             | %     |
| *entity_name~cpu_core*#core.cpu.utilization.1m.percentage | %     |
| *entity_name~cpu_core*#core.cpu.utilization.5m.percentage | %     |
| *entity_name~cpu_core*#core.cpu.utilization.5s.percentage | %     |

</TabItem>
<TabItem value="Interfaces" label="Interfaces">

| Metric Name                                               | Unit  |
|:----------------------------------------------------------|:------|
| status                                                    |       |
| *interface_name*#interface.traffic.in.bitspersecond       | b/s   |
| *interface_name*#interface.traffic.out.bitspersecond      | b/s   |
| *interface_name*#interface.packets.in.error.percentage    | %     |
| *interface_name*#interface.packets.in.discard.percentage  | %     |
| *interface_name*#interface.packets.out.error.percentage   | %     |
| *interface_name*#interface.packets.out.discard.percentage | %     |

</TabItem>
<TabItem value="Memory" label="Memory">

| Metric Name                                       | Unit  |
|:--------------------------------------------------|:------|
| *entity_name~memory_name*#memory.usage.bytes      | B     |
| *entity_name~memory_name*#memory.free.bytes       | B     |
| *entity_name~memory_name*#memory.usage.percentage | %     |

</TabItem>
<TabItem value="Storage" label="Storage">

| Metric Name                                         | Unit  |
|:----------------------------------------------------|:------|
| *entity_name~storage_name*#storage.usage.bytes      | B     |
| *entity_name~storage_name*#storage.free.bytes       | B     |
| *entity_name~storage_name*#storage.usage.percentage | %     |

</TabItem>
<TabItem value="Uptime" label="Uptime">

| Metric Name           | Unit  |
|:----------------------|:------|
| system.uptime.seconds | s     |

</TabItem>
</Tabs>

## Prerequisites

### SNMP Configuration

To use this pack, the SNMP service must be properly configured on your **Enterasys**
server.

### Network flow

The target server must be reachable from the Centreon poller on the UDP/161
SNMP port.

## Setup

### Monitoring Pack

If the platform uses an *online* license, you can skip the package installation
instruction below as it is not required to have the pack displayed within the
**Configuration > Monitoring Connector Manager** menu.
If the platform uses an *offline* license, install the package on the **central server**
with the command corresponding to the operating system's package manager:

<Tabs groupId="sync">
<TabItem value="Alma / RHEL / Oracle Linux 8" label="Alma / RHEL / Oracle Linux 8">

```bash
dnf install centreon-pack-network-enterasys-snmp
```

</TabItem>
<TabItem value="CentOS 7" label="CentOS 7">

```bash
yum install centreon-pack-network-enterasys-snmp
```

</TabItem>
<TabItem value="Debian 11" label="Debian 11">

```bash
apt install centreon-pack-network-enterasys-snmp
```

</TabItem>
</Tabs>

Whatever the license type (*online* or *offline*), install the **Enterasys SNMP** Pack through
the **Configuration > Monitoring Connector Manager** menu.

### Plugin

Since Centreon 22.04, you can benefit from the 'Automatic plugin installation' feature.
When this feature is enabled, you can skip the installation part below.

You still have to manually install the plugin on the poller(s) when:
- Automatic plugin installation is turned off
- You want to run a discovery job from a poller that doesn't monitor any resource of this kind yet

> More information in the [Installing the plugin](/docs/monitoring/pluginpacks/#installing-the-plugin) section.

Use the commands below according to your operating system's package manager:

<Tabs groupId="sync">
<TabItem value="Alma / RHEL / Oracle Linux 8" label="Alma / RHEL / Oracle Linux 8">

```bash
dnf install centreon-plugin-Network-Enterasys-Snmp
```

</TabItem>
<TabItem value="CentOS 7" label="CentOS 7">

```bash
yum install centreon-plugin-Network-Enterasys-Snmp
```

</TabItem>
<TabItem value="Debian 11" label="Debian 11">

```bash
apt install centreon-plugin-network-enterasys-snmp
```

</TabItem>
</Tabs>

## Configuration

### Host

* Log into Centreon and add a new host through **Configuration > Hosts**.
* Fill the **Name**, **Alias** & **IP Address/DNS** fields according to your **Enterasys** server settings.
* Apply the **Net-Enterasys-SNMP-custom** template to the host.

If you are using SNMP Version 3, use the **SNMPEXTRAOPTIONS** macro to configure
> When using SNMP v3, use the SNMPEXTRAOPTIONS Macro to add specific authentication parameters.
> More information in the [Troubleshooting SNMP](../getting-started/how-to-guides/troubleshooting-plugins.md#snmpv3-options-mapping) section.

| Mandatory   | Macro            | Description                                  |
|:------------|:-----------------|:---------------------------------------------|
|             | SNMPEXTRAOPTIONS | Configure your own SNMPv3 credentials combo  |

## How to check in the CLI that the configuration is OK and what are the main options for?

Once the plugin is installed, log into your Centreon poller's CLI using the
**centreon-engine** user account (`su - centreon-engine`) and test the plugin by
running the following command:

```bash
/usr/lib/centreon/plugins//centreon_enterasys_snmp.pl \
    --plugin=network::enterasys::snmp::plugin \
    --mode=memory \
    --hostname='10.0.0.1' \
    --snmp-version='2c' \
    --snmp-community='my-snmp-community' \
    --verbose
```

The expected command output is shown below:

```bash
OK: Memory 'RAM device' [unit-2] total: 256.00 MB used: 232.65 MB (90.88%) free: 23.35 MB (9.12%) | 'unit-2~RAM device#memory.usage.bytes'=243950592B;;;0;268431360 'unit-2~RAM device#memory.free.bytes'=24480768B;;;0;268431360 'unit-2~RAM device#memory.usage.percentage'=90.88%;;;0;100
Memory 'RAM device' [unit-2] total: 256.00 MB used: 232.65 MB (90.88%) free: 23.35 MB (9.12%)
```

All available options for a given mode can be displayed by adding the
`--help` parameter to the command:

```bash
/usr/lib/centreon/plugins//centreon_enterasys_snmp.pl \
    --plugin=network::enterasys::snmp::plugin \
    --mode=memory \
    --help
```

All available modes can be displayed by adding the `--list-mode` parameter to
the command:

```bash
/usr/lib/centreon/plugins//centreon_enterasys_snmp.pl \
    --plugin=network::enterasys::snmp::plugin \
    --list-mode
```

### Troubleshooting

Please find the [troubleshooting documentation](../getting-started/how-to-guides/troubleshooting-plugins.md)
for Centreon Plugins typical issues.
