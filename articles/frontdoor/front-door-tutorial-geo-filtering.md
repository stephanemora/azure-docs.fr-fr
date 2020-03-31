---
title: 'Tutoriel : Configurer une stratégie WAF de géofiltrage - Azure Front Door'
description: Dans ce didacticiel, vous allez apprendre à créer une stratégie de géofiltrage et à l’associer à votre hôte frontend Front Door existant
services: frontdoor
documentationcenter: ''
author: teresayao
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: tyao
ms.openlocfilehash: e3119745e35140d0344d25f34f54b63939d2542d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79471453"
---
# <a name="how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Guide pratique pour configurer une stratégie WAF de géofiltrage pour votre porte d’entrée
Dans ce didacticiel, vous allez apprendre à utiliser Azure PowerShell pour créer un exemple de stratégie de géofiltrage et à l’associer à votre hôte frontend Front Door existant. Cet exemple de stratégie de géofiltrage bloquera les demandes de tous les autres pays/régions, à l’exception des États-Unis.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) maintenant.

## <a name="prerequisites"></a>Prérequis
Avant de définir une stratégie de géofiltrage, configurez votre environnement PowerShell et créez un profil Front Door.
### <a name="set-up-your-powershell-environment"></a>Configurer votre environnement PowerShell
Azure PowerShell fournit un ensemble d’applets de commande qui utilisent le modèle [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) pour gérer vos ressources Azure. 

Vous pouvez installer [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) sur votre ordinateur local et l’utiliser sur n’importe quelle session PowerShell. Suivez les instructions sur la page pour vous connecter avec vos informations d’identification Azure, puis installez le module Az PowerShell.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Se connecter à Azure avec une boîte de dialogue interactive
```
Install-Module -Name Az
Connect-AzAccount
```
Vérifiez que la version actuelle de PowerShellGet est installée. Exécutez la commande ci-dessous, puis rouvrez PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>Installer le module Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>Créer un profil Front Door
Créez un profil Front Door en suivant les instructions décrites dans [Démarrage rapide : créer un profil Front Door](quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Définir la condition de correspondance du géofiltrage

Créez un exemple de condition de correspondance qui sélectionne les demandes ne provenant pas de « US » en utilisant [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) sur les paramètres lors de la création d’une condition de correspondance. Les indicatifs pays à deux lettres pour effectuer un mappage pays sont fournis [ici](front-door-geo-filtering.md).

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>Ajouter une condition de correspondance du géofiltrage à une règle à l’aide d’une action et d’une priorité

Créez un objet CustomRule `nonUSBlockRule` basé sur la condition de correspondance, sur une action et sur une priorité à l’aide de [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject).  Un CustomRule peut avoir plusieurs MatchCondition.  Dans cet exemple, une action est définie pour bloquer et une priorité est définie comme 1, il s’agit de la priorité la plus élevée.

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>Ajouter des règles à une stratégie
Recherchez le nom du groupe de ressources qui contient le profil Front Door à l’aide de `Get-AzResourceGroup`. Ensuite, créez un objet de stratégie `geoPolicy` contenant `nonUSBlockRule` à l’aide de [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) dans le groupe de ressources spécifié qui abrite le profil Front Door. Vous devez fournir un nom unique pour la stratégie de géofiltrage. 

L’exemple ci-dessous utilise le nom de groupe de ressources *myResourceGroupFD1* en partant du principe que vous avez créé le profil Front Door à l’aide des instructions fournies dans l’article [Démarrage rapide : Créer un profil Front Door](quickstart-create-front-door.md). Dans l’exemple ci-dessous, remplacez le nom de la stratégie *geoPolicyAllowUSOnly* par un nom de stratégie unique.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Lier une stratégie WAF et un hôte front-end Front Door
Liez l’objet de stratégie WAF à l’hôte front-end Front Door existant et mettez à jour les propriétés de la porte d’entrée. 

Pour ce faire, commencez par récupérer votre objet Front Door à l’aide de [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor). 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Ensuite, définissez la propriété WebApplicationFirewallPolicyLink front-end sur l’ID de ressource de `geoPolicy` à l’aide de [Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Vous ne devrez définir la propriété WebApplicationFirewallPolicyLink qu’une seule fois pour lier une stratégie WAF à un hôte front-end Front Door. Les mises à jour de stratégie suivantes sont automatiquement appliquées à l’hôte front-end.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur le [pare-feu d’application web Azure](waf-overview.md).
- Découvrez comment [créer une porte d’entrée](quickstart-create-front-door.md).
