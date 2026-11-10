# oauth-identity-providers AutoShift Policy

## Overview

Configures the identity providers of the cluster `OAuth` object and, optionally, binds identity
provider groups to `cluster-admin`. It installs no operator.

## Enable

```yaml
labels:
  oauth-identity-providers: 'true'
config:
  oauthIdentityProviders:
    identityProviders:
      - name: example-sso
        mappingMethod: claim
        type: OpenID
        openID:
          issuer: 'https://sso.example.com/realms/example'
          clientID: example-cluster
          clientSecret:
            name: example-sso-client-secret
          claims:
            preferredUsername: [preferred_username]
            name: [name]
            email: [email]
            groups: [groups]
    clusterAdminGroups:
      - example-cluster-admins
```

| Key | Type | Description |
|---|---|---|
| `identityProviders` | list | Entries of `spec.identityProviders` on the cluster `OAuth` object, written as declared. Any provider type OpenShift supports can be used |
| `clusterAdminGroups` | list | Group names bound to `cluster-admin`, one `ClusterRoleBinding` named `autoshift-cluster-admin-<group>` each |

## Secrets

A provider's client secret, bind password or CA bundle is a Secret or ConfigMap in
`openshift-config` that the entry references by name. AutoShift renders config into ConfigMaps on
the hub, so no credential belongs in it. Create the Secret yourself or have the
external-secrets-operator policy deliver it; until it exists the authentication operator reports
the provider as degraded and existing logins keep working.

## Relationship to remove-kubeadmin

Bind at least one group with `clusterAdminGroups`, and confirm a member of it can log in, before
enabling `remove-kubeadmin`.
