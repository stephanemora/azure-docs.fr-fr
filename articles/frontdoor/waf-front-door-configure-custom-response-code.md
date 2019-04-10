---
title: Configurer une réponse personnalisée pour le pare-feu d’application web à Azure porte d’entrée
description: Découvrez comment configurer un code de réponse personnalisée et un message lorsque le pare-feu d’applications web (WAF) bloque une demande.
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 2d16893420f27caf4f8b00dc32069e3296d7c236
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59363020"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Configurer une réponse personnalisée pour le pare-feu d’application web Azure

Par défaut, lorsque le pare-feu d’applications Azure web (WAF) avec Azure porte d’entrée bloque une demande en raison d’une règle de mise en correspondance, elle retourne un code de 403 état avec **la demande est bloquée** message. Cet article décrit comment configurer un code d’état de réponse personnalisée et un message de réponse lorsqu’une demande est bloquée par le pare-feu d’applications Web.

## <a name="set-up-your-powershell-environment"></a>Configurer votre environnement PowerShell
Azure PowerShell fournit un ensemble d’applets de commande qui utilisent le modèle [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) pour gérer vos ressources Azure. 

Vous pouvez installer [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) sur votre ordinateur local et l’utiliser sur n’importe quelle session PowerShell. Suivez les instructions sur la page pour vous connecter avec vos informations d’identification Azure, puis installez le module Az PowerShell.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Se connecter à Azure avec une boîte de dialogue interactive
```
Connect-AzAccount
Install-Module -Name Az
```
Vérifiez que la version actuelle de PowerShellGet est installée. Exécutez la commande ci-dessous, puis rouvrez PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Installer le module Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Dans Azure, vous allouez les ressources associées à un groupe de ressources. Dans cet exemple, vous créez un groupe de ressources à l’aide de [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>Créer une nouvelle stratégie de pare-feu d’applications Web avec une réponse personnalisée 

Voici un exemple de création d’une nouvelle stratégie de pare-feu d’applications Web avec code d’état de réponse personnalisée 405 et le message pour la valeur **vous êtes bloqué.** à l’aide de [New-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/new-azfrontdoorfirewallPolicy).

```azurepowershell
# WAF policy setting
New-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

Modifier le code de réponse personnalisée ou des paramètres de corps de réponse d’une stratégie de pare-feu d’applications Web existante, à l’aide de [Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```azurepowershell
# modify WAF response code
Set-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Set-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title> Forbidden</title></head><body></body></html>"
```

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur [porte d’entrée](front-door-overview.md)