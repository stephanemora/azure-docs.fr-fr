---
title: Règles personnalisées du pare-feu d’applications web Azure (WAF) v2 sur Application Gateway
description: Cet article fournit une vue d’ensemble des règles personnalisées de la deuxième version (v2) du pare-feu d’applications web Azure (WAF) intégré à la passerelle Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/04/2019
ms.author: victorh
ms.openlocfilehash: 323f01e08007260d4fb6d651b20937c5d5d5e357
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645087"
---
# <a name="custom-rules-for-web-application-firewall-v2-on-azure-application-gateway"></a>Règles personnalisées du pare-feu d’applications web v2 sur Azure Application Gateway

Le pare-feu d’applications web (WAF) v2 Azure Application Gateway est fourni avec un ensemble de règles préconfigurées, géré une plateforme, qui offre une protection contre de nombreux types d’attaques. Ces attaques comprennent l’exécution de scripts de site à site, l’injection de code SQL etc. Si vous êtes un administrateur de pare-feu d’applications web, vous souhaiterez peut-être écrire vos propres règles pour augmenter les règles CRS. Vos règles peuvent bloquer ou autoriser le trafic demandé en fonction de critères de correspondance.

Les règles personnalisées vous permettent de créer vos propres règles évaluées pour chaque requête passant par le pare-feu d’applications Web (WAF). Ces règles ont une priorité plus élevée que les autres règles des ensembles de règles gérés. Les règles personnalisées contiennent un nom de règle, une priorité de règle et un tableau des conditions de correspondance. Si ces conditions sont remplies, une action est entreprise (pour autoriser ou bloquer).

Par exemple, vous pouvez bloquer toutes les demandes à partir d’une adresse IP de la plage 192.168.5.4/24. Dans cette règle, l’opérateur est *IPMatch*, matchValues correspond à la plage d’adresses IP (192.168.5.4/24) et l’action consiste à bloquer le trafic. Vous définissez également le nom et la priorité de la règle.

Les règles personnalisées prennent en charge l’utilisation de la logique de composition pour établir des règles plus avancées répondant à vos besoins de sécurité. Par exemple, (Condition 1 **et** Condition 2) **ou** Condition 3).  Cet exemple signifie que si Condition 1 **et** Condition 2 sont remplies, **ou** si Condition 3 est remplie, le pare-feu d’applications web doit effectuer l’action spécifiée dans la règle personnalisée.

Différentes conditions de correspondance au sein de la même règle sont toujours composées à l’aide de **et**. Par exemple, bloquer le trafic à partir d’une adresse IP spécifique, et seulement si un certain navigateur est utilisé.

Si vous souhaitez **ou** deux conditions différentes, les deux conditions doivent se trouver dans des règles différentes. Par exemple, bloquer le trafic à partir d’une adresse IP spécifique, et seulement si un certain navigateur est utilisé.

