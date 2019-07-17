---
title: Méthode des exemples de dictionnaire de l’API de traduction de texte Translator Text
titlesuffix: Azure Cognitive Services
description: Utilisez la méthode des exemples de dictionnaire de l’API de traduction de texte Translator Text.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: e4665157803409b884c3333d9a3514403e5630bd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67435119"
---
# <a name="translator-text-api-30-dictionary-examples"></a>API de traduction de texte Translator Text 3.0 : Exemples de dictionnaire

Fournit des exemples qui illustrent l’utilisation en contexte des termes du dictionnaire. Cette opération est utilisée conjointement à la [recherche dans le dictionnaire](./v3-0-dictionary-lookup.md).

## <a name="request-url"></a>URL de la demande

Envoyez une demande `POST` à :

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Paramètres de la demande

Les paramètres de demande transmis à la chaîne de requête sont les suivants :

<table width="100%">
  <th width="20%">Paramètre de requête.</th>
  <th>Description</th>
  <tr>
    <td>api-version</td>
    <td>*Paramètre obligatoire*.<br/>Version de l’API demandée par le client. La valeur doit être `3.0`.</td>
  </tr>
  <tr>
    <td>from</td>
    <td>*Paramètre obligatoire*.<br/>Spécifie la langue du texte d’entrée. La langue source doit être l’une des [langues prises en charge](./v3-0-languages.md) incluses dans l’étendue `dictionary`.</td>
  </tr>
  <tr>
    <td>to</td>
    <td>*Paramètre obligatoire*.<br/>Spécifie la langue du texte de sortie. La langue cible doit être l’une des [langues prises en charge](./v3-0-languages.md) incluses dans l’étendue `dictionary`.</td>
  </tr>
</table>

Les en-têtes de demande sont les suivants :

<table width="100%">
  <th width="20%">headers</th>
  <th>Description</th>
  <tr>
    <td>En-tête(s) d’authentification</td>
    <td><em>En-tête de demande obligatoire</em>.<br/>Voir les <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">options disponibles pour l’authentification</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*En-tête de demande obligatoire*.<br/>Spécifie le type de contenu de la charge utile. Les valeurs possibles sont les suivantes : `application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*En-tête de demande obligatoire*.<br/>Longueur du corps de la demande.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Facultative*.<br/>GUID généré par le client pour identifier de façon unique la demande. Vous pouvez omettre cet en-tête si vous incluez l’ID de trace dans la chaîne de requête à l’aide d’un paramètre de requête appelé `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Corps de la demande

Le corps de la demande est un tableau JSON. Chaque élément du tableau est un objet JSON avec les propriétés suivantes :

  * `Text`: chaîne spécifiant le terme à rechercher. Elle doit correspondre à la valeur d’un champ `normalizedText` d’après les traductions inverses d’une précédente demande de [recherche dans le dictionnaire](./v3-0-dictionary-lookup.md). Elle peut également correspondre à la valeur du champ `normalizedSource`.

  * `Translation`: chaîne spécifiant le texte traduit et retourné au préalable par l’opération de [recherche dans le dictionnaire](./v3-0-dictionary-lookup.md). Elle doit correspondre à la valeur du champ `normalizedTarget` dans la liste `translations` de la réponse à la [recherche dans le dictionnaire](./v3-0-dictionary-lookup.md). Le service renvoie des exemples pour la paire de mots source-cible indiquée.

Voici un exemple :

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

Les limites suivantes s'appliquent :

* Le tableau ne peut pas compter plus de 10 éléments.
* La valeur texte d’un élément de tableau ne peut pas dépasser 100 caractères, espaces compris.

## <a name="response-body"></a>Response body

Une réponse correcte est un tableau JSON avec un résultat pour chaque chaîne dans le tableau d’entrée. Un objet de résultat inclut les propriétés suivantes :

  * `normalizedSource`: chaîne indiquant la forme normalisée du terme source. En règle générale, elle doit être identique à la valeur du champ `Text` dans l’index de la liste correspondante, dans le corps de la demande.
    
  * `normalizedTarget`: chaîne indiquant la forme normalisée du terme cible. En règle générale, elle doit être identique à la valeur du champ `Translation` dans l’index de la liste correspondante, dans le corps de la demande.
  
  * `examples`: liste d’exemples pour la paire (terme source, terme cible). Chaque élément de la liste est un objet dont les propriétés sont les suivantes :

    * `sourcePrefix`: chaîne à concaténer _avant_ la valeur de `sourceTerm` pour former un exemple complet. N’ajoutez aucun espace, car la chaîne en contient déjà si besoin. Cette valeur peut être une chaîne vide.

    * `sourceTerm`: chaîne égale au terme à rechercher. Cette chaîne est ajoutée avec `sourcePrefix` et `sourceSuffix` pour former un exemple complet. Sa valeur est séparée afin de pouvoir être signalée dans une interface utilisateur (par exemple, en caractères gras).

    * `sourceSuffix`: chaîne à concaténer _après_ la valeur de `sourceTerm` pour former un exemple complet. N’ajoutez aucun espace, car la chaîne en contient déjà si besoin. Cette valeur peut être une chaîne vide.

    * `targetPrefix`: chaîne similaire à `sourcePrefix`, mais pour la cible.

    * `targetTerm`: chaîne similaire à `sourceTerm`, mais pour la cible.

    * `targetSuffix`: chaîne similaire à `sourceSuffix`, mais pour la cible.

    > [!NOTE]
    > Si le dictionnaire ne contient aucun exemple, la réponse est 200 (OK), mais la liste `examples` est vide.

## <a name="examples"></a>Exemples

Cet exemple montre comment rechercher des exemples pour la paire composée du terme anglais `fly` et sa traduction espagnole `volar`.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

---

Le corps de la réponse (abrégé pour plus de clarté) est :

```
[
    {
        "normalizedSource":"fly",
        "normalizedTarget":"volar",
        "examples":[
            {
                "sourcePrefix":"They need machines to ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Necesitan máquinas para ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },      
            {
                "sourcePrefix":"That should really ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Eso realmente debe ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```
