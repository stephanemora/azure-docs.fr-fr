---
title: Règles personnalisées du pare-feu d’applications web Azure (WAF) v2
description: Cet article fournit une vue d’ensemble des règles personnalisées de la deuxième version (v2) du pare-feu d’applications web Azure (WAF) intégré à la passerelle Azure Application Gateway.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 8ae5c9b6b52ea13e3d0981664e8c920cc5b47a01
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263548"
---
# <a name="overview-custom-rules-for-web-application-firewall-v2"></a>Vue d’ensemble : Règles personnalisées de la deuxième version (v2) du pare-feu d’applications web (WAF)

La deuxième version (ou « v2 ») du pare-feu d’applications web (ou « WAF » pour « Web Application Firewall ») intégré à Azure Application Gateway propose un ensemble de règles préconfigurées géré par une plateforme. Elle offre une protection contre de nombreux types d’attaques. Ces attaques comprennent l’exécution de scripts de site à site, l’injection de code SQL etc. Si vous êtes un administrateur de pare-feu d’applications web, nous vous recommandons d’écrire vos propres règles pour augmenter l’ensemble des règles de base. Vos règles peuvent bloquer ou autoriser le trafic demandé en fonction de critères de correspondance.

Les règles personnalisées vous permettent de créer vos propres règles évaluées pour chaque requête passant par WAF. Ces règles ont une priorité plus élevée que les autres règles des ensembles de règles gérés. Les règles personnalisées contiennent un nom de règle, une priorité de règle et un tableau des conditions de correspondance. Si ces conditions sont remplies, une action est entreprise (pour autoriser ou bloquer).

Par exemple, vous pouvez créer une règle pour bloquer toutes les requêtes provenant d’une adresse IP de la plage 192.168.5.4/24. Dans cette règle, l’opérateur est *IPMatch*, *matchValues* correspond à la plage d’adresses IP (192.168.5.4/24) et l’*action* consiste à bloquer le trafic. Vous définissez également le nom et la priorité de la règle.

Les règles personnalisées prennent en charge l’utilisation de la logique de composition pour établir des règles plus avancées répondant à vos besoins de sécurité. Par exemple, "((Condition 1 *and* Condition 2) *or* Condition 3)" signifie que si les conditions 1 et (*and*) 2 sont remplies, ou (*or*) si la condition 3 est remplie, WAF effectue l’action spécifiée dans la règle personnalisée.

Différentes conditions de correspondance au sein de la même règle sont toujours composées à l’aide du connecteur *and*. Par exemple, une règle qui utilise *and* peut spécifier un blocage du trafic à partir d’une adresse IP précise, et ce uniquement si l’utilisateur correspondant à cette adresse utilise un navigateur particulier.

Si vous souhaitez utiliser le connecteur *or* pour lier deux conditions, ces conditions doivent se trouver dans des règles différentes. Par exemple, une règle qui utilise *or* peut spécifier un blocage du trafic à partir d’une adresse IP précise ou un blocage du trafic si l’utilisateur correspondant à cette adresse utilise un navigateur particulier.

