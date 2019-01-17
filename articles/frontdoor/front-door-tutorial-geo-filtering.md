---
title: Didacticiel - Configurer un filtrage géographique sur un domaine Azure Front Door Service | Microsoft Docs
description: Dans ce didacticiel, vous allez apprendre à créer une stratégie de filtrage géographique simple et à l’associer à votre hôte frontend Front Door existant
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2018
ms.author: sharadag
ms.openlocfilehash: 68da9a0255cde6cbad5c675901c80193888bf255
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214876"
---
# <a name="how-to-set-up-a-geo-filtering-policy-for-your-front-door"></a>Comment configurer une stratégie de filtrage géographique pour votre porte d’entrée
Dans ce didacticiel, vous allez apprendre à utiliser Azure PowerShell pour créer un exemple de stratégie de filtrage géographique et à l’associer à votre hôte frontend Front Door existant. Cet exemple de stratégie de filtrage géographique bloquera les demandes de tous les autres pays, à l’exception des États-Unis.

## <a name="1-set-up-your-powershell-environment"></a>1. Configurer votre environnement PowerShell
Azure PowerShell fournit un ensemble d’applets de commande qui utilisent le modèle [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) pour gérer vos ressources Azure. 

Vous pouvez installer [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) sur votre ordinateur local et l’utiliser sur n’importe quelle session PowerShell. Suivez les instructions sur la page, pour vous connecter avec vos informations d’identification Azure, puis installez AzureRM.
```
# Connect to Azure with an interactive dialog for sign-in
Connect-AzureRmAccount
Install-Module -Name AzureRM
```
> [!NOTE]
>  La prise en charge [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) sera bientôt disponible.

Avant d’installer le module Front Door, assurez-vous que vous avez installé la version actuelle de PowerShellGet. Exécutez la commande ci-dessous, puis rouvrez PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

Installez le module AzureRM.FrontDoor. 

```
Install-Module -Name AzureRM.FrontDoor -AllowPrerelease
```

## <a name="2-define-geo-filtering-match-conditions"></a>2. Définir la/les condition(s) de correspondance du filtrage géographique
Tout d’abord, créez un exemple de condition de correspondance qui sélectionne les demandes ne provenant ne pas de « US ». Reportez-vous au [guide](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoormatchconditionobject) des paramètres PowerShell lors de la création d’une condition de correspondance. L’indicatif pays à deux lettres pour effectuer un mappage pays est fourni [ici](front-door-geo-filtering.md).

```
$nonUSGeoMatchCondition = New-AzureRmFrontDoorMatchConditionObject -MatchVariable RemoteAddr -OperatorProperty GeoMatch -NegateCondition $true -MatchValue "US"
```
 
## <a name="3-add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>3. Ajouter une condition de correspondance du filtrage géographique à une règle à l’aide d’une action et d’une priorité

Puis créez un objet CustomRule `nonUSBlockRule` basé sur la condition de correspondance, sur une action et sur une priorité.  Un CustomRule peut avoir plusieurs MatchCondition.  Dans cet exemple, une action est définie pour bloquer et une priorité est définie comme 1, il s’agit de la priorité la plus élevée.

```
$nonUSBlockRule = New-AzureRmFrontDoorCustomRuleObject -Name "geoFilterRule" -RuleType MatchRule -MatchCondition $nonUSGeoMatchCondition -Action Block -Priority 1
```

Reportez-vous au [guide](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoorcustomruleobject) des paramètres PowerShell lors de la création d’un objet CustomRule.

## <a name="4-add-rules-to-a-policy"></a>4. Ajouter des règles à une stratégie
Cette étape crée un `geoPolicy` contenant d’objets de stratégie `nonUSBlockRule` à partir de l’étape précédente dans le groupe de ressources spécifié. Utilisez `Get-AzureRmResourceGroup` pour rechercher votre ResourceGroupName $resourceGroup.

```
$geoPolicy = New-AzureRmFrontDoorFireWallPolicy -Name "geoPolicyAllowUSOnly" -resourceGroupName $resourceGroup -Customrule $nonUSBlockRule  -Mode Prevention -EnabledState Enabled
```

Reportez-vous au [guide](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoorfirewallpolicy) des paramètres PowerShell lors de la création d’une stratégie.

## <a name="5-link-policy-to-a-front-door-frontend-host"></a>5. Lier une stratégie et un hôte frontend Front Door
Les dernières étapes consistent à lier l’objet de stratégie de protection à un hôte frontend Front Door existant et de mettre à jour les propriétés de la porte d’entrée. Vous récupérerez en premier votre objet Front Door à l’aide de [Get-AzureRmFrontDoor](https://docs.microsoft.com/azure/frontdoor/get-azurermfrontdoor), puis en définissant sa propriété de serveur frontal WebApplicationFirewallPolicyLink pour l’ID de ressource de `geoPolicy`.

```
$geoFrontDoorObjectExample = Get-AzureRmFrontDoor -ResourceGroupName $resourceGroup
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Utilisez la [commande](https://docs.microsoft.com/azure/frontdoor/set-azurermfrontdoor) suivante pour mettre à jour l’objet Front Door.

```
Set-AzureRmFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Vous ne devrez définir la propriété WebApplicationFirewallPolicyLink qu’une seule fois pour lier une stratégie de protection à un hôte frontend Front Door. Les mises à jour de stratégie suivantes seront automatiquement appliquées à l’hôte frontend.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [sécurité de la couche Application avec Front Door](front-door-application-security.md).
- Découvrez comment [créer une porte d’entrée](quickstart-create-front-door.md).
