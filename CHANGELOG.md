Changelog
---------

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
