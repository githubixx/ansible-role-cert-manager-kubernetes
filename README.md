cert-manager-kubernetes
=======================

This Ansible role installs [cert-manager](https://cert-manager.io/) on a Kubernetes cluster. Behind the doors it uses the official [Helm chart](https://charts.jetstack.io). Currently procedures like installing, upgrading and deleting the cert-manager deployment are supported.

Versions
--------

I tag every release and try to stay with [semantic versioning](http://semver.org). If you want to use the role I recommend to checkout the latest tag. The master branch is basically development while the tags mark stable releases. But in general I try to keep master in good shape too. A tag `1.0.0+1.1.0` means this is release `1.0.0` of this role and it contains cert-manager chart version `1.1.0`. If the role itself changes `X.Y.Z` before `+` will increase. If the cert-manager chart version changes `X.Y.Z` after `+` will increase too. This allows to tag bugfixes and new major versions of the role while it's still developed for a specific cert-manager release.

Requirements
------------

You need to have [Helm 3](https://helm.sh/) binary installed on that host where `ansible-playbook` runs. You can either try to use your favorite package manager if your distribution includes `helm` in its repository or use one of the Ansible `Helm` roles (e.g. https://galaxy.ansible.com/gantsign/helm) or directly download the binary from https://github.com/helm/helm/releases and put it into `/usr/local/bin/` directory e.g. For Archlinux Helm can be installed via `sudo pacman -S helm` e.g.

And of course you need a Kubernetes Cluster ;-)

Role Variables
--------------

```yaml
# Helm chart version
cert_manager_chart_version: "v1.1.0"

# Helm release name
cert_manager_release_name: "cert-manager"

# Helm repository name
cert_manager_repo_name: "jetstack"

# Helm chart name
cert_manager_chart_name: "{{ cert_manager_repo_name }}/{{ cert_manager_release_name }}"

# Helm chart URL
cert_manager_chart_url: "https://charts.jetstack.io"

# Kubernetes namespace where cert-manager resources should be installed
cert_manager_namespace: "cert-manager"

# The following list contains the configurable parameters of the cert-manager
# Helm chart. For all possible values see:
# https://artifacthub.io/packages/helm/jetstack/cert-manager#configuration
# But for most users "installCRDs=true" should be sufficient.
# If true, CRD resources will be installed as part of the Helm chart.
# If enabled, when uninstalling CRD resources will be deleted causing all
# installed custom resources to be DELETED.
cert_manager_values:
  - installCRDs=true
```

Usage
-----

First check if you want to change any of the default values in `default/main.yml`. As usual those values can be overridden in `host_vars` or `group_vars`. Normally there is no need to change that much. Besides the `cert_manager_chart_version` you might want do add a few options to `cert_manager_values`. It contains the configurable parameters of the cert-manager Helm chart. The list is submitted "as is" to `helm` binary for `template`, `install` or `upgrade` commands.

The default action is to just render the Kubernetes resources YAML file after replacing all Jinja2 variables and stuff like that (that means not specifying any value via `--extra-vars cm_action=...` to `ansible-playbook`). In the `Example Playbook` section below there is an `Example 2 (assign tag to role)`. The role `githubixx.cert_manager_kubernetes` has a tag `role-cert-manager-kubernetes` assigned.

So to render the YAML files the WOULD be applied (nothing will be installed at this time) and the playbook is called `k8s.yml` execute the following command:

```bash
ansible-playbook --tags=role-cert-manager-kubernetes k8s.yml
```

One of the final tasks is called `TASK [githubixx.cert-manager-kubernetes : Output rendered template]`. This allows to check the YAML file before cert-manager gets deployed. Probably the output isn't that "pretty" in your case. To get around this you can either set `ANSIBLE_STDOUT_CALLBACK=debug` environment variable or `stdout_callback = debug` in `ansible.cfg`. If you run the `ansible-playbook` command again now, the YAML output should now look way better.

If the rendered output contains everything you need, the role can be installed which finally deploys cert-manager (still assuming the playbook file is called `k8s.yml` - if not please adjust accordingly):

```bash
ansible-playbook --tags=role-cert-manager-kubernetes --extra-vars cm_action=install k8s.yml
```

To check if everything was deployed use the usual `kubectl` commands like `kubectl -n <cert_manager_namespace> get pods -o wide`. Before the playbook finishes it waits for the first `cert-manager-webhooks` pod to become ready.

Sooner or later there will be a new cert-manager version and you want to upgrade. Before doing the upgrade read the cert-manager [upgrade guide](https://cert-manager.io/docs/installation/upgrading/) carefully! If everything is in place you basically only need to change `cert_manager_chart_version` variable e.g. from `v1.1.0` to `v1.2.0` to upgrade from `v1.1.0` to `v1.2.0`. So to do the update run

```bash
ansible-playbook --tags=role-cert-manager-kubernetes --extra-vars cm_action=upgrade k8s.yml
```

And finally if you want to get rid of cert-manager you can delete all resources (this of course deletes EVERYTHING cert-manager related and this might also include certificates and secrets cert-manager already created - so be careful!):

```bash
ansible-playbook --tags=role-cert-manager-kubernetes --extra-vars cm_action=delete k8s.yml
```

TODO
----

- add possibility to manage cert-manager resources like `(Cluster)Issuer` and `Certificate` esp. for ACME (Let's Encrypt)
- add option to install cert-manager plugin for `kubectl`
- add more error checks

Example Playbook
----------------

Example 1 (without role tag):

```yaml
- hosts: cert_manager
  roles:
    - githubixx.cert_manager_kubernetes
```

Example 2 (assign tag to role):

```yaml
-
  hosts: cert_manager
  roles:
    - role: githubixx.cert_manager_kubernetes
      tags: role-cert-manager-kubernetes
```

License
-------

GNU GENERAL PUBLIC LICENSE Version 3

Author Information
------------------

[http://www.tauceti.blog](http://www.tauceti.blog)