> [!NOTE]
> Le nombre maximal de règles personnalisées WAF est de 100. Pour en savoir plus sur les limites de la passerelle Application Gateway, consultez [Limites, quotas et contraintes applicables aux services et abonnements Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

Les expressions régulières sont également prises en charge dans les règles personnalisées, comme dans les ensembles de règles CRS. Pour voir d’autres exemples de règle personnalisée, consultez les exemples 3 et 5 de [Créer et utiliser des règles de pare-feu d’applications web personnalisées](create-custom-waf-rules.md)

## <a name="allowing-vs-blocking"></a>Autoriser et bloquer

Autoriser et bloquer le trafic est simple avec des règles personnalisées. Par exemple, vous pouvez bloquer tout le trafic entrant à partir d’une plage d’adresses IP. Vous pouvez créer une autre règle autorisant le trafic si la requête provient d’un navigateur spécifique.

Pour autoriser quelque chose, vérifiez que le paramètre `-Action` est défini sur **Autoriser**. Pour bloquer quelque chose, vérifiez que le paramètre `-Action` est défini sur **Bloquer**.

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

Le `$BlockRule` précédent est mis en correspondance avec la règle personnalisée suivante dans Azure Resource Manager :

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

Cette règle personnalisée contient un nom, une priorité, une action et le tableau des conditions de correspondance qui doivent être remplies pour que l’action puisse être effectuée. Pour en savoir plus sur ces champs, consultez les descriptions suivantes. Pour voir des exemples de règles personnalisées, consultez [Créer et utiliser des règles de pare-feu d’applications web personnalisées](create-custom-waf-rules.md).

## <a name="fields-for-custom-rules"></a>Champs de règles personnalisées

### <a name="name-optional"></a>Name [facultatif]

Il s’agit du nom de la règle. Ce nom apparaît dans les journaux.

### <a name="priority-required"></a>Priorité [requise]

- Détermine l’ordre d’évaluation de la règle. Plus la valeur est faible, plus l’évaluation de la règle est récente. La plage autorisée est comprise entre 1 et 100. 
- Doit être unique parmi toutes les règles personnalisées. Une règle dont la priorité est égale à 40 est évaluée avant une règle dont la priorité est égale à 80.

### <a name="rule-type-required"></a>Type de règle [obligatoire]

Actuellement, ce doit être **MatchRule**.

### <a name="match-variable-required"></a>Variable de correspondance [obligatoire]

Doit être l’une des variables suivantes :

- Remaddr - Adresse IP/nom d’hôte de la connexion de l’ordinateur distant
- RequestMethod - Méthode de requête HTTP (GET, POST, PUT, DELETE etc.)
- Chaîne de requête - Variable de l’URI
- PostArgs – Arguments envoyés dans le corps POST. Les règles personnalisées utilisant cette variable de correspondance ne sont appliquées que si l’en-tête « Content-Type » est défini sur « application/x-www-form-urlencoded » et « multipart/form-data ».
- RequestUri - URI de la requête
- RequestHeaders - En-têtes de la requête
- RequestBody : contient le corps de la demande en tant que tout. Les règles personnalisées utilisant cette variable de correspondance ne sont appliquées que si l’en-tête « Content-Type » est défini sur « application/x-www-form-urlencoded ». 
- RequestCookies - Cookies de la requête

### <a name="selector-optional"></a>Sélecteur [facultatif]

Décrit le champ de la collection matchVariable. Par exemple, si matchVariable est RequestHeaders, le sélecteur peut être sur l’en-tête *User-Agent*.

### <a name="operator-required"></a>Opérateur [obligatoire]

Il doit s’agir de l’un des opérateurs suivants :

- IPMatch - Utilisé uniquement lorsque matchVariable est *RemoteAddr*
- Equals - la saisie est identique à MatchValue
- Contient
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- Expression régulière
- Geomatch (préversion)

### <a name="negate-condition-optional"></a>Negate condition [facultatif]

Annule la condition actuelle.

### <a name="transform-optional"></a>Transform [facultatif]

Liste de chaînes avec les noms des transformations à effectuer avant la tentative de la mise en correspondance. Il peut s’agir des transformations suivantes :

- Minuscules
- SupprEspace
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Match values [obligatoire]

Liste des valeurs selon lesquelles effectuer la mise en correspondance, ce qui peut être considéré comme étant soumis à l’opérateur *OR*. Par exemple, il peut s’agir d’adresses IP ou d’autres chaînes. Le format de la valeur dépend de l’opérateur précédent.

### <a name="action-required"></a>Action [obligatoire]

- Autoriser - Autorise la transaction, en ignorant toutes les règles qui en découle. Cela signifie que la requête spécifiée est ajoutée à la liste verte et une fois la mise en correspondance effectuée, la requête interrompt l’évaluation et est renvoyée vers le pool back-end. Les règles qui se trouvent sur la liste verte ne sont pas évaluées selon d’autres règles personnalisées ou managées.
- Bloquer - Bloque la transaction selon *SecDefaultAction* (mode de détection/prévention). Tout comme l’action d’autorisation, une fois la requête évaluée et ajoutée à la liste rouge, l’évaluation est interrompue et la requête bloquée. Toute requête consécutive qui répond aux mêmes conditions n’est pas évaluée et est simplement bloquée. 
- Journal - Autorise la règle à écrire dans le journal, mais autorise l’exécution du reste des règles pour évaluation. Les règles personnalisées consécutives sont évaluées par ordre de priorité suivies par les règles managées.

## <a name="geomatch-custom-rules-preview"></a>Règles personnalisées Geomatch (préversion)

Les règles personnalisées permettent de créer des règles adaptées pour répondre aux besoins exacts de vos applications et de vos stratégies de sécurité. Vous pouvez désormais restreindre l’accès à vos applications web par pays/région ; ceci est disponible en préversion publique. Comme avec toutes les règles personnalisées, cette logique peut être composée d’autres règles pour répondre aux besoins de votre application. 

   > [!NOTE]
   > Les règles personnalisées de géocorrespondance sont disponibles dans les régions USA Centre Sud et Europe Nord. Pour y accéder dans le portail, utilisez [ce lien](https://aka.ms/AppGWWAFGeoMatch) jusqu’à ce qu’elles soient disponibles pour tout le monde. 

Si vous utilisez l’opérateur Geomatch, les sélecteurs peuvent correspondre à l’un des codes pays à deux chiffres suivants. 

|Code pays | Nom du pays |
| ----- | ----- |
| AD | Andorre |
| AE | Émirats Arabes Unis|
| AF | Afghanistan|
| Groupe de disponibilité | Antigua-et-Barbuda|
| AL | Albanie|
| AM | Arménie|
| AO | Angola|
| AR | Argentine|
| AS | Samoa américaines|
| AT | Autriche|
| AU | Australie|
| AZ | Azerbaïdjan|
| BA | Bosnie-Herzégovine|
| BB | Barbade|
| BD | Bangladesh|
| BE | Belgique|
| BF | Burkina Faso|
| BG | Bulgarie|
| BH | Bahreïn|
| BI | Burundi|
| BJ | Bénin|
| BL | Saint-Barthélemy|
| BN | Brunéi Darussalam|
| BO | Bolivie|
| BR | Brésil|
| BS | Les Bahamas|
| BT | Bhoutan|
| BW | Botswana|
| BY | Bélarus|
| BZ | Belize|
| CA | Canada|
| CD | République démocratique du Congo|
| CF | République centrafricaine|
| CH | Suisse|
| CI | Côte d'Ivoire|
| CL | Chili|
| CM | Cameroun|
| CN | Chine|
| CO | Colombie|
| CR | Costa Rica|
| CU | Cuba|
| CV | Cabo Verde|
| CY | Chypre|
| CZ | République tchèque|
| DE | Allemagne|
| DK | Danemark|
| DO | République dominicaine|
| DZ | Algérie|
| EC | Équateur|
| EE | Estonie|
| EG | Égypte|
| ES | Espagne|
| ET | Éthiopie|
| FI | Finlande|
| FJ | Fidji|
| FM | Micronésie, États fédérés de|
| FR | France|
| Go | United Kingdom|
| GE | Géorgie|
| GF | Guyane française|
| GH | Ghana|
| GN | Guinée|
| GP | Guadeloupe|
| GR | Grèce|
| GT | Guatemala|
| GY | Guyane|
| HK | Hong Kong (R.A.S.)|
| HN | Honduras|
| HR | Croatie|
| HT | Haïti|
| HU | Hongrie|
| id | Indonésie|
| IE | Irlande|
| IL | Israël|
| IN | Inde|
| IQ | Irak|
| IR | Iran, République islamique|
| IS | Islande|
| IT | Italie|
| JM | Jamaïque|
| JO | Jordanie|
| JP | Japon|
| KE | Kenya|
| KG | Kirghizistan|
| KH | Cambodge|
| KI | Kiribati|
| KN | Saint-Christophe-et-Niévès|
| KP | Corée, République populaire démocratique de|
| KR | Corée, République de|
| KW | Koweït|
| KY | Caïmans (îles)|
| KZ | Kazakhstan|
| LA | Laos, République démocratique|
| LB | Liban|
| LI | Liechtenstein|
| LK | Sri Lanka|
| LR | Liberia|
| LS | Lesotho|
| LT | Lituanie|
| LU | Luxembourg|
| LV | Lettonie|
| LY | Libye |
| MA | Maroc|
| MD | Moldova|
| MG | Madagascar|
| MK | Macédoine du Nord|
| ML | Mali|
| MM | Myanmar|
| MN | Mongolie|
| MO | Macao R.A.S.|
| MQ | Martinique|
| MR | Mauritanie|
| MT | Malte|
| MV | Maldives|
| MW | Malawi|
| MX | Mexique|
| MY | Malaisie|
| MZ | Mozambique|
| N/D | Namibie|
| NE | Niger|
| NG | Nigeria|
| NI | Nicaragua|
| NL | Pays-bas|
| Non | Norvège|
| NP | Népal|
| NR | Nauru|
| NZ | Nouvelle-Zélande|
| OM | Oman|
| PA | Panama|
| PE | Pérou|
| PH | Philippines|
| PK | Pakistan|
| PL | Pologne|
| PR | Porto Rico|
| PT | Portugal|
| PW | Palau|
| PY | Paraguay|
| QA | Qatar|
| RE | La réunion|
| RO | Roumanie|
| RS | Serbie|
| RU | Fédération de Russie|
| L/E | Rwanda|
| SA | Arabie Saoudite|
| SD | Soudan|
| SE | Suède|
| SG | Singapour|
| SI | Slovénie|
| SK | Slovaquie|
| SN | Sénégal|
| SO | Somalie|
| SR | Surinam|
| SS | Soudan du Sud|
| SV | El Salvador|
| SY | République arabe syrienne|
| SZ | Swaziland|
| TC | Turques-et-Caïques (îles)|
| TG | Togo|
| MJ | Thaïlande|
| TN | Tunisie|
| TR | Turquie|
| TT | Trinité-et-Tobago|
| TW | Taïwan|
| TZ | Tanzanie, République de|
| UA | Ukraine|
| UG | Ouganda|
| US | États-Unis|
| UY | Uruguay|
| UZ | Ouzbékistan|
| VC | Saint-Vincent-et-les-Grenadines|
| VE | Venezuela|
| VG | Vierges britanniques (îles)|
| VI | Vierges américaines (îles)|
| VN | Vietnam|
| ZA | Afrique du Sud|
| ZM | Zambie|
| ZW | Zimbabwe|

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous en savez plus sur les règles personnalisées, [créez vos propres règles personnalisées](create-custom-waf-rules.md).
