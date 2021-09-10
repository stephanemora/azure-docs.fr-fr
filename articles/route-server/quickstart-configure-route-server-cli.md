---
title: 'Démarrage rapide : Créer et configurer Route Server à l’aide d’Azure CLI'
description: Dans ce guide de démarrage rapide, vous apprenez à créer et à configurer un serveur de routage à l’aide d’Azure CLI.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 09/01/2021
ms.author: duau
ms.openlocfilehash: 1682a1b80f3b2d85e0d263b9ea2648da2b57a561
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430897"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-cli"></a>Démarrage rapide : Créer et configurer Route Server à l’aide d’Azure CLI 

Cet article vous aide à configurer Azure Route Server pour effectuer un peering avec une appliance virtuelle réseau (NVA) dans votre réseau virtuel à l’aide d’Azure PowerShell. Serveur de routes apprend les routes provenant de votre appliance virtuelle réseau et il les programme sur les machines virtuelles du réseau virtuel. Azure Route Server publiera également les routes du réseau virtuel menant à l’appliance virtuelle réseau. Pour plus d’informations, consultez [Serveur de routes Azure](overview.md).

:::image type="content" source="media/quickstart-configure-route-server-portal/environment-diagram.png" alt-text="Diagramme de l’environnement de déploiement du serveur de routes avec l’interface Azure CLI" border="false":::

##  <a name="prerequisites"></a>Prérequis 

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
* Vérifiez que vous disposez de l’interface Azure CLI la plus récente, sinon vous pouvez utiliser Azure Cloud Shell dans le portail. 
* Vérifiez les [limites du service pour Azure Route Server](route-server-faq.md#limitations). 

##  <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Vous connecter à votre compte Azure et sélectionner votre abonnement. 

Pour commencer votre configuration, connectez-vous à votre compte Azure. Si vous utilisez l’option « Essayer » de Cloud Shell, vous êtes connecté automatiquement. Utilisez les exemples suivants pour faciliter votre connexion :

```azurecli-interactive
az login
```

Vérifiez les abonnements associés au compte.

```azurecli-interactive
az account list
```

Sélectionnez l’abonnement pour lequel vous souhaitez créer un circuit ExpressRoute.

```azurecli-interactive
az account set \
    --subscription "<subscription ID>"
```

## <a name="create-a-resource-group-and-a-virtual-network"></a>Créer un groupe de ressources et un réseau virtuel 

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Avant de pouvoir créer un serveur de routes Azure, vous devez créer un groupe de ressources pour l’héberger. Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). Cet exemple crée un groupe de ressources nommé **myRouteServerRG** dans l’emplacement **westus** :

```azurecli-interactive
az group create \
    --name myRouteServerRG \
    --location westus
```

### <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Créez un réseau virtuel avec la commande [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Cet exemple crée un réseau virtuel par défaut nommé **myVirtualNetwork**. Si vous disposez déjà d’un réseau virtuel, vous pouvez passer à la section suivante.

```azurecli-interactive
az network vnet create \
    --name myVirtualNetwork \
    --resource-group myRouteServerRG \
    --address-prefix 10.0.0.0/16 
``` 

### <a name="add-a-dedicated-subnet"></a>Ajouter un sous-réseau dédié 

Serveur de routes Azure nécessite un sous-réseau dédié nommé *RouteServerSubnet*. La taille du sous-réseau doit être au moins égale à 27 ou un préfixe abrégé (par exemple /26 ou /25), ou vous recevrez un message d’erreur lors du déploiement du serveur de routes. Créez une configuration de sous-réseau nommée **RouteServerSubnet** avec [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) :

1. Exécutez la commande suivante pour ajouter *RouteServerSubnet* à votre réseau virtuel.

    ```azurecli-interactive 
    az network vnet subnet create \
        --name RouteServerSubnet \
        --resource-group myRouteServerRG \
        --vnet-name myVirtualNetwork \
        --address-prefix 10.0.0.0/24)
    ``` 

1. Notez l’ID RouteServerSubnet. Pour obtenir et stocker l’ID de ressource du *RouteServerSubnet* à la variable `subnet_id`, utilisez [az network vnet subnet show](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_show) :

    ```azurecli-interactive 
    subnet_id=$(az network vnet subnet show \
        --name RouteServerSubnet \
        --resource-group myRouteServerRG \
        --vnet-name myVirtualNetwork \
        --query id -o tsv) 

    echo $subnet_id
    ```

## <a name="create-the-route-server"></a>Créer le serveur de routage 

1. Pour garantir la connectivité au service back-end qui gère la configuration de Serveur de routes, l’affectation d’une adresse IP publique est nécessaire. Créer une adresse IP publique standard nommée **RouteServerIP** avec [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) :

    ```azurecli-interactive
    az network public-ip create \
        --name RouteServerIP \
        --resource-group myRouteServerRG \
        --version IPv4 \
        --sku Standard
    ```

