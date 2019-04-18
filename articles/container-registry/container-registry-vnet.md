---
title: Déployer un Azure container registry dans un réseau virtuel
description: Autoriser l’accès à un Registre de conteneurs Azure uniquement à partir de ressources dans un réseau virtuel Azure ou de plages d’adresses IP publiques.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.openlocfilehash: 15b67218b129b5e017e67651587c389af412d7a1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59268408"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Restreindre l’accès à un Registre de conteneurs Azure à l’aide d’un réseau virtuel Azure ou des règles de pare-feu

[Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) fournit la mise en réseau privé et sécurisé pour votre Azure et les ressources locales. En déployant votre Registre de conteneurs Azure privé dans un réseau virtuel Azure, vous pouvez vous assurer que seules les ressources dans le réseau virtuel d’accéder au Registre. Pour des scénarios intersite, vous pouvez également configurer des règles de pare-feu pour autoriser l’accès de Registre uniquement à partir d’adresses IP spécifiques.

Cet article présente deux scénarios pour créer des règles d’accès réseau pour limiter l’accès à un Registre de conteneurs Azure : à partir d’un ordinateur virtuel déployé dans le même réseau, ou à partir de l’adresse IP d’une machine virtuelle.

> [!IMPORTANT]
> Cette fonctionnalité est actuellement en préversion et certaines [limitations s’appliquent](#preview-limitations). Les préversions sont à votre disposition, à la condition d’accepter les [conditions d’utilisation supplémentaires][terms-of-use]. Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale.
>

## <a name="preview-limitations"></a>Limitations de la version préliminaire

* Uniquement une **Premium** Registre de conteneurs peut être configuré avec les règles d’accès réseau. Pour plus d’informations sur les niveaux de service de Registre, consultez [références SKU Azure Container Registry](container-registry-skus.md). 

* Uniquement une [Azure Kubernetes Service](../aks/intro-kubernetes.md) cluster ou Azure [machine virtuelle](../virtual-machines/linux/overview.md) peut être utilisé en tant qu’hôte pour accéder à un Registre de conteneurs dans un réseau virtuel. *Autres services Azure, y compris Azure Container Instances ne sont pas actuellement pris en charge.*

* [Tâches de l’ACR](container-registry-tasks-overview.md) opérations ne sont pas actuellement prises en charge dans un Registre de conteneurs déployé sur un réseau virtuel.

* Chaque registre prend en charge un maximum de 100 règles de réseau virtuel.

## <a name="prerequisites"></a>Conditions préalables

* Pour utiliser Azure CLI décrites dans cet article, Azure CLI version 2.0.58 ou ultérieure est requise. Si vous devez installer ou mettre à niveau, consultez [Installer Azure CLI 2.0][azure-cli].

* Si vous ne disposez pas d’un Registre de conteneurs, créez-le (référence SKU Premium requis) et envoyer un exemple d’image comme `hello-world` du Hub Docker. Par exemple, utiliser le [portail Azure] [ quickstart-portal] ou [Azure CLI] [ quickstart-cli] pour créer un Registre. 

## <a name="about-network-rules-for-a-container-registry"></a>Sur les règles de réseau pour un Registre de conteneurs

Un Registre de conteneurs Azure par défaut accepte les connexions via internet à partir d’hôtes sur n’importe quel réseau. Avec un réseau virtuel, vous pouvez autoriser uniquement les ressources Azure comme un cluster AKS ou machine virtuelle Azure à accéder en toute sécurité le Registre, sans dépasser une limite réseau. Vous pouvez également configurer des règles de pare-feu de réseau à la liste blanche d’adresses internet publiques spécifiques des plages d’adresses IP. 

Pour limiter l’accès à un Registre, d’abord modifier l’action par défaut du Registre afin qu’il refuse toutes les connexions réseau. Ensuite, ajoutez des règles d’accès réseau. Les clients accordé un accès via les règles de réseau doivent continuer à [s’authentifier auprès du Registre de conteneurs](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) et être autorisé à accéder aux données.

### <a name="service-endpoint-for-subnets"></a>Point de terminaison de service pour les sous-réseaux

Pour autoriser l’accès à partir d’un sous-réseau dans un réseau virtuel, vous devez ajouter un [point de terminaison de service](../virtual-network/virtual-network-service-endpoints-overview.md) pour le service Azure Container Registry. 

Services de l’architecture mutualisées, comme Azure Container Registry, utilisent un seul ensemble d’adresses IP pour tous les clients. Un point de terminaison de service affecte un point de terminaison pour accéder à un Registre. Ce point de terminaison donne le trafic une route optimale à la ressource sur le réseau principal Azure. Les identités du réseau virtuel et du sous-réseau sont également transmises avec chaque demande.

### <a name="firewall-rules"></a>Règles de pare-feu

Pour les règles de réseau IP, fournir internet autorisées des plages d’adresses à l’aide de la notation CIDR comme *16.17.18.0/24* ou une adresse IP individuelle traite comme *16.17.18.19*. Règles de réseau IP sont autorisées uniquement pour *public* des adresses IP internet. Plages d’adresses IP réservées aux réseaux privés (tel que défini dans le document RFC 1918) ne sont pas autorisés dans les règles IP.

## <a name="create-a-docker-enabled-virtual-machine"></a>Créer une machine virtuelle prenant en charge de Docker

Pour cet article, utilisez une VM Ubuntu prenant en charge de Docker pour accéder à un Registre de conteneurs Azure. Pour utiliser l’authentification Azure Active Directory dans le Registre, installez également le [Azure CLI] [ azure-cli] sur la machine virtuelle. Si vous disposez déjà d’une machine virtuelle, ignorez cette étape de la création.

Vous pouvez utiliser le même groupe de ressources pour votre machine virtuelle et votre Registre de conteneurs. Ce programme d’installation simplifie le nettoyage à la fin, mais n’est pas nécessaire. Si vous choisissez de créer un groupe de ressources distinct pour la machine virtuelle et le réseau virtuel, exécutez [az groupe créer][az-group-create]. L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* dans le *westcentralus* emplacement :

```azurecli
az group create --name myResourceGroup --location westus
```

Une valeur par défaut à présent déployer machine virtuelle Ubuntu Azure avec [créer az vm][az-vm-create]. L’exemple suivant crée une machine virtuelle nommée *myDockerVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Il faut quelques minutes pour que la machine virtuelle soit créée. Après l’exécution de la commande, notez la valeur de `publicIpAddress` qui s’affiche dans l’interface Azure CLI. Utilisez cette adresse pour établir des connexions SSH à la machine virtuelle et éventuellement pour une installation ultérieure de règles de pare-feu.

### <a name="install-docker-on-the-vm"></a>Installer Docker sur la machine virtuelle

Établissez une connexion SSH avec la machine virtuelle dès qu’elle est en cours d’exécution. Remplacez *publicIpAddress* par l’adresse IP publique de votre machine virtuelle.

```bash
ssh azureuser@publicIpAddress
```

Exécutez la commande suivante pour installer Docker sur la VM Ubuntu :

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

Suivez les étapes de la section [Installer Azure CLI avec apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) pour installer Azure CLI sur votre machine virtuelle Ubuntu. Pour cet article, veillez à installer la version 2.0.58 ou version ultérieure.

Quittez la connexion SSH.

## <a name="allow-access-from-a-virtual-network"></a>Autoriser l’accès à partir d’un réseau virtuel

Dans cette section, configurez votre Registre de conteneurs pour autoriser l’accès à partir d’un sous-réseau dans un réseau virtuel Azure. Les étapes équivalentes à l’aide de l’interface CLI Azure et le portail Azure sont fournis.

### <a name="allow-access-from-a-virtual-network---cli"></a>Autoriser l’accès à partir d’un réseau virtuel - CLI

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Ajoutez un point de terminaison de service à un sous-réseau

Lorsque vous créez une machine virtuelle, Azure par défaut crée un réseau virtuel dans le même groupe de ressources. Le nom du réseau virtuel est basé sur le nom de la machine virtuelle. Par exemple, si vous nommez votre machine virtuelle *myDockerVM*, le nom de réseau virtuel par défaut est *myDockerVMVNET*, avec un sous-réseau nommé *myDockerVMSubnet*. Vérifiez cela dans le portail Azure ou à l’aide de la [liste de réseau virtuel az réseau] [ az-network-vnet-list] commande :

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

Utilisez le [mise à jour de sous-réseau de réseau virtuel az réseau] [ az-network-vnet-subnet-update] commande pour ajouter un **Microsoft.ContainerRegistry** le point de terminaison de service à votre sous-réseau. Remplacez les noms de votre réseau virtuel et un sous-réseau dans la commande suivante :

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Utilisez le [az réseau réseau virtuel sous-réseau afficher] [ az-network-vnet-subnet-show] commande pour récupérer l’ID de ressource du sous-réseau. Vous avez besoin dans une étape ultérieure pour configurer une règle d’accès réseau.

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

#### <a name="change-default-network-access-to-registry"></a>Modifier l’accès réseau par défaut dans le Registre

Par défaut, un Registre de conteneurs Azure autorise les connexions à partir d’hôtes sur n’importe quel réseau. Pour limiter l’accès à un réseau sélectionné, modifier l’action par défaut pour refuser l’accès. Remplacez le nom de votre Registre dans l’exemple suivant [mise à jour de az acr] [ az-acr-update] commande :

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Ajouter la règle de réseau au Registre

Utilisez le [az acr-règle de réseau ajouter] [ az-acr-network-rule-add] commande pour ajouter une règle de réseau à votre Registre qui permet l’accès à partir sous-réseau de la machine virtuelle. Remplacez le nom du Registre de conteneur et l’ID de ressource du sous-réseau dans la commande suivante : 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

Continuer à [vérifier l’accès au Registre](#verify-access-to-the-registry).

### <a name="allow-access-from-a-virtual-network---portal"></a>Autoriser l’accès à partir d’un réseau virtuel - portail

#### <a name="add-service-endpoint-to-subnet"></a>Ajouter le point de terminaison de service au sous-réseau

Lorsque vous créez une machine virtuelle, Azure par défaut crée un réseau virtuel dans le même groupe de ressources. Le nom du réseau virtuel est basé sur le nom de la machine virtuelle. Par exemple, si vous nommez votre machine virtuelle *myDockerVM*, le nom de réseau virtuel par défaut est *myDockerVMVNET*, avec un sous-réseau nommé *myDockerVMSubnet*.

Pour ajouter un point de terminaison de service pour Azure Container Registry à un sous-réseau :

1. Dans la zone de recherche en haut de la [Azure portal][azure-portal], entrez *réseaux virtuels*. Quand la mention **Réseaux virtuels** apparaît dans les résultats de recherche, sélectionnez-la.
1. Dans la liste des réseaux virtuels, sélectionnez le réseau virtuel où est déployée votre machine virtuelle, tel que *myDockerVMVNET*.
1. Sous **paramètres**, sélectionnez **sous-réseaux**.
1. Sélectionnez le sous-réseau où est déployée votre machine virtuelle, tel que *myDockerVMSubnet*.
1. Sous **points de terminaison de Service**, sélectionnez **Microsoft.ContainerRegistry**.
1. Sélectionnez **Enregistrer**.

![Ajouter le point de terminaison de service au sous-réseau][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Configurer l’accès réseau pour le Registre

Par défaut, un Registre de conteneurs Azure autorise les connexions à partir d’hôtes sur n’importe quel réseau. Pour limiter l’accès au réseau virtuel :

1. Dans le portail, accédez à votre Registre de conteneurs.
1. Sous **paramètres**, sélectionnez **pare-feu et réseaux virtuels**.
1. Pour refuser l’accès par défaut, choisissez d’autoriser l’accès à partir de **Réseaux sélectionnés**. 
1. Sélectionnez **ajouter un réseau virtuel existant**, puis sélectionnez le réseau virtuel et le sous-réseau que vous avez configuré avec un point de terminaison de service. Sélectionnez **Ajouter**.
1. Sélectionnez **Enregistrer**.

![Configurer un réseau virtuel pour le Registre de conteneurs][acr-vnet-portal]

Continuer à [vérifier l’accès au Registre](#verify-access-to-the-registry).

## <a name="allow-access-from-an-ip-address"></a>Autoriser l’accès à partir d’une adresse IP

Dans cette section, configurez votre Registre de conteneurs pour autoriser l’accès à partir d’un sous-réseau dans un réseau virtuel Azure. Les étapes équivalentes à l’aide de l’interface CLI Azure et le portail Azure sont fournis.

### <a name="allow-access-from-an-ip-address---cli"></a>Autoriser l’accès à partir d’une adresse IP - CLI

#### <a name="change-default-network-access-to-registry"></a>Modifier l’accès réseau par défaut dans le Registre

Si vous n’avez pas déjà fait, mettez à jour la configuration du Registre pour refuser l’accès par défaut. Remplacez le nom de votre Registre dans l’exemple suivant [mise à jour de az acr] [ az-acr-update] commande :

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>Supprimez la règle de réseau à partir du Registre

Si vous avez ajouté précédemment une règle de réseau pour autoriser l’accès à partir du sous-réseau de la machine virtuelle, supprimez le point de terminaison de service du sous-réseau et de la règle de réseau. Remplacez le nom du Registre de conteneur et l’ID de ressource du sous-réseau que vous avez récupéré à l’étape précédente dans le [supprimer de la règle de réseau az acr] [ az-acr-network-rule-remove] commande : 

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

#### <a name="add-network-rule-to-registry"></a>Ajouter la règle de réseau au Registre

Utilisez le [az acr-règle de réseau ajouter] [ az-acr-network-rule-add] commande pour ajouter une règle de réseau à votre Registre qui autorise l’accès à partir de l’adresse IP de la machine virtuelle. Remplacez le nom du Registre de conteneur et l’adresse IP publique de la machine virtuelle dans la commande suivante.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

Continuer à [vérifier l’accès au Registre](#verify-access-to-the-registry).

### <a name="allow-access-from-an-ip-address---portal"></a>Autoriser l’accès à partir d’une adresse IP - portail

#### <a name="remove-existing-network-rule-from-registry"></a>Supprimer la règle de réseau existante à partir du Registre

Si vous avez ajouté précédemment une règle de réseau pour autoriser l’accès à partir du sous-réseau de la machine virtuelle, supprimez la règle existante. Ignorer cette section si vous souhaitez accéder au Registre à partir d’une autre machine virtuelle.

* Mettre à jour les paramètres de sous-réseau pour supprimer le point de terminaison de service du sous-réseau pour Azure Container Registry. 

  1. Dans le [Azure portal][azure-portal], accédez au réseau virtuel dans lequel votre machine virtuelle est déployée.
  1. Sous **paramètres**, sélectionnez **sous-réseaux**.
  1. Sélectionnez le sous-réseau sur lequel votre machine virtuelle est déployée.
  1. Sous **points de terminaison de Service**, la case à cocher pour supprimer **Microsoft.ContainerRegistry**. 
  1. Sélectionnez **Enregistrer**.

* Supprimer la règle de réseau qui autorise le sous-réseau d’accéder au Registre.

  1. Dans le portail, accédez à votre Registre de conteneurs.
  1. Sous **paramètres**, sélectionnez **pare-feu et réseaux virtuels**.
  1. Sous **réseaux virtuels**, sélectionnez le nom du réseau virtuel, puis **supprimer**.
  1. Sélectionnez **Enregistrer**.

#### <a name="add-network-rule-to-registry"></a>Ajouter la règle de réseau au Registre

1. Dans le portail, accédez à votre Registre de conteneurs.
1. Sous **paramètres**, sélectionnez **pare-feu et réseaux virtuels**.
1. Si vous n’avez pas déjà fait, choisissez d’autoriser l’accès à partir de **réseaux sélectionnés**. 
1. Sous **réseaux virtuels**, vérifiez aucun réseau n’est sélectionné.
1. Sous **pare-feu**, entrez l’adresse IP publique d’une machine virtuelle. Ou bien, entrez une plage d’adresses en notation CIDR qui contient l’adresse IP de la machine virtuelle.
1. Sélectionnez **Enregistrer**.

![Configurer la règle de pare-feu pour le Registre de conteneurs][acr-vnet-firewall-portal]

Continuer à [vérifier l’accès au Registre](#verify-access-to-the-registry).

## <a name="verify-access-to-the-registry"></a>Vérifier l’accès au Registre

Après avoir attendu quelques minutes mettre à jour la configuration, vérifiez que la machine virtuelle peut accéder au Registre de conteneur. Établissez une connexion SSH à votre machine virtuelle, puis exécutez le [compte de connexion az acr] [ az-acr-login] commande pour vous connecter à votre Registre. 

```bash
az acr login --name mycontainerregistry
```

Vous pouvez effectuer les opérations de Registre telle que l’exécution `docker pull` pour extraire une image d’exemple à partir du Registre. Remplacez par une valeur d’image et étiquette appropriée pour votre Registre, le préfixe le nom de serveur de connexion du Registre (tout en minuscules) :

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker extrait avec succès l’image à la machine virtuelle.

Cet exemple montre que vous pouvez accéder le Registre de conteneurs privé via la règle d’accès réseau. Toutefois, le Registre ne sont pas accessibles à partir d’un hôte de connexion différente qui n’a pas une règle d’accès réseau configurée. Si vous essayez de vous connecter à partir d’un autre hôte à l’aide de la `az acr login` commande ou `docker login` commande, la sortie est similaire à ce qui suit :

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Restaurer l’accès de Registre par défaut

Pour restaurer le Registre pour autoriser l’accès par défaut, supprimez toutes les règles de réseau qui sont configurés. Puis définissez l’action par défaut pour autoriser l’accès. Les étapes équivalentes à l’aide de l’interface CLI Azure et le portail Azure sont fournis.

### <a name="restore-default-registry-access---cli"></a>Restaurer l’accès de Registre par défaut - CLI

#### <a name="remove-network-rules"></a>Supprimer des règles de réseau

Pour afficher la liste de règles réseau configurées pour votre Registre, exécutez la commande suivante [liste de règle de réseau az acr] [ az-acr-network-rule-list] commande :

```azurecli
az acr network-rule list--name mycontainerregistry 
```

Pour chaque règle est configurée, exécutez le [supprimer de la règle de réseau az acr] [ az-acr-network-rule-remove] commande pour la supprimer. Par exemple : 

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

Remplacez le nom de votre Registre dans l’exemple suivant [mise à jour de az acr] [ az-acr-update] commande :
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Restauration par défaut accès au Registre - portail


1. Dans le portail, accédez à votre Registre de conteneurs et sélectionnez **pare-feu et réseaux virtuels**.
1. Sous **réseaux virtuels**, sélectionnez chaque réseau virtuel, puis **supprimer**.
1. Sous **pare-feu**, sélectionnez chaque plage d’adresses, puis sélectionnez l’icône de suppression.
1. Sous **autoriser l’accès à partir de**, sélectionnez **tous les réseaux**. 
1. Sélectionnez **Enregistrer**.

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous avez créé toutes les ressources Azure dans la même ressource de groupe et ne sont plus nécessaires, vous pouvez éventuellement supprimer les ressources à l’aide d’un seul [suppression du groupe az](/cli/azure/group) commande :

```azurecli
az group delete --name myResourceGroup
```

Pour nettoyer vos ressources dans le portail, accédez au groupe de ressources myResourceGroup. Une fois le groupe de ressources est chargé, cliquez sur **supprimer le groupe de ressources** pour supprimer le groupe de ressources et les ressources qui y sont stockés.

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
