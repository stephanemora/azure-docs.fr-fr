---
title: Limites de la taille des demandes adressées au pare-feu d’applications web et listes d’exclusions dans Azure Application Gateway - Portail Azure
description: Cet article fournit des informations sur la configuration des limites de la taille des demandes adressées au pare-feu d’applications web et des listes d’exclusions dans Application Gateway avec le portail Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.workload: infrastructure-services
ms.date: 1/29/2019
ms.author: victorh
ms.openlocfilehash: 24cb5d7ddb1bfbb64796d871aa79109dfdfa94be
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55492927"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Limites de la taille des demandes adressées au pare-feu d’application web et listes d’exclusions

Le pare-feu d’applications web (WAF) Azure Application Gateway fournit une protection pour les applications web. Cet article décrit la configuration des limites de la taille des demandes adressées au WAF et des listes d’exclusions.

## <a name="waf-request-size-limits"></a>Limites de la taille des demandes adressées au WAF

![Limites de la taille des demandes](media/application-gateway-waf-configuration/waf-requestsizelimit.png)

Le pare-feu d’applications web vous permet de configurer des limites maximale et minimale de la taille des demandes. Les configurations de limites à deux tailles suivantes sont disponibles :

- Le champ de la taille maximale du corps de la demande est spécifié en Ko et contrôle la limite globale de la taille de la demande à l’exclusion de tout chargement de fichier. La valeur de ce champ peut varier de 1 Ko à 128 Ko. La valeur par défaut pour la taille du corps de la demande est de 128 Ko.
- Le champ de la limite de chargement de fichier est spécifié en Mo et régit la taille maximale autorisée pour la limite de chargement de fichier. La valeur de ce champ peut être comprise entre 1 Mo et 500 Mo pour les grandes instances SKU et 100 Mo pour les instances SKU intermédiaires. La valeur par défaut pour la limite de chargement de fichier est de 100 Mo.

Le WAF offre également un bouton configurable qui permet d’activer ou de désactiver l’inspection du corps de la demande. Par défaut, l’inspection du corps de la demande est activée. Si l’inspection du corps de la demande est désactivée, le WAF n’évalue pas le contenu du corps du message HTTP. Dans ce cas, le WAF continue d’appliquer ses règles sur les en-têtes, les cookies et les URI. Si l’inspection du corps de la demande est désactivée, le champ de la taille maximale du corps de la demande n’est pas applicable et ne peut pas être défini. La désactivation de l’inspection du corps de la demande permet l’envoi au WAF de messages dont la taille est supérieure à 128 Ko, mais le corps du message n’est pas inspecté.

## <a name="waf-exclusion-lists"></a>Listes d’exclusions du WAF

![waf-exclusion.png](media/application-gateway-waf-configuration/waf-exclusion.png)

Les listes d’exclusion du WAF vous permettent d’omettre certains attributs de la demande dans une évaluation par le WAF. À titre d’exemple courant, citons les jetons Active Directory insérés qui sont utilisés pour les champs d’authentification ou de mot de passe. Ces attributs sont sujets à contenir des caractères spéciaux qui peuvent déclencher un faux positif dans les règles du pare-feu d’applications web. Une fois ajouté à la liste d’exclusions du WAF, un attribut n’est pris en considération par aucune règle du pare-feu d’applications web configurée et active. Les listes d’exclusions ont une portée globale.

Les attributs suivants peuvent être ajoutés aux listes d’exclusion :

* En-têtes de demande
* Cookies de requête
* Corps de la requête

   * Données de plusieurs parties de formulaire
   * XML
   * JSON

Vous pouvez spécifier une correspondance exacte avec l'en-tête ou le corps d'une requête, un cookie ou un attribut de chaîne de requête  ou spécifier des correspondances partielles. L'exclusion porte toujours sur un champ d'en-tête, jamais sur sa valeur. Les règles d'exclusion ont une portée globale, et s'appliquent à toutes les pages et à toutes les règles.

Voici les opérateurs de critères de correspondance pris en charge :

- **Est égal à** :  cet opérateur est utilisé pour une correspondance exacte. Par exemple, pour sélectionner l’en-tête **bearerToken**, utilisez l’opérateur d’égalité avec le sélecteur défini sur **bearerToken**.
- **Commence par** : cet opérateur correspond à tous les champs qui commencent par la valeur de sélecteur spécifiée.
- **Se termine par** :  cet opérateur correspond à tous les champs de demande qui se terminent par la valeur de sélecteur spécifiée.
- **Contient** : cet opérateur correspond à tous les champs de demande qui se contiennent la valeur de sélecteur spécifiée.

Dans tous les cas, la correspondance respecte la casse, et les expressions régulières ne sont pas autorisées en guise de sélecteurs.

### <a name="examples"></a>Exemples

L’extrait de code Azure PowerShell suivant montre comment utiliser les exclusions :

```azurepowershell
// exclusion 1: exclude request head start with xyz
// exclusion 2: exclude request args equals a

$exclusion1 = New-AzureRmApplicationGatewayFirewallExclusionConfig -MatchVariable "RequestHeaderNames" -SelectorMatchOperator "StartsWith" -Selector "xyz"

$exclusion2 = New-AzureRmApplicationGatewayFirewallExclusionConfig -MatchVariable "RequestArgNames" -SelectorMatchOperator "Equals" -Selector "a"

// add exclusion lists to the firewall config

$firewallConfig = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention -RuleSetType "OWASP" -RuleSetVersion "2.2.9" -DisabledRuleGroups $disabledRuleGroup1,$disabledRuleGroup2 -RequestBodyCheck $true -MaxRequestBodySizeInKb 80 -FileUploadLimitInMb 70 -Exclusions $exclusion1,$exclusion2
```

L’extrait de code json suivant montre comment utiliser les exclusions :

```json
"webApplicationFirewallConfiguration": {
          "enabled": "[parameters('wafEnabled')]",
          "firewallMode": "[parameters('wafMode')]",
          "ruleSetType": "[parameters('wafRuleSetType')]",
          "ruleSetVersion": "[parameters('wafRuleSetVersion')]",
          "disabledRuleGroups": [],
          "exclusions": [
            {
                "matchVariable": "RequestArgNames",
                "selectorMatchOperator": "StartsWith",
                "selector": "a^bc"
            }
```

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré vos paramètres du pare-feu d’applications web, vous pouvez apprendre à afficher vos journaux WAF. Pour plus d’informations, consultez [Diagnostics Application Gateway](application-gateway-diagnostics.md#diagnostic-logging).
