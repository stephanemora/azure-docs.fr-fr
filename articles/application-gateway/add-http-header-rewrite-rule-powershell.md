---
title: Réécrire les en-têtes HTTP dans une passerelle d’application Azure Application Gateway
description: Cet article fournit des informations sur la façon de réécrire les en-têtes HTTP dans Azure Application Gateway à l’aide d’Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: bfafc74cbcb97f28cc085196a2cbaf4e9bf2e871
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548317"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Réécrire les en-têtes de demande et de réponse HTTP avec Azure Application Gateway - Azure PowerShell

Cet article vous montre comment utiliser Azure PowerShell pour configurer un [référence (SKU) de passerelle d’Application v2](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) pour réécrire les en-têtes HTTP dans les demandes et réponses.

> [!IMPORTANT]
> La référence SKU de la passerelle d’application redondante interzone et avec mise à l’échelle automatique est disponible en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables

- Pour ce didacticiel, vous devez exécuter Azure PowerShell localement. Vous devez avoir installé la version du module Az 1.0.0 ou version ultérieure. Exécutez `Import-Module Az`, puis`Get-Module Az` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Après avoir vérifié la version PowerShell, exécutez `Login-AzAccount` pour créer une connexion avec Azure.
- Vous devez disposer d’un environnement v2 passerelle d’Application étant donné que la capacité de réécriture de l’en-tête dans la référence (SKU) n’est pas pris en charge pour la référence SKU v1. Si vous n’avez pas la référence (SKU) v2, créez un [référence (SKU) de passerelle d’Application v2](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps>) avant de commencer.

## <a name="what-is-required-to-rewrite-a-header"></a>Ce qui est nécessaire de réécrire un en-tête

Pour configurer la réécriture d’en-tête HTTP, vous devez :

1. Créer les nouveaux objets requis pour réécrire les en-têtes HTTP :

   - **RequestHeaderConfiguration** : cet objet est utilisé pour spécifier les champs d’en-tête de requête que vous souhaitez réécrire et la nouvelle valeur de réécriture des en-têtes d’origine.

   - **ResponseHeaderConfiguration** : cet objet est utilisé pour spécifier les champs d’en-tête de réponse que vous souhaitez réécrire et la nouvelle valeur de réécriture des en-têtes d’origine.

   - **ActionSet** : cet objet contient les configurations des en-têtes de requête et de réponse spécifiés ci-dessus.

   - **Condition** : Il est une configuration facultative. Si une condition de la réécriture est ajoutée, il est évalué le contenu des requêtes HTTP (S) et des réponses. La décision d’exécuter l’action de réécriture associée à la condition de réécriture reposera si la demande de HTTP (S) ou la réponse mise en correspondance avec la condition de réécriture. 

     Si plusieurs conditions sont associé à une action, puis l’action sera exécutée uniquement lorsque toutes les conditions sont remplies, par exemple, une opération AND logique sera effectuée.

   - **RewriteRule**: contient plusieurs réécriture action - combinaisons de condition de réécriture.

   - **RuleSequence**: Il s’agit d’une configuration facultative. Il permet de déterminer l’ordre dans lequel les règles de réécriture différents sont exécutées. Cela est utile lorsqu’il existe plusieurs règles de réécriture dans un ensemble de réécriture. La règle de réécriture avec la valeur de séquence de règle inférieure obtient exécutée en premier. Si vous fournissez la même séquence de règle à deux règles de réécriture, l’ordre d’exécution sera non déterministe.

     Si vous ne spécifiez pas explicitement le RuleSequence, définira une valeur par défaut de 100.

   - **RewriteRuleSet**: cet objet contient plusieurs règles de réécriture qui seront associés à une règle de routage de demande.

2. Vous devez attacher le rewriteRuleSet avec une règle de routage. Il s’agit, car la configuration de la réécriture est attachée à l’écouteur source via la règle de routage. Lorsque vous utilisez une règle d’acheminement de base, la configuration de réécriture de l’en-tête est associée à un écouteur de la source et est une réécriture de l’en-tête global. Lorsqu’une règle d’acheminement basée sur le chemin d’accès est utilisée, la configuration de réécriture de l’en-tête est définie sur le mappage de chemin d’accès d’URL. Elle s’applique donc exclusivement à la zone de chemin d’accès spécifique d’un site.

Vous pouvez créer plusieurs jeux de réécriture d’en-tête http et chaque jeu de réécriture peut être appliqué à plusieurs écouteurs. Toutefois, vous pouvez appliquer uniquement un ensemble à un port d’écoute spécifique de réécriture.

## <a name="sign-in-to-azure"></a>Connexion à Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>**Spécifier la configuration de règle de réécriture de votre en-tête HTTP**

Dans cet exemple, nous allons modifier l’URL de redirection en réécrivant l’en-tête d’emplacement dans la réponse http, chaque fois que l’en-tête d’emplacement contient une référence à « azurewebsites.net ». Pour ce faire, nous allons ajouter une condition à évaluer si l’en-tête d’emplacement dans la réponse contient azurewebsites.net en utilisant le modèle `(https?):\/\/.*azurewebsites\.net(.*)$`. Nous allons utiliser `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` en tant que la valeur d’en-tête. Cette opération va remplacer *azurewebsites.net* avec *contoso.com* dans l’en-tête location.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-configuration-of-your-existing-application-gateway"></a>Récupérer la configuration de votre passerelle d’application existante

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-configuration-of-your-existing-request-routing-rule"></a>Récupérer la configuration de votre règle de routage des demandes existante

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Mettre à jour de la passerelle d’application avec la configuration pour la réécriture des en-têtes HTTP

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Supprimer une règle de réécriture

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la configuration requise pour accomplir certaines courantes cas d’utilisation, consultez [scénarios de réécriture d’en-tête commun](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).