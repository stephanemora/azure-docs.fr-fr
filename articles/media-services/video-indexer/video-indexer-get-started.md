---
title: S’inscrire à Video Indexer et charger votre première vidéo - Azure
titleSuffix: Azure Media Services
description: Découvrez comment s’inscrire et charger votre première vidéo à l’aide du portail Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 01/13/2020
ms.author: juliako
ms.openlocfilehash: 3a5ddf5bd4614b68e97e7616173a3e0640007530
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941556"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>Démarrage rapide : Comment s’inscrire et charger votre première vidéo

Ce tutoriel de démarrage vous montre comment vous connecter au site web Video Indexer et charger votre première vidéo.

Lorsque vous créez un compte Video Indexer, vous pouvez choisir un compte d’essai gratuit (où vous obtenez un certain nombre de minutes d’indexation gratuites) ou une option payante (où vous n’êtes pas limités par le quota). Avec l’essai gratuit, Video Indexer fournit jusqu’à 600 heures d’indexation gratuite aux utilisateurs du site web et jusqu’à 2 400 heures d’indexation gratuite aux utilisateurs de l’API. Avec l’option payante, vous créez un compte Video Indexer [connecté à votre abonnement Azure et un compte Azure Media Services](connect-to-azure.md). Vous payez pour les minutes indexées, ainsi que pour les frais liés au compte Azure Media Services. 

## <a name="sign-up-for-video-indexer"></a>S’inscrire à Video Indexer

Pour commencer à développer avec Video Indexer, accédez au site web [Video Indexer](https://www.videoindexer.com) et inscrivez-vous.

## <a name="upload-a-video-using-the-video-indexer-website"></a>Charger une vidéo à l’aide du site web Video Indexer

> [!NOTE]
> Un nom de vidéo ne doit pas dépasser 80 caractères.

### <a name="supported-file-formats-for-video-indexer"></a>Formats de fichiers pris en charge pour Video Indexer

Pour obtenir la liste des formats de fichiers que vous pouvez utiliser avec Video Indexer, consultez l’article [conteneur d’entrée/formats de fichiers](../latest/media-encoder-standard-formats.md#input-containerfile-formats).

### <a name="upload-a-video"></a>Charger une vidéo

1. Connectez-vous au site web [Video Indexer](https://www.videoindexer.ai/).
2. Pour charger une vidéo, appuyez sur le bouton ou le lien **Charger**.

    ![Télécharger](./media/video-indexer-get-started/video-indexer-upload.png)

    Une fois votre vidéo chargée, Video Indexer démarre l’indexation et l’analyse.

    ![Téléchargé](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Une fois l’analyse terminée par Video Indexer, vous obtiendrez une notification contenant un lien vers votre vidéo et une brève description des éléments découverts dedans. Par exemple : personnes, rubriques, OCR.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [Charger et indexer des vidéos](upload-index-videos.md).

Une fois que vous avez chargé et indexé une vidéo, vous pouvez commencer à utiliser le site web [Video Indexer](video-indexer-view-edit.md) ou [Video Indexer Developer Portal](video-indexer-use-apis.md) pour voir les insights de la vidéo. 

## <a name="see-also"></a>Voir aussi

[Présentation de Video Indexer](video-indexer-overview.md)

[Commencer à utiliser des API](video-indexer-use-apis.md).

