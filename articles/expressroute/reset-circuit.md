---
title: 'Réinitialiser un circuit en échec - ExpressRoute : PowerShell : Azure | Microsoft Docs'
description: Cet article vous aide à réinitialiser un circuit ExpressRoute en état d’échec.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/07/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 84c8275b7a7257530a735e8612047ef42e2f8a7c
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98011337"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Réinitialiser un circuit ExpressRoute en échec

Quand une opération sur un circuit ExpressRoute ne se termine pas correctement, le circuit peut passer en état d’échec. Cet article vous aidera à réinitialiser un circuit Azure ExpressRoute en état d’échec.

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

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

Ouvrir un ticket de support auprès de [Support Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) si vous rencontrez encore des problèmes.
