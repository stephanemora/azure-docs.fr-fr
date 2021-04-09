---
title: Utiliser l’éditeur Video Indexer pour créer des projets et ajouter des clips vidéo
titleSuffix: Azure Media Services
description: Cette rubrique montre comment utiliser l’éditeur Video Indexer pour créer des projets et ajouter des clips vidéo.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/28/2020
ms.author: juliako
ms.openlocfilehash: 07e4b05e12a5994c707a171c5736aea04a9c9723
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98632924"
---
# <a name="add-video-clips-to-your-projects"></a>Ajouter des clips vidéo à vos projets

Le site web [Video Indexer](https://www.videoindexer.ai/) vous permet d’utiliser des insights détaillés sur votre vidéo pour trouver le contenu multimédia adapté, rechercher les passages qui vous intéressent et utiliser les résultats dans le but de créer un tout nouveau projet. 

Une fois créé, le projet peut être restitué et téléchargé depuis Video Indexer, puis utilisé dans vos propres applications d’édition ou dans vos flux de travail en aval.

Voici quelques scénarios pour lesquels l’utilisation de cette fonctionnalité peut être pratique : 

* Création de moments forts du film pour les bandes-annonces.
* Utilisation d’anciens clips vidéos dans des bulletins d’informations.
* Création de contenus plus courts pour les réseaux sociaux.

Cet article explique comment créer un projet et ajouter des clips sélectionnés à partir des vidéos au projet. 

## <a name="create-new-project-and-manage-videos"></a>Créer un projet et gérer des vidéos

1. Accédez au site web [Video Indexer](https://www.videoindexer.ai/) et connectez-vous.
1. Sélectionnez l’onglet **Projets**. Si vous avez créé des projets avant, vous les verrez tous à cet endroit.
1. Cliquez sur **Créer un projet**.  

    :::image type="content" source="./media/video-indexer-view-edit/new-project.png" alt-text="Création d'un projet":::
1. Nommez votre projet en cliquant sur l’icône de crayon. Remplacez le texte « Projet sans titre » par le nom de votre projet, puis cliquez sur la coche.

    :::image type="content" source="./media/video-indexer-view-edit/new-project-edit-name.png" alt-text="Un nouveau projet":::
    
### <a name="add-videos-to-the-project"></a>Ajouter des vidéos au projet

> [!NOTE]
> Actuellement, les projets peuvent uniquement contenir des vidéos indexées dans la même langue. </br>Une fois que vous avez sélectionné une vidéo dans une langue, vous ne pouvez pas ajouter les vidéos de votre compte qui sont dans une autre langue ; ces vidéos sont grisées/désactivées.

1. Ajoutez les vidéos que vous souhaitez utiliser dans ce projet en sélectionnant **Ajouter des vidéos**.

    Toutes les vidéos de votre compte s’afficheront, ainsi qu’une zone de recherche indiquant « Search for text, keywords, or visual content » (Recherche de texte, de mots-clés ou de contenus visuels). Vous pouvez rechercher des vidéos qui ont une personne, une étiquette, une marque, un mot clé ou une occurrence spécifiés dans la transcription et l’OCR.
    
    Par exemple, dans l’image ci-dessous, nous recherchons des vidéos qui mentionnent « custom vision » dans la transcription uniquement (utilisez **Filtre** si vous souhaitez filtrer les résultats de la recherche).
    
    :::image type="content" source="./media/video-indexer-view-edit/custom-vision.png" alt-text="Capture d’écran montrant une recherche des vidéos qui mentionnent custom vision":::
1. Cliquez sur **Ajouter** pour ajouter des vidéos au projet.
1. Vous voyez maintenant toutes les vidéos que vous avez choisies. Voici les vidéos à partir desquelles vous allez sélectionner des clips pour votre projet.

    Vous pouvez réorganiser l’ordre des vidéos en les glissant-déposant ou en sélectionnant le bouton de menu de liste et en cliquant sur **Descendre** ou **Monter**. Dans le menu de la liste, vous serez également en mesure de supprimer la vidéo de ce projet. 
    
    Vous pouvez ajouter plus de vidéos à ce projet à tout moment en sélectionnant **Ajouter des vidéos**. Vous pouvez également ajouter plusieurs occurrences de la même vidéo à votre projet. Vous souhaiterez peut-être le faire si vous voulez diffuser un clip d’une vidéo, puis un clip à partir d’un autre vidéo, pour finir par un autre clip de la première vidéo. 

### <a name="select-clips-to-use-in-your-project"></a>Sélectionner des clips à utiliser dans votre projet

Si vous cliquez sur la flèche vers le bas dans la partie droite de chaque vidéo, vous allez ouvrir les informations dans la vidéo basées sur les horodatages (clips de la vidéo). 

1. Pour créer des requêtes pour des clips spécifiques, utilisez la zone de recherche qui indique «Search in transcript, visual text, people, and labels» (Rechercher dans la transcription, le texte visuel, les personnes et les étiquettes).
1. Sélectionnez **Afficher les informations** pour personnaliser les informations que vous souhaitez voir ou non. 

    :::image type="content" source="./media/video-indexer-view-edit/search-try-cognitive-services.png" alt-text="Capture d’écran montrant la recherche de vidéos qui mentionnent Try cognitive services":::
1. Ajoutez des filtres pour affiner les détails sur les scènes que vous cherchez en sélectionnant **Options de filtre**.

    Vous pouvez ajouter plusieurs filtres. 
1. Une fois que vous êtes satisfait de vos résultats, sélectionnez les clips que vous souhaitez ajouter à ce projet en sélectionnant le segment que vous souhaitez ajouter. Vous pouvez désélectionner ce clip en cliquant une nouvelle fois sur le segment.
    
    Ajoutez tous les segments d’une vidéo (ou tous ceux qui ont été retournés après votre recherche) en cliquant sur l’option de menu de liste à côté de la vidéo, puis en sélectionnant **Tout sélectionner**. 

À mesure que vous sélectionnez et organiser vos clips, vous pouvez afficher un aperçu de la vidéo dans le lecteur sur le côté droit de la page. 

> [!IMPORTANT]
> Pensez à enregistrer votre projet lorsque vous apportez des modifications en sélectionnant **Enregistrer le projet** en haut de la page. 

### <a name="render-and-download-the-project"></a>Restituer et télécharger le projet

> [!NOTE]
> Pour les comptes payants Video Indexer, la restitution de votre projet implique des coûts d’encodage. Les comptes d’essai gratuit Video Indexer sont limités à une restitution de 5 heures.

1. Une fois que vous avez terminé, assurez-vous que votre projet a été enregistré. Vous pouvez maintenant restituer ce projet. Cliquez sur **Rendu**. Une fenêtre contextuelle vous indique que Video Indexer va restituer un fichier, puis le lien de téléchargement sera envoyé à votre adresse e-mail. Sélectionnez Continuer. 

    :::image type="content" source="./media/video-indexer-view-edit/render-download.png" alt-text="Capture d’écran montrant Video Indexer avec l’option permettant d’afficher et de télécharger votre projet":::
    
    Vous verrez également une notification en haut de la page indiquant que le projet est en cours de restitution. Une fois cette opération terminée, une nouvelle notification vous en informera. Cliquez sur la notification pour télécharger le projet. Le projet sera téléchargé au format mp4.
1. Vous pouvez accéder à des projets enregistrés depuis l’onglet **Projets**. 

    Si vous sélectionnez ce projet, vous voyez toutes les informations et la chronologie de ce projet. Si vous sélectionnez **Éditeur de vidéo**, vous pouvez continuer à apporter des modifications à ce projet. Les modifications incluent l’ajout ou la suppression de vidéos et de clips ou le renommage du projet.
    
## <a name="create-a-project-from-your-video"></a>Créer un projet à partir de votre vidéo

Vous pouvez créer un projet directement à partir d’une vidéo dans votre compte. 

1. Accédez à l’onglet **Bibliothèque** du site web de Video Indexer.
1. Ouvrez la vidéo que vous souhaitez utiliser pour créer votre projet. Sur la page des informations et de la chronologie, sélectionnez le bouton **Éditeur de vidéo**.

    Vous êtes redirigé vers la même page que celle permettant de créer un projet. Contrairement au nouveau projet, vous voyez les segments d’informations horodatés de la vidéo que vous aviez commencé à modifier précédemment.

## <a name="see-also"></a>Voir aussi

[Présentation de Video Indexer](video-indexer-overview.md)

