# etcd-hardware-speed AutoShift Policy

## Overview

Sets `controlPlaneHardwareSpeed` on the cluster `Etcd` operator object, which selects etcd's
heartbeat interval and leader election timeout. It installs no operator.

## Enable

```yaml
labels:
  etcd-hardware-speed: Slower
```

| Value | Effect |
|---|---|
| `Standard` | The default timing |
| `Slower` | Longer heartbeat and election timeouts, for control planes on network or shared storage |

A cluster without the label is not managed and keeps the platform default. Changing the value
rolls the etcd pods one at a time, so check `oc get co etcd` afterwards.

Use `Slower` when etcd logs repeated leader elections or `etcdserver: request timed out` while
the disks are healthy but shared: virtual machines on network block storage are the usual case.
