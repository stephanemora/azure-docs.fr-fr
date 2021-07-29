---
title: Publier des itinéraires personnalisés pour des clients de passerelle VPN point à site
titleSuffix: Azure VPN Gateway
description: Découvrez comment publier des itinéraires personnalisés sur vos clients de passerelle VPN point à site. Cet article présente les étapes du tunneling forcé du client VPN.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/08/2021
ms.author: cherylmc
ms.openlocfilehash: b37b63fda6b142fc1aba458c007b6fe0eb5db814
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111810945"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>Publier des routes personnalisées pour des clients VPN point à site

Vous pouvez être amené à publier des routes personnalisées pour tous vos clients VPN point à site. Ce peut être le cas si vous avez activé des points de terminaison de stockage dans votre réseau virtuel et que vous souhaitez que les utilisateurs distants puissent accéder à ces comptes de stockage par le biais de la connexion VPN. Vous pouvez publier l’adresse IP du point de terminaison de stockage pour tous vos utilisateurs distants afin que le trafic à destination du compte de stockage passe par le tunnel VPN, et non par l’Internet public. Vous pouvez également utiliser des routes personnalisées afin de configurer le tunneling forcé pour les clients VPN.

:::image type="content" source="./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png" alt-text="Diagramme de la publication de routes personnalisées.":::

## <a name="advertise-custom-routes"></a>Publier des routes personnalisées

Pour publier des routes personnalisées, utilisez `Set-AzVirtualNetworkGateway cmdlet`. L’exemple suivant montre comment publier l’adresse IP pour les [tables de compte de stockage Contoso](https://contoso.table.core.windows.net).

1. Effectuez un test ping sur *contoso.table.core.windows.net* et notez l’adresse IP. Par exemple :

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. Lancer les commandes PowerShell suivantes :

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. Pour ajouter plusieurs routes personnalisées, utilisez une virgule et des espaces afin de séparer les adresses. Par exemple :

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```

## <a name="advertise-custom-routes---forced-tunneling"></a>Publier des routes personnalisées - Tunneling forcé

Vous pouvez diriger tout le trafic vers le tunnel VPN en publiant 0.0.0.0/1 et 128.0.0.0/1 comme routes personnalisées vers les clients. La raison de la répartition de 0.0.0.0/0 en deux sous-réseaux plus petits est que ces préfixes plus petits sont plus spécifiques que la route par défaut qui peut déjà être configurée sur la carte réseau locale et, par conséquent, est préférable lors de l’acheminement du trafic.

1. Pour activer le tunneling forcé, utilisez les commandes suivantes :

    ```azurepowershell-interactive    
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 0.0.0.0/1 , 128.0.0.0/1
    ```
## <a name="view-custom-routes"></a>Afficher les routes personnalisées

Utilisez l’exemple suivant pour afficher les routes personnalisées :

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="delete-custom-routes"></a>Supprimer des routes personnalisées

Utilisez l’exemple suivant pour supprimer des itinéraires personnalisés :

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le routage de point à site, consultez [À propos du routage de point à site](vpn-gateway-about-point-to-site-routing.md).
