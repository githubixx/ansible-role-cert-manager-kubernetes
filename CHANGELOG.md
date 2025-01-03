# Changelog

## 12.0.0+1.16.2

- **Update**
  - update cert-manager to `v1.16.2`

## 11.0.0+1.15.3

**Note:** Please read the [cert-manager release notes](https://cert-manager.io/docs/releases/release-notes/release-notes-1.15/) for `v1.15` before upgrading!

- **Potentially breaking**
  - `installCRDs` Helm chart option is deprecated. Previously it was set to `true` by default. So the Custom Resource Definitions (CRDs) needed by `cert-manager` were installed automatically. This was replaced by `crds.enabled=true` which means that the previous default behavior haven't changed. Additionally `crds.keep=true` was added. If the `cert-manager` gets deleted by deleting the Helm chart e.g. then the CRDs will be kept. This is to prevent accidental data loss.

- **Update**
  - update cert-manager to `v1.15.3`

- **Other**
  - update `.yamllint`
  - add `.gitignore`

## 10.0.2+1.13.3

- replace all dashes with underscores in role name in Github workflow
- update `meta/main.yml`

## 10.0.1+1.13.3

Please read `cert-manager` `v1.13` [release notes](https://cert-manager.io/docs/releases/release-notes/release-notes-1.13/) and [changelog](https://github.com/cert-manager/cert-manager/releases/tag/v1.13.0) before upgrading!

**IMPORTANT NOTE**: If upgrading from a version below `v1.12`, upgrade to the latest `v1.12` release before upgrading to `v1.13`. Otherwise, some certificates may be unexpectedly re-issued.

- update cert-manager to `v1.13.3`
- adjust Github action because of Ansible Galaxy changes

## 10.0.0+1.13.2

Please read `cert-manager` `v1.13` [release notes](https://cert-manager.io/docs/releases/release-notes/release-notes-1.13/) and [changelog](https://github.com/cert-manager/cert-manager/releases/tag/v1.13.0) before upgrading!

**IMPORTANT NOTE**: If upgrading from a version below `v1.12`, upgrade to the latest `v1.12` release before upgrading to `v1.13`. Otherwise, some certificates may be unexpectedly re-issued.

- update cert-manager to `v1.13.2`

## 9.0.0+1.12.6

Please read `cert-manager` `v1.12` [release notes](https://cert-manager.io/docs/releases/release-notes/release-notes-1.12/) before upgrading!

- update cert-manager to `v1.12.6`

## 8.0.1+1.11.1

- update cert-manager to `v1.11.1`

## 8.0.0+1.11.0

- update cert-manager to `v1.11.0`
- add `.yamlint`
- Avoid using free-form when calling module actions (ansible.builtin.set_fact)

## 7.3.1+1.10.1

- update cert-manager to `v1.10.1`

## 7.3.0+1.10.0

- update cert-manager to `v1.10.0`

## 7.2.0+1.9.1

- add Github release action to push new release to Ansible Galaxy

## 7.1.0+1.9.1

- fix various `ansible-lint` issues

## 7.0.0+1.9.1

- update cert-manager to `v1.9.1`

## 6.0.0+1.7.1

- update cert-manager to `v1.7.1` (**NOTE**: Please also read [cert-manager release notes for v1.7.0](https://github.com/cert-manager/cert-manager/releases/tag/v1.7.0) as this release removes deprecated APIs e.g. cert-manager API versions `v1alpha2`, `v1alpha3`, and `v1beta1`. If you use `v1` already then the upgrade shouldn't be much of a problem.)

## 5.0.1+1.6.1

- make ansible-lint happy
- remove unneeded directories

## 5.0.0+1.6.1

- update cert-manager to `v1.6.1`

## 4.0.1+1.5.3

- fix README + add link to [Internal error occurred: failed calling webhook "webhook.cert-manager.io": Post https://cert-manager-webhook.cert-manager.svc:443/mutate?timeout=30s: net/http: TLS handshake timeout](https://github.com/jetstack/cert-manager/issues/2602) issue

## 4.0.0+1.5.3

- remove `startupapicheck.enabled=false` from `cert_manager_values` (should enabled by default and only disabled in case of problems)
- some documentation updates

## 3.0.0+1.5.3

- update cert-manager to `v1.5.3`
- add label `cert-manager.io/disable-validation: "true"` to cert-manager namespace
- add two new options to `cert_manager_values`: `global.leaderElection.namespace="{{ cert_manager_namespace }}"` and `startupapicheck.enabled=false`

## 2.2.0+1.5.1

- update cert-manager to `v1.5.1`

## 2.1.0+1.3.1

- update cert-manager to `v1.3.1`

## 2.0.0+1.1.0

- rename extra vars `cm_(install|upgrade|delete)=true` to `action=(install|upgrade|delete)`
- the role now supports to install "ClusterIssuer" for Let's Encrypt staging and production (see "cert_manager_le_clusterissuer_options" variable)

## 1.0.0+1.1.0

- initial commit for cert-manager `v1.1.0`
