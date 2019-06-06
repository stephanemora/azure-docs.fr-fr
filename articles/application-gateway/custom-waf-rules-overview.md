---
title: Règles personnalisées des pare-feu d’applications Web (WAF) Azure
description: Cet article fournit une vue d’ensemble du pare-feu d’applications web (WAF) de règles personnalisées dans Azure Application Gateway.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 62259749e04d66d78206a0bba77ce88f2c08c82f
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66689000"
---
# <a name="custom-rules-for-web-application-firewall"></a>Règles personnalisées pour le pare-feu d’Application Web

> [!IMPORTANT]
> Règles personnalisées d’Azure Application Gateway WAF est actuellement une préversion publique. **Règles personnalisées sont uniquement disponibles pour la référence (SKU) WAF_v2**.
> Cette préversion publique est fournie sans contrat de niveau de service et ne doit pas être utilisée pour les charges de travail de production. Il est possible que certaines fonctionnalités ne soient pas prises en charge, disposent de capacités limitées ou ne soient pas accessibles à tous les emplacements Azure. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le pare-feu d’applications web Azure Application Gateway (WAF) est fourni avec un ensemble de règles préconfigurée, géré par la plateforme qui offre une protection contre de nombreux types d’attaques. Ces attaques incluent cross XSS, injection de code SQL et d’autres. Si vous êtes un administrateur de WAF, vous souhaiterez écrire vos propres règles pour augmenter la règle core définir des règles de (CRS). Vos règles peuvent bloquer ou autoriser le trafic demandé en fonction de critères de correspondance.

Règles personnalisées permettent de créer vos propres règles sont évaluées pour chaque demande qui passe par le pare-feu WAF. Ces règles contiennent une priorité plus élevée que le reste des règles dans les ensembles de règles managé. Les règles personnalisées contiennent un nom de règle, priorité de la règle et un tableau de conditions de correspondance. Si ces conditions sont remplies, une action est entreprise (pour autoriser ou bloquer).

Par exemple, vous pouvez bloquer toutes les demandes à partir d’une adresse IP dans la plage 192.168.5.4/24. Dans cette règle, l’opérateur est *IPMatch*, le matchValues est la plage d’adresses IP (192.168.5.4/24) et l’action consiste à bloquer le trafic. Vous définissez également le nom et la priorité de la règle.

Règles personnalisées prennent en charge à l’aide de la logique de composition pour rendre des règles plus avancées répondant à que vos besoins de sécurité. Par exemple, (Condition 1 **et** Condition 2) **ou** Condition 3).  Cet exemple signifie que si Condition 1 **et** Condition 2 sont remplies, **ou** si 3 de la Condition est remplie, le pare-feu WAF doit effectuer l’action spécifiée dans la règle personnalisée.

Différentes conditions de correspondance dans la même règle sont compliquent toujours à l’aide de **et**. Par exemple, bloquer le trafic à partir d’une adresse IP spécifique, et seulement s’ils utilisent un certain navigateur.

Si vous souhaitez **ou** deux différentes conditions, les deux conditions doivent être dans des règles différentes. Par exemple, bloquer le trafic à partir d’une adresse ou bloquer le trafic IP spécifique si qu’ils utilisent un navigateur spécifique.

