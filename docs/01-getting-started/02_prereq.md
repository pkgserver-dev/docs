# Prerequisites

## kubectl

Install [kubectl][kubectl]

## Auto completions for kubectl (optional)

/// tab | bash

```
source <(kubectl completion bash)
alias k=kubectl
complete -o default -F __start_kubectl k
```
///

/// tab | zsh
```
source <(kubectl completion zsh)
alias k=kubectl
complete -F _start_kubectl k
```
///

## pkgctl (optional)

pkgctl is a single binary built for linux and Mac OS, distributed via [ghreleases][ghreleases] specialized to interface with the pkgserver.

/// tab | linux/Mac OS

To download & install the latest release the following automated [installation script][installscript] can be used.

```bash
bash -c "$(curl -sL https://github.com/pkgserver-dev/pkgctl/raw/main/install.sh)"
```

As a result, the latest `pkgctl` version will be installed in the /usr/local/bin directory and the version information will be printed out.


To install a specific version of `pkgctl`, provide the version with -v flag to the installation script:

```bash
bash -c "$(curl -sL https://github.com/pkgserver-dev/pkgctl/raw/main/install.sh)" -- -v 0.2.0
```

///

/// tab | Packages

Linux users running distributions with support for deb/rpm packages can install gnmic using pre-built packages:

```bash
bash -c "$(curl -sL https://github.com/pkgserver-dev/pkgctl/raw/main/install.sh)" -- --use-pkg
```

///

## Install Kubernetes (optional)

pkgserver does not have dependencies on a particular k8s distribution. You can install any of the distributions you like. Below we listed some options. 

/// tab | kind

First install kind using [kind][kind-install]

In this example we install a [`kind`][kind] cluster with name `sdc`. 

```
kind create cluster --name sdc
```
///

/// tab | other
///

[kind-install]: https://kind.sigs.k8s.io/docs/user/quick-start/#installation
[kind]: https://kind.sigs.k8s.io/
[kubectl]: https://kubernetes.io/docs/tasks/tools/
[ghreleases]: https://github.com/pkgserver-dev/pkgctl/releases
[installscript]: https://github.com/pkgserver-dev/pkgctl/blob/main/install.sh