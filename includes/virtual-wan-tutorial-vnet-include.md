---
title: Fichier Include
description: Fichier Include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 660bbf50e1a8ae73bd7bbe1f7c42691ed62d276a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57552971"
---
Si vous n’avez pas déjà de réseaux virtuels, vous pouvez en créer un rapidement à l’aide de PowerShell. Vous pouvez également créer un réseau virtuel au moyen du portail Azure.

* Veillez à vérifier que l’espace d’adresse pour le réseau virtuel que vous créez ne chevauche pas les plages d’adresses d’autres réseaux virtuels auxquels vous souhaitez vous connecter, ou avec les espaces d’adresse de votre réseau local. 
* Si vous avez déjà un réseau virtuel, vérifiez qu’il répond aux critères requis et ne dispose pas d’une passerelle de réseau virtuel.

Vous pouvez facilement créer votre réseau virtuel en cliquant sur « Essayez-le » dans cet article pour ouvrir une console PowerShell. Ajustez les valeurs, puis copiez et collez les commandes dans la fenêtre de console.

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Ajuster les commandes PowerShell, puis créez un groupe de ressources.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Créer un réseau virtuel

Ajustez les commandes PowerShell pour créer le réseau virtuel compatible avec votre environnement.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```