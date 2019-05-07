---
title: SSH dans les nœuds de cluster Azure Kubernetes Service (AKS)
description: Découvrez comment créer une connexion SSH avec des nœuds de cluster AKS (Azure Kubernetes Service) pour les tâches de maintenance et de dépannage.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/05/2019
ms.author: iainfou
ms.openlocfilehash: 680e087e80d3e9891e201e7cb474ccfcf7fcc70b
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65072626"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Se connecter avec SSH à des nœuds de cluster AKS (Azure Kubernetes Service) pour effectuer des tâches de maintenance ou de dépannage

Tout au long du cycle de vie de votre cluster AKS (Azure Kubernetes Service ), vous pouvez être amené à accéder à un nœud AKS, que ce soit pour effectuer une tâche de maintenance, une collecte de journaux ou d’autres opérations de dépannage. Les nœuds AKS étant des machines virtuelles Linux, vous pouvez y accéder à l’aide de SSH. Pour des raisons de sécurité, les nœuds AKS ne sont pas exposés à Internet.

Cet article vous montre comment créer une connexion SSH avec un nœud AKS à l’aide de ses adresses IP privées.

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez un cluster AKS existant. Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec Azure CLI][aks-quickstart-cli] ou [avec le portail Azure][aks-quickstart-portal].

Vous également besoin d’Azure CLI version 2.0.59 ou ultérieur installé et configuré. Exécutez  `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez  [Installation d’Azure CLI 2.0][install-azure-cli].

## <a name="add-your-public-ssh-key"></a>Ajouter votre clé SSH publique

Par défaut, les clés SSH sont générées quand vous créez un cluster AKS. Si vous n’avez pas spécifié vos propres clés SSH quand vous avez créé votre cluster AKS, ajoutez vos clés SSH publiques aux nœuds AKS.

Pour ajouter votre clé SSH à un nœud AKS, effectuez les étapes suivantes :

1. Obtenez le nom du groupe de ressources pour vos ressources de cluster AKS à l’aide de la commande [az aks show][az-aks-show]. Fournissez vos propres groupe de ressources principal et nom du cluster AKS :

    ```azurecli-interactive
    az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
    ```

1. Répertoriez les machines virtuelles dans le groupe de ressources de cluster AKS avec la commande [az vm list][az-vm-list]. Ces machines virtuelles sont vos nœuds AKS :

    ```azurecli-interactive
    az vm list --resource-group MC_myResourceGroup_myAKSCluster_eastus -o table
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
      --resource-group MC_myResourceGroup_myAKSCluster_eastus \
      --name aks-nodepool1-79590246-0 \
      --username azureuser \
      --ssh-key-value ~/.ssh/id_rsa.pub
    ```

## <a name="get-the-aks-node-address"></a>Obtenir l’adresse des nœuds AKS

Les nœuds AKS ne sont pas exposés publiquement sur Internet. Pour vous connecter avec SSH aux nœuds AKS, vous utilisez l’adresse IP privée. Dans l’étape suivante, vous créez un pod d’assistance dans votre cluster AKS qui vous permet de SSH à cette adresse IP privée du nœud.

Affichez l’adresse IP privée d’un nœud de cluster AKS à l’aide de la commande [az vm list-ip-addresses][az-vm-list-ip-addresses]. Fournissez le nom de votre propre groupe de ressources de cluster AKS obtenu à une étape [az-aks-show][az-aks-show] précédente :

```azurecli-interactive
az vm list-ip-addresses --resource-group MC_myAKSCluster_myAKSCluster_eastus -o table
```

L’exemple de sortie suivant montre les adresses IP privées des nœuds AKS :

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>Créer la connexion SSH

Pour créer une connexion SSH à un nœud AKS, vous exécutez un pod d’assistance dans votre cluster AKS. Ce pod d’assistance vous fournit l’accès SSH au cluster, puis l’accès au nœud SSH. Pour créer et utiliser ce pod d’assistance, effectuez les étapes suivantes :

1. Exécutez une image conteneur `debian` et attachez-y une session de terminal. Vous pouvez ensuite utiliser ce conteneur pour créer une session SSH avec n’importe quel nœud du cluster AKS :

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

1. L’image Debian de base n’inclut pas de composants SSH. Une fois la session de terminal connectée au conteneur, installez un client SSH en utilisant `apt-get` comme suit :

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Dans une nouvelle fenêtre de terminal, non connectée à votre conteneur, répertoriez les pods sur votre cluster AKS à l’aide de la commande [kubectl get pods][kubectl-get]. Le pod créé à l’étape précédente commence par le nom *aks-ssh*, comme illustré dans l’exemple suivant :

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

Si vous avez besoin de données de dépannage supplémentaires, vous pouvez [afficher les journaux d’activité kubelet][view-kubelet-logs] ou [afficher les journaux d’activité de nœud principal Kubernetes][view-master-logs].

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
