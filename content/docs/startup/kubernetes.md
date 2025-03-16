---
title: Kubernetes
weight: 1
prev: /docs/startup/
next: /docs/startup/fluxcd/
---

## 🚧 Under Construction

## Installing K3s

- Run K3s installation script with helm controller disabled, this because K3s comes with a default Helm controller but I want to use the FluxCD one

```shell
sudo su -
curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC=" --disable=helm-controller" sh
sudo systemctl status k3s.service
```

## Exporting Kubernetes configuration

```shell
cd
sudo cp /etc/rancher/k3s/k3s.yaml .
exit
```

On main system

```shell
scp <user>@<local_ip>:/home/<user>/k3s.yaml .
vim k3s.yaml # edit the IP
mkdir ~/.kube
mv k3s.yaml .kube/config
```
