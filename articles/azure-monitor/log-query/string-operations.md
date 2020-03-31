---
title: Utilisation de chaînes dans des requêtes de journal Azure Monitor | Microsoft Docs
description: Décrit comment modifier et comparer des chaînes ainsi qu’effectuer des recherches et de nombreuses autres opérations sur celles-ci dans des requêtes de journal Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: a394fee7178b2e3e167c8bd905ab175b25d1d813
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75397467"
---
# <a name="work-with-strings-in-azure-monitor-log-queries"></a>Utilisation de chaînes dans des requêtes de journal Azure Monitor


> [!NOTE]
> Vous devez terminer [Bien démarrer avec Azure Monitor Log Analytics](get-started-portal.md) et [Bien démarrer avec les requêtes de journaux Azure Monitor](get-started-queries.md) avant de suivre ce tutoriel.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Cet article décrit comment modifier et comparer des chaînes ainsi qu’effectuer des recherches et de nombreuses autres opérations sur celles-ci.

Chaque caractère dans une chaîne a un numéro d’index, en fonction de son emplacement. Le premier caractère est à l'index 0, le caractère suivant à l'index 1, et ainsi de suite. Différentes fonctions de chaîne utilisent des numéros d’index comme indiqué dans les sections suivantes. La plupart des exemples suivants utilisent la commande **print** pour illustrer la manipulation de chaînes sans utiliser de source de données spécifique.


## <a name="strings-and-escaping-them"></a>Chaînes et échappement
Les valeurs de chaîne sont entourées de guillemets simples ou doubles. La barre oblique inverse (\\) est utilisée pour échapper les caractères vers le caractère suivant, comme \t pour la tabulation, \n pour le saut de ligne et \" pour le guillemet lui-même.

```Kusto
print "this is a 'string' literal in double \" quotes"
```

```Kusto
print 'this is a "string" literal in single \' quotes'
```

Pour empêcher « \\ » d’agir comme un caractère d’échappement, ajoutez « \@ » en tant que préfixe à la chaîne :

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


## <a name="string-comparisons"></a>Comparaisons de chaînes

Opérateur       |Description                         |Respecte la casse|Exemple (génère `true`)
---------------|------------------------------------|--------------|-----------------------
`==`           |Égal à                              |Oui           |`"aBc" == "aBc"`
`!=`           |Non égal à                          |Oui           |`"abc" != "ABC"`
`=~`           |Égal à                              |Non            |`"abc" =~ "ABC"`
`!~`           |Non égal à                          |Non            |`"aBc" !~ "xyz"`
`has`          |Le terme de droite est un terme entier dans le terme de gauche |Non|`"North America" has "america"`
`!has`         |Le terme de droite n’est pas un terme entier dans le terme de gauche       |Non            |`"North America" !has "amer"` 
`has_cs`       |Le terme de droite est un terme entier dans le terme de gauche |Oui|`"North America" has_cs "America"`
`!has_cs`      |Le terme de droite n’est pas un terme entier dans le terme de gauche       |Oui            |`"North America" !has_cs "amer"` 
`hasprefix`    |Le terme de droite est un préfixe dans le terme de gauche         |Non            |`"North America" hasprefix "ame"`
`!hasprefix`   |Le terme de droite n’est pas un préfixe dans le terme de gauche     |Non            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |Le terme de droite est un préfixe dans le terme de gauche         |Oui            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |Le terme de droite n’est pas un préfixe dans le terme de gauche     |Oui            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |Le terme de droite est un suffixe dans le terme de gauche         |Non            |`"North America" hassuffix "ica"`
`!hassuffix`   |Le terme de droite n’est pas un suffixe dans le terme de gauche     |Non            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |Le terme de droite est un suffixe dans le terme de gauche         |Oui            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |Le terme de droite n’est pas un suffixe dans le terme de gauche     |Oui            |`"North America" !hassuffix_cs "icA"`
`contains`     |Le terme de droite est une sous-séquence du terme de gauche  |Non            |`"FabriKam" contains "BRik"`
`!contains`    |Le terme de droite ne se produit pas dans le terme de gauche           |Non            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Le terme de droite est une sous-séquence du terme de gauche  |Oui           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |Le terme de droite ne se produit pas dans le terme de gauche           |Oui           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |Le terme de droite est une sous-séquence initiale du terme de gauche|Non            |`"Fabrikam" startswith "fab"`
`!startswith`  |Le terme de droite n’est pas une sous-séquence initiale du terme de gauche|Non        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |Le terme de droite est une sous-séquence initiale du terme de gauche|Oui            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |Le terme de droite n’est pas une sous-séquence initiale du terme de gauche|Oui        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |Le terme de droite est une sous-séquence finale du terme de gauche|Non             |`"Fabrikam" endswith "Kam"`
`!endswith`    |Le terme de droite n’est pas une sous-séquence finale du terme de gauche|Non         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |Le terme de droite est une sous-séquence finale du terme de gauche|Oui             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |Le terme de droite n’est pas une sous-séquence finale du terme de gauche|Oui         |`"Fabrikam" !endswith "brik"`
`matches regex`|Le terme de gauche contient une correspondance du terme de droite        |Oui           |`"Fabrikam" matches regex "b.*k"`
`in`           |Égal à l’un des éléments       |Oui           |`"abc" in ("123", "345", "abc")`
`!in`          |N’est égal à aucun des éléments   |Oui           |`"bca" !in ("123", "345", "abc")`


