# Dell iDRAC SNMP

Zabbix 7.4 template for Dell PowerEdge server hardware, read from the iDRAC
management controller over SNMP (IDRAC-MIB, OID base
`1.3.6.1.4.1.674.10892.5`). The iDRAC reports the physical health that the
operating system cannot see, so this template complements, rather than
replaces, OS level monitoring. The headline case is a degraded RAID array: a
physical disk fails, the server keeps running, and nothing in Windows notices
until the second disk goes. The iDRAC sees it immediately.

## What it monitors

| Area | Items |
|------|-------|
| Inventory | service tag, model, hostname, operating system |
| Global health | overall system status, storage/RAID status |
| Power supplies (per PSU) | status, failure trigger |
| Physical disks (per disk) | status, failure / degraded trigger |
| Temperatures (per sensor) | value (deg C), warning and critical thresholds |
| Fans (per fan) | status, low priority fault trigger |

All component groups are discovered with low level discovery from the iDRAC
name tables, so each disk, PSU, sensor and fan appears with its real label
(for example `System Board Inlet Temp`, `PS1 Status`) instead of a bare index.

## Dell status scale

Dell reports component health as an integer: **3 = OK, 4 = Warning,
5/6 = Critical**. Triggers map 4 to a warning and 5/6 to high severity.
Temperatures are returned in tenths of a degree and divided by 10 on ingest.

## Macros

| Macro | Default | Purpose |
|-------|---------|---------|
| `{$SNMP_COMMUNITY}` | `public` | SNMP community, override per host |
| `{$IDRAC_TEMP_WARN}` | 70 | Temperature warning (deg C) |
| `{$IDRAC_TEMP_CRIT}` | 85 | Temperature critical (deg C) |

## Design note

The template deliberately watches the global system and storage status plus the
individual power supplies, disks and temperatures (the components that are both
actionable and invisible to the OS), and keeps fan faults at low severity since
the global status already covers the critical cases. This keeps the alerts that
reach an operator meaningful instead of noisy.

## Usage

1. Enable the SNMP agent on the iDRAC (iDRAC Settings -> Services -> SNMP Agent).
2. Import `dell_idrac_snmp.xml` under Data collection -> Templates.
3. Create a host with an SNMP interface on the iDRAC address, set
   `{$SNMP_COMMUNITY}` and link the template.
