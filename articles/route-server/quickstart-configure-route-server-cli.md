---
title: 'Démarrage rapide : Créer et configurer Route Server à l’aide d’Azure CLI'
description: Dans ce guide de démarrage rapide, vous apprenez à créer et à configurer un serveur de routage à l’aide d’Azure CLI.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: e9c583db7493afc04b2c66553801f62d364b0a80
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419606"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-cli"></a>Démarrage rapide : Créer et configurer Route Server à l’aide d’Azure CLI 

Cet article vous aide à configurer Azure Route Server pour effectuer un peering avec des appliances virtuelles réseau (NVA) dans votre réseau virtuel à l’aide d’Azure CLI. Azure Route Server va apprendre les routes provenant de l’appliance virtuelle réseau et il les programmera pour les machines virtuelles du réseau virtuel. Il publiera également les routes du réseau virtuel menant à l’appliance virtuelle réseau. Pour plus d’informations, consultez [Azure Route Server](overview.md).

> [!IMPORTANT]
> Azure Route Server (préversion) est en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

##  <a name="prerequisites"></a>Prérequis 

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
* Vérifiez que vous disposez de l’interface Azure CLI la plus récente, sinon vous pouvez utiliser Azure Cloud Shell dans le portail. 
* Vérifiez les [limites du service pour Azure Route Server](route-server-faq.md#limitations). 

##  <a name="create-a-route-server"></a>Créer un serveur de routage 

###  <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Vous connecter à votre compte Azure et sélectionner votre abonnement. 

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
az account set --subscription "<subscription ID>"
```

### <a name="create-a-resource-group-and-virtual-network"></a>Créer un groupe de ressources et un réseau virtuel 

Avant de pouvoir créer un serveur de routage Azure, vous devez prévoir un réseau virtuel pour héberger le déploiement. Utilisez la commande suivante pour créer un groupe de ressources et un réseau virtuel. Si vous disposez déjà d’un réseau virtuel, vous pouvez passer à la section suivante.

```azurecli-interactive
az group create -n "RouteServerRG" -l "westus" 
az network vnet create -g "RouteServerRG" -n "myVirtualNetwork" --address-prefix "10.0.0.0/16" 
``` 

### <a name="add-a-subnet"></a>Ajouter un sous-réseau 

1. Ajoutez un sous-réseau nommé *RouteServerSubnet*, dans lequel déployer le serveur de routage Azure. Ce sous-réseau est un sous-réseau dédié uniquement pour Azure Route Server. RouteServerSubnet doit correspondre à /27 ou à un préfixe plus court (comme /26, /25), à défaut de quoi un message d’erreur s’affichera lorsque vous ajouterez le serveur de routage Azure.

    ```azurecli-interactive 
    az network vnet subnet create -g "RouteServerRG" --vnet-name "myVirtualNetwork" --name "RouteServerSubnet" --address-prefix "10.0.0.0/24"  
    ``` 

1. Obtenez l’ID RouteServerSubnet. Pour afficher l’ID de ressource de tous les sous-réseaux du réseau virtuel, utilisez la commande suivante : 

    ```azurecli-interactive 
    $subnet_id = $(az network vnet subnet show -n "RouteServerSubnet" --vnet-name "myVirtualNetwork" -g "RouteServerRG" --query id -o tsv) 
    ``` 

L’ID RouteServerSubnet ressemble à ce qui suit : 

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>Créer le serveur de routage 

Créez le serveur de routage à l’aide de la commande suivante : 

```azurecli-interactive
az network routeserver create -n "myRouteServer" -g "RouteServerRG" --hosted-subnet $subnet_id  
``` 

L’emplacement doit correspondre à l’emplacement de votre réseau virtuel. HostedSubnet est l’ID de RouteServerSubnet que vous avez obtenu à la section précédente. 

## <a name="create-peering-with-an-nva"></a>Créer un peering avec une appliance virtuelle réseau 

Utilisez la commande suivante pour établir le peering, du serveur de routage à l’appliance virtuelle réseau : 

```azurecli-interactive 

az network routeserver peering create --routeserver-name "myRouteServer" -g "RouteServerRG" --peer-ip "nva_ip" --peer-asn "nva_asn" -n "NVA1_name" 

``` 

« nva_ip » est l’adresse IP du réseau virtuel affectée à l’appliance virtuelle réseau. « nva_asn » est le numéro de système autonome (NSA) configuré dans l’appliance virtuelle réseau. Le NSA peut être n’importe quel nombre de 16 bits situé en dehors de la plage 65515-65520. Cette plage de NSA est réservée par Microsoft. 

Pour configurer le peering avec différentes appliances virtuelles réseau, ou une autre instance de la même appliance virtuelle réseau à des fins de redondance, utilisez la commande suivante :

```azurecli-interactive 

az network routeserver peering create --routeserver-name "myRouteServer" -g "RouteServerRG" --peer-ip "nva_ip" --peer-asn "nva_asn" -n "NVA2_name" 
``` 

## <a name="complete-the-configuration-on-the-nva"></a>Terminer la configuration sur l’appliance virtuelle réseau 

Pour achever la configuration sur l’appliance virtuelle réseau et activer les sessions BGP, vous avez besoin de l’adresse IP et du NSA d’Azure Route Server. Vous pouvez récupérer ces informations au moyen de cette commande : 

```azurecli-interactive 
az network routeserver show -g "RouteServerRG" -n "myRouteServer" 
``` 

La sortie présente les informations suivantes. 

```azurecli-interactive 
RouteServerAsn  : 65515 

RouteServerIps  : {10.5.10.4, 10.5.10.5}  "virtualRouterAsn": 65515, 

  "virtualRouterIps": [ 

    "10.0.0.4", 

    "10.0.0.5" 

  ], 

``` 

## <a name="configure-route-exchange"></a>Configurer l’échange de routage 

Si vous disposez d’une passerelle ExpressRoute et d’une passerelle VPN Azure dans le même réseau virtuel, et que vous souhaitez qu’elles échangent des routes, vous pouvez activer l’échange de routage sur le serveur de routage Azure.

> [!IMPORTANT]
> Pour les déploiements Greenfield, veillez à créer la passerelle VPN Azure avant de créer Azure Route Server sinon le déploiement de la passerelle VPN Azure échouera.
> 

1. Pour activer l’échange de routage entre Azure Route Server et la ou les passerelles, utilisez la commande suivante :

```azurecli-interactive 
az network routeserver update -g "RouteServerRG" -n "myRouteServer" --allow-b2b-traffic true 

``` 

2. Pour désactiver l’échange de routage entre Azure Route Server et la ou les passerelles, utilisez la commande suivante :

```azurecli-interactive
az network routeserver update -g "RouteServerRG" -n "myRouteServer" --allow-b2b-traffic false 
``` 

## <a name="troubleshooting"></a>Dépannage 

Vous pouvez afficher les routes publiées et reçues par Azure Route Server à l’aide de cette commande :

```azurecli-interactive 
az network routeserver peering list-advertised-routes -g RouteServerRG --vrouter-name myRouteServer -n NVA1_name 
az network routeserver peering list-learned-routes -g RouteServerRG --vrouter-name myRouteServer -n NVA1_name 
``` 

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin du serveur de routage Azure, utilisez ces commandes pour supprimer le peering BGP, puis le serveur de routage. 

1. Supprimez le peering BGP entre Azure Route Server et une appliance virtuelle réseau à l’aide de cette commande :

```azurecli-interactive
az network routeserver peering delete --routeserver-name "myRouteServer" -g "RouteServerRG" -n "NVA2_name" 
``` 

2. Supprimez Azure Route Server au moyen de cette commande : 

```azurecli-interactive 
az network routeserver delete -n "myRouteServer" -g "RouteServerRG" 
``` 

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé le serveur de routage Azure, découvrez à présent de quelle façon Azure Route Server interagit avec les passerelles VPN et ExpressRoute : 

> [!div class="nextstepaction"]
> [Prise en charge d’Azure ExpressRoute et de VPN Azure](expressroute-vpn-support.md)
 
