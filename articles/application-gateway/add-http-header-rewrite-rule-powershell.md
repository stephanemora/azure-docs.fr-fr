---
title: Réécrire les en-têtes HTTP dans une passerelle d’application Azure Application Gateway
description: Cet article fournit des informations sur la façon de réécrire les en-têtes HTTP dans Azure Application Gateway à l’aide d’Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 47fe6a5247622e3ad3b3720955068580e0329913
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64947201"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Réécrire les en-têtes de demande et de réponse HTTP avec Azure Application Gateway - Azure PowerShell

Cet article explique comment utiliser Azure PowerShell pour configurer un [référence (SKU) de passerelle d’Application v2](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) instance à réécrire les en-têtes HTTP dans les demandes et réponses.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="before-you-begin"></a>Avant de commencer

- Vous devez exécuter Azure PowerShell localement pour effectuer les étapes décrites dans cet article. Vous devez également disposer Az version 1.0.0 du module ou version ultérieure. Exécutez `Import-Module Az` , puis `Get-Module Az` pour déterminer la version que vous avez installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Après avoir vérifié la version PowerShell, exécutez `Login-AzAccount` pour créer une connexion avec Azure.
- Vous devez disposer d’une instance de référence (SKU) de passerelle d’Application v2. Réécriture des en-têtes n’est pas pris en charge dans la référence (SKU) v1. Si vous n’avez pas la référence (SKU) v2, créez un [référence (SKU) de passerelle d’Application v2](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) instance avant de commencer.

## <a name="create-required-objects"></a>Créer des objets requis

Pour configurer la réécriture d’en-tête HTTP, vous devez suivre ces étapes.

1. Créer les objets qui sont requis pour la réécriture d’en-tête HTTP :

   - **RequestHeaderConfiguration**: Permet de spécifier les champs d’en-tête de demande que vous souhaitez réécrire et la nouvelle valeur pour les en-têtes.

   - **ResponseHeaderConfiguration**: Permet de spécifier les champs d’en-tête de réponse que vous souhaitez réécrire et la nouvelle valeur pour les en-têtes.

   - **ActionSet**: Contient les configurations des en-têtes de demande et de réponse spécifiés précédemment.

   - **Condition** : Une configuration facultative. Conditions de réécriture évaluent le contenu des requêtes HTTP (S) et des réponses. L’action de réécriture se produira si la demande de HTTP (S) ou la réponse correspond à la condition de réécriture.

     Si vous associez plusieurs conditions à une action, l’action se produit uniquement lorsque toutes les conditions sont remplies. En d’autres termes, l’opération est une opération AND logique.

   - **RewriteRule**: Contient plusieurs réécriture action / réécrire les combinaisons de condition.

   - **RuleSequence**: Exécution d’une configuration facultative qui permet de déterminer l’ordre dans lequel les règles de réécriture. Cette configuration est utile lorsque vous avez plusieurs règles de réécriture dans un ensemble de réécriture. Une règle de réécriture qui a une valeur de séquence de règle inférieure s’exécute en premier. Si vous affectez la même valeur de séquence de règle à deux règles de réécriture, l’ordre d’exécution est non déterministe.

     Si vous ne spécifiez pas explicitement le RuleSequence, une valeur par défaut de 100 est définie.

   - **RewriteRuleSet**: Contient plusieurs règles de réécriture qui seront associés à une règle de routage de demande.

2. Attachez le RewriteRuleSet à une règle de routage. La configuration de la réécriture est attachée à l’écouteur source via la règle de routage. Lorsque vous utilisez une règle de routage de base, la configuration de réécriture de l’en-tête est associée à un écouteur de la source et est une réécriture de l’en-tête global. Lorsque vous utilisez une règle de routage basée sur le chemin d’accès, la configuration de réécriture de l’en-tête est définie sur le mappage de chemin d’accès d’URL. Dans ce cas, il s’applique uniquement à la zone de chemin d’accès spécifique d’un site.

Vous pouvez créer plusieurs jeux de réécriture d’en-tête HTTP et appliquer chaque réécriture définie sur plusieurs écouteurs. Mais vous pouvez appliquer uniquement un ensemble à un port d’écoute spécifique de réécriture.

## <a name="sign-in-to-azure"></a>Connexion à Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>Spécifier la configuration de la règle de réécriture de l’en-tête HTTP

Dans cet exemple, nous allons modifier une URL de redirection en réécrivant l’en-tête d’emplacement dans la réponse HTTP, chaque fois que l’en-tête d’emplacement contient une référence à azurewebsites.net. Pour ce faire, nous allons ajouter une condition à évaluer si l’en-tête d’emplacement dans la réponse contient azurewebsites.net. Nous allons utiliser le modèle `(https?):\/\/.*azurewebsites\.net(.*)$`. Et nous allons utiliser `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` en tant que la valeur d’en-tête. Cette valeur remplacera *azurewebsites.net* avec *contoso.com* dans l’en-tête location.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>Récupérer la configuration de votre passerelle d’application

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>Récupérer la configuration de votre règle de routage de demande

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

Pour en savoir plus sur la façon de configurer certains scénarios courants d’utilisation, consultez [scénarios de réécriture d’en-tête commun](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).