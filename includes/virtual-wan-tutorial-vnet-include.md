---
title: Fichier Include
description: Fichier Include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 04/23/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 40ae634897361219c39e60d2161d3576cc44a400
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077520"
---
Cliquez sur « Essayez-le » dans cet article pour ouvrir une console PowerShell et créer facilement un réseau virtuel dans Azure Cloud Shell. Ajustez les valeurs, puis copiez et collez les commandes dans la fenêtre de console. 

Veillez à vérifier que l’espace d’adresse pour le réseau virtuel que vous créez ne chevauche pas les plages d’adresses d’autres réseaux virtuels auxquels vous souhaitez vous connecter, ou avec les espaces d’adresse de votre réseau local.

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Si vous ne disposez d’aucun groupe de ressources à utiliser, créez-en un. Ajustez les commandes PowerShell pour refléter le nom du groupe de ressources que vous voulez utiliser, puis exécutez l’applet de commande suivante :

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Créer un réseau virtuel

Ajustez les commandes PowerShell pour créer un réseau virtuel compatible avec votre environnement.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```
