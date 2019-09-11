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
ms.date: 08/29/2019
ms.author: juliako
ms.openlocfilehash: a7d178972a8c8b413e3c6336c403348f1eb78abb
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232597"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Notes de publication d’Azure Media Services Video Indexer

Pour vous informer des développements les plus récents, cet article détaille les thèmes suivants :

* Versions les plus récentes
* Problèmes connus
* Résolution des bogues
* Fonctionnalités dépréciées

## <a name="august-2019"></a>Août 2019
 
### <a name="video-indexer-deployed-in-uk-south"></a>Déploiement de Video Indexer dans la région Royaume-Uni Sud

Vous pouvez maintenant créer un compte Video Indexer payant dans la région Royaume-Uni Sud.

### <a name="new-editorial-shot-type-insights-available"></a>Nouvelles informations éditoriales disponibles

De nouvelles balises ajoutées aux séquences vidéo fournissent des « types de prises de vue » éditoriales pour les identifier à l'aide de phrases éditoriales courantes utilisées dans le processus de création de contenu, par exemple : gros plan extrême, gros plan, large, moyen, deux plans, extérieur, intérieur, face gauche et face droite (disponible dans le fichier JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>Extraction de nouvelles entités Personnes et Lieux disponible

Video Indexer identifie les lieux et les personnes nommés via le traitement automatique du langage naturel (TALN) à partir de la reconnaissance optique de caractères et de la transcription de la vidéo. Video Indexer utilise un algorithme d'apprentissage automatique pour reconnaître le moment où certains lieux (par exemple, la Tour Eiffel) ou certaines personnes (par exemple, Monsieur Dupont) sont appelés dans une vidéo.

### <a name="keyframes-extraction-in-native-resolution"></a>Extraction d'images clés en résolution native

Les images clés extraites par Video Indexer sont disponibles dans la résolution d'origine de la vidéo.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>Lancement du mode Disponibilité générale pour la formation de modèles de visage personnalisés à partir d'images

Passage du mode Préversion au mode Disponibilité générale pour la formation des visages à partir d'images (disponible via l'API et sur le portail).

> [!NOTE]
> Ce passage du mode Préversion au mode Disponibilité générale n'a aucun impact sur les prix.

### <a name="hide-gallery-toggle-option"></a>Option Masquer la galerie

L'utilisateur peut choisir de masquer l'onglet de la galerie sur le portail (comme pour l'onglet des exemples).
 
### <a name="maximum-url-size-increased"></a>Augmentation de la taille maximale des URL

Prise en charge de la chaîne de requête d'URL de 4096 (au lieu de 2048) pour l'indexation d'une vidéo.
 
### <a name="support-for-multi-lingual-projects"></a>Prise en charge des projets multilingues

Les projets peuvent maintenant être créés à partir de vidéos indexées dans différents langages (API uniquement).

## <a name="july-2019"></a>Juillet 2019

### <a name="editor-as-a-widget"></a>Éditeur en tant que widget

L’Éditeur IA Video Indexer est désormais disponible en tant que widget pour être incorporé dans les applications clientes.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Mise à jour du modèle de langage personnalisé à partir du fichier de sous-titres à partir du portail

Les clients peuvent fournir des fichiers aux formats VTT, SRT et TTML comme entrée pour les modèles de langage dans la page de personnalisation du portail.

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
