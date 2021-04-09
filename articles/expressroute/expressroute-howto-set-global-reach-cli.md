---
title: 'Azure ExpressRoute : Configurer ExpressRoute Global Reach : Interface de ligne de commande'
description: Découvrez comment associer des circuits ExpressRoute afin de constituer un réseau privé entre vos réseaux locaux et d’activer Global Reach à l’aide d’Azure CLI.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/07/2021
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 55f1e4c030dc0e7c8d0011dcff0cc1c9f8fec06e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99260229"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli"></a>Configurer ExpressRoute Global Reach à l’aide d’Azure CLI

Cet article vous aide à configurer Azure ExpressRoute Global Reach à l’aide d’Azure CLI. Pour plus d’informations, consultez [ExpressRoute Global Reach](expressroute-global-reach.md).
 
Avant de commencer la configuration, vérifiez les prérequis suivants :

* Vous devez avoir installé la dernière version d’Azure CLI. Consultez [Installer Azure CLI](/cli/azure/install-azure-cli) et [Bien démarrer avec Azure CLI](/cli/azure/get-started-with-azure-cli).
* Vous devez comprendre les [workflows](expressroute-workflows.md) de provisionnement des circuits ExpressRoute.
* Assurez-vous que les circuits ExpressRoute présentent l’état Provisionné.
* Assurez-vous que le peering privé Azure est configuré sur vos circuits ExpressRoute.  

### <a name="sign-in-to-your-azure-account"></a>Connexion à votre compte Azure

Pour commencer la configuration, connectez-vous à votre compte Azure. La commande suivante ouvre votre navigateur par défaut et vous invite à entrer les informations d’identification de connexion de votre compte Azure :  

```azurecli
az login
```

Si vous avez plusieurs abonnements Azure, listez les abonnements associés au compte :

```azurecli
az account list
```

Spécifiez l’abonnement que vous souhaitez utiliser :

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identifiez les circuits ExpressRoute à configurer

Vous pouvez activer Global Reach ExpressRoute entre deux circuits ExpressRoute quelconques. Les circuits doivent se trouver dans des pays/régions pris en charge et avoir été créés à différents emplacements de Peering. Si votre abonnement possède les deux circuits, vous pouvez sélectionner le circuits de votre choix pour exécuter la configuration. Toutefois, si les deux circuits se trouvent dans des abonnements Azure différents, vous devez créer une clé d’autorisation à partir de l’un des circuits. À l’aide de la clé d’autorisation générée par le premier circuit, vous pouvez activer Global Reach sur le second circuit.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Autoriser la connexion entre vos réseaux locaux

Quand vous exécutez la commande pour activer la connectivité, tenez compte des exigences suivantes relatives aux valeurs de paramètre :

* *peer-circuit* doit correspondre à l'ID de ressource complet. Par exemple :

  > /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering

* *address-prefix* doit correspondre à un sous-réseau IPv4 « /29 » (par exemple, « 10.0.0.0/29 »). Nous utilisons les adresses IP de ce sous-réseau pour établir la connexion entre les deux circuits ExpressRoute. Vous ne pouvez pas les utiliser dans vos réseaux virtuels Azure ou vos réseaux locaux.

Exécutez la commande CLI suivante pour connecter deux circuits ExpressRoute :

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

La sortie CLI se présente ainsi :

```output
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

Une fois cette opération terminée, la connexion entre vos réseaux locaux est établie des deux côtés par le biais des deux circuits ExpressRoute.

## <a name="enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions"></a>Activer la connectivité entre des circuits ExpressRoute dans des abonnements Azure différents

Si les deux circuits ne se trouvent pas dans le même abonnement Azure, vous avez besoin d’une autorisation. Dans la configuration suivante, vous générez une autorisation dans l’abonnement du circuit 2. Transmettez ensuite la clé d’autorisation au circuit 1.

1. Générez une clé d’autorisation :

   ```azurecli
   az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
   ```

   La sortie CLI se présente ainsi :

   ```output
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

1. Notez l’ID de ressource et la clé d’autorisation du circuit 2.

1. Exécutez la commande suivante sur le circuit 1, en passant l’ID de ressource et la clé d’autorisation du circuit 2 :

   ```azurecli
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

Une fois cette opération terminée, la connexion entre vos réseaux locaux est établie des deux côtés par le biais des deux circuits ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Obtenir et vérifier la configuration

Utilisez la commande suivante pour vérifier la configuration sur le circuit où elle a été effectuée (le circuit 1 dans l’exemple précédent) :

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

La sortie CLI affiche *CircuitConnectionStatus*. Cette valeur indique si la connectivité entre les deux circuits est établie (« Connected ») ou pas (« Disconnected »). 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Désactiver la connexion entre vos réseaux locaux

Pour désactiver la connectivité, exécutez la commande suivante sur le circuit où la configuration a été effectuée (le circuit 1 dans l’exemple précédent).

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Utilisez la commande ```show``` pour vérifier l’état.

À la fin de cette opération, la connectivité entre vos réseaux locaux par le biais de vos circuits ExpressRoute est désactivée.

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur ExpressRoute Global Reach](expressroute-global-reach.md)
* [Vérifier la connectivité ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Lier un circuit ExpressRoute à un réseau virtuel](expressroute-howto-linkvnet-arm.md)
