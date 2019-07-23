---
title: Créer et utiliser les règles personnalisées du pare-feu d’applications web (WAF) Azure v2
description: Cet article fournit des informations sur la création des règles personnalisées de pare-feu d’applications web (WAF) v2 dans Application Gateway.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 86ddb0b608cd17814cbcbb902f0b2905fe61094a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164669"
---
# <a name="create-and-use-web-application-firewall-v2-custom-rules"></a>Créer et utiliser les règles personnalisées du pare-feu d’applications web v2

Le pare-feu d’applications web (WAF) v2 Azure Application Gateway fournit une protection pour les applications web. Cette protection est fournie par le jeu de règles (Core Rule Set, CRS) de l’Open Web Application Security Project (OWASP). Dans certains cas, vous devrez peut-être créer vos propres règles personnalisées pour répondre à vos besoins spécifiques. Pour plus d’informations sur les règles de pare-feu d’applications web personnalisées, consultez [Vue d’ensemble des règles personnalisées pour un pare-feu d’applications web](custom-waf-rules-overview.md).

Cet article montre quelques exemples de règles personnalisées que vous pouvez créer et utiliser avec votre pare-feu d’applications web v2. Pour savoir comment déployer un pare-feu d’applications web avec une règle personnalisée à l’aide d’Azure PowerShell, consultez [Configurer les règles personnalisées du pare-feu d’applications web à l’aide d’Azure PowerShell](configure-waf-custom-rules.md).

>[!NOTE]
> Si votre passerelle Application Gateway n’utilise pas la couche WAF, l’option de mise à niveau de la passerelle Application Gateway vers la couche WAF s’affiche dans le volet de droite.

![Activer WAF][fig1]

## <a name="example-1"></a>Exemple 1

Vous souhaitez empêcher un robot nommé *evilbot* d’analyser votre site web. Dans ce cas, le blocage doit porter sur *evilbot* de l’agent utilisateur dans les en-têtes de requête.

Logique : p

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable `
   -Operator Contains `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name blockEvilBot `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

Voici le JSON correspondant :

```json
  {
    "customRules": [
      {
        "name": "blockEvilBot",
        "ruleType": "MatchRule",
        "priority": 2,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "User-Agent",
            "matchValues": [
              "evilbot"
            ]
          }
        ]
      }
    ]
  }
```

Pour voir un pare-feu d’applications web déployé à l’aide de cette règle personnalisée, consultez [Configurer une règle personnalisée du pare-feu d’applications web à l’aide d’Azure PowerShell](configure-waf-custom-rules.md).

### <a name="example-1a"></a>Exemple 1a

Vous pouvez accomplir la même chose à l’aide d’une expression régulière :

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable `
   -Operator Regex `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name blockEvilBot `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

Voici le JSON correspondant :

```json
  {
    "customRules": [
      {
        "name": "blockEvilBot",
        "ruleType": "MatchRule",
        "priority": 2,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "User-Agent",
            "matchValues": [
              "evilbot"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="example-2"></a>Exemple 2

Vous souhaitez bloquer toutes les requêtes provenant d’adresses IP comprises dans la plage 198.168.5.4/24.

Dans cet exemple, vous allez bloquer tout le trafic provenant d’une plage d’adresses IP. Le nom de la règle est *myrule1* et la priorité est définie sur 100.

Logique : p

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.4/24" `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 100 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block
```

Voici le JSON correspondant :

```json
  {
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 100,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RemoteAddr",
            "operator": "IPMatch",
            "matchValues": [
              "192.168.5.4/24"
            ]
          }
        ]
      }
    ]
  }
```

Règle CRS correspondante : `SecRule REMOTE_ADDR "@ipMatch 192.168.5.4/24" "id:7001,deny"`

## <a name="example-3"></a>Exemple 3

Pour cet exemple, vous souhaitez bloquer *evilbot* de l’en-tête User-Agent et le trafic compris dans la plage 192.168.5.4/24. Pour ce faire, vous pouvez créer deux conditions de correspondance distinctes et les placer dans la même règle. Cela garantit le blocage d’*evilbot* de l’en-tête User-Agent **et** des adresses IP de la plage 192.168.5.4/24.

Logique : p **and** q

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

 $variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.4/24" `
   -NegationCondition $False

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

 $rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule `
   -Priority 100 `
   -RuleType MatchRule `
   -MatchCondition $condition1, $condition2 `
   -Action Block
