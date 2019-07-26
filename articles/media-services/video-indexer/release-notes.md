---
title: Notes de publication d’Azure Media Services Video Indexer | Microsoft Docs
description: Pour vous informer des développements les plus récents, cet article détaille les toutes dernières mises à jour concernant Azure Media Services Video Indexer.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 07/22/2019
ms.author: juliako
ms.openlocfilehash: fd67e867b5eac58838551ac29e1fca5e860b15ff
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414219"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Notes de publication d’Azure Media Services Video Indexer

Pour vous informer des développements les plus récents, cet article détaille les thèmes suivants :

* Versions les plus récentes
* Problèmes connus
* Résolution des bogues
* Fonctionnalités dépréciées

## <a name="june-2019"></a>Juin 2019

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer déployé dans la région Japon Est

Vous pouvez maintenant créer un compte payé Video Indexer dans la région Japon Est.

### <a name="create-and-repair-account-api-preview"></a>API de création et de réparation de compte (préversion)

Ajout d’une nouvelle API qui vous permet de [mettre à jour la clé ou le point de terminaison de connexion Azure Media Services](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag).

### <a name="improve-error-handling-on-upload"></a>Amélioration de la gestion des erreurs lors du chargement 

Un message descriptif est retourné en cas de configuration incorrecte du compte Azure Media Services sous-jacent.

### <a name="player-timeline-keyframes-preview"></a>Affichage d’un aperçu des images clés dans la chronologie du lecteur 

Vous pouvez maintenant afficher un aperçu de l’image pour chaque instant sur la chronologie du lecteur.

### <a name="editor-semi-select"></a>Semi-sélection dans l’éditeur

Vous pouvez maintenant afficher un aperçu de tous les insights qui sont sélectionnés suite au choix d’une période d’insight spécifique dans l’éditeur.

## <a name="may-2019"></a>Mai 2019

### <a name="update-custom-language-model-from-closed-caption-file"></a>Mise à jour du modèle de langage personnalisé à partir du fichier de sous-titres

Les API de [création de modèle de langage personnalisé](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) et de [mise à jour des modèles de langage personnalisés](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) prennent désormais en charge les formats de fichier VTT, SRT et TTML comme entrée pour les modèles de langage.

Quand vous appelez l’[API Update Video Transcript](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript), la transcription est ajoutée automatiquement. Le modèle d’entraînement associé à la vidéo est également mis à jour automatiquement. Pour plus d’informations sur la façon de personnaliser et d’entraîner vos modèles de langage, consultez [Personnaliser un modèle de langage avec Video Indexer](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Nouveaux formats de téléchargement de transcription – TXT et CSV

Outre le format de sous-titrage déjà pris en charge (SRT VTT et TTML), Video Indexer prend désormais en charge le téléchargement de la transcription aux formats TXT et CSV.

## <a name="next-steps"></a>Étapes suivantes

[Vue d'ensemble](video-indexer-overview.md)
