---
title: RDP dans les nœuds Windows Server de cluster AKS (Azure Kubernetes Service)
description: Découvrez comment créer une connexion RDP avec des nœuds Windows Server de cluster AKS (Azure Kubernetes Service) pour les tâches de maintenance et de résolution des problèmes.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/04/2019
ms.author: mlearned
ms.openlocfilehash: 0238278b81255d735f8a950ca307d0e05100cfec
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614575"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Se connecter avec RDP à des nœuds Windows Server de cluster AKS (Azure Kubernetes Service) à des fins de maintenance ou de résolution des problèmes

Tout au long du cycle de vie de votre cluster AKS (Azure Kubernetes Service ), vous pouvez être amené à accéder à un nœud Windows Server AKS, que ce soit pour effectuer une tâche de maintenance, une collecte de journaux ou d’autres opérations de dépannage. Vous pouvez accéder aux nœuds Windows Server AKS à l’aide de RDP. Ou bien, si vous souhaitez utiliser SSH pour accéder aux nœuds Windows Server AKS et que vous avez accès à la paire de clés qui a été utilisée pendant la création du cluster, vous pouvez suivre les étapes décrites dans [SSH dans des nœuds de cluster AKS (Azure Kubernetes Service)][ssh-steps]. Pour des raisons de sécurité, les nœuds AKS ne sont pas exposés à Internet.

La prise en charge des nœuds Windows Server dans AKS est actuellement disponible en préversion.

Cet article vous montre comment créer une connexion RDP avec des nœuds AKS en utilisant leurs adresses IP privées.

## <a name="before-you-begin"></a>Avant de commencer

Cet article part de l’hypothèse que vous disposez déjà d’un cluster AKS avec un nœud Windows Server. Si vous avez besoin d’un cluster AKS, consultez l’article traitant de la [création d’un cluster AKS avec un conteneur Windows à partir d’Azure CLI][aks-windows-cli]. You need the Windows administrator username and password for the Windows Server node you want to troubleshoot. You also need an RDP client such as [Microsoft Remote Desktop][rdp-mac]. Azure CLI version 2.0.61 ou ultérieure doit également être installé et configuré. Exécutez  `az --version` pour trouver la version.

Si vous devez installer ou mettre à niveau, consultez  [Installation d’Azure CLI][install-azure-cli]. Déployer une machine virtuelle dans le même sous-réseau que votre cluster Les nœuds Windows Server de votre cluster AKS n’ont pas d’adresses IP accessibles en externe.

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Pour établir une connexion RDP, vous pouvez déployer une machine virtuelle dotée d’une adresse IP accessible publiquement dans le même sous-réseau que vos nœuds Windows Server.

Dans l’exemple suivant, une machine virtuelle nommée *myVM* est créée dans le groupe de ressources *myResourceGroup*. Dans un premier temps, obtenez le sous-réseau utilisé par votre pool de nœuds Windows Server.

Pour obtenir l’ID du sous-réseau, vous avez besoin du nom du sous-réseau.

Pour obtenir le nom du sous-réseau, vous avez besoin du nom du réseau virtuel. Obtenez le nom du réseau virtuel en demandant la liste des réseaux à votre cluster. Pour interroger le cluster, vous avez besoin de son nom. Vous pouvez obtenir tous ces éléments en exécutant la commande suivante dans Azure Cloud Shell : Maintenant que vous disposez du SUBNET_ID, exécutez la commande suivante dans la même fenêtre Azure Cloud Shell pour créer la machine virtuelle : L’exemple de sortie suivant montre que la machine virtuelle a bien été créée et présente l’adresse IP publique de la machine virtuelle.

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Enregistrez l’adresse IP publique de la machine virtuelle.

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

Vous utiliserez cette adresse dans une étape ultérieure.

```console
13.62.204.18
```

Obtenir l’adresse des nœuds Pour gérer un cluster Kubernetes, vous utilisez [kubectl][kubectl], le client de ligne de commande Kubernetes.

## <a name="get-the-node-address"></a>Si vous utilisez Azure Cloud Shell, `kubectl` est déjà installé.

Pour installer `kubectl` en local, utilisez la commande [az aks install-cli][az-aks-install-cli] : Pour configurer `kubectl` afin de vous connecter à votre cluster Kubernetes, exécutez la commande [az aks get-credentials][az-aks-get-credentials]. Cette commande télécharge les informations d’identification et configure l’interface CLI Kubernetes pour les utiliser.
    
```azurecli-interactive
az aks install-cli
```

Listez les adresses IP internes des nœuds Windows Server en utilisant la commande [kubectl get][kubectl-get] : L’exemple de sortie suivant présente les adresses IP internes de tous les nœuds du cluster, notamment des nœuds Windows Server.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Enregistrez l’adresse IP interne du nœud Windows Server dont vous voulez résoudre les problèmes.

```console
kubectl get nodes -o wide
```

Vous utiliserez cette adresse dans une étape ultérieure.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Se connecter à la machine virtuelle et au nœud Connectez-vous à l’adresse IP publique de la machine virtuelle que vous avez créée précédemment à l’aide d’un client RDP comme le [Bureau à distance Microsoft][rdp-mac].

## <a name="connect-to-the-virtual-machine-and-node"></a>Image illustrant la connexion à la machine virtuelle à l’aide d’un client RDP

Après vous être connecté à la machine virtuelle, connectez-vous à l’*adresse IP interne* du nœud Windows Server dont vous voulez résoudre les problèmes à l’aide d’un client RDP depuis votre machine virtuelle.

![Image illustrant la connexion au nœud Windows Server à l’aide d’un client RDP](media/rdp/vm-rdp.png)

Vous êtes maintenant connecté à votre nœud Windows Server.

![Image de la fenêtre cmd dans le nœud Windows Server](media/rdp/node-rdp.png)

Vous pouvez maintenant exécuter des commandes de dépannage dans la fenêtre *cmd*.

![Comme les nœuds Windows Server utilisent Windows Server Core, vous ne disposez pas d’interface GUI complète ou d’autres outils GUI au moment où vous vous connectez à un nœud Windows Server via RDP.](media/rdp/node-session.png)

Retirer l’accès RDP Lorsque vous avez terminé, quittez la connexion RDP au nœud Windows Server, puis quittez la session RDP sur la machine virtuelle.

## <a name="remove-rdp-access"></a>Après avoir quitté les deux sessions RDP, supprimez la machine virtuelle avec la commande [az vm delete][az-vm-delete] :

Étapes suivantes Si vous avez besoin de données de résolution des problèmes supplémentaires, vous pouvez [afficher les journaux de nœud principal Kubernetes][view-master-logs] or [Azure Monitor][azure-monitor-containers].

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Next steps

If you need additional troubleshooting data, you can <bpt id="p1">[</bpt>view the Kubernetes master node logs<ept id="p1">][view-master-logs]</ept> or <bpt id="p2">[</bpt>Azure Monitor<ept id="p2">][azure-monitor-containers]</ept>.

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
