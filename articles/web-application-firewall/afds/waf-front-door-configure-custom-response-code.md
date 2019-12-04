---
title: Configurer une réponse personnalisée pour le pare-feu d’applications web à l’aide d’Azure Front Door
description: Découvrez comment configurer un code et un message de réponse personnalisée lorsque le pare-feu d’applications web (WAF) bloque une requête.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 08/21/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 215d4058937ad5fded6bef7a36e873b52a1b5ae9
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185341"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Configurer une réponse personnalisée pour le pare-feu d’applications web Azure

Par défaut, lorsque le pare-feu d’applications web (WAF) Azure avec Azure Front Door bloque une requête en raison d’une règle vérifiée, il retourne un code d’état 403 avec le message **La requête est bloquée**. Cet article décrit comment configurer un code d’état de réponse et un message de réponse personnalisés lorsqu’une requête est bloquée par le pare-feu d’applications web (WAF).

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

## <a name="create-a-new-waf-policy-with-custom-response"></a>Créer une nouvelle stratégie de pare-feu d’applications web avec une réponse personnalisée 

Voici un exemple de création d’une nouvelle stratégie de pare-feu d’applications web avec le code d’état de réponse personnalisé 405 et le message **Vous avez été bloqué.** à l’aide de [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```azurepowershell
# WAF policy setting
New-AzFrontDoorWafPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

Modifiez le code de réponse personnalisé ou des paramètres du corps de réponse d’une stratégie de pare-feu d’applications web existante à l’aide de [Update-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy).

```azurepowershell
# modify WAF response code
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title> Forbidden</title></head><body></body></html>"
```

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur le [pare-feu d’applications web avec Azure Front Door](../afds/afds-overview.md)