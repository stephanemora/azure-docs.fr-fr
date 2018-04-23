---
title: 'Réinitialiser un circuit Azure ExpressRoute en échec : PowerShell | Microsoft Docs'
description: Cet article vous aide à réinitialiser un circuit ExpressRoute en état d’échec.
documentationcenter: na
services: expressroute
author: anzaman
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: anzaman;cherylmc
ms.openlocfilehash: 423bc1d6409e5b7fe02339a05d0775f4ff42de49
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2018
---
# <a name="reset-a-failed-expressroute-circuit"></a>Réinitialiser un circuit ExpressRoute en échec

Quand une opération sur un circuit ExpressRoute ne se termine pas correctement, le circuit peut passer en état d’échec. Cet article vous aide à réinitialiser un circuit Azure ExpressRoute en état d’échec.

## <a name="reset-a-circuit"></a>Réinitialiser un circuit

1. Installez la dernière version des applets de commande PowerShell Azure Resource Manager. Pour plus d’informations, consultez [Installer et configurer Azure PowerShell](/powershell/azure/install-azurerm-ps).

2. Ouvrez la console PowerShell avec des privilèges élevés et connectez-vous à votre compte. Utilisez l’exemple suivant pour faciliter votre connexion :

  ```powershell
  Connect-AzureRmAccount
  ```
3. Si vous disposez de plusieurs abonnements Azure, vérifiez les abonnements associés au compte.

  ```powershell
  Get-AzureRmSubscription
  ```
4. Spécifiez l’abonnement à utiliser.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
5. Exécutez les commandes suivantes pour réinitialiser un circuit en état d’échec :

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

Le circuit doit maintenant être sain. Ouvrez un ticket de support auprès du [support Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) si le circuit est toujours en état d’échec.

## <a name="next-steps"></a>Étapes suivantes

Ouvrez un ticket de support auprès du [support Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) si vous rencontrez encore des problèmes.
