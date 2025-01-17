---
title: Restreindre l’accès avec un point de terminaison de service
description: Restreignez l’accès à un registre de conteneurs Azure à l’aide d’un point de terminaison de service dans un réseau virtuel Azure. L’accès au point de terminaison de service est une fonctionnalité du niveau de service Premium.
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: 6c76e7e98f049a46c161c5f4ff00b5282ba52c1a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131043468"
---
# <a name="restrict-access-to-a-container-registry-using-a-service-endpoint-in-an-azure-virtual-network"></a>Restreindre l’accès à un registre de conteneurs à l’aide d’un point de terminaison de service dans un réseau virtuel Azure

Un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) fournit un accès réseau privé et sécurisé à vos ressources Azure et locales. Un [point de terminaison de service](../virtual-network/virtual-network-service-endpoints-overview.md) vous permet de sécuriser l’adresse IP publique de votre registre de conteneurs uniquement pour votre réseau virtuel. Ce point de terminaison donne au trafic une route optimale vers la ressource via le réseau principal Azure. Les identités du réseau virtuel et du sous-réseau sont également transmises avec chaque demande.

Cet article explique comment configurer un point de terminaison de service de registre de conteneurs (préversion) dans un réseau virtuel. 

Chaque registre prend en charge un maximum de 100 règles de réseau virtuel.

> [!IMPORTANT]
> Azure Container Registry prend désormais en charge [Azure Private Link](container-registry-private-link.md), ce qui permet de placer des points de terminaison privés d’un réseau virtuel sur un registre. Les points de terminaison privés sont accessibles à partir du réseau virtuel, à l’aide d’adresses IP privées. Nous vous recommandons d’utiliser des points de terminaison privés plutôt que des points de terminaison de service dans la plupart des scénarios réseau.

La configuration d’un point de terminaison de service du registre est disponible dans le niveau de service de registre de conteneurs **Premium**. Pour plus d’informations sur les niveaux de service et les limites de registre, consultez [Niveaux de service d’Azure Container Registry](container-registry-skus.md).

## <a name="preview-limitations"></a>Limitations de la version préliminaire

* Le développement futur des points de terminaison de service pour Azure Container Registry n’est pas planifié actuellement. Nous vous recommandons d’utiliser des [points de terminaison privés](container-registry-private-link.md) à la place.
* Vous ne pouvez pas utiliser le portail Azure pour configurer des points de terminaison de service sur un registre.
* Seul un cluster [Azure Kubernetes Service](../aks/intro-kubernetes.md) ou une [machine virtuelle](../virtual-machines/linux/overview.md) Azure peuvent être utilisés en tant qu’hôtes pour accéder à un registre de conteneurs dans un réseau virtuel. *Les autres services Azure, y compris Azure Container Instances, ne sont pas pris en charge.*
* Les points de terminaison de service pour Azure Container Registry ne sont pas pris en charge dans le cloud Azure US Government ni le cloud Azure Chine.

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Prérequis

* Pour utiliser les étapes Azure CLI décrites dans cet article, vous devez disposer d’Azure CLI version 2.0.58 ou ultérieure. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][azure-cli].

* Si vous ne disposez pas d’un registre de conteneurs, créez-en un (niveau Premium requis) et envoyez (push) un exemple d’image comme `hello-world` à partir de Docker Hub. Par exemple, utilisez le [portail Azure][quickstart-portal] ou [Azure CLI][quickstart-cli] pour créer un registre. 

* Si vous souhaitez restreindre l’accès au registre à l’aide d’un point de terminaison de service dans un autre abonnement Azure, inscrivez le fournisseur de ressources pour Azure Container Registry dans cet abonnement. Par exemple :

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="configure-network-access-for-registry"></a>Configurer l’accès réseau pour le registre

Dans cette section, configurez votre registre de conteneurs pour autoriser l’accès à partir d’un sous-réseau dans un réseau virtuel Azure. Les étapes sont fournies à l’aide d’Azure CLI.

### <a name="add-a-service-endpoint-to-a-subnet"></a>Ajouter un point de terminaison de service à un sous-réseau

Quand vous créez une machine virtuelle, Azure crée par défaut un réseau virtuel dans le même groupe de ressources. Le nom du réseau virtuel est basé sur le nom de la machine virtuelle. Par exemple, si vous nommez votre machine virtuelle *myDockerVM*, le nom de réseau virtuel par défaut est *myDockerVMVNET*, avec un sous-réseau nommé *myDockerVMSubnet*. Vérifiez cela à l’aide de la commande [az network vnet list][az-network-vnet-list] :

