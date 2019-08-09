---
title: SSH dans les nœuds de cluster Azure Kubernetes Service (AKS)
description: Découvrez comment créer une connexion SSH avec des nœuds de cluster AKS (Azure Kubernetes Service) pour les tâches de maintenance et de dépannage.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 6ddd1b160110e7a751f54f89b387a62d94e9308e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "67614480"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Se connecter avec SSH à des nœuds de cluster AKS (Azure Kubernetes Service) pour effectuer des tâches de maintenance ou de dépannage

Tout au long du cycle de vie de votre cluster AKS (Azure Kubernetes Service ), vous pouvez être amené à accéder à un nœud AKS, que ce soit pour effectuer une tâche de maintenance, une collecte de journaux ou d’autres opérations de dépannage. Vous pouvez accéder aux nœuds AKS avec SSH, y compris aux nœuds Windows Server (actuellement en préversion dans AKS). Vous pouvez également [vous connecter aux nœuds Windows Server à l’aide de connexions RDP (Remote Desktop Protocol)][aks-windows-rdp]. Pour des raisons de sécurité, les nœuds AKS ne sont pas exposés à Internet.

Cet article vous montre comment créer une connexion SSH avec un nœud AKS à l’aide de ses adresses IP privées.

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez un cluster AKS existant. Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec Azure CLI][aks-quickstart-cli]ou avec le [Portail Azure][aks-quickstart-portal].

