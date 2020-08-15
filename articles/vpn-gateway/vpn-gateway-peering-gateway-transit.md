---
title: Configurer le transit par passerelle VPN pour le peering de réseaux virtuels
description: Configurez le transit par passerelle pour l’appairage de réseaux virtuels, afin de connecter en toute transparence deux réseaux virtuels Azure en un seul à des fins de connectivité.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.tgt_pltfrm: na
ms.date: 03/25/2018
ms.author: yushwang
ms.openlocfilehash: b5649c804cb21a221ca287711380c91846f55306
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031692"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>Configurer le transit par passerelle VPN pour le peering de réseaux virtuels

Cet article explique comment configurer le transit par passerelle pour le peering de réseaux virtuels.Cet article explique comment configurer le transit par passerelle pour le peering de réseaux virtuels. Le [peering de réseaux virtuels](../virtual-network/virtual-network-peering-overview.md) connecte en toute transparence deux réseaux virtuels Azure de manière à les fusionner en un seul réseau virtuel à des fins de connectivité. Le [transit par passerelle](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) est une propriété de peering qui permet à un réseau virtuel d’exploiter la passerelle VPN du réseau virtuel appairé pour la mise en œuvre d’une connectivité intersite ou de réseau virtuel à réseau virtuel. Le diagramme suivant illustre le fonctionnement du transit par passerelle avec le peering de réseaux virtuels.