## <a name="countof"></a>countof

Compte les occurrences d’une sous-chaîne dans une chaîne. Peut correspondre à des chaînes simples ou utiliser des expressions régulières. Les correspondances de chaînes simples peuvent se chevaucher, mais pas les correspondances d’expression régulière.

### <a name="syntax"></a>Syntaxe
```
countof(text, search [, kind])
```

### <a name="arguments"></a>Arguments :
- `text` - Chaîne d’entrée 
- `search` - Chaîne simple ou expression régulière à faire correspondre au texte.
- `kind` - _normal_ | _regex_ (par défaut : normal).

### <a name="returns"></a>Retours

Le nombre de fois où la chaîne de recherche peut être mise en correspondance dans le conteneur. Les correspondances de chaînes simples peuvent se chevaucher, mais pas les correspondances d’expression régulière.

### <a name="examples"></a>Exemples

#### <a name="plain-string-matches"></a>Correspondances de chaînes simples

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Correspondances d’expression régulière

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


## <a name="extract"></a>extract

Obtient une correspondance pour une expression régulière à partir d’une chaîne donnée. Peut également convertir la sous-chaîne extraite selon le type indiqué.

### <a name="syntax"></a>Syntaxe

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

### <a name="arguments"></a>Arguments

- `regex` - Expression régulière.
- `captureGroup` - Constante entière positive identifiant le groupe de capture à extraire. 0 pour la correspondance entière, 1 pour la valeur mise en correspondance par la première '('parenthèse')' dans l’expression régulière, 2 ou plus pour les parenthèses suivantes.
- `text` - Chaîne à rechercher.
- `typeLiteral` - Littéral de type facultatif (par exemple, typeof(long)). Si elle est fournie, la sous-chaîne extraite est convertie dans ce type.

### <a name="returns"></a>Retours
Sous-chaîne correspondant au groupe de capture indiqué, captureGroup, éventuellement convertie en typeLiteral.
Si aucune correspondance n’est trouvée ou si la conversion de type échoue, la valeur null est retournée.

### <a name="examples"></a>Exemples

L’exemple suivant extrait le dernier octet de *ComputerIP* à partir d’un enregistrement de pulsation :
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

L’exemple suivant extrait le dernier octet, le caste en type *real* (nombre) et calcule la valeur d’adresse IP suivante
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

Dans l’exemple ci-dessous, une définition de « Duration » est recherchée dans la chaîne *Trace*. La correspondance est castée en *real* et multipliée par une constante de temps (1 s) *qui caste Duration en type timespan*.
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


## <a name="isempty-isnotempty-notempty"></a>isempty, isnotempty, notempty

- *isempty* retourne true si l’argument est une chaîne vide ou null (voir aussi *isnull*).
- *isnotempty* retourne true si l’argument n’est pas une chaîne vide ou null (voir aussi *isnotnull*). alias : *notempty*.

### <a name="syntax"></a>Syntaxe

```Kusto
isempty(value)
isnotempty(value)
```

### <a name="examples"></a>Exemples