2. Créez le serveur de routes Azure avec [az network routeserver create](/cli/azure/network/routeserver#az_network_routeserver_create). Cet exemple crée un serveur de routes Azure nommé **myRouteServer**. Le *hosted-subnet* est l’ID de ressource du RouteServerSubnet créé dans la section précédente.

    ```azurecli-interactive
    az network routeserver create \
        --name myRouteServer \
        --resource-group myRouteServerRG \
        --hosted-subnet $subnet_id \
        --public-ip-address RouteServerIP
    ``` 

## <a name="create-bgp-peering-with-an-nva"></a>Créer un peering BGP avec une appliance virtuelle réseau 

Utilisez [az network routeserver peering create](/cli/azure/network/routeserver/peering#az_network_routeserver_peering_create) pour établir un peering BGP entre le serveur de routes et l’appliance virtuelle réseau : 

Le `peer-ip` est l’adresse IP du réseau virtuel affectée à l’appliance virtuelle réseau. Le `peer-asn` est le numéro de système autonome (NSA) configuré dans l’appliance virtuelle réseau. Le NSA peut être n’importe quel nombre de 16 bits situé en dehors de la plage 65515-65520. Cette plage de NSA est réservée par Microsoft.

```azurecli-interactive 
az network routeserver peering create \
    --name myNVA \
    --peer-ip 192.168.0.1 \
    --peer-asn 65501 \
    --routeserver myRouteServer \
    --resource-group myRouteServerRG
``` 

Pour configurer le peering avec une autre appliance virtuelle réseau ou une autre instance de la même appliance pour la redondance, utilisez la même commande que ci-dessus avec des valeurs de *PeerName*, *PeerIp* et *PeerAsn* différentes.

## <a name="complete-the-configuration-on-the-nva"></a>Terminer la configuration sur l’appliance virtuelle réseau 

Pour achever la configuration sur l’appliance virtuelle réseau et activer les sessions BGP, vous avez besoin de l’adresse IP et du NSA d’Azure Route Server. Vous pouvez obtenir ces informations en utilisant [az network routeserver show](/cli/azure/network/routeserver#az_network_routeserver_show) :

```azurecli-interactive 
az network routeserver show \
    --name myRouteServer \
    --resource-group myRouteServerRG 
``` 

La sortie se présente comme suit :

``` 
RouteServerAsn  : 65515 

RouteServerIps  : {10.5.10.4, 10.5.10.5}  "virtualRouterAsn": 65515, 

  "virtualRouterIps": [ 

    "10.0.0.4", 

    "10.0.0.5" 

  ], 

``` 

## <a name="configure-route-exchange"></a>Configurer l’échange de routage 

Si vous disposez d’ExpressRoute et d’une passerelle VPN Azure dans le même réseau virtuel, et que vous souhaitez qu’elles échangent des routes, vous pouvez activer l’échange de routage sur le serveur de routage Azure.

> [!IMPORTANT]
> Pour les déploiements Greenfield, veillez à créer la passerelle VPN Azure avant de créer Azure Route Server sinon le déploiement de la passerelle VPN Azure échouera.
> 

1. Pour activer l’échange d’itinéraire entre le serveur de routes Azure et la ou les passerelles, utilisez [az network routerserver update](/cli/azure/network/routeserver#az_network_routeserver_update) avec l’indicateur `--allow-b2b-traffic` défini sur **true** :

    ```azurecli-interactive 
    az network routeserver update \
        --name myRouteServer \
        --resource-group myRouteServerRG \
        --allow-b2b-traffic true 
    ``` 

2. Pour désactiver l’échange d’itinéraire entre le serveur de routes Azure et la ou les passerelles, utilisez [az network routerserver update](/cli/azure/network/routeserver#az_network_routeserver_update) avec l’indicateur `--allow-b2b-traffic` défini sur **false** :

    ```azurecli-interactive
    az network routeserver update \
        --name myRouteServer \
        --resource-group myRouteServerRG \
        --allow-b2b-traffic false 
    ``` 

## <a name="troubleshooting"></a>Dépannage 

Utilisez [az network routeserver peering list-advertised-routes](/cli/azure/network/routeserver/peering#az_network_routeserver_peering_list_advertised_routes) pour afficher les itinéraires annoncés par le serveur de routes Azure :

```azurecli-interactive 
az network routeserver peering list-advertised-routes \
    --name myNVA \
    --routeserver myRouteServer \
    --resource-group myRouteServerRG
```

Utilisez [az network routeserver peering list-learned-routes](/cli/azure/network/routeserver/peering#az_network_routeserver_peering_list_learned_routes) pour afficher les itinéraires appris par le serveur de routes Azure:

```azurecli-interactive
az network routeserver peering list-learned-routes \
    --name myNVA \
    --routeserver myRouteServer
    --resource-group myRouteServerRG \
``` 

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin du serveur de routage Azure, utilisez la première commande pour supprimer le peering BGP, puis la seconde pour le serveur de routage. 

1. Supprimez le peering BGP entre le serveur de routes Azure et une NVA avec [az network routeserver peering delete](/cli/azure/network/routeserver/peering#az_network_routeserver_peering_delete) :

    ```azurecli-interactive
    az network routeserver peering delete \
        --name myNVA \
        --routeserver myRouteServer \
        --resource-group myRouteServerRG
    ``` 

2. Supprimez le serveur de routes Azure avec [az network routeserver delete](/cli/azure/network/routeserver#az_network_routeserver_delete) : 

    ```azurecli-interactive 
    az network routeserver delete \
        --name myRouteServer \
        --resource-group myRouteServerRG
    ``` 

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé le serveur de routes Azure, découvrez à présent de quelle façon Serveur de routes Azure interagit avec les passerelles VPN et ExpressRoute : 

> [!div class="nextstepaction"]
> [Prise en charge d’Azure ExpressRoute et de VPN Azure](expressroute-vpn-support.md)
 
