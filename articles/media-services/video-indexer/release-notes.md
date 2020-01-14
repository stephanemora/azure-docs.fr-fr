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
ms.date: 12/19/2019
ms.author: juliako
ms.openlocfilehash: a8f4174fca1a8703bb112c19e785d4d9686a82f5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453301"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Notes de publication d’Azure Media Services Video Indexer

>Soyez notifié de la disponibilité des mises à jour sur cette page en faisant un copier-coller de cette URL : `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` dans votre lecteur de flux RSS.

Pour vous informer des développements les plus récents, cet article détaille les thèmes suivants :

* Versions les plus récentes
* Problèmes connus
* Résolution des bogues
* Fonctionnalités dépréciées

## <a name="november-2019"></a>Novembre 2019
 
* Prise en charge des modèles de langage personnalisés en coréen

    Video Indexer prend désormais en charge les modèles de langage personnalisés coréens (`ko-KR`) à la fois dans l’API et le portail. 
* Nouvelles langues prises en charge pour la reconnaissance vocale (STT)

    Les API Video Indexer prennent désormais en charge STT en Arabe levantin (ar-SY), en dialecte anglais britannique (en-GB) et en dialecte anglais australien (en-AU).
    
    Pour le téléchargement de vidéos, nous avons remplacé zh-HANS par zh-CN. Les deux sont pris en charge, mais zh-CN est recommandé et plus précis.
    
## <a name="october-2019"></a>2 octobre 2019
 
* Rechercher des caractères animés dans la galerie

    Lorsque vous indexez des caractères animés, vous pouvez à présent les rechercher dans la galerie vidéo du compte. Pour plus d’informations, consultez [Reconnaissance de personnages animés](animated-characters-recognition.md).

## <a name="september-2019"></a>Septembre 2019
 
Plusieurs améliorations ont été annoncées au salon IBC 2019 :
 
* Reconnaissance de caractères animés (préversion publique)

    Possibilité de détecter, regrouper et reconnaître les caractères dans un contenu animé, par le biais de l’intégration à une vision personnalisée. Pour plus d’informations, consultez [Détection de personnages animés](animated-characters-recognition.md).
* Identification multilingue (préversion publique)

    Détectez les segments en plusieurs langues dans la piste audio et créez une transcription multilingue à partir de ces derniers. Prise en charge initiale : anglais, espagnol, allemand et français. Pour plus d’informations, consultez [Identifier et transcrire automatiquement un contenu multilingue](multi-language-identification-transcription.md).
* Extraction d’entité nommée pour les personnes et la localisation

    extrait des marques, des emplacements et des personnes à partir de la reconnaissance vocale et du texte visuel via le traitement en langage naturel (NLP).
* Classification des types de plans éditoriaux

    Étiquetage des plans avec des types éditoriaux tels que gros plan, plan moyen, deux plans, intérieur, extérieur, etc. Pour plus d’informations, consultez [Détection du type de plan éditorial](scenes-shots-keyframes.md#editorial-shot-type-detection).
* Amélioration de l’inférence de rubrique, avec à présent couverture du niveau 2
    
    Le modèle d’inférence de rubrique prend maintenant en charge la précision plus profonde de la taxonomie IPTC. Pour plus d’informations, consultez [Azure Media Services new AI-powered innovation](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/).

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
