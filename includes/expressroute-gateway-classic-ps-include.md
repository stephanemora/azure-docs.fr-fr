---
title: Fichier Include
description: Fichier Include
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 11/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fab00e5281bb91bce10228b3bc2e9cfd503d5d5b
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219815"
---
Vous devez créer un réseau virtuel et un sous-réseau de passerelle avant d’effectuer les tâches suivantes.

> [!NOTE]
> Ces exemples ne s’appliquent pas aux configurations de coexistence S2S/ExpressRoute.
> Pour plus d’informations sur l’utilisation de passerelles dans une configuration de coexistence, consultez [Configurer la coexistence de connexions](../articles/expressroute/expressroute-howto-coexist-classic.md#gw).

## <a name="add-a-gateway"></a>Ajout d’une passerelle

Utilisez la commande suivante pour créer une passerelle. Veillez à remplacer les valeurs pas les vôtres.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Vérification de la création de la passerelle

Utilisez la commande suivante pour vérifier que la passerelle a été créée. Cette commande récupère également l’ID de passerelle dont vous avez besoin pour d’autres opérations.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Redimensionner une passerelle

Il existe un certain nombre de [Références (SKU) de passerelle](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Vous pouvez utiliser la commande suivante pour modifier la référence de passerelle à tout moment.

> [!IMPORTANT]
> Cette commande ne fonctionne pas pour la passerelle UltraPerformance. Pour modifier votre passerelle en passerelle UltraPerformance, commencez par supprimer la passerelle ExpressRoute, puis créez une passerelle UltraPerformance. Pour mettre à niveau votre passerelle à partir d’une passerelle UltraPerformance, commencez par supprimer la passerelle UltraPerformance, puis créez-en une. 
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>Supprimer une passerelle

Utilisez la commande suivante pour supprimer une passerelle.

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```
