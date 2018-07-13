---
title: Azure Content Moderator - Modération du texte | Microsoft Docs
description: Utilisez la modération du texte pour détecter le texte indésirable, les informations d’identification personnelle (PII) et les listes de termes personnalisées.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/30/2018
ms.author: sajagtap
ms.openlocfilehash: 5783a7a06d75a409969abad011de3bbd31dec292
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368737"
---
# <a name="text-moderation"></a>Modération de texte

Utilisez la modération du texte assistée par ordinateur et les fonctionnalités à [intervention humaine](Review-Tool-User-Guide/human-in-the-loop.md) de Content Moderator pour modérer le contenu du texte.

Les entreprises utilisent le service de modération du texte pour bloquer, approuver ou réviser le contenu en fonction de leurs stratégies et de leurs seuils. Le service de modération du texte peut être utilisé pour augmenter la modération humaine des environnements qui exigent des partenaires, des employés et des consommateurs qu’ils génèrent du contenu de texte. Ces environnements incluent notamment les salles de conversation, les forums de discussion, les bots conversationnels, les catalogues de commerce électronique et les documents. 

L’API analyse le texte entrant (au maximum 1 024 caractères) pour détecter les propos injurieux, classe le texte indésirable possible (préversion), corrige automatiquement le texte et détecte les éventuelles informations d’identification personnelle (PII). Elle met également en correspondance des listes personnalisées de termes. La fonctionnalité de correction automatique permet d’intercepter les mots délibérément mal orthographiés. Une fois le contenu traité, le service retourne une réponse détaillée. Vous pouvez utiliser cette réponse pour créer une révision par un opérateur humain dans l’outil de révision ou la noter, etc.

La réponse du service inclut les informations suivantes :

- Propos injurieux : correspondance basée sur des termes avec une liste prédéfinie de termes injurieux dans plusieurs langues
- Classification : classification en trois catégories assistée par ordinateur
- Informations d’identification personnelle (PII)
- Texte corrigé automatiquement
- Texte d’origine
- Langue

## <a name="profanity"></a>Propos injurieux

Si l’API détecte des termes injurieux dans l’une des [langues prises en charge](Text-Moderation-API-Languages.md), ces termes sont inclus dans la réponse. La réponse contient également leur emplacement (`Index`) dans le texte d’origine. La valeur `ListId` dans l’exemple JSON suivant fait référence à des termes se trouvant dans les [listes de termes personnalisées](try-terms-list-api.md), le cas échéant.

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> Pour le paramètre **langage**, attribuez `eng` ou laissez-le vide pour voir la réponse de la **classification** assistée par ordinateur (fonctionnalité d’évaluation). **Cette fonctionnalité prend en charge uniquement l’anglais**.
>
> Pour la détection des **termes injurieux**, utilisez le [code ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) des langues prises en charge répertoriées dans cet article, ou laissez le paramètre vide.

## <a name="classification"></a>Classification

La **fonctionnalité de classification de texte** assistée par ordinateur de Content Moderator, qui prend en charge **uniquement l’anglais**, permet de détecter le contenu potentiellement indésirable. Le contenu marqué d’un indicateur peut être jugé comme inapproprié en fonction du contexte. En plus d’exprimer la probabilité de chaque catégorie, elle peut recommander une révision du contenu par un opérateur humain. Cette fonctionnalité utilise un modèle entraîné pour identifier un langage potentiellement abusif, dépréciatif ou discriminatoire. Cela inclut l’argot, les mots abrégés, les mots offensants et les mots intentionnellement mal orthographiés à réviser. 

L’extrait suivant de l’extrait de code JSON présente un exemple de sortie :

> [!NOTE]
> La fonctionnalité « Classification » assistée par ordinateur est en préversion.

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
    }

### <a name="explanation"></a>Explication

- `Category1` représente la présence de langage pouvant être considéré comme sexuellement explicite ou réservé aux adultes dans certaines situations.
- `Category2` représente la présence de langage pouvant être considéré comme sexuellement suggestif ou réservé aux adultes dans certaines situations.
- `Category3` représente la présence de langage pouvant être considéré comme choquant dans certaines situations.
- `Score` est compris entre 0 et 1. Plus le score est élevé, plus le modèle prédit que la catégorie peut être applicable. Cette préversion s’appuie sur un modèle statistique plutôt que sur des résultats codés manuellement. Nous vous recommandons d’effectuer le test avec votre propre contenu afin de déterminer comment chaque catégorie correspond à vos besoins.
- La valeur de `ReviewRecommended` est true ou false, selon les seuils de score internes. Les clients doivent évaluer l’opportunité d’utiliser cette valeur ou choisir des seuils personnalisés en fonction de leurs stratégies de contenu.

## <a name="personally-identifiable-information-pii"></a>Informations d’identification personnelle (PII)

La fonctionnalité PII détecte la présence potentielle des informations suivantes :

- Adresse de messagerie
- Adresse postale aux États-Unis
- Adresse IP
- Numéro de téléphone aux États-Unis
- Numéro de téléphone au Royaume-Uni
- Numéro de sécurité sociale (SSN)

L’exemple suivant illustre une réponse :

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
            "Index": 212
            }, {
            "CountryCode": "UK",
            "Text": "+44 870 608 4000",
            "Index": 208
            }, {
            "CountryCode": "UK",
            "Text": "0344 800 2400",
            "Index": 228
            }, {
            "CountryCode": "UK",
            "Text": "0800 820 3300",
            "Index": 245
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
            "Index": 267
            }]
        }

## <a name="auto-correction"></a>Correction automatique

Supposons que le texte d’entrée soit le suivant (les fautes « lzay » et « f0x » sont intentionnelles) :

    The qu!ck brown f0x jumps over the lzay dog.

Si vous demandez la correction automatique, la réponse contient la version corrigée du texte :

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Création et gestion de vos listes personnalisées de termes

Bien que la liste globale par défaut de termes fonctionne parfaitement pour la plupart des cas, vous pouvez passer au crible un texte pour détecter des termes qui sont spécifiques à vos besoins professionnels. Vous pouvez, par exemple, filtrer tous les noms de marques de concurrents dans des posts d’utilisateurs. Le seuil du contenu de texte autorisé peut être différent de la liste par défaut.

> [!NOTE]
> Il existe une limite maximale de **5 listes de termes**, chaque liste ne devant **pas dépasser 10 000 termes**.
>

L’exemple suivant présente l’ID de liste correspondant :

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

Content Moderator fournit une [API de liste de termes](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) avec des opérations pour la gestion des listes de termes personnalisées. Commencez avec la [Console d’API de listes de termes personnalisées](try-terms-list-api.md) et utilisez les exemples de code API REST. Consultez également le [démarrage rapide .NET des listes de termes](term-lists-quickstart-dotnet.md) si vous connaissez déjà Visual Studio et C#.

## <a name="next-steps"></a>Étapes suivantes

Évaluez la [Console d’API Modération du texte](try-text-api.md) et utilisez les exemples de code API REST. Consultez également le [démarrage rapide .NET de API de modération du texte](text-moderation-quickstart-dotnet.md) si vous connaissez déjà Visual Studio et C#.
