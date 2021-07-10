---
title: 'Conteneur : Méthode translate de Translator'
titleSuffix: Azure Cognitive Services
description: Comprendre les paramètres, les en-têtes et les messages de corps de la méthode Translate du conteneur Azure Cognitive Services Translator pour traduire du texte.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/12/2021
ms.author: lajanuar
ms.openlocfilehash: 25fefcdbf5fc7b8bd9ad8bc81a9a1bd6bd7d3e11
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111957434"
---
# <a name="container-translator-translate-method"></a>Conteneur : Méthode translate de Translator

Permet de traduire du texte.

## <a name="request-url"></a>URL de la demande

Envoyez une demande `POST` à :

```HTTP
http://localhost:{port}/translate?api-version=3.0
```

Exemple : http://<span></span>localhost:5000/translate?api-version=3.0

## <a name="request-parameters"></a>Paramètres de la demande

Les paramètres de demande transmis à la chaîne de requête sont les suivants :

### <a name="required-parameters"></a>Paramètres obligatoires

| Paramètre de requête. | Description |
| --- | --- |
| api-version | _Paramètre obligatoire_.  <br>Version de l’API demandée par le client. La valeur doit être `3.0`. |
| de | _Paramètre obligatoire_.  <br>Spécifie la langue du texte d’entrée. Trouvez les langues disponibles pour la traduction en recherchant [langues prises en charge](../reference/v3-0-languages.md) à l’aide de l’étendue `translation`.|
| to  | _Paramètre obligatoire_.  <br>Spécifie la langue du texte de sortie. La langue cible doit être l’une des [langues prises en charge](../reference/v3-0-languages.md) incluses dans l’étendue `translation`. Par exemple, utilisez `to=de` pour traduire en allemand.  <br>Il est possible de traduire en plusieurs langues simultanément en répétant le paramètre dans la chaîne de requête. Par exemple, utilisez `to=de&to=it` pour traduire en allemand et italien. |

### <a name="optional-parameters"></a>Paramètres facultatifs

| Paramètre de requête. | Description |
| --- | --- |
| textType | _Paramètre facultatif_.  <br>Définit si le texte en cours de traduction est au format texte brut ou HTML. Tout code HTML doit être un élément bien formé et complet. Les valeurs possibles sont : `plain` (par défaut) ou `html`. |
| includeSentenceLength | _Paramètre facultatif_.  <br>Spécifie s’il faut inclure des limites de longueur de phrase aux texte d’entrée et au texte traduit. Les valeurs possibles sont `true` ou `false` (par défaut). |

Les en-têtes de demande sont les suivants :

