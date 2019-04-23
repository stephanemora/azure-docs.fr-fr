---
title: Configurer une stratégie de pare-feu (WAF) d’application web avec des règles personnalisées et l’ensemble par défaut Ruse pour porte - Azure PowerShell
description: Découvrez comment configurer un pare-feu WAF stratégie est constituée de règles personnalisées et gérés pour un point de terminaison existant porte d’entrée.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: kumud;tyao
ms.openlocfilehash: 7d024dd958e6b29b52f095a9a55a67154bf6cde6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792078"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Configurer une stratégie de pare-feu d’applications web à l’aide d’Azure PowerShell
Stratégie de pare-feu (WAF) d’application web Azure définit des inspections requises lorsqu’une demande arrive à la porte d’entrée.
Cet article explique comment configurer une stratégie de pare-feu d’applications Web qui se compose de certaines règles personnalisées et avec Azure managed ensemble par défaut Ruse activé.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables
Avant de commencer à configurer une stratégie de limite de débit, configurez votre environnement PowerShell et créer un profil de la porte d’entrée.
### <a name="set-up-your-powershell-environment"></a>Configurer votre environnement PowerShell
Azure PowerShell fournit un ensemble d’applets de commande qui utilisent le modèle [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) pour gérer vos ressources Azure. 

Vous pouvez installer [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) sur votre ordinateur local et l’utiliser sur n’importe quelle session PowerShell. Suivez les instructions sur la page, pour vous connecter avec vos informations d’identification Azure et installer le module PowerShell de Az.

#### <a name="sign-in-to-azure"></a>Connexion à Azure
```
Connect-AzAccount

```
Avant d’installer le module Front Door, assurez-vous que vous avez installé la version actuelle de PowerShellGet. Exécutez la commande ci-dessous, puis rouvrez PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Installer le module Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Créer un profil Front Door
Créez un profil Front Door en suivant les instructions décrites dans [Démarrage rapide : Créer un profil de la porte d’entrée](quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>Règle personnalisée en fonction des paramètres de http

L’exemple suivant montre comment configurer une règle personnalisée avec deux conditions de correspondance à l’aide de [New-AzFrontDoorMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoormatchconditionobject). Les demandes sont à partir d’un site spécifié, tel que défini par le point d’accès et chaîne de requête ne contient-elle pas de « password ». 

```powershell-interactive
$referer = New-AzFrontDoorMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Règle personnalisée selon la méthode de demande http
Créer une règle de blocage « PUT » l’à l’aide de la méthode [New-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-AzFrontDoorCustomRuleObject) comme suit :

```powershell-interactive
$put = New-AzFrontDoorMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Créer une règle personnalisée en fonction de la contrainte de taille

L’exemple suivant crée une règle qui bloque les demandes avec l’Url de plus de 100 caractères, à l’aide d’Azure PowerShell :
```powershell-interactive
$url = New-AzFrontDoorMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Ajouter managé par défaut d’ensemble de règles

L’exemple suivant crée un managé par défaut règle définie à l’aide d’Azure PowerShell :
```powershell-interactive
$managedRules = New-AzFrontDoorManagedRuleObject -Type DefaultRuleSet -Version "preview-0.1"
```
## <a name="configure-a-security-policy"></a>Configurer une stratégie de sécurité

Recherchez le nom du groupe de ressources qui contient le profil Front Door à l’aide de `Get-AzResourceGroup`. Ensuite, configurez une stratégie de sécurité avec les règles créées dans les étapes précédentes à l’aide de [New-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/new-azfrontdoorfirewallPolicy) dans le groupe de ressources spécifié qui contient le profil de la porte d’entrée.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorFireWallPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Stratégie de liaison à un hôte de serveur frontal porte d’entrée
Lier l’objet de stratégie de sécurité à un hôte de serveur frontal existant porte d’entrée et de mettre à jour les propriétés de la porte d’entrée. Récupérez tout d’abord l’objet de la porte d’entrée en utilisant [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor).
Ensuite, définissez le serveur frontal *WebApplicationFirewallPolicyLink* propriété le *resourceId* de « $$myWAFPolicy » créé dans l’étape précédente avec [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor). 

L’exemple ci-dessous utilise le nom de groupe de ressources *myResourceGroupFD1* en partant du principe que vous avez créé le profil Front Door à l’aide des instructions fournies dans l’article [Démarrage rapide : Créer un profil Front Door](quickstart-create-front-door.md). En outre, dans l’exemple ci-dessous, remplacez $frontDoorName par le nom de votre profil porte d’entrée. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Vous devez uniquement définir *WebApplicationFirewallPolicyLink* propriété une fois pour lier une stratégie de sécurité à une porte d’entrée frontal. Mises à jour de stratégie suivantes sont automatiquement appliquées à la partie frontale.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [porte d’entrée](front-door-overview.md) 
- En savoir plus sur [WAF pour porte d’entrée](waf-overview.md)
