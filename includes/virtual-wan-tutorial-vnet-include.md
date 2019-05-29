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
ms.openlocfilehash: 40c8cb41ad3bcd46e9973a5f96134ff1bfd02fd2
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66150792"
---
Cliquez sur « Essayez-le » dans cet article pour ouvrir une console PowerShell et créer facilement un réseau virtuel. Ajustez les valeurs, puis copiez et collez les commandes dans la fenêtre de console. Pour en savoir plus sur le nouveau module Az et la compatibilité avec AzureRM, consultez [Présentation du nouveau module Az d’Azure PowerShell](/powershell/azure/new-azureps-module-az). Pour des instructions d’installation du module Az, consultez [Installer Azure PowerShell](/powershell/azure/install-az-ps).

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