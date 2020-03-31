---
title: Configuration du peering de réseaux virtuels globaux pour Azure Virtual WAN | Microsoft Docs
description: Connectez un réseau virtuel dans une région différente de celle de votre hub Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 340472f84d2dd2c4f46d180992745a57e8ad1884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73587066"
---
# <a name="configure-global-vnet-peering-cross-region-vnet-for-virtual-wan"></a>Configurer le peering de réseaux virtuels globaux (réseaux virtuels inter-régions) pour les Virtual WAN

Vous pouvez connecter un réseau virtuel dans une région différente de celle de votre hub Virtual WAN.

## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous respectez les critères suivants :

* Le réseau virtuel inter-régions (spoke) n’est pas connecté à un autre hub Virtual WAN. Un spoke ne peut être connecté qu’à un seul hub virtuel.
* Le réseau virtuel (spoke) ne contient pas de passerelle de réseau virtuel (par exemple une passerelle VPN Azure ou une passerelle de réseau virtuel ExpressRoute). Si le réseau virtuel contient une passerelle de réseau virtuel, vous devez supprimer la passerelle avant de connecter le réseau virtuel spoke au hub.

## <a name="register-this-feature"></a><a name="register"></a>Inscrire cette fonctionnalité

Vous pouvez inscrire cette fonctionnalité à l’aide de PowerShell. Si vous sélectionnez « Essayez » dans l’exemple ci-dessous, l’interpréteur de commandes Azure Cloud s’ouvre et vous n’avez pas besoin d’installer les applets de commande PowerShell localement sur votre ordinateur. Si nécessaire, vous pouvez modifier les abonnements à l’aide de l’applet de commande « Select-AzSubscription -SubscriptionId <subid>».

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Network'
```

## <a name="verify-registration"></a><a name="verify"></a>Vérifier l’état d’inscription

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
```

## <a name="connect-a-vnet-to-the-hub"></a><a name="hub"></a>Connecter un réseau virtuel au hub

Dans cette étape, vous créez la connexion de peering entre votre hub et un réseau virtuel inter-régions. Répétez ces étapes pour chaque réseau virtuel que vous souhaitez connecter.

1. Sur la page de votre WAN virtuel, cliquez sur **Connexion de réseau virtuel**.
2. Dans la page de connexion de réseau virtuel, cliquez sur **+ Ajouter une connexion**.
3. Dans la page **Ajouter une connexion**, renseignez les champs suivants :

    * **Nom de connexion** : nommez votre connexion.
    * **Hubs** : sélectionnez le hub que vous souhaitez associer à cette connexion.
    * **Abonnement** : vérifiez l’abonnement.
    * **Réseau virtuel** : sélectionnez le réseau virtuel que vous souhaitez connecter à ce hub. Le réseau virtuel ne peut pas avoir une passerelle de réseau virtuel déjà existante.
4. Cliquez sur **OK** pour créer la connexion du peering.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Virtual WAN, consultez [Vue d'ensemble de Virtual WAN](virtual-wan-about.md).