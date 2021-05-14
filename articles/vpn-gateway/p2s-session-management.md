---
title: Gestion de session VPN de point à site
titleSuffix: Azure VPN Gateway
description: Apprenez à afficher et à déconnecter des sessions VPN de point à site.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/26/2021
ms.author: cherylmc
ms.openlocfilehash: 79432bfd65feeae79017a883be990d88134cbb10
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108202530"
---
# <a name="point-to-site-vpn-session-management"></a>Gestion de session VPN de point à site

Les passerelles de réseau virtuel Azure offrent un moyen simple d’afficher et de déconnecter les sessions VPN de point à site actuelles. Cet article vous aide à afficher et à déconnecter les sessions actuelles. L’état de la session est mis à jour toutes les 5 minutes. Il n’est pas mis à jour immédiatement. 


## <a name="portal"></a>Portail

>[!NOTE]
> Les informations relatives à la source de connexion sont fournies pour les connexions IKEv2 et OpenVPN uniquement.
> 

Pour afficher et déconnecter une session dans le portail :

1. Accédez à la passerelle VPN.
1. Dans la section **Surveillance**, sélectionnez **Sessions point à site**.

   :::image type="content" source="./media/p2s-session-management/portal.png" alt-text="Exemple de portail":::
1. Vous pouvez afficher toutes les sessions en cours dans le volet.
1. Sélectionnez **« ... »** pour la session que vous souhaitez déconnecter, puis sélectionnez **Déconnecter**.

Actuellement, vous ne pouvez pas utiliser cette fonctionnalité dans le portail pour les références SKU VpnGw4 et VpnGw5. Si vous avez une de ces passerelles, utilisez la méthode PowerShell décrite dans la section suivante.

## <a name="powershell"></a>PowerShell

Pour afficher et déconnecter une session à l’aide de PowerShell :

1. Exécutez la commande PowerShell suivante pour répertorier les sessions actives :

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGatewayVpnClientConnectionHealth -VirtualNetworkGatewayName <name of the gateway>  -ResourceGroupName <name of the resource group>
   ```
1. Copiez le **VpnConnectionId** de la session que vous souhaitez déconnecter.

   :::image type="content" source="./media/p2s-session-management/powershell.png" alt-text="Exemple PowerShell":::
1. Pour déconnecter la session, exécutez la commande suivante :

   ```azurepowershell-interactive
   Disconnect-AzVirtualNetworkGatewayVpnConnection -VirtualNetworkGatewayName <name of the gateway> -ResourceGroupName <name of the resource group> -VpnConnectionId <VpnConnectionId of the session>
   ```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les connexions point à site, consultez [À propos des VPN point à site](point-to-site-about.md).
