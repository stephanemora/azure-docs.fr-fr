---
title: 'SDK et exemples : Content Moderator, Python, Java, Node.js et .NET'
titlesuffix: Azure Cognitive Services
description: Obtenir des kits de développement logiciel (SDK) et des exemples Content Moderator
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: sample
ms.date: 02/27/2018
ms.author: sajagtap
ms.openlocfilehash: a57f6a312b00d7ec3d927c6fda319f1de8663c9c
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220446"
---
# <a name="content-moderator-sdks-and-samples"></a>Kits de développement logiciel (SDK) et exemples Content Moderator

## <a name="sdks-for-python-java-nodejs-and-net"></a>Kits de développement logiciel (SDK) pour Python, Java, Node.js et .NET

- [Kit de développement logiciel (SDK) Content Moderator pour Python](https://pypi.python.org/pypi/azure-cognitiveservices-vision-contentmoderator)
- [Kit de développement logiciel (SDK) Content Moderator pour Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-cognitiveservices-contentmoderator%22)
- [Kit de développement logiciel (SDK) Content Moderator pour Node.js](https://www.npmjs.com/package/azure-cognitiveservices-contentmoderator)
- [Kit de développement logiciel (SDK) Content Moderator pour .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)

## <a name="net-sdk-samples"></a>Exemples du Kit de développement logiciel .NET

La liste suivante inclut des liens vers des exemples de code générés à l’aide du kit SDK Azure Content Moderator pour .NET.

- **Bibliothèque d’assistance** : [Créer un client Content Moderator pour une utilisation dans d’autres exemples](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). Consultez le guide de [démarrage rapide](content-moderator-helper-quickstart-dotnet.md).

### <a name="moderation"></a>Modération 
- **Modération des images** : [Évaluer le contenu, le texte ou les visages osés ou choquants d’une image](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Consultez le guide de [démarrage rapide](image-moderation-quickstart-dotnet.md).
- **Images personnalisées** : [Modérer avec des listes d’images personnalisées](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Consultez le guide de [démarrage rapide](image-lists-quickstart-dotnet.md).

> [!NOTE]
> Il existe une limite maximale de **5 listes d’images**, chaque liste ne devant **pas dépasser 10 000 images**.
>

- **Modération de texte** : [Passer au crible du texte pour détecter les propos injurieux et les informations d’identification personnelle (PII)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Consultez le guide de [démarrage rapide](text-moderation-quickstart-dotnet.md).
- **Termes personnalisés** : [Modérer avec des listes de termes personnalisés](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Consultez le guide de [démarrage rapide](term-lists-quickstart-dotnet.md).

> [!NOTE]
> Il existe une limite maximale de **5 listes de termes**, chaque liste ne devant **pas dépasser 10 000 termes**.
>

- **Modération des vidéos** : [Analyser une vidéo pour détecter du contenu osé ou choquant et obtenir des résultats](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Consultez le guide de [démarrage rapide](video-moderation-api.md).

### <a name="review"></a>Révision
- **Travaux d’image** : [Démarrer un travail de modération qui analyse et crée des révisions](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Consultez le guide de [démarrage rapide](moderation-jobs-quickstart-dotnet.md).
- **Révisions des images** : [Créer des révisions pour une intervention humaine](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Consultez le guide de [démarrage rapide](moderation-reviews-quickstart-dotnet.md).
- **Révisions des vidéos** : [Créer des révisions des vidéos pour une intervention humaine](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Consultez le guide de [démarrage rapide](video-reviews-quickstart-dotnet.md)
- **Révisions de la transcription des vidéos** : [Créer des révisions de la transcription des vidéos pour une intervention humaine.](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) Consultez le guide de [démarrage rapide](video-reviews-quickstart-dotnet.md)

Consultez tous les exemples .NET disponibles dans la rubrique [Exemples .NET Content Moderator sur GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).

## <a name="rest-api-samples-in-c"></a>Exemples d’API REST en C#

- [Modération d’image](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageModeration)
- [Modération de texte](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/TextModeration)
- [Modération de vidéo](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/VideoModeration)
- [Révisions d’images](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageReviews)
- [Travaux d’image](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageJob)

Pour obtenir les procédures pas à pas de ces exemples, consultez le [webinaire à la demande](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html).

## <a name="tutorials"></a>Didacticiels
- [Modération du catalogue e-Commerce](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration). Consulter le [tutoriel](ecommerce-retail-catalog-moderation.md).
- [Modération du contenu Facebook](https://github.com/MicrosoftContentModerator/samples-fbPageModeration). Consulter le [tutoriel](facebook-post-moderation.md).
- [Solution de modération et de révision des vidéos et transcriptions](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) Consulter le [tutoriel](video-transcript-moderation-review-tutorial-dotnet.md)

## <a name="on-demand-webinars"></a>Webinaires à la demande
- [Modération du contenu assistée par ordinateur à grande échelle avec Content Moderator](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html)
