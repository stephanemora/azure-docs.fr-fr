---
title: SSH dans les nœuds de cluster Azure Kubernetes Service (AKS)
description: Découvrez comment créer une connexion SSH avec des nœuds de cluster AKS (Azure Kubernetes Service) pour les tâches de maintenance et de dépannage.
services: container-service
ms.topic: article
ms.date: 05/17/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: 7ca318a21e4b3f764060757e1102e6e4665aec3e
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110467825"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Se connecter avec SSH à des nœuds de cluster AKS (Azure Kubernetes Service) pour effectuer des tâches de maintenance ou de dépannage

Tout au long du cycle de vie de votre cluster AKS (Azure Kubernetes Service ), vous pouvez être amené à accéder à un nœud AKS, que ce soit pour effectuer une tâche de maintenance, une collecte de journaux ou d’autres opérations de dépannage. Vous pouvez accéder aux nœuds AKS avec SSH, y compris aux nœuds Windows Server. Vous pouvez également [vous connecter aux nœuds Windows Server à l’aide de connexions RDP (Remote Desktop Protocol)][aks-windows-rdp]. Pour des raisons de sécurité, les nœuds AKS ne sont pas exposés à Internet. Pour établir une connexion SSH aux nœuds AKS, vous utilisez `kubectl debug` ou l’adresse IP privée.

Cet article vous explique comment créer une connexion SSH à un nœud AKS.

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez un cluster AKS existant. Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec Azure CLI][aks-quickstart-cli]ou avec le [Portail Azure][aks-quickstart-portal].

Cet article présuppose également que vous disposez d’une clé SSH. Vous pouvez créer une clé SSH à l’aide de [macOS, de Linux][ssh-nix] ou de [Windows][ssh-windows]. Si vous utilisez PuTTY Gen pour créer la paire de clés, enregistrez cette paire de clés dans un format OpenSSH plutôt que dans le format de clé privé PuTTy par défaut (fichier .ppk).

Le logiciel Azure CLI version 2.0.64 ou ultérieure doit également être installé et configuré. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][install-azure-cli].

## <a name="create-the-ssh-connection-to-a-linux-node"></a>Créer la connexion SSH à un nœud Linux

Pour créer une connexion SSH à un nœud AKS, utilisez `kubectl debug` pour exécuter un conteneur privilégié sur votre nœud. Pour afficher la liste de vos nœuds, utilisez `kubectl get nodes` :

```output
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE     VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                         KERNEL-VERSION     CONTAINER-RUNTIME
aks-nodepool1-12345678-vmss000000   Ready    agent   13m     v1.19.9   10.240.0.4    <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aks-nodepool1-12345678-vmss000001   Ready    agent   13m     v1.19.9   10.240.0.35   <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aksnpwin000000                      Ready    agent   87s     v1.19.9   10.240.0.67   <none>        Windows Server 2019 Datacenter   10.0.17763.1935    docker://19.3.1
```

Utilisez `kubectl debug` pour exécuter une image conteneur sur le nœud afin de vous y connecter.

```azurecli-interactive
kubectl debug node/aks-nodepool1-12345678-vmss000000 -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

Cette commande démarre un conteneur privilégié sur votre nœud et s’y connecte via une connexion SSH.

```output
$ kubectl debug node/aks-nodepool1-12345678-vmss000000 -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
Creating debugging pod node-debugger-aks-nodepool1-12345678-vmss000000-bkmmx with container debugger on node aks-nodepool1-12345678-vmss000000.
If you don't see a command prompt, try pressing enter.
root@aks-nodepool1-12345678-vmss000000:/#
```

Ce conteneur privilégié donne accès au nœud.

## <a name="create-the-ssh-connection-to-a-windows-node"></a>Créer la connexion SSH à un nœud Windows

À ce stade, vous ne pouvez pas vous connecter à un nœud Windows Server via le protocole SSH directement à l’aide de `kubectl debug`. Au lieu de cela, vous devez d’abord vous connecter à un autre nœud du cluster, puis vous connecter au nœud Windows Server à partir de ce nœud via SSH. Vous pouvez également [vous connecter aux nœuds Windows Server à l’aide de connexions utilisant le protocole RDP (Remote Desktop Protocol)][aks-windows-rdp] plutôt que d’une clé SSH.

Pour vous connecter à un autre nœud du cluster, utilisez `kubectl debug`. Pour plus d’informations, consultez [Créer la connexion SSH à un nœud Linux][ssh-linux-kubectl-debug].

Pour créer la connexion SSH au nœud Windows Server à partir d’un autre nœud, utilisez les clés SSH fournies lors de la création du cluster AKS et l’adresse IP interne du nœud Windows Server.

Ouvrez une nouvelle fenêtre de terminal et utilisez `kubectl get pods` pour obtenir le nom du pod démarré par `kubectl debug`.

```output
$ kubectl get pods

