---
title: Réécrire les en-têtes HTTP dans une Azure Application Gateway existante
description: Cet article fournit des informations sur la façon de réécrire les en-têtes HTTP dans une Azure Application Gateway existante à l’aide d’Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: 68da63bcad3c670c5e8bda62dda656e29c41f899
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55692914"
---
# <a name="rewrite-http-headers-in-an-existing-application-gateway"></a>Réécrire les en-têtes HTTP dans une Application Gateway existante

Vous pouvez utiliser Azure PowerShell pour configurer des [règles de réécriture d’en-têtes de requête et de réponse HTTP](rewrite-http-headers.md) dans une [référence SKU de passerelle d’application se mettant à l’échelle automatiquement et redondante dans la zone](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) existante

> [!IMPORTANT]
> La référence SKU de la passerelle d’application redondante interzone et avec mise à l’échelle automatique est disponible en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
>
> * Récupérer la configuration d’une passerelle d’application existante
> * Spécifier la configuration de règle de réécriture de votre en-tête http
> * Mettre à jour la passerelle d’application avec la configuration ci-dessus pour la réécriture des en-têtes http

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour ce didacticiel, vous devez exécuter Azure PowerShell localement. Vous devez avoir installé la version du module Az 1.0.0 ou version ultérieure. Exécutez `Import-Module Az`, puis`Get-Module Az` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps). Après avoir vérifié la version PowerShell, exécutez `Login-AzAccount` pour créer une connexion avec Azure.

## <a name="sign-in-to-azure"></a>Connexion à Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>**Spécifier la configuration de règle de réécriture de votre en-tête HTTP**

Configurez les nouveaux objets requis pour réécrire les en-têtes HTTP :

- **RequestHeaderConfiguration** : cet objet est utilisé pour spécifier les champs d’en-tête de requête que vous souhaitez réécrire et la nouvelle valeur de réécriture des en-têtes d’origine.
- **ResponseHeaderConfiguration** : cet objet est utilisé pour spécifier les champs d’en-tête de réponse que vous souhaitez réécrire et la nouvelle valeur de réécriture des en-têtes d’origine.
- **ActionSet** : cet objet contient les configurations des en-têtes de requête et de réponse spécifiés ci-dessus.
- **RewriteRule** : cet objet contient tous les *actionSets* spécifiés ci-dessus.
- **RewriteRuleSet** : cet objet contient toutes les *rewriteRules* et devra être attaché à une règle de routage des demandes, de base ou basée sur un chemin d’accès.

```azurepowershell
$requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
```

## <a name="retrieve-configuration-of-your-existing-application-gateway"></a>Récupérer la configuration de votre passerelle d’application existante

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-configuration-of-your-existing-request-routing-rule"></a>Récupérer la configuration de votre règle de routage des demandes existante

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name Rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Mettre à jour de la passerelle d’application avec la configuration pour la réécriture des en-têtes HTTP

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name rewriteRuleSet1 -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Supprimer une règle de réécriture

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "rewriteRuleSet1" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une passerelle d’application avec des règles d’acheminement par chemin d’URL](./tutorial-url-route-powershell.md)
