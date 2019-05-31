---
title: Configurer une règle de limite de taux de web application firewall pour porte - Azure PowerShell
description: Découvrez comment configurer une règle de limite de taux pour un point de terminaison existant porte d’entrée.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2019
ms.author: kumud;tyao
ms.openlocfilehash: 99b0cab3fd277f90a675f0e6087d572853053a08
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387348"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Configurer une règle de limite de taux de pare-feu web application à l’aide d’Azure PowerShell
La règle de limite de la vitesse (pare-feu) d’application web Azure pour Azure porte d’entrée contrôle le nombre de demandes autorisées à partir d’une adresse IP unique client pendant une durée d’une minute.
Cet article explique comment configurer une règle de limite de taux de WAF qui contrôle le nombre de demandes autorisées à partir d’un seul client à une application web qui contient */promo* dans l’URL à l’aide d’Azure PowerShell.

> [!IMPORTANT]
> La fonctionnalité de règles WAF taux limite pour Azure porte d’entrée est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables
Avant de commencer à configurer une stratégie de limite de débit, configurez votre environnement PowerShell et créer un profil de la porte d’entrée.
### <a name="set-up-your-powershell-environment"></a>Configurer votre environnement PowerShell
Azure PowerShell fournit un ensemble d’applets de commande qui utilisent le modèle [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) pour gérer vos ressources Azure. 

Vous pouvez installer [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) sur votre ordinateur local et l’utiliser sur n’importe quelle session PowerShell. Suivez les instructions sur la page, pour vous connecter avec vos informations d’identification Azure et installer le module PowerShell de Az.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Se connecter à Azure avec une boîte de dialogue interactive
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

## <a name="define-url-match-conditions"></a>Définir des conditions de correspondance url
Définir une condition de correspondance d’URL (URL contient /promo) à l’aide de [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject).
L’exemple suivant correspond à */promo* comme valeur de la *RequestUri* variable :

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Créer une règle de limite de taux personnalisé
Définir une limite de taux à l’aide [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject). Dans l’exemple suivant, la limite est définie sur 1000. Demandes à partir de n’importe quel client à la page de promotion supérieure à 1000 pendant une minute sont bloquées jusqu'à ce que la prochaine minute commence.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Configurer une stratégie de sécurité

Recherchez le nom du groupe de ressources qui contient le profil Front Door à l’aide de `Get-AzureRmResourceGroup`. Ensuite, configurez une stratégie de sécurité avec une règle de limite de taux personnalisé à l’aide [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) dans le groupe de ressources spécifié qui contient le profil de la porte d’entrée.

L’exemple ci-dessous utilise le nom de groupe de ressources *myResourceGroupFD1* en partant du principe que vous avez créé le profil Front Door à l’aide des instructions fournies dans l’article [Démarrage rapide : Créer un profil Front Door](quickstart-create-front-door.md).

 à l’aide de [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Stratégie de liaison à un hôte de serveur frontal porte d’entrée
Lier l’objet de stratégie de sécurité à un hôte de serveur frontal existant porte d’entrée et de mettre à jour les propriétés de la porte d’entrée. Tout d’abord récupérer l’objet de la porte d’entrée en utilisant [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) commande.
Ensuite, définissez le serveur frontal *WebApplicationFirewallPolicyLink* propriété le *resourceId* de « $ratePolicy » créé dans l’étape précédente avec [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) commande. 

L’exemple ci-dessous utilise le nom de groupe de ressources *myResourceGroupFD1* en partant du principe que vous avez créé le profil Front Door à l’aide des instructions fournies dans l’article [Démarrage rapide : Créer un profil Front Door](quickstart-create-front-door.md). En outre, dans l’exemple ci-dessous, remplacez $frontDoorName par le nom de votre profil porte d’entrée. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Vous devez uniquement définir *WebApplicationFirewallPolicyLink* propriété une fois pour lier une stratégie de sécurité à une porte d’entrée frontal. Mises à jour de stratégie suivantes sont automatiquement appliquées à la partie frontale.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [porte d’entrée](front-door-overview.md) 


