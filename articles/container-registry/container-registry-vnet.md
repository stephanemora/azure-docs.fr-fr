---
title: Restreindre l’accès à l’aide d’un réseau virtuel
description: Autorisez l’accès à un registre de conteneurs Azure uniquement à partir de ressources dans un réseau virtuel Azure ou de plages d’adresses IP publiques.
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: a6b89b074c25ea0948597ede7e5681b100c7f429
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454331"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Restreindre l’accès à un registre de conteneurs Azure à l’aide d’un réseau virtuel Azure ou de règles de pare-feu

Un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) fournit un accès réseau privé et sécurisé à vos ressources Azure et locales. En limitant l’accès à votre registre de conteneurs Azure privé à partir d’un réseau virtuel Azure, vous vous assurez que seules les ressources dans le réseau virtuel accèdent au registre. Pour les scénarios intersites, vous pouvez également configurer des règles de pare-feu pour autoriser l’accès au registre uniquement à partir d’adresses IP spécifiques.

Cet article présente deux scénarios pour configurer des règles d’accès réseau entrant sur un registre de conteneurs : à partir d’une machine virtuelle déployée dans un réseau virtuel ou à partir de l’adresse IP publique d’une machine virtuelle.

> [!IMPORTANT]
> Cette fonctionnalité est actuellement en préversion et certaines [limitations s’appliquent](#preview-limitations). Les préversions sont à votre disposition, à condition que vous acceptiez les [conditions d’utilisation supplémentaires][terms-of-use]. Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale.
>

Si vous devez plutôt configurer des règles d’accès pour permettre à des ressources d’atteindre un registre de conteneurs derrière un pare-feu, voir [Configurer des règles pour accéder à un registre de conteneurs Azure derrière un pare-feu](container-registry-firewall-access-rules.md).


## <a name="preview-limitations"></a>Limitations de la version préliminaire

* Seul un registre de conteneurs **Premium** peut être configuré avec des règles d’accès réseau. Pour plus d’informations sur les niveaux de service de registre, consultez [Références SKU Azure Container Registry](container-registry-skus.md). 

* Seul un cluster [Azure Kubernetes Service](../aks/intro-kubernetes.md) ou une [machine virtuelle](../virtual-machines/linux/overview.md) Azure peut être utilisé en tant qu’hôte pour accéder à un registre de conteneurs dans un réseau virtuel. *Les autres services Azure, y compris Azure Container Instances, ne sont pas pris en charge.*

* Les opérations [ACR Tasks](container-registry-tasks-overview.md) ne sont pas prises en charge dans un registre de conteneurs accessible par le biais d’un réseau virtuel.

* Chaque registre prend en charge un maximum de 100 règles de réseau virtuel.

## <a name="prerequisites"></a>Conditions préalables requises

* Pour utiliser les étapes Azure CLI décrites dans cet article, vous devez disposer d’Azure CLI version 2.0.58 ou ultérieure. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][azure-cli].

* Si vous ne disposez pas d’un registre de conteneurs, créez-en un (référence SKU Premium requise) et envoyez (push) un exemple d’image comme `hello-world` à partir de Docker Hub. Par exemple, utilisez le [portail Azure][quickstart-portal] ou [Azure CLI][quickstart-cli] pour créer un registre. 

* Si vous souhaitez restreindre l’accès au registre à l’aide d’un réseau virtuel dans un autre abonnement Azure, vous devez inscrire le fournisseur de ressources pour Azure Container Registry dans cet abonnement. Par exemple :

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

## <a name="about-network-rules-for-a-container-registry"></a>À propos des règles de réseau pour un registre de conteneurs

Par défaut, un registre de conteneurs Azure accepte les connexions via Internet à partir d’hôtes sur n’importe quel réseau. Avec un réseau virtuel, vous pouvez autoriser uniquement des ressources Azure telles qu’un cluster AKS ou une machine virtuelle Azure à accéder de manière sécurisée au registre, sans avoir à franchir une limite réseau. Vous pouvez également configurer des règles de pare-feu de réseau pour autoriser uniquement certaines plages d’adresses IP Internet publiques. 