Le logiciel Azure CLI version 2.0.64 ou ultérieure doit également être installé et configuré. Exécutez  `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez  [Installation de l’interface de ligne de commande Azure][install-azure-cli].

## <a name="add-your-public-ssh-key"></a>Ajouter votre clé SSH publique

Par défaut, les clés SSH sont obtenues ou générées, puis ajoutées aux nœuds lorsque vous créez un cluster AKS. Si vous avez besoin de spécifier des clés SSH différentes de celles utilisées lorsque vous avez créé votre cluster AKS, ajoutez votre clé SSH publique aux nœuds AKS Linux. Si nécessaire, vous pouvez créer une clé SSH à l’aide de [macOS, Linux][ssh-nix] ou [Windows][ssh-windows]. Si vous utilisez PuTTY Gen pour créer la paire de clés, enregistrez cette paire de clés dans un format OpenSSH plutôt que dans le format de clé privé PuTTy par défaut (fichier .ppk).

> [!NOTE]
> Les clés SSH peuvent actuellement uniquement être ajoutées à des nœuds Linux à l’aide de l’interface de ligne de commande Azure. Si vous utilisez des nœuds Windows Server, utilisez les clés SSH fournies lorsque vous avez créé le cluster AKS et passez à l’étape indiquant [comment obtenir l’adresse du nœud AKS](#get-the-aks-node-address). Ou [connectez-vous aux nœuds Windows Server à l’aide de connexions RDP (Remote Desktop Protocol)][aks-windows-rdp].

La procédure permettant d’obtenir l’adresse IP privée des nœuds AKS est différente selon le type de cluster AKS que vous exécutez :

* Pour la plupart des clusters AKS, suivez la procédure permettant d’[obtenir l’adresse IP pour des clusters AKS ordinaires](#add-ssh-keys-to-regular-aks-clusters).
* Si vous utilisez des fonctionnalités de préversion dans AKS qui utilisent des groupes de machines virtuelles identiques, tels que plusieurs pools de nœuds ou la prise en charge des conteneurs Windows Server, [suivez les étapes relatives aux clusters AKS basés sur des groupes de machines virtuelles identiques](#add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters).

### <a name="add-ssh-keys-to-regular-aks-clusters"></a>Ajouter des clés SSH aux clusters AKS ordinaires

Pour ajouter votre clé SSH à un nœud AKS Linux, effectuez les étapes suivantes :

1. Obtenez le nom du groupe de ressources pour vos ressources de cluster AKS à l’aide de la commande [az aks show][az-aks-show]. Le nom du cluster est affecté à la variable nommée *CLUSTER_RESOURCE_GROUP*. Remplacez *myResourceGroup* par le nom du groupe de ressources où se trouve votre cluster AKS :

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. Listez les machines virtuelles dans le groupe de ressources du cluster AKS à l’aide de la commande [az vm list][az-vm-list]. Ces machines virtuelles sont vos nœuds AKS :

    ```azurecli-interactive
    az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
    ```

    L’exemple de sortie suivant montre les nœuds AKS :

    ```
    Name                      ResourceGroup                                  Location
    ------------------------  ---------------------------------------------  ----------
    aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
    ```

1. Pour ajouter vos clés SSH au nœud, utilisez la commande [az vm user update][az-vm-user-update]. Fournissez le nom de groupe de ressources, puis un des nœuds AKS obtenus à l’étape précédente. Par défaut, le nom d’utilisateur pour les nœuds AKS est *azureuser*. Indiquez l’emplacement de votre propre clé publique SSH, tel que *~/.ssh/id_rsa.pub*, ou collez le contenu de votre clé publique SSH :

    ```azurecli-interactive
    az vm user update \
      --resource-group $CLUSTER_RESOURCE_GROUP \
      --name aks-nodepool1-79590246-0 \
      --username azureuser \
      --ssh-key-value ~/.ssh/id_rsa.pub
    ```

### <a name="add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters"></a>Ajouter des clés SSH aux clusters AKS basés sur des groupes de machines virtuelles identiques

Pour ajouter votre clé SSH à un nœud AKS Linux qui fait partie d’un groupe de machines virtuelles identiques, effectuez les étapes suivantes :

1. Obtenez le nom du groupe de ressources pour vos ressources de cluster AKS à l’aide de la commande [az aks show][az-aks-show]. Le nom du cluster est affecté à la variable nommée *CLUSTER_RESOURCE_GROUP*. Remplacez *myResourceGroup* par le nom du groupe de ressources où se trouve votre cluster AKS :

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. Ensuite, obtenez le groupe de machines virtuelles identiques pour votre cluster AKS à l’aide de la commande [az vmss list][az-vmss-list]. Le nom du groupe de machines virtuelles identiques est affecté à la variable nommée *SCALE_SET_NAME* :

    ```azurecli-interactive
    SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
    ```

1. Pour ajouter vos clés SSH aux nœuds dans un groupe de machines virtuelles identiques, utilisez la commande [az vmss extension set][az-vmss-extension-set]. Le groupe de ressources de cluster et le nom du groupe de machines virtuelles identiques sont fournis à partir des commandes précédentes. Par défaut, le nom d’utilisateur pour les nœuds AKS est *azureuser*. Si nécessaire, mettez à jour l’emplacement de votre propre emplacement de clé publique SSH, sous la forme *~/.ssh/id_rsa.pub* :

    ```azurecli-interactive
    az vmss extension set  \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --vmss-name $SCALE_SET_NAME \
        --name VMAccessForLinux \
        --publisher Microsoft.OSTCExtensions \
        --version 1.4 \
        --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"
    ```

1. Appliquez la clé SSH aux nœuds à l’aide de la commande [az vmss update-instances][az-vmss-update-instances] :

    ```azurecli-interactive
    az vmss update-instances --instance-ids '*' \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --name $SCALE_SET_NAME
    ```

## <a name="get-the-aks-node-address"></a>Obtenir l’adresse des nœuds AKS

Les nœuds AKS ne sont pas exposés publiquement sur Internet. Pour vous connecter avec SSH aux nœuds AKS, vous utilisez l’adresse IP privée. À l’étape suivante, vous allez créer un pod d’assistance dans votre cluster AKS qui vous permettra de vous connecter via SSH à cette adresse IP privée du nœud. La procédure permettant d’obtenir l’adresse IP privée des nœuds AKS est différente selon le type de cluster AKS que vous exécutez :

* Pour la plupart des clusters AKS, suivez la procédure permettant d’[obtenir l’adresse IP pour des clusters AKS ordinaires](#ssh-to-regular-aks-clusters).
* Si vous utilisez des fonctionnalités de préversion dans AKS qui utilisent des groupes de machines virtuelles identiques, tels que plusieurs pools de nœuds ou la prise en charge des conteneurs Windows Server, [suivez les étapes relatives aux clusters AKS basés sur des groupes de machines virtuelles identiques](#ssh-to-virtual-machine-scale-set-based-aks-clusters).

### <a name="ssh-to-regular-aks-clusters"></a>Se connecter via SSH à des clusters AKS ordinaires

Affichez l’adresse IP privée d’un nœud de cluster AKS à l’aide de la commande [az vm list-ip-addresses][az-vm-list-ip-addresses]. Fournissez le nom de votre propre groupe de ressources de cluster AKS obtenu à une étape [az-aks-show][az-aks-show] précédente :

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

L’exemple de sortie suivant montre les adresses IP privées des nœuds AKS :

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

### <a name="ssh-to-virtual-machine-scale-set-based-aks-clusters"></a>Se connecter via SSH aux clusters AKS basés sur des groupes de machines virtuelles identiques

Listez les adresses IP internes des nœuds à l’aide de la commande [kubectl get][kubectl-get] :

```console
kubectl get nodes -o wide
```

L’exemple de sortie suivant affiche les adresses IP internes de tous les nœuds du cluster, y compris un nœud Windows Server.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Enregistrez l’adresse IP interne du nœud que vous souhaitez dépanner. Vous utiliserez cette adresse dans une étape ultérieure.

## <a name="create-the-ssh-connection"></a>Créer la connexion SSH

Pour créer une connexion SSH à un nœud AKS, vous exécutez un pod d’assistance dans votre cluster AKS. Ce pod d’assistance vous fournit l’accès SSH au cluster, puis l’accès au nœud SSH. Pour créer et utiliser ce pod d’assistance, effectuez les étapes suivantes :

1. Exécutez une image conteneur `debian` et attachez-y une session de terminal. Vous pouvez ensuite utiliser ce conteneur pour créer une session SSH avec n’importe quel nœud du cluster AKS :

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Si vous utilisez des nœuds Windows Server (actuellement en préversion dans AKS), ajoutez un sélecteur de nœud à la commande pour planifier le conteneur Debian sur un nœud Linux, comme suit :
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. L’image Debian de base n’inclut pas de composants SSH. Une fois la session de terminal connectée au conteneur, installez un client SSH en utilisant `apt-get` comme suit :

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Dans une nouvelle fenêtre de terminal, non connectée à votre conteneur, listez les pods sur votre cluster AKS à l’aide de la commande [kubectl get pods][kubectl-get]. Le pod créé à l’étape précédente commence par le nom *aks-ssh*, comme illustré dans l’exemple suivant :

    ```
    $ kubectl get pods
    
    NAME                       READY     STATUS    RESTARTS   AGE
    aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
    ```

1. À la première étape de cet article, vous avez ajouté votre clé SSH publique au nœud AKS. À présent, copiez votre clé SSH privée dans le pod. Cette clé privée permet de créer la connexion SSH aux nœuds AKS.

    Fournissez votre propre nom de pod *aks-ssh* obtenu à l’étape précédente. Si nécessaire, remplacez *~/.ssh/id_rsa* par l’emplacement de votre clé SSH privée :

    ```console
    kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
    ```

1. Dans la session de terminal dans votre conteneur, mettez à jour les autorisations sur la clé privée SSH `id_rsa` copiée afin qu’elle soit accessible à l’utilisateur en lecture seule :

    ```console
    chmod 0600 id_rsa
    ```

1. À présent, créez une connexion SSH à votre nœud AKS. Là encore, le nom d’utilisateur par défaut pour les nœuds AKS est *azureuser*. Acceptez l’invite pour poursuivre la connexion puisque la clé SSH est approuvée en premier. Ensuite, l’invite bash de votre nœud AKS apparaît :

    ```console
    $ ssh -i id_rsa azureuser@10.240.0.4
    
    ECDSA key fingerprint is SHA256:A6rnRkfpG21TaZ8XmQCCgdi9G/MYIMc+gFAuY9RUY70.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.240.0.4' (ECDSA) to the list of known hosts.
    
    Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-1018-azure x86_64)
    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
    
      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud
    
    [...]
    
    azureuser@aks-nodepool1-79590246-0:~$
    ```

## <a name="remove-ssh-access"></a>Supprimer l’accès SSH

Quand vous avez terminé, quittez (`exit`) la session SSH, puis quittez (`exit`) la session de conteneur interactive. Quand cette session de conteneur se ferme, le pod utilisé pour l’accès SSH à partir du cluster AKS est supprimé.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin de données de dépannage supplémentaires, vous pouvez consulter [les journaux d’activité kubelet][view-kubelet-logs] ou [les journaux d’activité de nœud principal Kubernetes][view-master-logs].

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-vm-list]: /cli/azure/vm#az-vm-list
[az-vm-user-update]: /cli/azure/vm/user#az-vm-user-update
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az-vmss-list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az-vmss-extension-set
[az-vmss-update-instances]: /cli/azure/vmss#az-vmss-update-instances