```azurecli
az network vnet list \
  --resource-group myResourceGroup \
  --query "[].{Name: name, Subnet: subnets[0].name}"
```

Sortie :

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet&quot;: &quot;myDockerVMSubnet"
  }
]
```

Utilisez la commande [az network vnet subnet update][az-network-vnet-subnet-update] pour ajouter un point de terminaison de service **Microsoft.ContainerRegistry** à votre sous-réseau. Substituez les noms de vos réseau virtuel et sous-réseau dans la commande suivante :

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Utilisez la commande [az network vnet subnet show][az-network-vnet-subnet-show] pour récupérer l’ID de ressource du sous-réseau. Vous en aurez besoin à une étape ultérieure pour configurer une règle d’accès réseau.

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

Sortie :

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

### <a name="change-default-network-access-to-registry"></a>Changer l’accès réseau par défaut au registre

Par défaut, un registre de conteneurs Azure autorise les connexions à partir d’hôtes sur n’importe quel réseau. Pour limiter l’accès à un réseau sélectionné, changez l’action par défaut de manière à refuser l’accès. Substituez le nom de votre registre dans la commande [az acr update][az-acr-update] suivante :

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Ajouter une règle de réseau au registre

Utilisez la commande [az acr network-rule add][az-acr-network-rule-add] pour ajouter à votre registre une règle de réseau qui autorise l’accès à partir du sous-réseau de la machine virtuelle. Substituez le nom du registre de conteneur et l’ID de ressource du sous-réseau dans la commande suivante : 

```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --subnet <subnet-resource-id>
```

## <a name="verify-access-to-the-registry"></a>Vérifier l’accès au registre

Après avoir attendu quelques minutes que la configuration se mette à jour, vérifiez que la machine virtuelle peut accéder au registre de conteneurs. Établissez une connexion SSH à votre machine virtuelle, puis exécutez la commande [az acr login][az-acr-login] pour vous connecter à votre registre. 

```bash
az acr login --name mycontainerregistry
```

Vous pouvez effectuer des opérations de registre telles que l’exécution de `docker pull` pour tirer (pull) un exemple d’image du registre. Substituez une valeur d’image et d’étiquette appropriée pour votre registre, précédée du nom du serveur de connexion du registre (le tout en minuscules) :

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker tire (pull) correctement l’image sur la machine virtuelle.

Cet exemple montre que vous pouvez accéder au registre de conteneurs privé via la règle d’accès réseau. Toutefois, le registre est inaccessible à partir d’un hôte de connexion pour lequel aucune règle d’accès réseau n’est configurée. Si vous essayez de vous connecter à partir d’un autre hôte à l’aide de la commande `az acr login` ou `docker login`, la sortie est similaire à ce qui suit :

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Restaurer l’accès au registre par défaut

Pour restaurer le registre afin d’autoriser l’accès par défaut, supprimez toutes les règles de réseau qui sont configurées. Ensuite, définissez l’action par défaut pour autoriser l’accès. 

### <a name="remove-network-rules"></a>Supprimer les règles de réseau

Pour afficher la liste des règles de réseau configurées pour votre registre, exécutez la commande [az acr network-rule list][az-acr-network-rule-list] suivante :

```azurecli
az acr network-rule list --name mycontainerregistry 
```

Pour chaque règle configurée, exécutez la commande [az acr network-rule remove][az-acr-network-rule-remove] pour la supprimer. Par exemple :

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

### <a name="allow-access"></a>Autoriser l’accès

Substituez le nom de votre registre dans la commande [az acr update][az-acr-update] suivante :
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous avez créé toutes les ressources Azure dans le même groupe de ressources et que vous n’en avez plus besoin, vous pouvez éventuellement supprimer les ressources à l’aide d’une seule commande [az group delete](/cli/azure/group) :

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

* Pour restreindre l’accès à un registre à l’aide d’un point de terminaison privé dans un réseau virtuel, consultez [Configurer Azure Private Link pour un registre de conteneurs Azure](container-registry-private-link.md).
* Si vous devez configurer des règles d’accès au registre derrière un pare-feu client, consultez [Configurer des règles pour accéder à un registre de conteneurs Azure derrière un pare-feu](container-registry-firewall-access-rules.md).


<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_repository_list
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az_acr_network_rule_add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az_acr_network_rule_remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az_acr_network_rule_list
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-update]: /cli/azure/acr#az_acr_update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_show
[az-network-vnet-list]: /cli/azure/network/vnet/#az_network_vnet_list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
