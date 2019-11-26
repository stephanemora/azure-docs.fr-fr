---
title: 'Azure ExpressRoute : Ajouter une passerelle à un réseau virtuel : classique'
description: Configurez une passerelle de réseau virtuel pour un réseau virtuel suivant le modèle de déploiement classique avec PowerShell pour une configuration ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 11/05/2018
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 75c2b400a2caf4e5f2e14589fc007bb506b46338
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74031873"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>Configurer une passerelle de réseau virtuel pour ExpressRoute à l’aide de PowerShell (classique)
> [!div class="op_single_selector"]
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Classic - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Vidéo - portail Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Cet article vous montrera les étapes nécessaires pour ajouter, redimensionner et supprimer une passerelle de réseau virtuel pour un réseau virtuel existant. Les étapes de cette configuration sont spécifiquement adaptées aux réseaux virtuels qui ont été créés à l’aide du **modèle de déploiement classique** et qui seront utilisés dans une configuration ExpressRoute. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**À propos des modèles de déploiement Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Avant tout chose
Vérifiez que vous avez installé les applets de commande Azure PowerShell nécessaires pour cette configuration (1.0.2 ou ultérieure). Si vous n’avez pas installé les applets de commande, vous devez le faire avant de commencer les étapes de configuration. Pour plus d’informations sur l’installation d’Azure PowerShell, consultez la page [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez créé la passerelle de réseau virtuel, vous pouvez lier votre réseau virtuel à un circuit ExpressRoute. Consultez [Liaison d’un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-classic.md).

