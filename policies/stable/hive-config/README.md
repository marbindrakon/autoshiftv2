# hive-config AutoShift Policy

## Overview

Merges settings into the `HiveConfig` object named `hive` on a hub. multicluster engine creates
that object; this policy only adds the fields a site needs. It installs no operator and is placed
on hub clusters only.

## Enable

```yaml
hubClusterSets:
  hub:
    labels:
      hive-config: 'true'
    config:
      hiveConfig:
        additionalCertificateAuthoritiesSecretRef:
          - name: example-ca-bundle
        managedDomains:
          - domains:
              - clusters.example.com
            aws:
              credentialsSecretRef:
                name: example-route53-credentials
```

`config.hiveConfig` is the part of the `HiveConfig` spec to set. Fields are merged with
`musthave`, so what multicluster engine wrote is kept, and a list declared here must contain the
whole list you want.

## Secrets

Every credential or CA bundle the spec names is a Secret in the `hive` namespace, referenced by
name. AutoShift renders config into ConfigMaps on the hub, so no credential belongs in it. The
external-secrets-operator policy can deliver those Secrets.
