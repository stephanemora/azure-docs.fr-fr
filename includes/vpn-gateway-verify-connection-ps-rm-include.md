---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/17/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 267234cb9ecea1dc097f13739bf98ee11206ad06
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444094"
---
Vous pouvez vérifier que votre connexion a réussi via l’applet de commande « Get-AzureRmVirtualNetworkGatewayConnection » avec ou sans « -Debug ». 

1. Utilisez l’exemple d’applet de commande suivant, en configurant les valeurs sur les vôtres. Si vous y êtes invité, sélectionnez A pour exécuter tout. Dans l’exemple, «  -Name » fait référence au nom de la connexion que vous voulez tester.

   ```azurepowershell-interactive
   Get-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
   ```
2. Une fois l’applet de commande exécutée, affichez les valeurs. Dans l’exemple ci-dessous, l’état de la connexion indique « Connecté » et vous pouvez voir les octets d’entrée et de sortie.
   
   ```
   "connectionStatus": "Connected",
   "ingressBytesTransferred": 33509044,
   "egressBytesTransferred": 4142431
   ```