> [!NOTE]
> Le nombre maximal de règles personnalisées WAF est de 100. Pour en savoir plus sur les limites de la passerelle Application Gateway, consultez [Limites, quotas et contraintes applicables aux services et abonnements Azure](../azure-subscription-service-limits.md#application-gateway-limits).

Les expressions régulières sont également prises en charge dans les règles personnalisées, comme dans l’ensemble de règles de base. Pour voir d’autres exemples de ces règles, veuillez consulter les exemples 3 et 5 de la section [Créer et utiliser des règles de pare-feu d’applications web personnalisées](create-custom-waf-rules.md).

## <a name="allowing-or-blocking-traffic"></a>Autorisation ou blocage du trafic

Les règles personnalisées permettent d’autoriser et bloquer facilement le trafic. Par exemple, vous pouvez bloquer tout le trafic entrant à partir d’une plage d’adresses IP. Vous pouvez créer une autre règle autorisant le trafic si la requête provient d’un navigateur spécifique.

Pour autoriser quelque chose, vérifiez que le paramètre `-Action` est défini sur **Autoriser**. Pour bloquer quelque chose, vérifiez que le paramètre `-Action` est défini sur Bloquer (**Block**), comme indiqué dans ce code :

```azurepowershell
$AllowRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example1 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Allow

$BlockRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example2 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

La règle de blocage (`$BlockRule`) précédente correspond à la règle personnalisée suivante dans Azure Resource Manager :

```json
"customRules": [
      {
        "name": "blockEvilBot",
        "priority": 2,
        "ruleType": "MatchRule",
        "action": "Block",
        "matchConditions": [
          {
            "matchVariables": [
              {
                "variableName": "RequestHeaders",
                "selector": "User-Agent"
              }
            ],
            "operator": "Contains",
            "negationConditon": false,
            "matchValues": [
              "evilbot"
            ],
            "transforms": [
              "Lowercase"
            ]
          }
        ]
      }
    ], 
```

Cette règle personnalisée contient un nom, une priorité, une action et un tableau des conditions de correspondance qui doivent être remplies pour que l’action puisse être effectuée. Vous trouverez ci-dessous les descriptions de chaque champ d’une règle personnalisée. Pour consulter des exemples de règles personnalisées, veuillez vous reporter à la page [Créer et utiliser des règles de pare-feu d’applications web personnalisées](create-custom-waf-rules.md).

## <a name="custom-rule-fields"></a>Champs d’une règle personnalisée

### <a name="name-optional"></a>Name (facultatif)

Il s’agit du nom de la règle. Ce nom apparaît dans les journaux.

### <a name="priority-required"></a>Priority (obligatoire)

- Il s’agit de la priorité d’ordre d’évaluation des règles. Plus la valeur est faible, plus l’évaluation de la règle est récente. La plage autorisée est comprise entre 1 et 100.
- La priorité de chaque règle personnalisée doit être unique (vous ne pouvez pas utiliser la même valeur pour chaque règle). Une règle avec une priorité de 40 est évaluée avant une règle avec une priorité de 80.

### <a name="rule-type-required"></a>Rule type (obligatoire)

Il s’agit du type de la règle. Actuellement, ce type doit être **MatchRule**.

### <a name="match-variable-required"></a>Match variable (obligatoire)

Il s’agit de la variable de correspondance. Elle doit être définie sur l’une des valeurs suivantes :

- RemoteAddr : adresse IP ou nom d’hôte de la connexion de l’ordinateur distant
- RequestMethod : méthode de requête HTTP (GET, POST, PUT, DELETE etc.).
- QueryString : variable comprise dans l’URI.
- PostArgs : arguments envoyés dans le corps POST. Les règles personnalisées utilisant cette variable de correspondance s’appliquent uniquement si l’en-tête « Content-Type » est défini sur « application/x-www-form-urlencoded » et « multipart/form-data ».
- RequestUri : URI de la requête.
- RequestHeaders : en-têtes de la requête.
- RequestBody : variable contenant l’ensemble du corps de la requête. Les règles personnalisées utilisant cette variable de correspondance s’appliquent uniquement si l’en-tête « Content-Type » est défini sur « application/x-www-form-urlencoded ». 
- RequestCookies : cookies de la requête.

### <a name="selector-optional"></a>Selector (facultatif)

Il s’agit du sélecteur décrivant le champ de la collection matchVariable. Par exemple, si matchVariable est « RequestHeaders », le sélecteur peut être sur l’en-tête User-Agent.

### <a name="operator-required"></a>Operator (obligatoire)

Il s’agit de l’opérateur, qui doit être l’un de ceux-ci :

- IPMatch : cet opérateur est utilisé uniquement lorsque la variable de correspondance est *RemoteAddr* .
- Equals : indique que la saisie est identique à MatchValue.
- Contains
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- Expression régulière

### <a name="negate-condition-optional"></a>Negate condition (facultatif)

Annule la condition actuelle.

### <a name="transform-optional"></a>Transform (facultatif)

Il s’agit d’une liste des chaînes contenant les noms des transformations à effectuer avant la tentative de la mise en correspondance. Les transformations sont :

- Lowercase
- Trim
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Match values (obligatoire)

Il s’agit d’une liste des valeurs selon lesquelles effectuer la mise en correspondance, ce qui peut être considéré comme étant soumis à l’opérateur *OR*. Par exemple, il peut s’agir d’adresses IP ou d’autres types de chaînes. Le format de la valeur dépend de l’opérateur précédent.

### <a name="action-required"></a>Action (obligatoire)

Le champ Action propose ces options : 

- Allow : autorise la transaction, en ignorant toutes les règles qui en découlent. Cela signifie que la requête spécifiée est ajoutée à la liste Allow, puis, une fois la mise en correspondance effectuée, la requête interrompt l’évaluation et est renvoyée vers le pool back-end. Les règles qui se trouvent sur la liste Allow ne sont pas évaluées selon d’autres règles personnalisées ou managées.

- Block : bloque la transaction selon le paramètre *SecDefaultAction* (mode de détection/prévention). Tout comme l’action Allow, une fois la requête évaluée et ajoutée à la liste Block, l’évaluation est interrompue et la requête bloquée. Les requêtes suivantes qui remplissent les mêmes conditions ne sont pas évaluées. Elles sont uniquement bloquées. 

- Log : autorise la règle à écrire dans le journal, puis autorise l’exécution du reste des règles pour évaluation. Les règles personnalisées consécutives sont évaluées par ordre de priorité, puis c’est le tour des les règles managées.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous en savez plus sur les règles personnalisées, n’hésitez pas à [créer les vôtres](create-custom-waf-rules.md) !