![Transit par passerelle](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

Dans le diagramme, le transit par passerelle permet aux réseaux virtuels homologués d’utiliser la passerelle VPN du réseau virtuel Hub-RM. La connectivité disponible sur la passerelle VPN, notamment les connexions S2S, P2S et de réseau virtuel à réseau virtuel, s’applique aux trois réseaux virtuels. L’option de transit est disponible pour le peering entre des modèles de déploiement identiques ou différents. Toutefois, la passerelle VPN peut uniquement se trouver dans le réseau virtuel qui utilise le modèle de déploiement Resource Manager, comme illustré dans le diagramme.

Dans l’architecture réseau hub-and-spoke, le transit par passerelle permet aux réseaux virtuels spoke d’exploiter la passerelle VPN du hub, évitant ainsi d’avoir à déployer des passerelles VPN dans chaque réseau virtuel spoke. Les itinéraires vers les réseaux locaux ou les réseaux virtuels connectés à la passerelle sont propagés aux tables de routage pour les réseaux virtuels homologués à l’aide du transit par passerelle. Vous pouvez désactiver la propagation automatique des itinéraires à partir de la passerelle VPN. Créez une table de routage avec l’option **Désactiver la propagation des itinéraires BGP** et associez la table de routage aux sous-réseaux afin d’empêcher la distribution des itinéraires à ces derniers. Pour plus d’informations, consultez [Virtual network routing table](../virtual-network/manage-route-table.md) (Table de routage de réseau virtuel).

Deux scénarios sont décrits dans le présent document :

1. Les deux réseaux virtuels utilisent le modèle de déploiement Resource Manager
2. Le réseau virtuel spoke utilise le modèle de déploiement Classic et le réseau virtuel hub avec la passerelle utilise le modèle Resource Manager


>[!NOTE]
> Si vous apportez un changement à la topologie de votre réseau et que vous avez des clients VPN Windows, vous devez retélécharger et réinstaller le package client VPN pour les clients Windows afin d’appliquer ce changement au client.
>

## <a name="requirements"></a>Spécifications



L’exemple présenté dans ce document requiert la création des ressources suivantes :

1. Réseau virtuel Hub-RM avec une passerelle VPN
2. Réseau virtuel Spoke-RM
3. Réseau virtuel Spoke-Classic avec le modèle de déploiement Classic
4. Le compte que vous utilisez doit être doté des rôles et autorisations nécessaires. Consultez la section [Autorisations](#permissions) de cet article pour plus d’informations.

Consultez les documents suivants pour connaître les procédures associées :

1. [Créer une passerelle VPN dans un réseau virtuel](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
2. [Créer un peering de réseaux virtuels présentant le même modèle de déploiement](../virtual-network/tutorial-connect-virtual-networks-portal.md)
3. [Créer un peering de réseaux virtuels présentant des modèles de déploiement différents](../virtual-network/create-peering-different-deployment-models.md)

## <a name="permissions"></a><a name="permissions"></a>Autorisations

Les comptes que vous utilisez pour créer un peering de réseaux virtuels doivent être dotés des autorisations ou des rôles nécessaires. Dans l’exemple ci-dessous, si vous avez effectué un peering entre deux réseaux virtuels nommés Hub-RM et Spoke-Classic, votre compte doit être doté des autorisations ou des rôles suivants pour chaque réseau virtuel :
    
|Réseau virtuel|Modèle de déploiement|Role|Autorisations|
|---|---|---|---|
|Hub-RM|Gestionnaire de ressources|[Contributeur de réseau](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Classique|[Contributeur de réseau classique](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/A|
|Spoke-Classic|Gestionnaire de ressources|[Contributeur de réseau](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Classique|[Contributeur de réseau classique](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Apprenez-en davantage sur les [rôles intégrés](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) et l’affectation d’autorisations spécifiques aux [rôles personnalisés](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Gestionnaire des ressources uniquement).

## <a name="resource-manager-to-resource-manager-peering-with-gateway-transit"></a>Peering de deux réseaux virtuels Resource Manager avec transit par passerelle

Suivez les instructions ci-dessous pour créer ou mettre à jour les peerings de réseaux virtuels de manière à activer le transit par passerelle.

1. Créez ou mettez à jour le peering de réseaux virtuels de Spoke-RM à Hub-RM à partir du portail Azure. Accédez à la ressource du réseau virtuel Spoke-RM, cliquez sur Peerings, puis sur Ajouter :
    - Définissez l’option Resource Manager.
    - Sélectionnez le réseau virtuel Hub-RM dans l’abonnement correspondant.
    - Vérifiez que l’option Autoriser l’accès au réseau virtuel est activée.
    - Cochez l’option **Utiliser des passerelles distantes**.
    - Cliquez sur OK.

      ![Homologation de Spoke-RM à Hub-RM](./media/vpn-gateway-peering-gateway-transit/spokerm-hubrm-peering.png)

2. Si le peering est déjà créé, accédez à la ressource du peering, puis activez l’option **Utiliser des passerelles distantes**, comme dans la capture d’écran présentée à l’étape 1.

3. Créez ou mettez à jour le peering de réseaux virtuels de Hub-RM à Spoke-RM à partir du portail Azure. Accédez à la ressource du réseau virtuel Hub-RM, cliquez sur Peerings, puis sur Ajouter :
    - Définissez l’option Resource Manager.
    - Vérifiez que l’option Autoriser l’accès au réseau virtuel est activée.
    - Sélectionnez le réseau virtuel Spoke-RM dans l’abonnement correspondant.
    - Définissez l’option **Autoriser le trafic par passerelle**.
    - Cliquez sur OK.

      ![Homologation de Hub-RM à Spoke-RM](./media/vpn-gateway-peering-gateway-transit/hubrm-spokerm-peering.png)

4. Si le peering est déjà créé, accédez à la ressource du peering, puis activez l’option **Autoriser le trafic par passerelle**, comme dans la capture d’écran présentée à l’étape 3.

5. Vérifier que l’état de peering est défini sur **Connecté** pour les deux réseaux virtuels.

### <a name="powershell-sample"></a>Exemple de code PowerShell

Vous pouvez également utiliser PowerShell pour créer ou mettre à jour le peering avec l’exemple ci-dessous. Remplacez les variables par le nom de vos réseaux virtuels et de vos groupes de ressources.

```azurepowershell-interactive
$SpokeRG = "SpokeRG1"
$SpokeRM = "Spoke-RM"
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$spokermvnet = Get-AzVirtualNetwork -Name $SpokeRM -ResourceGroup $SpokeRG
$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name SpokeRMtoHubRM `
  -VirtualNetwork $spokermvnet `
  -RemoteVirtualNetworkId $hubrmvnet.Id `
  -UseRemoteGateways

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId $spokermvnet.Id `
  -AllowGatewayTransit
```

## <a name="classic-to-resource-manager-peering-with-gateway-transit"></a>Peering d’un réseau virtuel Classic et d’un réseau virtuel Resource Manager avec transit par passerelle

La procédure est similaire à celle de l’exemple impliquant deux réseaux virtuels Resource Manager, à ceci près que les opérations s’appliquent au réseau virtuel Hub-RM uniquement.

1. Créez ou mettez à jour le peering de réseaux virtuels de Hub-RM à Spoke-RM à partir du portail Azure. Accédez à la ressource du réseau virtuel Hub-RM, cliquez sur Peerings, puis sur Ajouter :
   - Définissez l’option Classic pour le modèle de déploiement du réseau virtuel.
   - Sélectionnez le réseau virtuel Spoke-Classic dans l’abonnement correspondant.
   - Vérifiez que l’option Autoriser l’accès au réseau virtuel est activée.
   - Définissez l’option **Autoriser le trafic par passerelle**.
   - Cliquez sur OK.

     ![Homologation de Hub-RM à Spoke-Classic](./media/vpn-gateway-peering-gateway-transit/hubrm-spokeclassic-peering.png)

2. Si le peering est déjà créé, accédez à la ressource du peering, puis activez l’option **Autoriser le trafic par passerelle**, comme dans la capture d’écran présentée à l’étape 1.

3. Aucune opération n’est requise pour le réseau virtuel Spoke-Classic.

4. Vérifier que l’état de peering est défini sur **Connecté** pour le réseau virtuel Hub-RM.

Une fois que l’état indique « Connecté », les réseaux virtuels spoke peuvent commencer à utiliser la connectivité de réseau virtuel à réseau virtuel ou intersite via la passerelle VPN du réseau virtuel hub.

### <a name="powershell-sample"></a>Exemple de code PowerShell

Vous pouvez également utiliser PowerShell pour créer ou mettre à jour le peering avec l’exemple ci-dessous. Remplacez les variables par les valeurs de votre réseau virtuel et de votre groupe de ressources, et « subscription ID » par votre ID d’abonnement. Vous devez uniquement créer le peering de réseaux virtuels sur le réseau virtuel hub.

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>Étapes suivantes

* Prenez connaissance des [comportements et contraintes importants du peering de réseaux virtuels](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) et les [paramètres de peering de réseaux virtuels](../virtual-network/virtual-network-manage-peering.md#create-a-peering) avant d’en créer un pour une utilisation en production.
* Découvrez comment [créer une topologie de réseau de type hub et spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) avec le peering de réseaux virtuels et le transit par passerelle.