```

Voici le JSON correspondant :

```json
{ 

    "customRules": [ 
      { 
        "name": "myrule", 
        "ruleType": "MatchRule", 
        "priority": 100, 
        "action": "block", 
        "matchConditions": [ 
            { 
              "matchVariable": "RemoteAddr", 
              "operator": "IPMatch", 
              "negateCondition": true, 
              "matchValues": [ 
                "192.168.5.4/24" 
              ] 
            }, 
            { 
              "matchVariable": "RequestHeaders", 
              "selector": "User-Agent", 
              "operator": "Contains", 
              "transforms": [ 
                "Lowercase" 
              ], 
              "matchValues": [ 
                "evilbot" 
              ] 
            } 
        ] 
      } 
    ] 
  } 
```

## <a name="example-4"></a>Exemple 4

Pour cet exemple, vous souhaitez l’activation du blocage si la demande est en dehors de la plage d’adresses IP *192.168.5.4/24*, ou si la chaîne de l’agent utilisateur n’est pas *chrome* (ce qui signifie que l’utilisateur n’utilise pas le navigateur Chrome). Étant donné que cette logique utilise **ou**, les deux conditions se trouvent dans des règles distinctes, comme cela a été abordé dans l’exemple suivant. *myrule1* et *myrule2* doivent toutes deux correspondre pour bloquer le trafic.

Logique : **not** (p **and** q) = **not** p **or not** q.

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

$variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.4/24" `
   -NegationCondition $True

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "chrome" `
   -Transform Lowercase `
   -NegationCondition $True

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 100 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block

$rule2 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule2 `
   -Priority 200 `
   -RuleType MatchRule `
   -MatchCondition $condition2 `
   -Action Block
```

Voici le JSON correspondant :

```json
{
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 100,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "IPMatch",
            "negateCondition": true,
            "matchValues": [
              "192.168.5.4/24"
            ]
          }
        ]
      },
      {
        "name": "myrule2",
        "ruleType": "MatchRule",
        "priority": 200,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "selector": "User-Agent",
            "operator": "Contains",
            "negateCondition": true,
            "transforms": [
              "Lowercase"
            ],
            "matchValues": [
              "chrome"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="example-5"></a>Exemple 5

Vous souhaitez bloquer du code SQLI personnalisé. Dans la mesure où la logique utilisée ici est **or**, et où toutes les valeurs se trouvent dans *RequestUri*, toutes les *MatchValues* peuvent se trouver dans une liste séparée par des virgules.

Logique : p **or** q **or** r

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri 
$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator Contains `
   -MatchValue "1=1", "drop tables", "'—" `
   -NegationCondition $False

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule4 `
   -Priority 100 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block
```

Voici le JSON correspondant :

```json
  {
    "customRules": [
      {
        "name": "myrule4",
        "ruleType": "MatchRule",
        “priority”: 100
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "1=1",
              "drop tables",
              "'--"
            ]
          }
        ]
      }
    ]
  }
```

Autre Azure PowerShell :

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri
$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator Contains `
   -MatchValue "1=1" `
   -NegationCondition $False

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 100 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
-Action Block

$variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "drop tables" `
   -NegationCondition $False

$rule2 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule2 `
   -Priority 200 `
   -RuleType MatchRule `
   -MatchCondition $condition2 `
   -Action Block

$variable3 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri

$condition3 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable3 `
   -Operator Contains `
   -MatchValue "’—" `
   -NegationCondition $False

$rule3 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule3 `
   -Priority 300 `
   -RuleType MatchRule `
   -MatchCondition $condition3 `
   -Action Block
```

Voici le JSON correspondant :

```json
  {
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 100,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "1=1"
            ]
          }
        ]
      },
      {
        "name": "myrule2",
        "ruleType": "MatchRule",
        "priority": 100,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "transforms": [
              "Lowercase"
            ],
            "matchValues": [
              "drop tables"
            ]
          }
        ]
      },
      {
        "name": "myrule3",
        "ruleType": "MatchRule",
        "priority": 100,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "'--"
            ]
          }
        ]
      }
    ]
  }
```

Règle ModSecurity correspondante :

`SecRule REQUEST_URI "@contains 1-1" "id:7001,deny"`

`SecRule REQUEST_URI "@contains --" "id:7001,deny"`

`SecRule REQUEST_URI "@contains drop tables" "id:7001,deny"`

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé vos règles personnalisées, vous pouvez apprendre à afficher vos journaux d’activité WAF. Pour plus d’informations, consultez [Diagnostics Application Gateway](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