```Kusto
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


## <a name="parseurl"></a>parseurl

Fractionne une URL en plusieurs parties (protocole, hôte, port, etc.) et retourne un objet dictionnaire qui contient les parties en tant que chaînes.

### <a name="syntax"></a>Syntaxe

```
parseurl(urlstring)
```

### <a name="examples"></a>Exemples

```Kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

Le résultat sera :
```
{
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
}
```


## <a name="replace"></a>remplacer

Remplace toutes les correspondances d’expression régulière par une autre chaîne. 

### <a name="syntax"></a>Syntaxe

```
replace(regex, rewrite, input_text)
```

### <a name="arguments"></a>Arguments

- `regex` - Expression régulière à mettre en correspondance. Elle peut contenir des groupes de capture entre '('parenthèses')'.
- `rewrite` - Expression régulière de remplacement pour toute correspondance trouvée par l’expression régulière correspondante. Utilisez \0 pour faire référence à la correspondance complète, \1 pour le premier groupe de capture, \2 et ainsi de suite pour les groupes de capture suivants.
- `input_text` - Chaîne d’entrée dans laquelle effectuer une recherche.

### <a name="returns"></a>Retours
Texte après le remplacement de toutes les correspondances de l’expression régulière par les évaluations de réécriture. Les correspondances ne se chevauchent pas.

### <a name="examples"></a>Exemples

```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

Peut présenter les résultats suivants :

Activité                                        |replaced
------------------------------------------------|----------------------------------------------------------
4663 - Une tentative d’accès à un objet a été effectuée  |ID d’activité 4663 : Une tentative d’accès à un objet a été effectuée.


## <a name="split"></a>split

Fractionne une chaîne donnée en fonction d’un délimiteur spécifié et retourne un tableau des sous-chaînes obtenues.

### <a name="syntax"></a>Syntaxe
```
split(source, delimiter [, requestedIndex])
```

### <a name="arguments"></a>Arguments :

- `source` - Chaîne à fractionner en fonction du délimiteur spécifié.
- `delimiter` - Délimiteur à utiliser pour fractionner la chaîne source.
- `requestedIndex` - Index de base zéro facultatif. S’il est fourni, le tableau de chaînes retourné contient uniquement cet élément (s’il existe).


### <a name="examples"></a>Exemples

```Kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

## <a name="strcat"></a>strcat

Concatène les arguments de chaîne (prend en charge 1 à 16 arguments).

### <a name="syntax"></a>Syntaxe
```
strcat("string1", "string2", "string3")
```

### <a name="examples"></a>Exemples
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


## <a name="strlen"></a>strlen

Retourne la longueur d'une chaîne.

### <a name="syntax"></a>Syntaxe
```
strlen("text_to_evaluate")
```

### <a name="examples"></a>Exemples
```Kusto
print strlen("hello")   // result: 5
```


## <a name="substring"></a>substring

Extrait une sous-chaîne d’une chaîne source donnée à partir de l’index indiqué. Éventuellement, la longueur de la sous-chaîne demandée peut être spécifiée.

### <a name="syntax"></a>Syntaxe
```
substring(source, startingIndex [, length])
```

### <a name="arguments"></a>Arguments :

- `source` - Chaîne source dont la sous-chaîne est extraite.
- `startingIndex` - Position du caractère de départ (base zéro) de la sous-chaîne demandée.
- `length` - Paramètre facultatif qui permet de spécifier la longueur demandée de la sous-chaîne retournée.

### <a name="examples"></a>Exemples
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


## <a name="tolower-toupper"></a>tolower, toupper

Convertit une chaîne donnée en minuscules ou en majuscules.

### <a name="syntax"></a>Syntaxe
```
tolower("value")
toupper("value")
```

### <a name="examples"></a>Exemples
```Kusto
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```



## <a name="next-steps"></a>Étapes suivantes
Continuer avec les tutoriels avancés :
* [Fonctions d’agrégation](aggregations.md)
* [Agrégations avancées](advanced-aggregations.md)
* [Graphiques et diagrammes](charts.md)
* [Utilisation de JSON et de structures de données](json-data-structures.md)
* [Écriture de requêtes avancées](advanced-query-writing.md)
* [Jointures : analyse croisée](joins.md)
