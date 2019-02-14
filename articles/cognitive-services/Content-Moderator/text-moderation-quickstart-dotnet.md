---
title: 'Démarrage rapide : Analyser le contenu d’un texte (C#) - Content Moderator'
titlesuffix: Azure Cognitive Services
description: Comment analyser le contenu d’un texte pour trouver des matériaux répréhensibles à l’aide du kit SDK Azure Content Moderator pour .NET
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 6383a495ed3e3876e96b1249cb7b2286b240a481
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884594"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-c"></a>Démarrage rapide : Analyser le contenu d’un texte pour trouver des matériaux répréhensibles en C# 

Cet article fournit des informations et des exemples de code qui vont vous aider à prendre en main le [kit SDK Content Moderator pour .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/). Vous allez apprendre à exécuter le filtrage basé sur des termes et la classification d’un contenu textuel dans le but de modérer les matériaux potentiellement répréhensibles.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 

## <a name="prerequisites"></a>Prérequis
- Une clé d’abonnement Content Moderator. Suivez les instructions dans [Créer un compte Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pour vous abonner à Content Moderator et obtenir votre clé.
- N’importe quelle édition de [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/)

> [!NOTE]
> Ce guide utilise un abonnement Content Moderator gratuit. Pour plus d’informations sur ce qui est proposé avec chaque niveau d’abonnement, consultez la page [Tarification et limites](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/).

## <a name="create-the-visual-studio-project"></a>Créer le projet Visual Studio

1. Dans Visual Studio, créez un nouveau projet **Application de console (.NET Framework)** et nommez-le **TextModeration**. 
1. S’il existe d’autres projets dans votre solution, sélectionnez celui-ci comme projet de démarrage unique.
1. Récupérez les packages NuGet requis. Cliquez avec le bouton droit sur votre projet dans l’Explorateur de solutions et sélectionnez **Gérer les packages NuGet** ; ensuite, recherchez et installez les packages suivants :
    - Microsoft.Azure.CognitiveServices.ContentModerator
    - Microsoft.Rest.ClientRuntime
    - Newtonsoft.Json

## <a name="add-text-moderation-code"></a>Ajouter un code de modération de texte

Vous allez ensuite copier et coller le code de ce guide dans votre projet pour implémenter un scénario de modération de contenu de base.

### <a name="include-namespaces"></a>Inclure des espaces de noms

Ajoutez les instructions `using` suivantes en haut de votre fichier *Program.cs*.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=1-8)]

### <a name="create-the-content-moderator-client"></a>Créer le client Content Moderator

Ajoutez le code suivant à votre fichier *Program.cs* afin de créer un fournisseur de client Content Moderator pour votre abonnement. Ajoutez le code ainsi que la classe **Program** dans le même espace de noms. Vous devrez mettre à jour les champs **AzureRegion** et **CMSubscriptionKey** avec les valeurs de votre identificateur de région et de votre clé d’abonnement.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=54-77)]

### <a name="set-up-input-and-output-targets"></a>Configurer les cibles d’entrée et de sortie

Ajoutez les champs statiques suivants à la classe **Program** dans _Program.cs_. Ils spécifient les fichiers pour le contenu textuel d’entrée et le contenu JSON de sortie.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=15-19)]

Vous devez créer le fichier d’entrée *TextFile.txt* et mettre à jour son chemin d’accès en conséquence (les chemins d’accès relatifs sont relatifs au répertoire d’exécution). Ouvrez _TextFile.txt_ et ajoutez le texte à modérer. Ce démarrage rapide utilise l’exemple de texte suivant :

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 
0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="load-the-input-text"></a>Charger le texte d’entrée

Ajoutez le code suivant à la méthode **Main**. La méthode **ScreenText** est l’opération essentielle. Ses paramètres spécifient les opérations de modération de contenu qui seront effectuées. Dans cet exemple, la méthode est configurée pour :
- Détecter les propos injurieux potentiels dans le texte.
- Normaliser le texte et corriger automatiquement les fautes de frappe.
- Détecter des informations d’identification personnelle (PII) telles que des numéros de téléphone aux États-Unis et au Royaume-Uni, des adresses e-mail et des adresses postales aux États-Unis
- Utilisez des modèles basés sur un apprentissage automatique pour classer le texte en trois catégories.

Si vous souhaitez en savoir plus sur le rôle de ces opérations, suivez le lien dans la section [Étapes suivantes](#next-steps).

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=23-48)]

## <a name="run-the-program"></a>Exécuter le programme

Le programme écrit des données de chaîne JSON dans le fichier _TextModerationOutput.txt_. L’exemple de texte utilisé dans ce démarrage rapide donne le résultat suivant :

```json
Autocorrect typos, check for matching terms, PII, and classify.
{
"OriginalText": "\"Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).\"",
"NormalizedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
"AutoCorrectedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
"Misrepresentation": null,
    
"Classification": {
        "Category1": {
        "Score": 1.5113095059859916E-06
        },
        "Category2": {
        "Score": 0.12747249007225037
        },
        "Category3": {
        "Score": 0.98799997568130493
        },
        "ReviewRecommended": true
  },
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "PII": {
        "Email": [
            {
            "Detected": "abcdef@abcd.com",
            "SubType": "Regular",
            "Text": "abcdef@abcd.com",
            "Index": 33
            }
            ],
        "IPA": [
            {
            "SubType": "IPV4",
            "Text": "255.255.255.255",
            "Index": 73
            }
            ],
        "Phone": [
            {
            "CountryCode": "US",
            "Text": "6657789887",
            "Index": 57
            },
            {
            "CountryCode": "US",
            "Text": "870 608 4000",
            "Index": 211
            },
            {
            "CountryCode": "UK",
            "Text": "+44 870 608 4000",
            "Index": 207
            },
            {
            "CountryCode": "UK",
            "Text": "0344 800 2400",
            "Index": 227
            },
            {
        "CountryCode": "UK",
            "Text": "0800 820 3300",
            "Index": 244
            }
            ],
         "Address": [{
             "Text": "1 Microsoft Way, Redmond, WA 98052",
             "Index": 89
                }]
    },
  "Language": "eng",
  "Terms": [
    {
        "Index": 22,
        "OriginalIndex": 22,
        "ListId": 0,
        "Term": "crap"
    }
  ],
  "TrackingId": "9392c53c-d11a-441d-a874-eb2b93d978d3"
}
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez développé une application .NET unique qui utilise le service Content Moderator pour retourner des informations pertinentes sur un exemple de texte donné. Ensuite, apprenez-en davantage sur la signification des différents indicateurs et classifications, afin que vous puissiez décider de quelles données vous avez besoin et la manière votre application doit les gérer.

> [!div class="nextstepaction"]
> [Guide de modération du texte](text-moderation-api.md)
