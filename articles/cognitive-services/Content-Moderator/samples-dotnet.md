---
title: Exemples de code - Content Moderator, .NET
titleSuffix: Azure Cognitive Services
description: Apprenez à utiliser Content Moderator d’Azure Cognitive Services dans vos applications .NET via le SDK.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 930571c841146e0b12efbf7325915ba2b23a7efa
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73744333"
---
# <a name="content-moderator-net-sdk-samples"></a>Exemples du SDK Content Moderator pour .NET

La liste suivante inclut des liens vers des exemples de code générés à l’aide du kit SDK Azure Content Moderator pour .NET.

## <a name="moderation"></a>Modération

- **Modération des images** : [Évaluer le contenu, le texte ou les visages osés ou choquants d’une image](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Consultez le [démarrage rapide du kit de développement logiciel (SDK) .NET](dotnet-sdk-quickstart.md).
- **Images personnalisées** : [Modérer avec des listes d’images personnalisées](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Consultez le [démarrage rapide du kit de développement logiciel (SDK) .NET](dotnet-sdk-quickstart.md).

> [!NOTE]
> Il existe une limite maximale de **5 listes d’images**, chaque liste ne devant **pas dépasser 10 000 images**.
>

- **Modération de texte** : [Rechercher dans le texte les propos injurieux et les données personnelles](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Consultez le [démarrage rapide du kit de développement logiciel (SDK) .NET](dotnet-sdk-quickstart.md).
- **Termes personnalisés** : [Modérer avec des listes de termes personnalisés](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Consultez le [démarrage rapide du kit de développement logiciel (SDK) .NET](dotnet-sdk-quickstart.md).

> [!NOTE]
> Il existe une limite maximale de **5 listes de termes**, chaque liste ne devant **pas dépasser 10 000 termes**.
>

- **Modération des vidéos** : [Analyser une vidéo pour détecter le contenu osé ou choquant et obtenir les résultats](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Consultez le guide de [démarrage rapide](video-moderation-api.md).

## <a name="review"></a>Révision

- **Travaux d’image** : [Démarrer un travail de modération qui analyse et crée des révisions](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Consultez le guide de [démarrage rapide](moderation-jobs-quickstart-dotnet.md).
- **Révisions des images** : [Créer des révisions d’images pour une intervention humaine](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Consultez le guide de [démarrage rapide](dotnet-sdk-quickstart.md).
- **Révisions des vidéos** : [Créer des révisions de vidéos pour une intervention humaine](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Consultez le guide de [démarrage rapide](video-reviews-quickstart-dotnet.md)
- **Révisions des transcriptions vidéo** : [Créer des révisions de transcriptions vidéo pour une intervention humaine](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs). Consultez le guide de [démarrage rapide](video-reviews-quickstart-dotnet.md)

Consultez tous les exemples .NET disponibles dans la rubrique [Exemples .NET Content Moderator sur GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).
