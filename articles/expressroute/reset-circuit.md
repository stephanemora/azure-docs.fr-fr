---
title: 'Réinitialiser un circuit en échec - ExpressRoute : PowerShell : Azure | Microsoft Docs'
description: Cet article vous aide à réinitialiser un circuit ExpressRoute en état d’échec.
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: article
ms.date: 11/28/2018
ms.author: anzaman
ms.custom: seodec18
ms.openlocfilehash: e94ba2f7f6981355f4736683b891fbdced001d03
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54425337"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Réinitialiser un circuit ExpressRoute en échec

Quand une opération sur un circuit ExpressRoute ne se termine pas correctement, le circuit peut passer en état d’échec. Cet article vous aide à réinitialiser un circuit Azure ExpressRoute en état d’échec.

## <a name="reset-a-circuit"></a>Réinitialiser un circuit

1. Installez la dernière version des applets de commande PowerShell Azure Resource Manager. Pour plus d’informations, consultez [Installer et configurer Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).

2. Ouvrez la console PowerShell avec des privilèges élevés et connectez-vous à votre compte. Utilisez l’exemple suivant pour faciliter votre connexion :

  ```azurepowershell-interactive
  Connect-AzureRmAccount
  ```
3. Si vous disposez de plusieurs abonnements Azure, vérifiez les abonnements associés au compte.

  ```azurepowershell-interactive
  Get-AzureRmSubscription
  ```
4. Spécifiez l’abonnement à utiliser.

  ```azurepowershell-interactive
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
5. Exécutez les commandes suivantes pour réinitialiser un circuit en état d’échec :

  ```azurepowershell-interactive
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

Le circuit doit maintenant être sain. Ouvrez un ticket de support auprès du [support Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) si le circuit est toujours en état d’échec.

## <a name="next-steps"></a>Étapes suivantes

Ouvrez un ticket de support auprès du [support Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) si vous rencontrez encore des problèmes.