| headers | Description |
| --- | --- |
| En-tête(s) d’authentification | _En-tête de demande obligatoire_.  <br>Voir les [options disponibles pour l’authentification](../reference/v3-0-reference.md#authentication). |
| Content-Type | _En-tête de demande obligatoire_.  <br>Spécifie le type de contenu de la charge utile.  <br>La valeur acceptée est `application/json; charset=UTF-8`. |
| Content-Length | _En-tête de demande obligatoire_.  <br>Longueur du corps de la demande. |
| X-ClientTraceId | _Facultatif_.  <br>GUID généré par le client pour identifier de façon unique la demande. Vous pouvez omettre cet en-tête si vous incluez l’ID de trace dans la chaîne de requête à l’aide d’un paramètre de requête appelé `ClientTraceId`. |

## <a name="request-body"></a>Corps de la demande

Le corps de la demande est un tableau JSON. Chaque élément du tableau est un objet JSON avec une propriété de chaîne nommée `Text`, qui représente la chaîne à traduire.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Les limites suivantes s'appliquent :

* Le tableau ne peut pas compter plus de 100 éléments.
* L’intégralité du texte inclus dans la demande ne peut pas dépasser 10 000 caractères, espaces comprises.

## <a name="response-body"></a>Response body

Une réponse correcte est un tableau JSON avec un résultat pour chaque chaîne dans le tableau d’entrée. Un objet de résultat inclut les propriétés suivantes :

* `translations`: tableau des résultats de la traduction. La taille du tableau correspond au nombre de langues cibles spécifié par le paramètre de requête `to`. Chaque élément dans le tableau inclut ce qui suit :

* `to`: chaîne représentant le code de langue de la langue cible.

* `text`: chaîne fournissant le texte traduit.

* `sentLen`: objet retournant les limites de longueur de phrase dans les textes d’entrée et de sortie.

* `srcSentLen`: tableau d’entiers représentant les longueurs des phrases dans le texte d’entrée. La longueur du tableau correspond au nombre de phrases, et les valeurs sont les longueurs des phrases.

* `transSentLen`:  tableau d’entiers représentant les longueurs des phrases dans le texte traduit. La longueur du tableau correspond au nombre de phrases, et les valeurs sont les longueurs des phrases.

    Les limites de longueur de phrase ne sont incluses que si le paramètre de requête `includeSentenceLength` est `true`.

  * `sourceText`: objet avec une propriété de chaîne unique nommée `text`, qui fournit le texte d’entrée dans le script par défaut de la langue source. La propriété `sourceText` est présente uniquement quand l’entrée est exprimée dans un script qui n’est pas le script habituel pour la langue. Par exemple, si l’entrée est de l’arabe écrit dans un script latin, `sourceText.text` est le même texte en arabe converti en script arabe.

Des exemples de réponses JSON sont fournis dans la section [exemples](#examples).

## <a name="response-headers"></a>En-têtes de réponse

| headers | Description |
| --- | --- |
| X-RequestId | Valeur générée par le service pour identifier la demande. Elle sert à des fins de dépannage. |
| X-MT-System | Spécifie le type de système qui a été utilisé pour la traduction pour chaque langue de destination (« to ») demandée pour la traduction. La valeur est une liste de chaînes séparées par des virgules. Chaque chaîne indique un type :  </br></br>&FilledVerySmallSquare; Personnalisé : la requête inclut un système personnalisé, et au moins un système personnalisé a été utilisé lors de la traduction.</br>&FilledVerySmallSquare; Équipe : toutes les autres requêtes. |

## <a name="response-status-codes"></a>Codes d’état de réponse

Si une erreur se produit, la requête renvoie également une réponse d'erreur JSON. Le code d’erreur est un nombre à 6 chiffres qui combine le code d’état HTTP à 3 chiffres et un nombre à 3 chiffres qui sert à catégoriser plus précisément l’erreur. Vous trouverez les codes d’erreur les plus courants sur la [page Référence de Translator v3](../reference/v3-0-reference.md#errors).

## <a name="examples"></a>Exemples

### <a name="translate-a-single-input"></a>Traduire une entrée unique

Cet exemple montre comment traduire une phrase unique de l’anglais en chinois simplifié.

```curl
curl -X POST "http://localhost:{port}/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Le corps de la réponse est le suivant :

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

Le `translations` tableau inclut un élément qui fournit la traduction de l’élément de texte dans l’entrée.

### <a name="translate-multiple-pieces-of-text"></a>Traduire plusieurs éléments de texte

La traduction de plusieurs chaînes en une fois nécessite simplement de spécifier un tableau de chaînes dans le corps de la demande.

```curl
curl -X POST "http://localhost:{port}/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

La réponse contient la traduction de tous les éléments de texte dans le même ordre que dans la requête.
Le corps de la réponse est le suivant :

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    },
    {
        "translations":[
            {"text":"我很好，谢谢你。","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-to-multiple-languages"></a>Traduire en plusieurs langues

Cet exemple montre comment traduire une même entrée en plusieurs langues en utilisant une seule requête.

```curl
curl -X POST "http://localhost:{port}/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Le corps de la réponse est le suivant :

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"},
            {"text":"Hallo, was ist dein Name?","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Traduire du contenu incluant un balisage et décider ce qui est traduit

Il est courant de traduire du contenu incluant un balisage, tel que le contenu d’une page HTML ou d’un document XML. Lors de la traduction de contenu incluant des balises, incluez le paramètre de requête `textType=html`. De plus, il est parfois utile d’exclure du contenu spécifique de la traduction. Vous pouvez utiliser l’attribut `class=notranslate` pour spécifier le contenu qui doit rester dans la langue d’origine. Dans l’exemple suivant, le contenu du premier élément `div` ne sera pas traduit, tandis que le contenu du deuxième élément `div` le sera.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Voici un exemple de demande.

```curl
curl -X POST "http://localhost:{port}/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

La réponse est la suivante :

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-with-dynamic-dictionary"></a>Traduire avec un dictionnaire dynamique

Si vous connaissez déjà la traduction que vous souhaitez appliquer à un mot ou à une phrase, vous pouvez la fournir en tant que balisage dans la demande. Le dictionnaire dynamique n’est sûr que pour des noms propres comme les noms de personnes et les noms de produits.

Le balisage à fournir utilise la syntaxe suivante.

```
<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>
```

Par exemple, considérez la phrase : « Le mot wordomatic est une entrée de dictionnaire ». Pour conserver le mot _wordomatic_ dans la traduction, envoyez la demande suivante :

```
curl -X POST "http://localhost:{port}/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

Le résultat est le suivant :

```
[
    {
        "translations":[
            {"text":"Das Wort \"wordomatic\" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

Cette fonctionnalité opère de la même façon avec `textType=text` ou `textType=html`. Elle doit être utilisée avec parcimonie. La façon appropriée et de loin préférable de personnaliser une traduction consiste à utiliser Custom Translator. Custom Translator utilise totalement le contexte et les probabilités statistiques. Si vous avez ou pouvez vous permettre de créer des données d’apprentissage qui montrent votre mot ou phrase en contexte, vous obtenez de bien meilleurs résultats. [En savoir plus sur Custom Translator](../customization.md).

## <a name="request-limits"></a>Limites de requête

Chaque demande de traduction est limitée à 10 000 caractères, dans toutes les langues cibles de traduction. Par exemple, l’envoi d’une demande de traduction de 3 000 caractères à traduire en trois langues différentes génère une demande d’une taille de 3 000 x 3 = 9 000 caractères, qui satisfait la limite de demande. Vous êtes facturé au caractère, et non pas au nombre de requêtes. Il est recommandé d’envoyer des requêtes plus courtes.

Le tableau suivant liste les limites d’éléments de tableau et de caractères pour chaque opération de **translation** dans Translator.

| Opération | Taille maximale d’un élément de tableau | Nombre maximal d’éléments de tableau | Taille de requête maximale (caractères) |
|:----|:----|:----|:----|
| translate | 10 000 | 100 | 10 000 |