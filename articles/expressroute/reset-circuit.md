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
ms.openlocfilehash: f6fd8e5f0e5730e55fa5bbe13f74be796dd6a4cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66115851"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Réinitialiser un circuit ExpressRoute en échec

Quand une opération sur un circuit ExpressRoute ne se termine pas correctement, le circuit peut passer en état d’échec. Cet article vous aide à réinitialiser un circuit Azure ExpressRoute en état d’échec.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reset-a-circuit"></a>Réinitialiser un circuit

1. Installez la dernière version des applets de commande PowerShell Azure Resource Manager. Pour plus d’informations, consultez [Installer et configurer Azure PowerShell](/powershell/azure/install-az-ps).

2. Ouvrez la console PowerShell avec des privilèges élevés et connectez-vous à votre compte. Utilisez l’exemple suivant pour faciliter votre connexion :

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
3. Si vous disposez de plusieurs abonnements Azure, vérifiez les abonnements associés au compte.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
4. Spécifiez l’abonnement à utiliser.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
5. Exécutez les commandes suivantes pour réinitialiser un circuit en état d’échec :

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

Le circuit doit maintenant être sain. Ouvrez un ticket de support auprès du [support Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) si le circuit est toujours en état d’échec.

## <a name="next-steps"></a>Étapes suivantes

Ouvrez un ticket de support auprès du [support Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) si vous rencontrez encore des problèmes.
