# chrony AutoShift Policy

## Overview

Sets the time sources of a cluster's nodes by writing `/etc/chrony.conf` through one
`MachineConfig` per MachineConfigPool. It installs no operator.

## Enable

```yaml
labels:
  chrony: 'true'
config:
  chrony:
    servers:
      - 192.0.2.1
      - 192.0.2.2
```

| Key | Type | Default | Description |
|---|---|---|---|
| `servers` | list | | NTP servers, each written as `server <address> iburst` |
| `pools` | list | | NTP pools, each written as `pool <name> iburst` |
| `ptpKvm` | bool | `false` | Also sync to the hypervisor clock: loads the `ptp_kvm` module and adds `/dev/ptp0` as a reference clock. For virtual machines on KVM |
| `roles` | list | `[master, worker]` | MachineConfigPools that receive the file. Add custom pools here |

At least one of `servers`, `pools` or `ptpKvm` is required; the policy fails rather than write a
configuration with no source.

## Effect on nodes

The objects are named `50-<role>-chrony`. Creating or changing a `MachineConfig` makes the Machine
Config Operator roll every node in that pool, one at a time. Enable the label when a rolling
restart is acceptable, and watch `oc get mcp`.

A cluster whose installer already wrote a chrony `MachineConfig` keeps it: both write the same
file, and the Machine Config Operator applies them in name order, so the one sorting last wins.
Give the two the same content or remove the installer's object.
