---
title: Déployer un groupe de conteneurs sur un réseau virtuel Azure
description: Découvrez comment déployer des groupes de conteneurs dans un réseau virtuel Azure.
ms.topic: article
ms.date: 01/06/2020
ms.author: danlep
ms.openlocfilehash: 12260dcb43a675414d38cb5067b230832dd2d16b
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887954"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Déployer des instance de conteneur dans un réseau virtuel Azure

Un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) fournit un accès réseau privé et sécurisé à vos ressources Azure et locales. En déployant des groupes de conteneurs dans un réseau virtuel Azure, vos conteneurs peuvent communiquer en toute sécurité avec d’autres ressources dans le réseau virtuel.

Les groupes de conteneurs déployés dans un réseau virtuel Azure autorisent les scénarios suivants :

* Communication directe entre les groupes de conteneurs dans le même sous-réseau
* Envoyer la sortie d’une charge de travail [basée sur des tâches](container-instances-restart-policy.md) à partir d’instances de conteneur à une base de données dans le réseau virtuel
* Récupérer le contenu des instances de conteneur à partir d’un [point de terminaison de service](../virtual-network/virtual-network-service-endpoints-overview.md) dans le réseau virtuel
* Communication de conteneurs avec des machines virtuelles dans le réseau virtuel
* Communication de conteneurs avec les ressources locales via un [passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [ExpressRoute](../expressroute/expressroute-introduction.md)

> [!IMPORTANT]
> Les déploiements de groupe de conteneurs sur un réseau virtuel sont généralement disponibles pour les charges de travail de production dans les régions suivantes uniquement : **USA Est, USA Centre Sud, USA Ouest 2**. Dans les autres régions où cette fonctionnalité peut être utilisée, les déploiements de réseaux virtuels sont actuellement en préversion ; leur disponibilité générale étant prévue dans un avenir proche. Les préversions sont à votre disposition, à condition que vous acceptiez les [conditions d’utilisation supplémentaires][terms-of-use]. 


## <a name="virtual-network-deployment-limitations"></a>Limitations concernant le déploiement de réseau virtuel

Certaines limitations s’appliquent lorsque vous déployez des groupes de conteneurs dans un réseau virtuel.

* Pour déployer des groupes de conteneurs dans un sous-réseau, le sous-réseau ne peut pas contenir d’autres types de ressources. Supprimez toutes les ressources d’un sous-réseau avant de déployer des groupes de conteneurs dans celui-ci, ou créez un sous-réseau.
* Vous ne pouvez pas utiliser une [identité managée](container-instances-managed-identity.md) dans un groupe de conteneurs déployé sur un réseau virtuel.
* En raison des ressources réseau supplémentaires impliquées, le déploiement d’un groupe de conteneurs sur un réseau virtuel est généralement plus lent que celui d’une instance de conteneur standard.

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

Les limites des ressources de conteneur peuvent différer des limites des instances de conteneur non connectées au réseau dans ces régions. Les conteneurs Linux sont actuellement les seuls pris en charge pour cette fonctionnalité. La prise en charge de Windows est prévue.

### <a name="unsupported-networking-scenarios"></a>Scénarios de mise en réseau non pris en charge 

* **Azure Load Balancer** - Le placement d’un équilibreur de charge Azure devant des instances de conteneurs dans un groupe de conteneurs en réseau n’est pas pris en charge.
* **Peering de réseau virtuel**
  * Le peering de réseaux virtuels ne fonctionne pas pour ACI si le réseau auquel le réseau virtuel ACI est appairé utilise un espace IP public. Le réseau appairé a besoin d’un espace IP privée RFC 1918 pour que le peering de réseaux virtuels fonctionne. 
  * Vous ne pouvez appairer votre réseau virtuel qu’à un autre réseau virtuel
* **Routage du trafic de réseau virtuel** : les itinéraires personnalisés peuvent pas être configurés sur des adresses IP publiques. Les itinéraires peuvent être configurés dans l’espace IP privé du sous-réseau délégué dans lequel les ressources ACI sont déployées. 
* **Groupes de sécurité réseau** - Les règles de sécurité de trafic sortant dans les groupes de sécurité réseau appliquées à un sous-réseau délégué à Azure Container Instances ne sont pas appliquées actuellement. 
* **Étiquette DNS ou adresse IP publique** - Les groupes de conteneurs déployés sur un réseau virtuel ne prennent actuellement pas en charge l’exposition de conteneurs directement sur Internet avec une adresse IP publique ou un nom de domaine complet.
* **Résolution de noms interne** - La résolution de noms pour les ressources Azure dans le réseau virtuel par le biais du système DNS Azure interne n’est pas prise en charge.

La **suppression de ressources réseau** requiert des [étapes supplémentaires](#delete-network-resources) après le déploiement de groupes de conteneurs dans le réseau virtuel.

## <a name="required-network-resources"></a>Ressources réseau requises

Trois ressources de réseau virtuel Azure sont requises pour déployer des groupes de conteneurs dans un réseau virtuel : le [réseau virtuel](#virtual-network) lui-même, un [sous-réseau délégué](#subnet-delegated) dans le réseau virtuel et un [profil réseau](#network-profile). 

### <a name="virtual-network"></a>Réseau virtuel

Un réseau virtuel définit l’espace d’adressage dans lequel vous créez un ou plusieurs sous-réseaux. Ensuite, vous déployez des ressources Azure (par exemple, des groupes de conteneurs) dans les sous-réseaux de votre réseau virtuel.

### <a name="subnet-delegated"></a>Sous-réseau (délégué)

Les sous-réseaux segmentent le réseau virtuel en espaces d’adressage distincts utilisables par les ressources Azure que vous placez dedans. Vous créez un ou plusieurs sous-réseaux dans un réseau virtuel.

Le sous-réseau que vous utilisez pour les groupes de conteneurs ne peut contenir que des groupes de conteneurs. Lorsque vous déployez d’abord un groupe de conteneurs dans un sous-réseau, Azure délègue ce sous-réseau à Azure Container Instances. Une fois délégué, le sous-réseau ne peut être utilisé que pour les groupes de conteneur. Si vous tentez de déployer des ressources autres que des groupes de conteneurs dans un sous-réseau délégué, l’opération échoue.

### <a name="network-profile"></a>Profil réseau

Un profil réseau est un modèle de configuration de réseau pour les ressources Azure. Il spécifie certaines propriétés réseau de la ressource, par exemple le sous-réseau dans lequel il doit être déployé. Lorsque vous utilisez pour la première fois la commande [az container create][az-container-create] pour déployer un groupe de conteneurs sur un sous-réseau (et donc un réseau virtuel), Azure crée un profil réseau pour vous. Vous pouvez ensuite utiliser ce profil réseau pour les déploiements futurs dans le sous-réseau. 

Pour utiliser un modèle Resource Manager, un fichier YAML ou une méthode de programmation pour déployer un groupe de conteneurs dans un sous-réseau, vous devez fournir l’ID de ressource Resource Manager complet d’un profil réseau. Vous pouvez utiliser un profil précédemment créé à l’aide de [az container create][az-container-create] ou créer un profil à l’aide d’un modèle Resource Manager (voir l’[exemple de modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) et la [référence](https://docs.microsoft.com/azure/templates/microsoft.network/networkprofiles)). Pour obtenir l’ID d’un profil précédemment créé, utilisez la commande [az network profile list][az-network-profile-list]. 

Dans le diagramme suivant, plusieurs groupes de conteneurs ont été déployés dans un sous-réseau délégué à Azure Container Instances. Une fois que vous avez déployé un groupe de conteneurs dans un sous-réseau, vous pouvez déployer d’autres groupes de conteneurs dans ce dernier en spécifiant le même profil réseau.

![Groupes de conteneurs dans un réseau virtuel][aci-vnet-01]

## <a name="deployment-scenarios"></a>Scénarios de déploiement

Vous pouvez utiliser [az container create][az-container-create] pour déployer des groupes de conteneurs dans un nouveau réseau virtuel et laisser Azure créer les ressources réseau nécessaires pour vous, ou les déployer dans un réseau virtuel existant. 

### <a name="new-virtual-network"></a>Nouveau réseau virtuel

Pour déployer dans un nouveau réseau virtuel et laisser Azure créer automatiquement les ressources réseau, spécifiez les éléments suivants lorsque vous exécutez [as container create][az-container-create] :

* Nom du réseau virtuel
* Préfixe d’adresse de réseau virtuel au format CIDR
* Nom du sous-réseau
* Préfixe d’adresse de sous-réseau au format CIDR

Le réseau virtuel et les préfixes d’adresse de sous-réseau spécifient les espaces d’adressage du réseau virtuel et du sous-réseau, respectivement. Ces valeurs sont représentées en notation CIDR (Classless Inter-domain Routing), par exemple `10.0.0.0/16`. Pour plus d’informations sur l’utilisation des sous-réseaux, consultez [Ajouter, modifier ou supprimer un sous-réseau de réseau virtuel](../virtual-network/virtual-network-manage-subnet.md).

Une fois que vous avez déployé votre premier groupe de conteneurs avec cette méthode, vous pouvez déployer dans le même sous-réseau en spécifiant les noms du réseau virtuel et du sous-réseau, ou le profil réseau créé automatiquement par Azure pour vous. Comme Azure délègue le sous-réseau à Azure Container Instances, vous ne pouvez déployer *que* des groupes de conteneurs dans le sous-réseau.

### <a name="existing-virtual-network"></a>Réseau virtuel existant

Pour déployer un groupe de conteneurs dans un réseau virtuel existant :

1. Créez un sous-réseau au sein de votre réseau virtuel existant, utilisez un sous-réseau existant dans lequel un groupe de conteneurs est déjà déployé, ou utilisez un sous-réseau existant vide de *toute* autre ressource.
1. Déployez un groupe de conteneurs avec [az container create][az-container-create] et spécifiez un des éléments suivants :
   * Nom du réseau virtuel et nom du sous-réseau
   * ID de ressource de réseau virtuel et ID de ressource de sous-réseau, ce qui permet d’utiliser un réseau virtuel d’un groupe de ressources différent
   * Nom ou ID du profil réseau que vous pouvez obtenir en utilisant [az network profile list][az-network-profile-list]

Lorsque vous déployez votre premier groupe de conteneurs dans un sous-réseau existant, Azure délègue ce sous-réseau à Azure Container Instances. Vous ne pouvez plus déployer des ressources autres que des groupes de conteneurs dans ce sous-réseau.

## <a name="deployment-examples"></a>Exemples de déploiements

Les sections suivantes décrivent comment déployer des groupes de conteneurs dans un réseau virtuel avec l’interface Azure CLI. Les exemples présentés correspondent à l’interpréteur de commandes **Bash**. Si vous préférez un autre interpréteur de commandes comme PowerShell ou l’invite de commande, modifiez les caractères de continuation de ligne en conséquence.

### <a name="deploy-to-a-new-virtual-network"></a>Déployer dans un nouveau réseau virtuel

Tout d’abord, déployez un groupe de conteneurs et spécifiez les paramètres d’un nouveau réseau virtuel et d’un nouveau sous-réseau. Lorsque vous spécifiez ces paramètres, Azure crée le réseau virtuel et le sous-réseau, délègue le sous-réseau à Azure Container instances et crée un profil réseau. Une fois ces ressources créées, votre groupe de conteneurs est déployé dans le sous-réseau.

Exécutez la commande [az container create][az-container-create] qui spécifie les paramètres d’un nouveau réseau virtuel et d’un nouveau sous-réseau. Vous devez fournir le nom d’un groupe de ressources ayant été créé dans une région où il est [possible](#virtual-network-deployment-limitations) de déployer un groupe de conteneurs sur un réseau virtuel. Cette commande déploie le conteneur Microsoft [aci-helloworld][aci-helloworld] public qui exécute un petit serveur web Node.js qui gère une page web statique. Dans la section suivante, vous allez déployer un deuxième groupe de conteneurs dans le même sous-réseau et tester la communication entre les deux instances de conteneur.

```azurecli
az container create \
    --name appcontainer \
    --resource-group myResourceGroup \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --vnet aci-vnet \
    --vnet-address-prefix 10.0.0.0/16 \
    --subnet aci-subnet \
    --subnet-address-prefix 10.0.0.0/24
```

Lorsque vous déployez dans un nouveau réseau virtuel avec cette méthode, l’opération peut prendre quelques minutes, le temps de créer les ressources réseau. Après le déploiement initial, les autres déploiements de groupe de conteneurs sont plus rapides.

### <a name="deploy-to-existing-virtual-network"></a>Déployer dans un réseau virtuel existant

Maintenant que vous avez déployé un groupe de conteneurs dans un nouveau réseau virtuel, déployez un deuxième groupe de conteneurs dans le même sous-réseau et vérifiez la communication entre les deux instances de conteneur.

Tout d’abord, obtenez l’adresse IP du premier groupe de conteneurs déployé, *appcontainer* :

```azurecli
az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
```

La sortie doit afficher l’adresse IP du groupe de conteneurs dans le sous-réseau privé :

```console
$ az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
10.0.0.4
```

Maintenant, attribuez à `CONTAINER_GROUP_IP` l’adresse IP que vous avez récupérée avec la commande `az container show` et exécutez la commande suivante `az container create`. Ce deuxième conteneur, *commchecker*, utilise une image Linux Alpine et exécute `wget` sur l’adresse IP du sous-réseau privé du premier groupe de conteneurs.

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-here>

az container create \
    --resource-group myResourceGroup \
    --name commchecker \
    --image alpine:3.5 \
    --command-line "wget $CONTAINER_GROUP_IP" \
    --restart-policy never \
    --vnet aci-vnet \
    --subnet aci-subnet
```

Après le déploiement de ce deuxième conteneur, consultez dans ses journaux d’activité la sortie de la commande `wget` qu’il a exécutée :

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Si le second conteneur a communiqué avec le premier, la sortie doit être similaire à celle-ci :

```console
$ az container logs --resource-group myResourceGroup --name commchecker
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

La sortie du journal doit montrer que `wget` a réussie à se connecter le fichier d’index et à le télécharger à partir du premier conteneur à l’aide de son adresse IP privée dans le sous-réseau local. Le trafic réseau entre les deux groupes de conteneurs est resté dans le réseau virtuel.

### <a name="deploy-to-existing-virtual-network---yaml"></a>Déployer dans un réseau virtuel existant - YAML

Vous pouvez également déployer un groupe de conteneurs sur un réseau virtuel existant à l’aide d’un fichier YAML, d’un modèle Resource Manager ou d’une autre méthode de programmation, comme le kit de développement logiciel (SDK) Python. Pour déployer dans un sous-réseau d’un réseau virtuel, spécifiez plusieurs propriétés supplémentaires dans le fichier YAML :

* `ipAddress`: paramètres d’adresse IP du groupe de conteneurs.
  * `ports`: ports à ouvrir, le cas échéant.
  * `protocol`: protocole (TCP ou UDP) du port ouvert.
* `networkProfile`: spécifie les paramètres réseau tels que le réseau virtuel et le sous-réseau d’une ressource Azure.
  * `id`: ID complet de ressource Resource Manager du `networkProfile`.

Pour déployer un groupe de conteneurs dans un réseau virtuel avec un fichier YAML, vous devez d’abord obtenir l’ID du profil réseau. Exécutez la commande [az network profile list][az-network-profile-list], en spécifiant le nom du groupe de ressources qui contient votre réseau virtuel et le sous-réseau délégué.

``` azurecli
az network profile list --resource-group myResourceGroup --query [0].id --output tsv
```

La sortie de la commande affiche l’ID de ressource complet du profil réseau :

```console
$ az network profile list --resource-group myResourceGroup --query [0].id --output tsv
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Une fois que vous avez l’ID du profil réseau, copiez le fichier YAML suivant dans un nouveau fichier nommé *vnet-deploy-aci.yaml*. Sous `networkProfile`, remplacez la valeur `id` par l’ID que vous venez de récupérer, puis enregistrez le fichier. Ce fichier YAML crée un groupe de conteneurs nommé *appcontaineryaml* dans votre réseau virtuel.

```YAML
apiVersion: '2018-09-01'
location: westus
name: appcontaineryaml
properties:
  containers:
  - name: appcontaineryaml
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  ipAddress:
    type: Private
    ports:
    - protocol: tcp
      port: '80'
  networkProfile:
    id: /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Déployez le groupe de conteneurs avec la commande [az container create][az-container-create], en spécifiant le nom du fichier YAML dans le paramètre `--file` :

```azurecli
az container create --resource-group myResourceGroup --file vnet-deploy-aci.yaml
```

Une fois le déploiement effectué, exécutez la commande [az container show][az-container-show] pour afficher son état :

```console
$ az container show --resource-group myResourceGroup --name appcontaineryaml --output table
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

### <a name="delete-container-instances"></a>Supprimer des instances de conteneur

Lorsque vous avez fini d’utiliser les instances de conteneur que vous avez créées, supprimez-les avec les commandes suivantes :

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Supprimer des ressources réseau


> [!NOTE]
> Si une erreur survient lorsque vous tentez de supprimer le profil réseau, comptez 2 ou 3 jours pour que la plateforme résolve automatiquement le problème et tente à nouveau la suppression. Si le problème persiste, [ouvrez une demande de support](https://azure.microsoft.com/support/create-ticket/).

Cette fonctionnalité nécessite pour le moment plusieurs commandes supplémentaires pour supprimer les ressources réseau que vous avez créées précédemment. Si vous avez utilisé les exemples de commande dans les sections précédentes de cet article pour créer votre réseau virtuel et votre sous-réseau, vous pouvez utiliser le script suivant pour supprimer ces ressources réseau.

Avant d’exécuter le script, attribuez à la variable `RES_GROUP` le nom du groupe de ressources contenant le réseau virtuel et le sous-réseau à supprimer. Mettez à jour le nom du réseau virtuel si vous n’avez pas utilisé le nom `aci-vnet` suggéré précédemment. Le script est mis en forme pour l’interpréteur de commandes Bash. Si vous préférez un autre interpréteur de commandes, PowerShell ou l’invite de commande, vous devrez ajuster les variables et les accesseurs en conséquence.

> [!WARNING]
> Ce script supprime les ressources ! Le réseau virtuel et tous les sous-réseaux qu’il contient sont supprimés. Assurez-vous de n’avoir plus besoin *des* ressources du réseau virtuel, y compris des sous-réseaux qu’il contient, avant d’exécuter ce script. Une fois supprimées, **ces ressources sont irrécupérables**.

```azurecli
# Replace <my-resource-group> with the name of your resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>Étapes suivantes

Pour déployer un réseau virtuel, un sous-réseau, un profil réseau et un groupe de conteneurs nouveaux à l’aide d’un modèle Resource Manager, consultez [Créer un groupe de conteneurs Azure avec un réseau virtuel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
).

Plusieurs ressources et fonctionnalités de réseau virtuel ont été abordées dans cet article, bien que brièvement. La documentation sur le réseau virtuel Microsoft Azure couvre largement les points suivants :

* [Réseau virtuel](../virtual-network/manage-virtual-network.md)
* [Sous-réseau](../virtual-network/virtual-network-manage-subnet.md)
* [Points de terminaison de service](../virtual-network/virtual-network-service-endpoints-overview.md)
* [Passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [ExpressRoute](../expressroute/expressroute-introduction.md)

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
