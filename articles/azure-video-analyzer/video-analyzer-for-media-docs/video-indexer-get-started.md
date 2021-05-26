---
title: Inscrivez-vous à Azure Video Analyzer for Media (anciennement Video Indexer) et chargez votre première vidéo - Azure
titleSuffix: Azure Media Services
description: Découvrez comment vous inscrire et charger votre première vidéo à l’aide du portail Azure Video Analyzer for Media (anciennement Video Indexer).
services: media-services
author: Juliako
manager: femila
ms.topic: quickstart
ms.date: 01/25/2021
ms.author: juliako
ms.openlocfilehash: 0f446af68f9bc314ecaefe06966ff2801e67f0be
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385596"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>Démarrage rapide : Comment s’inscrire et charger votre première vidéo

Ce guide de démarrage rapide vous montre comment vous connecter au site web Azure Video Analyzer for Media (anciennement Video Indexer) et comment charger votre première vidéo.

Lorsque vous créez un compte Azure Video Analyzer for Media, vous pouvez choisir un compte d’essai gratuit (où vous obtenez un certain nombre de minutes d’indexation gratuites) ou une option payante (où vous n’êtes pas limité par le quota). Avec l’essai gratuit, Azure Video Analyzer for Media fournit jusqu’à 600 heures d’indexation gratuite aux utilisateurs du site web et jusqu’à 2 400 heures d’indexation gratuite aux utilisateurs de l’API. Avec l’option payante, vous créez un compte Azure Video Analyzer for Media [connecté à votre abonnement Azure et un compte Azure Media Services](connect-to-azure.md). Vous payez pour les minutes indexées ; pour plus d’informations, consultez [Tarification Media Services](https://azure.microsoft.com/pricing/details/media-services/). 

## <a name="sign-up-for-video-analyzer-for-media"></a>S’inscrire à Azure Video Analyzer for Media

Pour commencer à développer avec Azure Video Analyzer for Media, accédez au site web [Video Analyzer for Media](https://www.videoindexer.ai/) et inscrivez-vous.

Une fois que vous avez commencé à utiliser Azure Video Analyzer for Media, l’ensemble de vos données stockées et du contenu chargé font l’objet d’un chiffrement (au repos) à clé gérée par Microsoft.

> [!NOTE]
> Passez en revue la section sur les [changements planifiés relatifs à l’authentification sur le site web Azure Video Analyzer for Media](./release-notes.md#planned-video-analyzer-for-media-website-authenticatication-changes).

## <a name="upload-a-video-using-the-video-analyzer-for-media-website"></a>Charger une vidéo à l’aide du site web Azure Video Analyzer for Media

### <a name="supported-file-formats-for-video-analyzer-for-media"></a>Formats de fichiers pris en charge pour Azure Video Analyzer for Media

Pour obtenir la liste des formats de fichiers que vous pouvez utiliser avec Azure Video Analyzer for Media, consultez l’article sur les [formats de conteneurs/fichiers d’entrée](../../media-services/latest/encode-media-encoder-standard-formats-reference.md).

### <a name="upload-a-video"></a>Charger une vidéo

1. Connectez-vous au site web [Azure Video Analyzer for Media](https://www.videoindexer.ai/).
1. Pour charger une vidéo, appuyez sur le bouton ou le lien **Charger**.

    > [!NOTE]
    > Le nom de la vidéo ne doit pas dépasser 80 caractères.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="Charger":::
1. Une fois votre vidéo chargée, Azure Video Analyzer for Media démarre l’indexation et l’analyse. La progression s’affiche. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="Progression du chargement":::
1. Au terme de l’analyse réalisée par Azure Video Analyzer for Media, vous recevez un e-mail contenant un lien vers votre vidéo et une brève description du contenu de celle-ci. Par exemple : personnes, mots prononcés et écrits, rubriques et entités nommées.
1. Vous pouvez retrouver ultérieurement votre vidéo dans la liste des bibliothèques et effectuer différentes opérations. Par exemple : recherche, réindexation, modification.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/uploaded.png" alt-text="Chargement du téléchargement":::

## <a name="supported-browsers"></a>Navigateurs pris en charge

Pour en savoir plus, consultez [Navigateurs pris en charge](video-indexer-overview.md#supported-browsers).

## <a name="see-also"></a>Voir aussi

Pour plus d’informations, consultez [Charger et indexer des vidéos](upload-index-videos.md).

Une fois que vous avez chargé et indexé une vidéo, vous pouvez commencer à utiliser le [site web Azure Video Analyzer for Media](video-indexer-view-edit.md) ou le [portail des développeurs Azure Video Analyzer for Media](video-indexer-use-apis.md) pour voir les insights de la vidéo. 

[Commencer à utiliser des API](video-indexer-use-apis.md)

## <a name="next-steps"></a>Étapes suivantes

Pour avoir une présentation détaillée, accédez à notre [lab de présentation](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md). 

Au terme de l’atelier, vous aurez une bonne compréhension du genre d’informations qui peuvent être extraites des contenus vidéo et audio, et vous serez plus à même d’identifier les opportunités en lien avec l’intelligence de contenu, de présenter l’intelligence artificielle vidéo sur Azure et d’expérimenter plusieurs scénarios sur Azure Video Analyzer for Media.

