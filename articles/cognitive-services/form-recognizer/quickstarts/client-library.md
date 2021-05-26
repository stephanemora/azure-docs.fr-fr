---
title: 'Démarrage rapide : Bibliothèque de client Form Recognizer ou API REST'
titleSuffix: Azure Applied AI Services
description: Utilisez la bibliothèque de client Form Recognizer ou l’API REST afin de créer une application de traitement des formulaires qui extrait des paires clé/valeur et des données de table de vos documents personnalisés.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 04/14/2021
ms.author: lajanuar
zone_pivot_groups: programming-languages-set-formre
ms.custom: devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
keywords: traitement des formulaires, traitement de données automatisé
ms.openlocfilehash: 34b2e591c8d050a168e82bb40c5167823c136e52
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110374156"
---
# <a name="quickstart-use-the-form-recognizer-client-library-or-rest-api"></a>Démarrage rapide : Utiliser la bibliothèque de client Form Recognizer ou l’API REST

Découvrez comment bien démarrer avec Form Recognizer dans le langage de développement de votre choix. Azure Form Recognizer est un service cognitif qui vous permet de créer des logiciels de traitement de données automatisé à l’aide des technologies du Machine Learning. Identifiez et extrayez du texte, des paires clé/valeur, des marques de sélection, des données de table et plus de vos formulaires : le service génère des données structurées qui incluent les relations dans le fichier d’origine. Vous pouvez utiliser Form Recognizer via l’API REST ou le kit SDK. Nous vous recommandons d’utiliser le service gratuit pendant que vous apprenez la technologie. N’oubliez pas que le nombre de pages gratuites est limité à 500 par mois.

Vous allez utiliser les API suivantes pour extraire des données structurées de formulaires et de documents :

|Nom |Description |
|---|---|
| **[Analyser la disposition](#analyze-layout)** | Analysez un document transmis sous forme de flux pour en extraire du texte, des marques de sélection, des tableaux et la structure |
| **[Analyser les reçus](#analyze-receipts)** | Analysez un document de reçu pour en extraire les informations clés et d’autres textes.|
| **[Analyser les cartes de visite](#analyze-business-cards)** | Analysez une carte de visite pour extraire du texte et des informations clés.|
| **[Analyser les factures](#analyze-invoices)** | Analysez une facture pour en extraire des informations clés, des tableaux et autre texte.|
| **[Analyser les documents d’identité](#analyze-identity-documents)** | Analysez des documents d’identité pour extraire des informations clés et un autre texte.|
| **[Entraîner un modèle personnalisé](#train-a-custom-model)**| Entraîner un nouveau modèle pour analyser vos formulaires avec 5 formulaires du même type. Définissez le paramètre _useLabelFile_ sur `true` pour effectuer l’entraînement avec des données étiquetées manuellement. |
| **[Analyser les formulaires avec un modèle personnalisé](#analyze-forms-with-a-custom-model)**|Analysez un formulaire transmis en tant que flux de données pour en extraire du texte, des paires clé/valeur et des tableaux avec votre modèle personnalisé.  |
|**[Gérer des modèles personnalisés](#manage-custom-models)**| Vous pouvez vérifier le nombre de modèles personnalisés disponibles dans votre compte Form Recognizer, obtenir un modèle spécifique à l’aide de son ID et supprimer un modèle de votre compte.|

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
