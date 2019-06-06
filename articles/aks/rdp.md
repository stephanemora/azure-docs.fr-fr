---
title: RDP dans les nœuds de Windows Server cluster Azure Kubernetes Service (AKS)
description: Découvrez comment créer une connexion RDP avec le cluster Azure Kubernetes Service (AKS) des nœuds de Windows Server pour les tâches de maintenance et de résolution des problèmes.
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: article
ms.date: 06/04/2019
ms.author: twhitney
ms.openlocfilehash: 11f6869d4d5a2ee0ef2e986ee8268c7a001ea015
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688634"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Se connecter avec RDP à Azure Kubernetes Service (AKS) nœuds du cluster Windows Server pour la maintenance ou la résolution des problèmes

Tout au long du cycle de vie de votre cluster Azure Kubernetes Service (AKS), vous devrez peut-être d’accéder à un nœud AKS Windows Server. que ce soit pour effectuer une tâche de maintenance, une collecte de journaux ou d’autres opérations de dépannage. Vous pouvez accéder aux nœuds AKS Windows Server à l’aide de RDP. Vous pouvez également, si vous souhaitez utiliser SSH pour accéder aux nœuds AKS Windows Server et que vous avez accès à la même paire de clés qui a été utilisé pendant la création du cluster, vous pouvez suivre les étapes décrites dans [SSH dans les nœuds de cluster Azure Kubernetes Service (AKS)] [ssh-steps]. Pour des raisons de sécurité, les nœuds AKS ne sont pas exposés à Internet.

Prise en charge de nœud Windows Server est actuellement en version préliminaire dans ACS.

Cet article vous montre comment créer une connexion RDP avec un nœud AKS à l’aide de leurs adresses IP privées.

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez un cluster ACS existant avec un nœud Windows Server. Si vous avez besoin d’un cluster ACS, consultez l’article sur [création d’un cluster AKS avec un conteneur Windows à l’aide de l’interface CLI][aks-windows-cli]. Vous avez besoin du nom d’utilisateur administrateur de Windows et le mot de passe pour le nœud de Windows Server que vous souhaitez résoudre les problèmes. Vous devez également un client RDP comme [Bureau à distance Microsoft][rdp-mac].

Vous également besoin d’Azure CLI version 2.0.61 ou ultérieur installé et configuré. Exécutez  `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez  [Installation d’Azure CLI 2.0][install-azure-cli].

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Déployer une machine virtuelle sur le même sous-réseau que votre cluster

Les nœuds Windows Server de votre cluster AKS n’ont pas les adresses IP accessibles en externe. Pour établir une connexion RDP, vous pouvez déployer une machine virtuelle avec une adresse IP accessible publiquement sur le même sous-réseau que vos nœuds Windows Server.

L’exemple suivant crée un ordinateur virtuel nommé *myVM* dans le *myResourceGroup* groupe de ressources.

Tout d’abord, obtenez le sous-réseau utilisé par votre pool de nœud Windows Server. Pour obtenir l’id de sous-réseau, vous devez le nom du sous-réseau. Pour obtenir le nom du sous-réseau, vous devez le nom du réseau virtuel. Obtenir le nom de réseau virtuel en interrogeant votre cluster pour sa liste de réseaux. Pour interroger le cluster, vous avez besoin de son nom. Vous pouvez obtenir tous ces éléments en exécutant la commande suivante dans Azure Cloud Shell :

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Maintenant que vous avez le SUBNET_ID, exécutez la commande suivante dans la même fenêtre Azure Cloud Shell pour créer la machine virtuelle :

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2019datacenter \
    --admin-username azureuser \
    --admin-password myP@ssw0rd12 \
    --subnet $SUBNET_ID \
    --query publicIpAddress -o tsv
```

L’exemple de sortie suivant montre la machine virtuelle a été créée avec succès et affiche l’adresse IP publique de la machine virtuelle.

```console
13.62.204.18
```

Enregistrez l’adresse IP publique de la machine virtuelle. Vous utiliserez cette adresse dans une étape ultérieure.

## <a name="get-the-node-address"></a>Obtenir l’adresse du nœud

Pour gérer un cluster Kubernetes, vous utilisez [kubectl][kubectl], le client de ligne de commande Kubernetes. Si vous utilisez Azure Cloud Shell, `kubectl` est déjà installé. Pour installer `kubectl` en local, utilisez la commande [az aks install-cli][az-aks-install-cli] :
    
```azurecli-interactive
az aks install-cli
```

Exécutez la commande [az aks get-credentials][az-aks-get-credentials] pour configurer `kubectl` afin de vous connecter à votre cluster Kubernetes. Cette commande télécharge les informations d’identification et configure l’interface CLI Kubernetes pour les utiliser.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

L’adresse IP interne des nœuds Windows Server à l’aide de la liste le [kubectl get] [ kubectl-get] commande :

```console
kubectl get nodes -o wide
```

L’exemple de sortie de suivi affiche les adresses IP internes de tous les nœuds du cluster, y compris les nœuds Windows Server.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Enregistrez l’adresse IP interne du nœud Windows Server que vous souhaitez résoudre les problèmes. Vous utiliserez cette adresse dans une étape ultérieure.

## <a name="connect-to-the-virtual-machine-and-node"></a>Se connecter à la machine virtuelle et le nœud

Se connecter à l’adresse IP publique de la machine virtuelle que vous avez créé précédemment à l’aide un client RDP comme [Bureau à distance Microsoft][rdp-mac].

![Image de la connexion à la machine virtuelle à l’aide d’un client RDP](media/rdp/vm-rdp.png)

Une fois que vous êtes connecté à votre machine virtuelle, vous connecter à la *adresse IP interne* du nœud Windows Server que vous souhaitez résoudre les problèmes à l’aide d’un client RDP à partir de votre machine virtuelle.

![Image de la connexion au nœud Windows Server à l’aide d’un client RDP](media/rdp/node-rdp.png)

Vous êtes maintenant connecté à votre nœud Windows Server.

![Image de la fenêtre cmd dans le nœud de Windows Server](media/rdp/node-session.png)

Vous pouvez maintenant exécuter des commandes de résolution des problèmes le *cmd* fenêtre. Étant donné que les nœuds Windows Server utilisent Windows Server Core, il n’est pas une interface GUI complète ou autres outils de l’interface graphique utilisateur lorsque vous vous connectez à un nœud de serveur Windows via RDP.

## <a name="remove-rdp-access"></a>Supprimer l’accès RDP

Lorsque vous avez terminé, quittez la connexion RDP vers le nœud du serveur de Windows, puis quittez la session RDP à la machine virtuelle. Après avoir quitté les deux sessions RDP, supprimer la machine virtuelle avec le [az vm delete] [ az-vm-delete] commande :

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin des données de dépannage supplémentaires, vous pouvez [afficher les journaux de nœud principal Kubernetes] [ view-master-logs] ou [Azure Monitor][azure-monitor-containers].

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-vm-delete]: /cli/azure/vm#az-vm-delete
[azure-monitor-containers]: ../azure-monitor/insights/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md
