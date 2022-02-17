cert-manager-kubernetes
=======================

This Ansible role installs [cert-manager](https://cert-manager.io/) on a Kubernetes cluster. Behind the doors it uses the official [Helm chart](https://charts.jetstack.io). Currently procedures like installing, upgrading and deleting the cert-manager deployment are supported.

Versions
--------

I tag every release and try to stay with [semantic versioning](http://semver.org). If you want to use the role I recommend to checkout the latest tag. The master branch is basically development while the tags mark stable releases. But in general I try to keep master in good shape too. A tag `6.0.0+1.7.1` means this is release `6.0.0` of this role and it contains cert-manager chart version `1.7.1`. If the role itself changes `X.Y.Z` before `+` will increase. If the cert-manager chart version changes `X.Y.Z` after `+` will increase too. This allows to tag bugfixes and new major versions of the role while it's still developed for a specific cert-manager release.

Requirements
------------

You need to have [Helm 3](https://helm.sh/) binary installed on that host where `ansible-playbook` runs. You can either try to use your favorite package manager if your distribution includes `helm` in its repository or use one of the Ansible `Helm` roles (e.g. https://galaxy.ansible.com/gantsign/helm) or directly download the binary from https://github.com/helm/helm/releases and put it into `/usr/local/bin/` directory e.g. For Archlinux Helm can be installed via `sudo pacman -S helm` e.g.

And of course you need a Kubernetes Cluster ;-)

Changelog
---------

see [CHANGELOG](https://github.com/githubixx/ansible-role-cert-manager-kubernetes/blob/master/CHANGELOG.md)

Role Variables
--------------

```yaml
# Helm chart version
cert_manager_chart_version: "v1.7.1"

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
# https://artifacthub.io/packages/helm/cert-manager/cert-manager#configuration
# But for most users "installCRDs=true" should be sufficient.
# If true, CRD resources will be installed as part of the Helm chart.
# If enabled, when uninstalling CRD resources will be deleted causing all
# installed custom resources to be DELETED.
cert_manager_values:
  - installCRDs=true
  - global.leaderElection.namespace="{{ cert_manager_namespace }}"

# If your Kubernetes control plane (`kube-apiserver` e.g.) has no route to the
# Pods and Services you can configure the "cert-manager-webhook" container to
# also listen on the host network which the controller and worker nodes share.
# So in the example above the cert-manager webhook service will listen on
# port "30001" in the pod network space and the host network. And the
# "webhook.url.host" option specifies that the control plane should connect
# to the webhook by connecting to "worker:30001" (of course you need to replace
# "worker" with a real hostname e.g.). And to make sure that the webhook
# TLS certificate has the correct DNS names `--dynamic-serving-dns-names`
# option is specified too. It can be a list of DNS names acutally. So you can
# also add fully qualified domain names (FQDN) if you want of course.
# (see also: https://github.com/jetstack/cert-manager/issues/2602)
#  - webhook.securePort="30001"
#  - webhook.hostNetwork=true
#  - webhook.url.host="worker:30001"
#  - webhook.extraArgs="{--dynamic-serving-dns-names=worker}"

# To install "ClusterIssuer" for Let's Encrypt (LE) "cert_manager_le_clusterissuer_options"
# needs to be defined. The variable contains a list of hashes and can be defined
# in "group_vars/all.yml" e.g.
#
# name:   Defines the name of the "ClusterIssuer"
# email:  Use a valid e-mail address to be alerted by LE in case a certificate
#         expires
# server: Hostname part of the LE URL
# private_key_secret_ref_name:  Name of the secret which stores the private key
# solvers_http01_ingress_class: Value of "kubernetes.io/ingress.class" annotation.
#                               Depends on your ingress controller. Common values
#                               are "traefik" for Traefik or "nginx" for nginx.
#
# Besides "email" the following values can be used as is and will create valid
# "ClusterIssuer" for Let's Encrypt staging and production. Only "email" needs
# to be adjusted if Traefik is used as ingress controller. For other ingress
# controllers "solvers_http01_ingress_class" needs to be adjusted too. Currently
# only "ClusterIssuer" and "http01" solver is implemented. For definition also
# see "tasks/install-issuer.yml".
#
# cert_manager_le_clusterissuer_options:
#   - name: letsencrypt-prod
#     email: insert@your-e-mail-address.here
#     server: acme-v02
#     private_key_secret_ref_name: letsencrypt-account-key
#     solvers_http01_ingress_class: "traefik"
#   - name: letsencrypt-staging
#     email: insert@your-e-mail-address.here
#     server: acme-staging-v02
#     private_key_secret_ref_name: letsencrypt-staging-account-key
#     solvers_http01_ingress_class: "traefik"
```

Usage
-----

First check if you want to change any of the default values in `default/main.yml`. As usual those values can be overridden in `host_vars` or `group_vars`. Normally there is no need to change that much. Besides the `cert_manager_chart_version` you might want do add a few options to `cert_manager_values`. It contains the configurable parameters of the cert-manager Helm chart. The list is submitted "as is" to `helm` binary for `template`, `install` or `upgrade` commands.

The default action is to just render the Kubernetes resources YAML file after replacing all Jinja2 variables and stuff like that (that means not specifying any value via `--extra-vars action=...` to `ansible-playbook`). In the `Example Playbook` section below there is an `Example 2 (assign tag to role)`. The role `githubixx.cert_manager_kubernetes` has a tag `role-cert-manager-kubernetes` assigned.

So to render the YAML files the WOULD be applied (nothing will be installed at this time) and the playbook is called `k8s.yml` execute the following command:

```bash
ansible-playbook --tags=role-cert-manager-kubernetes k8s.yml
```

One of the final tasks is called `TASK [githubixx.cert-manager-kubernetes : Output rendered template]`. This allows to check the YAML file before cert-manager gets deployed. Probably the output isn't that "pretty" in your case. To get around this you can either set `ANSIBLE_STDOUT_CALLBACK=debug` environment variable or `stdout_callback = debug` in `ansible.cfg`. If you run the `ansible-playbook` command again now, the YAML output should now look way better.

If the rendered output contains everything you need, the role can be installed which finally deploys cert-manager (still assuming the playbook file is called `k8s.yml` - if not please adjust accordingly):

```bash
ansible-playbook --tags=role-cert-manager-kubernetes --extra-vars action=install k8s.yml
```

To check if everything was deployed use the usual `kubectl` commands like `kubectl -n <cert_manager_namespace> get pods -o wide`. Before the playbook finishes it waits for the first `cert-manager-webhooks` pod to become ready.

Sooner or later there will be a new cert-manager version and you want to upgrade. Before doing the upgrade read the cert-manager [upgrade guide](https://cert-manager.io/docs/installation/upgrading/) carefully! If everything is in place you basically only need to change `cert_manager_chart_version` variable e.g. from `v1.1.0` to `v1.2.0` to upgrade from `v1.1.0` to `v1.2.0`. So to do the update run

```bash
ansible-playbook --tags=role-cert-manager-kubernetes --extra-vars action=upgrade k8s.yml
```

And finally if you want to get rid of cert-manager you can delete all resources (this of course deletes EVERYTHING cert-manager related and this might also include certificates and secrets cert-manager already created - so be careful!):

```bash
ansible-playbook --tags=role-cert-manager-kubernetes --extra-vars action=delete k8s.yml
```

Issuer
------

The role currently supports deploying a [ClusterIssuer](https://cert-manager.io/docs/concepts/issuer/) for [Let's Encrypt](https://letsencrypt.org/) (LE) for LE staging and production. The most relevant variable in this case is `cert_manager_le_clusterissuer_options`. Please see the role variables above for more information.

After `cert_manager_le_clusterissuer_options` variable is adjusted accordingly the `ClusterIssuer` can be installed:

```bash
ansible-playbook --tags=role-cert-manager-kubernetes --extra-vars action=install-issuer k8s.yml
```

After deploying the issuer the first time it takes a little bit until they are ready. To figure out if they are ready `kubectl` can be used:

```bash
kubectl get clusterissuers.cert-manager.io

NAME                  READY   AGE
letsencrypt-prod      True    10m
letsencrypt-staging   True    11m
```

Afterwards a certificate can be issued. This happens outside of this Ansible role. E.g. to get a certificate for domain `www.domain.name` from Let's Encrypt staging server (this one is only for testing and doesn't issue a valid certificate that browser will accept) create a YAML file (e.g. domain-name.yaml) like this:

```yaml
---
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: cert-name
  namespace: namespace-name
spec:
  commonName: www.domain.name
  secretName: secret-name
  dnsNames:
    - www.domain.name
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

After changing the values to your needs, apply this file with `kubectl apply -f domain-name.yaml`.

If you request a `(Cluster)Issuer` or a `Certificate` you can watch `cert-manager` logs to see what's going on e.g. (of course replace `cert-manager-76d899dd6c-8q8b` with the name of your `cert-manager` pod and in case you use a different namespace for `cert-manager` also change the namespace accordingly):

```bash
kubectl -n cert-manager logs -f --tail=50 cert-manager-76d899dd6c-8q8b
```

To get information about a `Certificate` this command can be used:

```bash
kubectl -n your-namespace get certificate cert-name -o yaml
```

And in case you want to delete the `ClusterIsser` use:

```bash
ansible-playbook --tags=role-cert-manager-kubernetes --extra-vars action=delete-issuer k8s.yml
```

TODO
----

- [ ] add option to install cert-manager plugin for `kubectl`
- [ } add more error checks

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