Pour limiter l’accès à un registre, commencez par changer l’action par défaut du registre afin qu’il refuse toutes les connexions réseau. Ensuite, ajoutez des règles d’accès réseau. Les clients auxquels est accordé l’accès via les règles de réseau doivent continuer à [s’authentifier auprès du registre de conteneurs](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) et être autorisés à accéder aux données.

### <a name="service-endpoint-for-subnets"></a>Point de terminaison de service pour les sous-réseaux

Pour autoriser l’accès à partir d’un sous-réseau dans un réseau virtuel, vous devez ajouter un [point de terminaison de service](../virtual-network/virtual-network-service-endpoints-overview.md) pour le service Azure Container Registry. 

Les services multilocataires, comme Azure Container Registry, utilisent un seul ensemble d’adresses IP pour tous les clients. Un point de terminaison de service attribue un point de terminaison pour accéder à un registre. Ce point de terminaison donne au trafic une route optimale vers la ressource via le réseau principal Azure. Les identités du réseau virtuel et du sous-réseau sont également transmises avec chaque demande.

### <a name="firewall-rules"></a>Règles de pare-feu

Pour les règles de réseau IP, fournissez les plages d’adresses Internet autorisées à l’aide de la notation CIDR, par exemple, *16.17.18.0/24*, ou d’adresses IP individuelles, telles que *16.17.18.19*. Les règles de réseau IP sont autorisées uniquement pour les adresses IP Internet *publiques*. Les plages d’adresses IP réservées aux réseaux privés (comme défini dans RFC 1918) ne sont pas autorisées dans les règles IP.

## <a name="create-a-docker-enabled-virtual-machine"></a>Créer une machine virtuelle compatible Docker

Dans le cadre de cet article, utilisez une machine virtuelle Ubuntu compatible Docker pour accéder à un registre de conteneurs Azure. Pour utiliser l’authentification Azure Active Directory auprès du registre, installez également l’[interface de ligne de commande Azure][azure-cli] sur la machine virtuelle. Si vous disposez déjà d’une machine virtuelle, ignorez cette étape de création.

Vous pouvez utiliser le même groupe de ressources pour votre machine virtuelle et votre registre de conteneurs. Cette configuration simplifie le nettoyage à la fin, mais n’est pas nécessaire. Si vous choisissez de créer un groupe de ressources distinct pour la machine virtuelle et le réseau virtuel, exécutez [az group create][az-group-create]. L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *westcentralus* :

```azurecli
az group create --name myResourceGroup --location westus
```

À présent, déployez une machine virtuelle Azure Ubuntu par défaut avec [az vm create][az-vm-create]. L’exemple suivant crée une machine virtuelle nommée *myDockerVM* :

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Il faut quelques minutes pour que la machine virtuelle soit créée. Après l’exécution de la commande, notez la valeur de `publicIpAddress` qui s’affiche dans l’interface Azure CLI. Utilisez cette adresse pour établir des connexions SSH à la machine virtuelle et éventuellement pour une configuration ultérieure de règles de pare-feu.

### <a name="install-docker-on-the-vm"></a>Installer Docker sur la machine virtuelle

Établissez une connexion SSH avec la machine virtuelle dès qu’elle est en cours d’exécution. Remplacez *publicIpAddress* par l’adresse IP publique de votre machine virtuelle.

```bash
ssh azureuser@publicIpAddress
```

Exécutez la commande suivante pour installer Docker sur la machine virtuelle Ubuntu :

```bash
sudo apt install docker.io -y
```

Après l’installation, exécutez la commande suivante pour vérifier que Docker s’exécute correctement sur la machine virtuelle :

```bash
sudo docker run -it hello-world
```

Sortie :

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Installer l’interface de ligne de commande Microsoft Azure

Suivez les étapes de la section [Installer Azure CLI avec apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) pour installer Azure CLI sur votre machine virtuelle Ubuntu. Dans le cadre de cet article, veillez à installer la version 2.0.58 ou une version ultérieure.

Quittez la connexion SSH.

