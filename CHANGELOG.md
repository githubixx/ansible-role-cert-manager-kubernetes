Changelog
---------

**2.1.0+1.3.1**

- update cert-manager to `v1.3.1`

**2.0.0+1.1.0**

- rename extra vars `cm_(install|upgrade|delete)=true` to `action=(install|upgrade|delete)`
- the role now supports to install "ClusterIssuer" for Let's Encrypt staging and production (see "cert_manager_le_clusterissuer_options" variable)

**1.0.0+1.1.0**

- initial commit for cert-manager `v1.1.0`
