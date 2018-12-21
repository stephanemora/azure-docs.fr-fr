---
title: 'Configurer ExpressRoute Global Reach : Interface de ligne de commande Azure | Microsoft Docs'
description: Cet article vous aide à associer des circuits ExpressRoute afin de constituer un réseau privé entre vos réseaux locaux et d’activer Global Reach.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/12/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 9a8e0a5df9383d8e3d7159aa916b0e4fbfeea948
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384046"
---
# <a name="configure-expressroute-global-reach-using-azure-cli-preview"></a>Configurer Azure ExpressRoute Global Reach à l’aide d’Azure CLI (préversion)
Cet article vous aide à configurer ExpressRoute Global Reach à l’aide d’Azure CLI. Pour plus d’informations, consultez [ExpressRoute Global Reach](expressroute-global-reach.md).
 
## <a name="before-you-begin"></a>Avant de commencer
> [!IMPORTANT]
> Cette préversion publique est fournie sans contrat de niveau de service et ne doit pas être utilisée pour les charges de travail de production. Certaines fonctionnalités peuvent ne pas être prises en charge, disposer de capacités limitées ou ne pas être disponibles dans tous les emplacements Azure. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 


Avant de commencer la configuration, vous devez vérifier les points suivants.

* Installez la dernière version de l’interface Azure CLI. Consultez [Installer l’interface de ligne de commande Azure](/cli/azure/install-azure-cli) et [Prise en main d’Azure CLI](/cli/azure/get-started-with-azure-cli).
* Vous devez connaître les [flux de travail](expressroute-workflows.md) d’approvisionnement du circuit ExpressRoute.
* Assurez-vous que l’état de vos circuits ExpressRoute est Approvisionné.
* Assurez-vous que l’homologation privée Azure est configurée sur vos circuits ExpressRoute.  

### <a name="log-into-your-azure-account"></a>Connectez-vous à votre compte Azure
Pour commencer la configuration, vous devez vous connecter à votre compte Azure. La commande ouvre votre navigateur par défaut et vous invite à entrer les informations d’identification de connexion de votre compte Azure.  

```azurecli
az login
```

Si vous disposez de plusieurs abonnements Azure, vérifiez les abonnements associés au compte.

```azurecli
az account list
```

Spécifiez l’abonnement à utiliser.

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identifiez les circuits ExpressRoute à configurer
Vous pouvez activer ExpressRoute Global Reach entre deux circuits ExpressRoute à condition qu’ils se situent dans les pays pris en charge et qu’ils aient été créés sur différents sites d’homologation. Si votre abonnement comprend les deux circuits, vous pouvez choisir celui de votre choix pour exécuter la configuration dans les sections ci-dessous. Si les deux circuits se trouvent dans des abonnements Azure différents, vous devez avoir l’autorisation d’un abonnement Azure et transférer la clé d’autorisation lorsque vous exécutez la commande de configuration dans l’autre abonnement Azure.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Autoriser la connexion entre vos réseaux locaux

Lorsque vous exécutez la commande pour activer la connectivité, tenez compte des valeurs suivantes :

* *peer-circuit* doit correspondre à l'ID de ressource complet. Par exemple :  

  ```
  /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}
  ```
* La valeur *- AddressPrefix* doit être un sous-réseau /29 IPv4, par exemple « 10.0.0.0/29 ». Nous allons utiliser les adresses IP de ce sous-réseau pour établir la connexion entre les deux circuits ExpressRoute. Vous ne devez pas les utiliser dans vos réseaux virtuels Azure ou vos réseaux locaux.

Exécutez l’interface de ligne de commande suivante pour connecter deux circuits ExpressRoute. Utilisez l'exemple de commande suivant :

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

La sortie de l'interface CLI est semblable à l'exemple suivant :

```azurecli
{
  "addressPrefix": "<__.__.__.__/29>",
  "authorizationKey": null,
  "circuitConnectionStatus": "Connected",
  "etag": "W/\"48d682f9-c232-4151-a09f-fab7cb56369a\"",
  "expressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<ResourceGroupName>"
  },
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering/connections/<ConnectionName>",
  "name": "<ConnectionName>",
  "peerExpressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<Circuit2ResourceGroupName>"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "<ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/peerings/connections"
}
```

Une fois l’opération terminée, la connexion entre vos réseaux locaux doit être établie des deux côtés par le biais des deux circuits ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Circuits ExpressRoute dans différents abonnements Azure

Si les deux circuits ne se trouvent pas dans le même abonnement Azure, vous devez disposer d’une autorisation. Dans la configuration suivante, l’autorisation est générée dans l’abonnement du circuit 2 et la clé d’autorisation est transférée au circuit 1.

Générez une clé d’autorisation. 
```azurecli
az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
```

La sortie de l’interface CLI se présente comme suit.

```azurecli
{
  "authorizationKey": "<authorizationKey>",
  "authorizationUseStatus": "Available",
  "etag": "W/\"cfd15a2f-43a1-4361-9403-6a0be00746ed\"",
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/authorizations/<AuthorizationName>",
  "name": "<AuthorizationName>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<Circuit2ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/authorizations"
}
```

Notez l’ID de ressource du circuit 2 ainsi que la clé d’autorisation.

Exécutez la commande suivante sur le circuit 1. Passez l’ID de ressource du circuit 2 et la clé d’autorisation. 
```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
```

Une fois l’opération terminée, la connexion entre vos réseaux locaux doit être établie des deux côtés par le biais des deux circuits ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Obtenir et vérifier la configuration

Utilisez la commande suivante pour vérifier la configuration sur le circuit où elle a été effectuée. Pour l’exemple précédent, il s’agit du circuit 1.

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

Dans la sortie de l’interface CLI figure *CircuitConnectionStatus*. La valeur associée (« Connected » ou « Disconnected ») indique si la connectivité entre les deux circuits est établie ou non. 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Désactiver la connexion entre vos réseaux locaux

Exécutez les commandes sur le circuit où la configuration a été effectuée. Pour l’exemple précédent, il s’agit du circuit 1.

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Vous pouvez exécuter la commande Show CLI (Afficher l’interface CLI) pour vérifier l’état. 

Une fois l’opération terminée, la connexion entre vos réseaux locaux n’est plus établie. 


## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur ExpressRoute Global Reach](expressroute-global-reach.md)
* [Vérifier la connectivité ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Lier un circuit ExpressRoute à un réseau virtuel Azure](expressroute-howto-linkvnet-arm.md)