## <a name="allow-access-from-a-virtual-network"></a>Autoriser l’accès à partir d’un réseau virtuel

Dans cette section, configurez votre registre de conteneurs pour autoriser l’accès à partir d’un sous-réseau dans un réseau virtuel Azure. Les étapes équivalentes à l’aide de l’interface de ligne de commande Azure et du portail Azure sont fournies.

### <a name="allow-access-from-a-virtual-network---cli"></a>Autoriser l’accès à partir d’un réseau virtuel - CLI

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Ajouter un point de terminaison de service à un sous-réseau

Quand vous créez une machine virtuelle, Azure crée par défaut un réseau virtuel dans le même groupe de ressources. Le nom du réseau virtuel est basé sur le nom de la machine virtuelle. Par exemple, si vous nommez votre machine virtuelle *myDockerVM*, le nom de réseau virtuel par défaut est *myDockerVMVNET*, avec un sous-réseau nommé *myDockerVMSubnet*. Vérifiez cela dans le portail Azure ou à l’aide de la commande [az network vnet list][az-network-vnet-list] :

```azurecli
az network vnet list --resource-group myResourceGroup --query "[].{Name: name, Subnet: subnets[0].name}"
```

Sortie :

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
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

#### <a name="change-default-network-access-to-registry"></a>Changer l’accès réseau par défaut au registre

Par défaut, un registre de conteneurs Azure autorise les connexions à partir d’hôtes sur n’importe quel réseau. Pour limiter l’accès à un réseau sélectionné, changez l’action par défaut de manière à refuser l’accès. Substituez le nom de votre registre dans la commande [az acr update][az-acr-update] suivante :

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Ajouter une règle de réseau au registre

Utilisez la commande [az acr network-rule add][az-acr-network-rule-add] pour ajouter à votre registre une règle de réseau qui autorise l’accès à partir du sous-réseau de la machine virtuelle. Substituez le nom du registre de conteneur et l’ID de ressource du sous-réseau dans la commande suivante : 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

