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
ms.openlocfilehash: 0332f189feb1b266eac29c9ea8a7f3ed86d6207a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58116569"
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
