---
title: 'Ajouter une passerelle de réseau virtuel à un réseau virtuel pour ExpressRoute : PowerShell : Azure | Microsoft Docs'
description: Cet article vous explique comment ajouter une passerelle de réseau virtuel à un réseau virtuel Resource Manager déjà créé pour ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: f126f4fe6f7c0a66ce6f1031945442927f48113b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60366376"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Configurer une passerelle de réseau virtuel pour ExpressRoute à l’aide de PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - Portail Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Classic - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Vidéo - portail Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Cet article vous permet d’ajouter, de redimensionner et de supprimer une passerelle de réseau virtuel pour un réseau virtuel préexistant. Les étapes de cette configuration s’appliquent aux réseaux virtuels créés à l’aide du modèle de déploiement Resource Manager pour une configuration ExpressRoute. Pour en savoir plus, consultez [À propos des passerelles de réseau virtuel pour ExpressRoute](expressroute-about-virtual-network-gateways.md).

## <a name="before-beginning"></a>Avant tout chose

### <a name="working-with-powershell"></a>Utilisation de PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloud-shell-powershell-about.md)]

### <a name="configuration-reference-list"></a>Liste de référence de configuration

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez créé la passerelle de réseau virtuel, vous pouvez lier votre réseau virtuel à un circuit ExpressRoute. Consultez [Liaison d’un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-arm.md).