---
title: Exemples de code - Content Moderator, .NET
description: Utilisez Content Moderator dans vos applications .NET à l’aide du SDK.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 43aa1ca624bce78fa113c34fa19da9ccfea69bbc
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55222097"
---
# <a name="content-moderator-net-sdk-samples"></a>Exemples du SDK Content Moderator pour .NET

La liste suivante inclut des liens vers des exemples de code générés à l’aide du kit SDK Azure Content Moderator pour .NET.

- **Bibliothèque d’assistance** : [Créer un client Content Moderator pour une utilisation dans d’autres exemples](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). Consultez le guide de [démarrage rapide](content-moderator-helper-quickstart-dotnet.md).

## <a name="moderation"></a>Modération

- **Modération des images** : [Évaluer le contenu, le texte ou les visages osés ou choquants d’une image](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Consultez le guide de [démarrage rapide](image-moderation-quickstart-dotnet.md).
- **Images personnalisées** : [Modérer avec des listes d’images personnalisées](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Consultez le guide de [démarrage rapide](image-lists-quickstart-dotnet.md).

> [!NOTE]
> Il existe une limite maximale de **5 listes d’images**, chaque liste ne devant **pas dépasser 10 000 images**.
>

- **Modération de texte** : [Analyser le texte pour détecter les propos injurieux et les informations d’identification personnelle (PII)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Consultez le guide de [démarrage rapide](text-moderation-quickstart-dotnet.md).
- **Termes personnalisés** : [Modérer avec des listes de termes personnalisés](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Consultez le guide de [démarrage rapide](term-lists-quickstart-dotnet.md).

> [!NOTE]
> Il existe une limite maximale de **5 listes de termes**, chaque liste ne devant **pas dépasser 10 000 termes**.
>

- **Modération des vidéos** : [Analyser une vidéo pour détecter le contenu osé ou choquant et obtenir les résultats](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Consultez le guide de [démarrage rapide](video-moderation-api.md).

## <a name="review"></a>Révision

- **Travaux d’image** : [Démarrer un travail de modération qui analyse et crée des révisions](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Consultez le guide de [démarrage rapide](moderation-jobs-quickstart-dotnet.md).
- **Révisions des images** : [Créer des révisions d’images pour une intervention humaine](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Consultez le guide de [démarrage rapide](moderation-reviews-quickstart-dotnet.md).
- **Révisions des vidéos** : [Créer des révisions de vidéos pour une intervention humaine](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Consultez le guide de [démarrage rapide](video-reviews-quickstart-dotnet.md)
- **Révisions des transcriptions vidéo** : [Créer des révisions de transcriptions vidéo pour une intervention humaine](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs). Consultez le guide de [démarrage rapide](video-reviews-quickstart-dotnet.md)

Consultez tous les exemples .NET disponibles dans la rubrique [Exemples .NET Content Moderator sur GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).