> [!NOTE]
> Le nombre maximal de règles personnalisées WAF est 100. Pour plus d’informations sur les limites de la passerelle d’Application, consultez [abonnement Azure et limites de service, quotas et contraintes](../azure-subscription-service-limits.md#application-gateway-limits).

Les expressions régulières sont également prises en charge dans les règles personnalisées, comme dans les ensembles de règles CRS. Pour obtenir des exemples de ces, consultez les exemples 3 et 5 dans [créer et utiliser règles de pare-feu d’application web personnalisée](create-custom-waf-rules.md).

## <a name="allowing-vs-blocking"></a>Autoriser et bloquer

Autoriser ou de bloquer le trafic sont simple avec des règles personnalisées. Par exemple, vous pouvez bloquer tout le trafic entrant à partir d’une plage d’adresses IP. Vous pouvez effectuer une autre règle autorise le trafic si la demande provient d’un navigateur spécifique.

Pour permettre à quelque chose, vérifiez que le `-Action` paramètre est défini sur **autoriser**. Pour bloquer quelque chose, vérifiez que le `-Action` paramètre est défini sur **bloc**.

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

Le précédent `$BlockRule` mappe à la règle personnalisée suivante dans Azure Resource Manager :

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

Cette règle personnalisée contient un nom, priorité, une action et le tableau de correspondance des conditions qui doivent être remplies pour l’action à effectuer sur place. Pour en savoir plus sur ces champs, consultez les descriptions de champ suivantes. Par exemple des règles personnalisées, consultez [créer et utiliser règles de pare-feu d’application web personnalisée](create-custom-waf-rules.md).

## <a name="fields-for-custom-rules"></a>Champs de règles personnalisées

### <a name="name-optional"></a>Nom [facultatif]

Il s’agit du nom de la règle. Ce nom apparaît dans les journaux.

### <a name="priority-required"></a>Priorité [requise]

- Détermine l’ordre dans lequel les règles sont évaluées dans. Faible valeur, plue la version d’évaluation de la règle.
-Doit être unique parmi toutes les règles personnalisées. Une règle avec une priorité 100 est évaluée avant une règle avec une priorité 200.

### <a name="rule-type-required"></a>Type de règle [requis]

Actuellement, doit être **MatchRule**.

### <a name="match-variable-required"></a>Variable de correspondance [requise]

Doit être une des variables :

- Remaddr – IP adresse/nom de l’hôte de la connexion de l’ordinateur distant
- RequestMethod – méthode de requête HTTP (GET, POST, PUT, DELETE et ainsi de suite.)
- Chaîne de requête – Variable dans l’URI
- PostArgs : Arguments envoyés dans le corps POST
- RequestUri : URI de la requête
- RequestHeaders : en-têtes de la demande
- RequestBody : le corps de la demande
- RequestCookies : les Cookies de la demande

### <a name="selector-optional"></a>Sélecteur de [facultatif]

Décrit le champ de la collection matchVariable. Par exemple, si le matchVariable est RequestHeaders, le sélecteur peut être sur le *Agent utilisateur* en-tête.

### <a name="operator-required"></a>Opérateur [requis]

Doit être un des opérateurs suivants :

- IPMatch - utilisé uniquement lors de la Variable de correspondance est *remaddr*
- Est égal à – d’entrée est le même que le MatchValue
- Contains
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- Expression régulière

### <a name="negate-condition-optional"></a>Inverser la condition [facultative]

Inverse la condition actuelle.

### <a name="transform-optional"></a>Transformation [facultative]

Une liste de chaînes avec les noms des transformations à faire avant la correspondance est tentée. Ils peuvent être les transformations suivantes :

- Minuscules
- Trim
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Correspondent aux valeurs [requis]

Liste de valeurs à mettre en correspondance, ce qui peut être considérée comme étant *ou*' ed. Par exemple, il peut être des adresses IP ou autres chaînes. Le format de la valeur dépend de l’opérateur précédente.

### <a name="action-required"></a>Action [requise]

- Autoriser : autorise la transaction, en ignorant toutes les règles. Cela signifie que la demande spécifiée est ajoutée à la liste verte et une fois mis en correspondance, la version d’évaluation supplémentaire s’arrête de demande et est envoyé vers le pool principal. Les règles qui se trouvent sur la liste verte ne sont pas évaluées pour n’importe quel davantage les règles personnalisées ou les règles managés.
- Bloquer : bloque la transaction selon *SecDefaultAction* (mode de détection et de prévention). Tout comme l’action d’autorisation, une fois la demande est évaluée et ajoutée à la liste rouge, arrêt de l’évaluation et la demande est bloquée. Toute demande après qui répond aux mêmes conditions ne peut pas être évaluée et sera simplement bloqué. 
- Journal – la règle vous permet d’écrire dans le journal, mais permet le reste des règles à exécuter pour l’évaluation. Les règles suivantes sont évaluées dans l’ordre de priorité, suivis par les règles managés.

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous découvrez des règles personnalisées, [créer vos propres règles personnalisées](create-custom-waf-rules.md).
