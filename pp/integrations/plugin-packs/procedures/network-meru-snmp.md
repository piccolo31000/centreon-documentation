---
id: network-meru-snmp
title: Meru SNMP
---
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';


## Monitoring Connector Assets

### Monitored Objects

The Monitoring Connector Meru SNMP collects metrics for:
* Alarms
* Cpu
* Interfaces
* Memory
* Wireless

### Collected Metrics

<Tabs groupId="sync">
<TabItem value="Alarms" label="Alarms">

| Metric name           | Description               | Unit |
| :-------------------- | :------------------------ | :--- |
| alarms.critical.count | Number of critical alarms |      |
| alarms.major.count    | Number of major alarms    |      |
| alarms.minor.count    | Number of minor alarms    |      |

</TabItem>
<TabItem value="Cpu" label="Cpu">

| Metric name                              | Description              | Unit |
| :--------------------------------------- | :----------------------- | :--- |
| cpu.utilization.percentage               | CPU utilization          | %    |

</TabItem>
<TabItem value="Interfaces" label="Interfaces">

| Metric name                                            | Description                                         | Unit |
| :----------------------------------------------------- | :-------------------------------------------------- | :--- |
| status                                                 | Status of the interface                             |      |
| *interface\_name*\#interface.traffic.in.bitspersecond  | Incoming traffic going through the interface.       | b/s  |
| *interface\_name*\#interface.traffic.in.percentage     | Percentage of the interface's *in* bandwidth usage  | %    |
| *interface\_name*\#interface.traffic.out.bitspersecond | Outgoing traffic going through the interface.       | b/s  |
| *interface\_name*\#interface.traffic.out.percentage    | Percentage of the interface's *out* bandwidth usage | %    |

A regexp filter is available to target a specific interface identifier - ifName [```--interface='^eth0$' --name```]

</TabItem>
<TabItem value="Memory" label="Memory">

| Metric name             | Description                               | Unit  |
| :---------------------  | :---------------------------------------- | :---- |
| memory.usage.bytes      | Memory usage                              | B     |
| memory.free.bytes       | Free memory                               | B     |
| memory.usage.percentage | Memory usage in percentage                | %     |

</TabItem>
<TabItem value="Wireless" label="Wireless">

| Metric name                 | Description                          | Unit  |
| :-------------------------- | :----------------------------------- | :---- |
| accesspoints.online.count   | Number of online access points       |       |
| stations.wireless.count     | Number of wireless stations          |       |

</TabItem>
</Tabs>

## Prerequisites

To control your Meru, the SNMP must be configured.

## Setup

<Tabs groupId="sync">
<TabItem value="Online License" label="Online License">

1. Install the Centreon Plugin on every Poller:

```bash
yum install centreon-plugin-Network-Meru-Snmp
```

2. On the Centreon Web interface in **Configuration > Monitoring Connector Manager**, install the *Meru Networks SNMP* Monitoring Connector

</TabItem>
<TabItem value="Offline License" label="Offline License">

1. Install the Centreon Plugin on every Poller:

```bash
yum install centreon-plugin-Network-Meru-Snmp
```

2. On the Centreon Central server, install the Centreon Monitoring Connector from the RPM:

```bash
yum install centreon-pack-network-meru-snmp
```

3. On the Centreon Web interface in **Configuration > Monitoring Connector Manager**, install the *Meru Networks SNMP* Monitoring Connector

</TabItem>
</Tabs>

## Host configuration

* Add a new Host and fill the *IP Address/FQDN*, *SNMP Version* and *SNMP Community* fields according to the device's configuration
* Apply the *Net-Meru-SNMP-Custom* Host Template

> When using SNMP v3, use the SNMPEXTRAOPTIONS Macro to add specific authentication parameters 
> More information in the [Troubleshooting SNMP](../getting-started/how-to-guides/troubleshooting-plugins.md#snmpv3-options-mapping) section.

| Mandatory | Name             | Description                                    |
| :-------- | :--------------- | :--------------------------------------------- |
|           | SNMPEXTRAOPTIONS | Configure your own SNMPv3 credentials combo    |

## FAQ

### How to test the Plugin and what are the main options for?

Once the plugin installed, log into your Centreon Poller CLI using the *centreon-engine* user account
and test the Plugin by running the following command:

```bash
/usr/lib/centreon/plugins/centreon_meru_snmp.pl \
    --plugin=network::meru::snmp::plugin \
    --mode=wireless \
    --hostname=10.30.2.114 \
    --snmp-version='2c' \
    --snmp-community='meru_ro' \
    --critical-accesspoints-online='@19:19' \
    --verbose
```

Expected command output is shown below:

```bash
OK: number of online access points: 19 - number of wireless stations: 3 | 'accesspoints.online.count'=19;;@19:19;0; 'stations.wireless.count'=3;;;0;
```

The command above monitors Meru device (```--plugin=network::meru::snmp::plugin --mode=wireless```) identified
by the IP address *10.30.2.114* (```--hostname=10.30.2.114```). As the Plugin is using the SNMP protocol to request the device, the related
*community* and *version* are specified (```--snmp-version='2c' --snmp-community='meru_ro'```).

This command would trigger a CRITICAL alarm if the number of online access points is above or below 19 
(```--critical-accesspoints-online='@19:19'```).

All the options as well as all the available thresholds can be displayed by adding the  ```--help```
parameter to the command:

```bash
/usr/lib/centreon/plugins/centreon_meru_snmp.pl \
    --plugin=network::meru::snmp::plugin \
    --mode=wireless \
    --help
```

## Troubleshooting

### UNKNOWN: SNMP GET Request : Timeout

If you get this message, you're probably facing one of theses issues:
* The SNMP agent of the device isn't started or is misconfigured
* An external device is blocking the request (firewall, ...)

### UNKNOWN: SNMP GET Request : Cant get a single value.

This error message often refers to the following issues: 
  - The agent doesn't support the MIB used by the plugin
  - The targeted SNMP OID cannot be fetched because of insufficient privileges on the device. 
    SNMP Agent must be capable of accessing to the enterprise branch: .1.3.6.1.4.1.15983
