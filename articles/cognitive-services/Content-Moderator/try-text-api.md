---
title: Modérer du texte à l’aide de l’API Modération du texte - Content Moderator
titlesuffix: Azure Cognitive Services
description: Évaluez la modération du texte à l’aide de l’API Modération de texte dans la console en ligne.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: 794638496931f72a12fcb3bd8819b04c7e2e7c97
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60995604"
---
# <a name="moderate-text-from-the-api-console"></a>Modérer du texte à partir de la console d’API

Utilisez le [l’API Modération du texte](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) dans Azure Content Moderator pour analyser le contenu de votre texte. L’opération analyse votre contenu pour détecter les propos injurieux et compare le contenu à des listes rouges personnalisées et partagées.


## <a name="get-your-api-key"></a>Obtenir votre clé API
Avant de pouvoir évaluer l’API dans la console en ligne, vous avez besoin de votre clé d’abonnement. Elle se trouve sous l’onglet **Paramètres**, dans la zone **Ocp-Apim-Subscription-Key**. Pour plus d’informations, consultez [Vue d’ensemble](overview.md).

## <a name="navigate-to-the-api-reference"></a>Accéder aux informations de référence sur l’API
Accédez aux [Informations de référence sur l’API Modération du texte](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  La page **Text - Screen** (Texte - Passer au crible) s’ouvre.

## <a name="open-the-api-console"></a>Ouvrir la console d’API
Pour l’option **Open API testing console** (Ouvrir la console de test d’API), sélectionnez la région qui décrit le mieux votre emplacement. 

  ![Sélection d’une région dans la page Text - Screen (Texte - Passer au crible)](images/test-drive-region.png)

  La console d’API **Text - Screen** (Texte - Passer au crible) s’ouvre.

## <a name="select-the-inputs"></a>Sélectionner les entrées

### <a name="parameters"></a>parameters
Sélectionnez les paramètres de requête que vous voulez utiliser dans le passage au crible de votre texte. Pour cet exemple, utilisez la valeur par défaut pour le paramètre **language**. Vous pouvez également le laisser vide, car l’opération détecte automatiquement la langue probable dans le cadre de son exécution.

> [!NOTE]
> Pour le paramètre **langage**, attribuez `eng` ou laissez-le vide pour voir la réponse de la **classification** assistée par ordinateur (fonctionnalité d’évaluation). **Cette fonctionnalité prend en charge uniquement l’anglais**.
>
> Pour la détection des **termes injurieux**, utilisez le [code ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) des langues prises en charge répertoriées dans cet article, ou laissez-le vide.

Pour les paramètres **autocorrect**, **PII** et **classify** (préversion), sélectionnez **true**. Laissez le champ **ListId** vide.

  ![Paramètres de requête de la console Text - Screen (Texte - Passer au crible)](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Type de contenu
Pour **Content-Type**, sélectionnez le type de contenu à passer au crible. Pour cet exemple, utilisez le type de contenu **text/plain** par défaut. Dans la zone **Ocp-Apim-abonnement-Key**, entrez votre clé d’abonnement.

### <a name="sample-text-to-scan"></a>Exemple de texte à analyser
Dans la zone **Request body** (Corps de la requête), entrez du texte. L’exemple suivant montre une faute de frappe intentionnelle dans le texte.

> [!NOTE]
> Le numéro de sécurité sociale non valide dans l’exemple de texte suivant est intentionnel. Le but est d’exprimer le format de l’exemple d’entrée et de sortie.

```
    Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
    These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.
    Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="text-classification-feature"></a>Fonctionnalité de classification de texte

Dans l’exemple suivant, vous voyez la réponse de la classification assistée par ordinateur de Content Moderator. Elle permet de détecter le contenu potentiellement indésirable. Le contenu marqué d’un indicateur peut être jugé comme inapproprié en fonction du contexte. En plus d’exprimer la probabilité de chaque catégorie, elle peut recommander une révision du contenu par un opérateur humain. Cette fonctionnalité utilise un modèle entraîné pour identifier un langage potentiellement abusif, dépréciatif ou discriminatoire. Cela inclut l’argot, les mots abrégés, les mots offensants et les mots intentionnellement mal orthographiés à réviser. 

#### <a name="explanation"></a>Explication

- `Category1` représente la présence de langage pouvant être considéré comme sexuellement explicite ou réservé aux adultes dans certaines situations.
- `Category2` représente la présence de langage pouvant être considéré comme sexuellement suggestif ou réservé aux adultes dans certaines situations.
- `Category3` représente la présence de langage pouvant être considéré comme choquant dans certaines situations.
- `Score` est compris entre 0 et 1. Plus le score est élevé, plus le modèle prédit que la catégorie peut être applicable. Cette préversion s’appuie sur un modèle statistique plutôt que sur des résultats codés manuellement. Nous vous recommandons d’effectuer le test avec votre propre contenu afin de déterminer comment chaque catégorie correspond à vos besoins.
- La valeur de `ReviewRecommended` est true ou false, selon les seuils de score internes. Les clients doivent évaluer l’opportunité d’utiliser cette valeur ou choisir des seuils personnalisés en fonction de leurs stratégies de contenu.

### <a name="analyze-the-response"></a>Analyser la réponse
La réponse suivante montre les différentes informations fournies par l’API. Elle contient les propos injurieux potentiels, les informations d’identification personnelle, la classification (préversion) et la version corrigée automatiquement.

> [!NOTE]
> La fonctionnalité « Classification » assistée par ordinateur est en préversion et prend en charge uniquement l’anglais.

```
{
    "OriginalText": "Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.\r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.\r\nAlso, 544-56-7788 looks like a social security number (SSN).",
    "NormalizedText": "Is this a grabage or crap email abcdef@ abcd. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. \r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. \r\nAlso, 544- 56- 7788 looks like a social security number ( SSN) .",
"Misrepresentation": null,
    "PII": {
            "Email": [{
                "Detected": "abcdef@abcd.com",
                "SubType": "Regular",
                "Text": "abcdef@abcd.com",
                "Index": 32
                }],
            "IPA": [{
                "SubType": "IPV4",
                "Text": "255.255.255.255",
                "Index": 72
                }],
            "Phone": [{
                "CountryCode": "US",
                "Text": "6657789887",
                "Index": 56
                }, {
                "CountryCode": "US",
                "Text": "870 608 4000",
                "Index": 211
                }, {
                "CountryCode": "UK",
                "Text": "+44 870 608 4000",
                "Index": 207
                }, {
                "CountryCode": "UK",
                "Text": "0344 800 2400",
                "Index": 227
                }, {
                "CountryCode": "UK",
                "Text": "0800 820 3300",
                "Index": 244
            }],
         "Address": [{
                 "Text": "1 Microsoft Way, Redmond, WA 98052",
                "Index": 89
            }],
            "SSN": [{
                "Text": "999999999",
                "Index": 56
            }, {
                "Text": "999-99-9999",
                "Index": 266
            }]
        },
    "Classification": {
        "ReviewRecommended": true,
        "Category1": {
            "Score": 1.5113095059859916E-06
        },
        "Category2": {
            "Score": 0.12747249007225037
        },
        "Category3": {
            "Score": 0.98799997568130493
        }
        },
    "Language": "eng",
    "Terms": [{
            "Index": 21,
            "OriginalIndex": 21,
            "ListId": 0,
         "Term": "crap"
        }],
    "Status": {
            "Code": 3000,
            "Description": "OK",
            "Exception": null
        },
     "TrackingId": "2eaa012f-1604-4e36-a8d7-cc34b14ebcb4"
}
```

Pour obtenir une explication détaillée de toutes les sections de la réponse JSON, reportez-vous à la [vue d’ensemble de l’API Modération de texte](text-moderation-api.md).

## <a name="next-steps"></a>Étapes suivantes

Utilisez l’API REST dans votre code ou commencez par le [démarrage rapide de .NET de la modération du texte](text-moderation-quickstart-dotnet.md) pour l’intégrer à votre application.
