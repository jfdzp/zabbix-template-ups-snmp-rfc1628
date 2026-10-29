# UPS SNMP (RFC 1628)

Zabbix 7.4 template for UPS devices built on the vendor neutral Standard
UPS-MIB (RFC 1628, OID base `1.3.6.1.2.1.33`). Because it uses the standard MIB
rather than a vendor private one, the same template works across APC, Eaton,
Riello, Online and most other SNMP capable UPS units.

## What it monitors

| Area | Items |
|------|-------|
| Identity | manufacturer, model |
| Battery | status, charge %, remaining runtime, temperature |
| Power state | output source (mains vs battery), time on battery |
| Input | voltage, frequency |
| Output | voltage, load %, output power |
| Availability | ICMP ping |

## Key triggers

- **Power failure**: output source switches to battery (the headline alert).
- **Low / depleted battery**: battery status reports low or depleted.
- **Battery charge** below warning and critical thresholds.
- **Runtime** below the configured number of minutes while on battery.
- **Load** and **battery temperature** above their thresholds.

## Macros

| Macro | Default | Purpose |
|-------|---------|---------|
| `{$SNMP_COMMUNITY}` | `public` | SNMP v2c community, override per host |
| `{$UPS_BATTERY_WARN}` / `{$UPS_BATTERY_CRIT}` | 50 / 20 | Battery charge thresholds (%) |
| `{$UPS_RUNTIME_WARN}` | 15 | Remaining runtime warning (minutes) |
| `{$UPS_LOAD_WARN}` | 80 | Load warning (%) |
| `{$UPS_TEMP_WARN}` | 40 | Battery temperature warning (deg C) |

## Compatibility note

Some three phase or higher end units do not implement every optional RFC 1628
object (battery temperature, input frequency and output load % are the usual
gaps). Those items will simply stay unsupported on such devices; the core
battery, runtime and power state monitoring still works.

## Usage

1. Import `ups_snmp_rfc1628.xml` under Data collection -> Templates.
2. Create a host with an SNMP v2c interface and set `{$SNMP_COMMUNITY}`.
3. Link the template.