À présent, [vérifiez l’accès au registre](#verify-access-to-the-registry).

### <a name="allow-access-from-a-virtual-network---portal"></a>Autoriser l’accès à partir d’un réseau virtuel - Portail

#### <a name="add-service-endpoint-to-subnet"></a>Ajouter un point de terminaison de service à un sous-réseau

Quand vous créez une machine virtuelle, Azure crée par défaut un réseau virtuel dans le même groupe de ressources. Le nom du réseau virtuel est basé sur le nom de la machine virtuelle. Par exemple, si vous nommez votre machine virtuelle *myDockerVM*, le nom de réseau virtuel par défaut est *myDockerVMVNET*, avec un sous-réseau nommé *myDockerVMSubnet*.

Pour ajouter un point de terminaison de service pour Azure Container Registry à un sous-réseau :

1. Dans la zone de recherche située en haut du [portail Azure][azure-portal], entrez *réseaux virtuels*. Quand la mention **Réseaux virtuels** apparaît dans les résultats de recherche, sélectionnez-la.
1. Dans la liste des réseaux virtuels, sélectionnez le réseau virtuel où est déployée votre machine virtuelle, tel que *myDockerVMVNET*.
1. Sous **Paramètres**, sélectionnez **Sous-réseaux**.
1. Sélectionnez le sous-réseau où est déployée votre machine virtuelle, tel que *myDockerVMSubnet*.
1. Sous **Points de terminaison de service**, sélectionnez **Microsoft.ContainerRegistry**.
1. Sélectionnez **Enregistrer**.

![Ajouter un point de terminaison de service à un sous-réseau][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Configurer l’accès réseau pour le registre

Par défaut, un registre de conteneurs Azure autorise les connexions à partir d’hôtes sur n’importe quel réseau. Pour limiter l’accès au réseau virtuel :

1. Dans le portail, accédez à votre registre de conteneurs.
1. Sous **Paramètres**, sélectionnez **Pare-feu et réseaux virtuels**.
1. Pour refuser l’accès par défaut, choisissez d’autoriser l’accès à partir de **Réseaux sélectionnés**. 
1. Sélectionnez **Ajouter un réseau virtuel existant**, puis sélectionnez le réseau virtuel et le sous-réseau que vous avez configurés avec un point de terminaison de service. Sélectionnez **Ajouter**.
1. Sélectionnez **Enregistrer**.

![Configurer le réseau virtuel pour le registre de conteneurs][acr-vnet-portal]

À présent, [vérifiez l’accès au registre](#verify-access-to-the-registry).

## <a name="allow-access-from-an-ip-address"></a>Autoriser l’accès à partir d’une adresse IP

Dans cette section, configurez votre registre de conteneurs pour autoriser l’accès à partir d’une plage ou d’une adresse IP spécifique. Les étapes équivalentes à l’aide de l’interface de ligne de commande Azure et du portail Azure sont fournies.

### <a name="allow-access-from-an-ip-address---cli"></a>Autoriser l’accès à partir d’une adresse IP - CLI

#### <a name="change-default-network-access-to-registry"></a>Changer l’accès réseau par défaut au registre

Si ce n’est déjà fait, mettez à jour la configuration du registre pour refuser l’accès par défaut. Substituez le nom de votre registre dans la commande [az acr update][az-acr-update] suivante :

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>Supprimer la règle de réseau du registre

Si vous avez ajouté une règle de réseau pour autoriser l’accès à partir du sous-réseau de la machine virtuelle, supprimez le point de terminaison de service du sous-réseau et la règle de réseau. Dans la commande [az acr network-rule remove][az-acr-network-rule-remove], substituez le nom du registre de conteneur et l’ID de ressource du sous-réseau que vous avez récupéré à l’étape précédente : 

```azurecli
# Remove service endpoint

az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints ""

# Remove network rule

az acr network-rule remove --name mycontainerregistry --subnet <subnet-resource-id>
```

#### <a name="add-network-rule-to-registry"></a>Ajouter une règle de réseau au registre

Utilisez la commande [az acr network-rule add][az-acr-network-rule-add] pour ajouter à votre registre une règle de réseau qui autorise l’accès à partir de l’adresse IP de la machine virtuelle. Substituez le nom du registre de conteneurs et l’adresse IP publique de la machine virtuelle dans la commande suivante.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

À présent, [vérifiez l’accès au registre](#verify-access-to-the-registry).

### <a name="allow-access-from-an-ip-address---portal"></a>Autoriser l’accès à partir d’une adresse IP - Portail

#### <a name="remove-existing-network-rule-from-registry"></a>Supprimer la règle de réseau existante du registre

Si vous avez ajouté une règle de réseau pour autoriser l’accès à partir du sous-réseau de la machine virtuelle, supprimez-la. Ignorez cette section si vous souhaitez accéder au registre à partir d’une autre machine virtuelle.

* Mettez à jour les paramètres de sous-réseau afin de supprimer le point de terminaison de service du sous-réseau pour Azure Container Registry. 

  1. Dans le [portail Azure][azure-portal], accédez au réseau virtuel dans lequel votre machine virtuelle est déployée.
  1. Sous **Paramètres**, sélectionnez **Sous-réseaux**.
  1. Sélectionnez le sous-réseau où est déployée votre machine virtuelle.
  1. Sous **Points de terminaison de service**, décochez la case associée à **Microsoft.ContainerRegistry**. 
  1. Sélectionnez **Enregistrer**.

* Supprimez la règle de réseau qui autorise le sous-réseau à accéder au registre.

  1. Dans le portail, accédez à votre registre de conteneurs.
  1. Sous **Paramètres**, sélectionnez **Pare-feu et réseaux virtuels**.
  1. Sous **Réseaux virtuels**, sélectionnez le nom du réseau virtuel, puis sélectionnez **Supprimer**.
  1. Sélectionnez **Enregistrer**.

#### <a name="add-network-rule-to-registry"></a>Ajouter une règle de réseau au registre

1. Dans le portail, accédez à votre registre de conteneurs.
1. Sous **Paramètres**, sélectionnez **Pare-feu et réseaux virtuels**.
1. Si ce n’est déjà fait, choisissez d’autoriser l’accès à partir de **Réseaux sélectionnés**. 
1. Sous **Réseaux virtuels**, vérifiez qu’aucun réseau n’est sélectionné.
1. Sous **Pare-feu**, entrez l’adresse IP publique d’une machine virtuelle. Ou bien, entrez en notation CIDR une plage d’adresses qui contient l’adresse IP de la machine virtuelle.
1. Sélectionnez **Enregistrer**.

![Configurer la règle de pare-feu pour le registre de conteneurs][acr-vnet-firewall-portal]

À présent, [vérifiez l’accès au registre](#verify-access-to-the-registry).

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

Cet exemple montre que vous pouvez accéder au registre de conteneurs privé via la règle d’accès réseau. Toutefois, le registre n’est pas accessible à partir d’un hôte de connexion différent pour lequel n’est pas configurée une règle d’accès réseau. Si vous essayez de vous connecter à partir d’un autre hôte à l’aide de la commande `az acr login` ou `docker login`, la sortie est similaire à ce qui suit :

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Restaurer l’accès au registre par défaut

Pour restaurer le registre afin d’autoriser l’accès par défaut, supprimez toutes les règles de réseau qui sont configurées. Ensuite, définissez l’action par défaut pour autoriser l’accès. Les étapes équivalentes à l’aide de l’interface de ligne de commande Azure et du portail Azure sont fournies.

### <a name="restore-default-registry-access---cli"></a>Restaurer l’accès au registre par défaut - CLI

#### <a name="remove-network-rules"></a>Supprimer les règles de réseau

Pour afficher la liste des règles de réseau configurées pour votre registre, exécutez la commande [az acr network-rule list][az-acr-network-rule-list] suivante :

```azurecli
az acr network-rule list--name mycontainerregistry 
```

Pour chaque règle configurée, exécutez la commande [az acr network-rule remove][az-acr-network-rule-remove] pour la supprimer. Par exemple :

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet

# Remove a rule that allows access for an IP address or CIDR range such as 23.45.1.0/24.

az acr network-rule remove \
  --name mycontainerregistry \
  --ip-address 23.45.1.0/24
```

#### <a name="allow-access"></a>Autoriser l’accès

Substituez le nom de votre registre dans la commande [az acr update][az-acr-update] suivante :
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Restaurer l’accès au registre par défaut - Portail


1. Dans le portail, accédez à votre registre de conteneurs et sélectionnez **Pare-feu et réseaux virtuels**.
1. Sous **Réseaux virtuels**, sélectionnez chaque réseau virtuel, puis sélectionnez **Supprimer**.
1. Sous **Pare-feu**, sélectionnez chaque plage d’adresses, puis sélectionnez l’icône de suppression.
1. Sous **Autoriser l’accès depuis**, cliquez sur **Tous les réseaux**. 
1. Sélectionnez **Enregistrer**.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous avez créé toutes les ressources Azure dans le même groupe de ressources et que vous n’en avez plus besoin, vous pouvez éventuellement supprimer les ressources à l’aide d’une seule commande [az group delete](/cli/azure/group) :

```azurecli
az group delete --name myResourceGroup
```

Pour supprimer vos ressources dans le portail, accédez au groupe de ressources myResourceGroup. Une fois le groupe de ressources chargé, cliquez sur **Supprimer le groupe de ressources** pour supprimer le groupe de ressources et les ressources à cet endroit.

## <a name="next-steps"></a>Étapes suivantes

Plusieurs ressources et fonctionnalités de réseau virtuel ont été abordées dans cet article, bien que brièvement. La documentation sur le réseau virtuel Microsoft Azure couvre largement les points suivants :

* [Réseau virtuel](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network)
* [Sous-réseau](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
* [Points de terminaison de service](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png
[acr-vnet-portal]: ./media/container-registry-vnet/acr-vnet-portal.png
[acr-vnet-firewall-portal]: ./media/container-registry-vnet/acr-vnet-firewall-portal.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/r/microsoft/aci-helloworld/
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
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
