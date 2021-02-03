---
title: 'Démarrage rapide : Bibliothèque de client Form Recognizer ou API REST'
titleSuffix: Azure Cognitive Services
description: Utilisez la bibliothèque de client Form Recognizer ou l’API REST afin de créer une application de traitement des formulaires qui extrait des paires clé/valeur et des données de table de vos documents personnalisés.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/29/2021
ms.author: pafarley
zone_pivot_groups: programming-languages-set-formre
ms.custom: devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
keywords: traitement des formulaires, traitement de données automatisé
ms.openlocfilehash: cc18cc9e54a5a8887ecd3e079285ba5130b88278
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218041"
---
# <a name="quickstart-use-the-form-recognizer-client-library-or-rest-api"></a>Démarrage rapide : Utiliser la bibliothèque de client Form Recognizer ou l’API REST

Découvrez comment bien démarrer avec Form Recognizer dans le langage de votre choix. Azure Form Recognizer est un service cognitif qui vous permet de créer des logiciels de traitement de données automatisé à l’aide des technologies du Machine Learning. Identifiez et extrayez du texte, des paires clé/valeur, des marques de sélection, des données de table et plus de vos formulaires : le service génère des données structurées qui incluent les relations dans le fichier d’origine. Vous pouvez utiliser Form Recognizer via l’API REST ou le kit SDK. Suivez les étapes suivantes pour installer le package du SDK et essayer l’exemple de code pour les tâches de base. 

Utilisez Form Recognizer pour :

* [Analyser la disposition](#analyze-layout)
* [Analyser les reçus](#analyze-receipts)
* [Analyser les cartes de visite](#analyze-business-cards)
* [Analyser les factures](#analyze-invoices)
* [Entraîner un modèle personnalisé](#train-a-custom-model)
* [Analyser les formulaires avec un modèle personnalisé](#analyze-forms-with-a-custom-model)
* [Gérer vos connecteurs personnalisés](#manage-your-custom-models)

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](../includes/quickstarts/javascript-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end
