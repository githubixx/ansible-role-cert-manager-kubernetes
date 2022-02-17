Changelog
---------

**6.0.0+1.7.1**

- update cert-manager to `v1.7.1` (**NOTE**: Please also read [cert-manager release notes for v1.7.0](https://github.com/cert-manager/cert-manager/releases/tag/v1.7.0) as this release removes deprecated APIs e.g. cert-manager API versions `v1alpha2`, `v1alpha3`, and `v1beta1`. If you use `v1` already then the upgrade shouldn't be much of a problem.)

**5.0.1+1.6.1**

- make ansible-lint happy
- remove unneeded directories

**5.0.0+1.6.1**

- update cert-manager to `v1.6.1`

**4.0.1+1.5.3**

- fix README + add link to [Internal error occurred: failed calling webhook "webhook.cert-manager.io": Post https://cert-manager-webhook.cert-manager.svc:443/mutate?timeout=30s: net/http: TLS handshake timeout](https://github.com/jetstack/cert-manager/issues/2602) issue

**4.0.0+1.5.3**

- remove `startupapicheck.enabled=false` from `cert_manager_values` (should enabled by default and only disabled in case of problems)
- some documentation updates

**3.0.0+1.5.3**

- update cert-manager to `v1.5.3`
- add label `cert-manager.io/disable-validation: "true"` to cert-manager namespace
- add two new options to `cert_manager_values`: `global.leaderElection.namespace="{{ cert_manager_namespace }}"` and `startupapicheck.enabled=false`

**2.2.0+1.5.1**

- update cert-manager to `v1.5.1`

**2.1.0+1.3.1**

- update cert-manager to `v1.3.1`

**2.0.0+1.1.0**

- rename extra vars `cm_(install|upgrade|delete)=true` to `action=(install|upgrade|delete)`
- the role now supports to install "ClusterIssuer" for Let's Encrypt staging and production (see "cert_manager_le_clusterissuer_options" variable)

**1.0.0+1.1.0**

- initial commit for cert-manager `v1.1.0`