NAME                                                    READY   STATUS    RESTARTS   AGE
node-debugger-aks-nodepool1-12345678-vmss000000-bkmmx   1/1     Running   0          21s
```

Dans l’exemple ci-dessus, *node-debugger-aks-nodepool1-12345678-vmss000000-bkmmx* est le nom du pod démarré par `kubectl debug`.

Copiez votre clé SSH privée dans le pod créé par `kubectl debug`. Cette clé privée est utilisée pour créer la connexion SSH au nœud Windows Server AKS. Si nécessaire, remplacez `~/.ssh/id_rsa` par l’emplacement de votre clé SSH privée :

```azurecli-interactive
kubectl cp ~/.ssh/id_rsa node-debugger-aks-nodepool1-12345678-vmss000000-bkmmx:/id_rsa
```

Utilisez `kubectl get nodes` pour afficher l’adresse IP interne du nœud Windows Server :

```output
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE     VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                         KERNEL-VERSION     CONTAINER-RUNTIME
aks-nodepool1-12345678-vmss000000   Ready    agent   13m     v1.19.9   10.240.0.4    <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aks-nodepool1-12345678-vmss000001   Ready    agent   13m     v1.19.9   10.240.0.35   <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aksnpwin000000                      Ready    agent   87s     v1.19.9   10.240.0.67   <none>        Windows Server 2019 Datacenter   10.0.17763.1935    docker://19.3.1
```

Dans l’exemple ci-dessus, *10.240.0.67* est l’adresse IP interne du nœud Windows Server.

Revenez au terminal démarré par `kubectl debug` et mettez à jour l’autorisation de la clé SSH privée que vous avez copiée dans le pod.

```azurecli-interactive
chmod 0400 id_rsa
```

Créez une connexion SSH au nœud Windows Server à l’aide de l’adresse IP interne. Le nom d’utilisateur par défaut pour les nœuds AKS est *azureuser*. Acceptez l’invite pour poursuivre la connexion. Vous obtenez alors l’invite Bash de votre nœud Windows Server :

```output
$ ssh -i id_rsa azureuser@10.240.0.67

The authenticity of host '10.240.0.67 (10.240.0.67)' can't be established.
ECDSA key fingerprint is SHA256:1234567890abcdefghijklmnopqrstuvwxyzABCDEFG.
Are you sure you want to continue connecting (yes/no)? yes

[...]

Microsoft Windows [Version 10.0.17763.1935]
(c) 2018 Microsoft Corporation. All rights reserved.

azureuser@aksnpwin000000 C:\Users\azureuser>
```

## <a name="remove-ssh-access"></a>Supprimer l’accès SSH

Quand vous avez terminé, quittez (`exit`) la session SSH, puis quittez (`exit`) la session de conteneur interactive. Quand cette session de conteneur se ferme, le pod utilisé pour l’accès SSH à partir du cluster AKS est supprimé.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin de données de dépannage supplémentaires, vous pouvez [Afficher les journaux d’activité kubelet][view-kubelet-logs] ou [Afficher les journaux d’activité du nœud principal Kubernetes][view-master-logs].


<!-- INTERNAL LINKS -->
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: ./view-control-plane-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[ssh-linux-kubectl-debug]: #create-the-ssh-connection-to-a-linux-node