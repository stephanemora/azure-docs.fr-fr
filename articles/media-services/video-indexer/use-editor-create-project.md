---
title: Utilisez l’éditeur de Video Indexer pour créer des projets
titlesuffix: Azure Media Services
description: Cette rubrique montre comment utiliser l’éditeur de Video Indexer pour créer des projets.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: a9d6396cab560a201b98497e787af4b6c7c2dabb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60560816"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>Utilisez l’éditeur de Video Indexer pour créer des projets

Site Web d’indexeur vidéo, vous permet d’utiliser des informations détaillées de vos vidéos à : rechercher le contenu multimédia de droite, recherchez les parties que vous êtes intéressé et utilisez les résultats pour créer un projet totalement nouveau. Une fois créé, le projet peut être rendu et téléchargé à partir de Video Indexer et être utilisé dans votre propre modification des applications ou d’un flux de travail en aval.

Certains scénarios où vous pouvez trouver cette fonctionnalité utile sont : 

* Création de film met en surbrillance pour les codes de fin.
* À l’aide de clips ancien de vidéos dans des casts de news.
* Création d’un contenu plus court pour les médias sociaux.

Cet article explique comment créer un projet à partir de zéro et également comment créer un projet à partir d’une vidéo dans votre compte.

## <a name="create-new-project-and-manage-videos"></a>Nouveau projet de créer et gérer des vidéos

1. Accédez au site web [Video Indexer](https://www.videoindexer.ai/) et connectez-vous.
1. Sélectionnez le **projets** onglet. Si vous avez créé des projets avant, vous verrez tous vos autres projets ici.
1. Cliquez sur **créer un nouveau projet**.  

    ![Nouveau projet](./media/video-indexer-view-edit/new-project.png)
1. Nommez votre projet en cliquant sur l’icône de crayon. Remplacez le texte qui indique « Projet sans titre » par le nom de votre projet, puis cliquez sur la vérification.

    ![Nouveau projet](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>Ajouter des vidéos au projet

> [!NOTE]
> Actuellement, les projets peuvent contenir uniquement les vidéos indexées dans la même langue. Une fois que vous sélectionnez une vidéo dans une langue, vous ne pouvez pas ajouter les vidéos dans votre compte qui se trouvent dans une autre langue.

1. Ajouter des vidéos que vous souhaitez utiliser dans ce projet en sélectionnant **ajouter des vidéos**.

    Vous verrez toutes les vidéos dans votre compte et une zone de recherche qui dit « Recherche de texte, mots clés ou contenu visuel ». Pour rechercher des vidéos qui ont un personne spécifiée, étiquette, marque, mot clé ou une occurrence dans la transcription et la reconnaissance optique de caractères.
    
    Par exemple, dans l’image ci-dessous, nous recherchons des vidéos qui mentionne « GitHub ».
    
    ![GitHub](./media/video-indexer-view-edit/github.png)

    Vous pouvez filtrer davantage vos résultats en sélectionnant **filtrer les résultats**. Vous pouvez filtrer pour afficher des vidéos qui contiennent une personne spécifique ou pour spécifier que vous voulez uniquement voir le résultat de vidéo est une dans une langue donnée ou avoir un propriétaire spécifique. <br/> Vous pouvez également spécifier l’étendue de votre requête. Par exemple, si vous voulez effectuer la recherche « GitHub » la reconnaissance optique de caractères, sélectionnez **visuelle du texte**.

    ![Filtrer](./media/video-indexer-view-edit/visual-text.png)

    Vous pouvez superposer plusieurs filtres à votre requête. Utilisez le **+** / **-** boutons Ajouter/supprimer des filtres. Utilisez **effacer les filtres** pour supprimer tous les filtres.
1. Pour ajouter des vidéos, sélectionnez-les, puis sélectionnez **ajouter**.
1. Maintenant, vous verrez toutes les vidéos que vous avez choisi. Voici les vidéos à partir de laquelle vous vous apprêtez à sélectionner des éléments de votre projet.

    Vous pouvez réorganiser l’ordre des vidéos en faisant glisser ou en sélectionnant le bouton de menu de liste et en sélectionnant **Descendre** ou **monter**. Dans le menu de la liste, vous serez également en mesure de supprimer la vidéo à partir de ce projet. 

    ![Réorganiser](./media/video-indexer-view-edit/rearrange.png)
    
    Vous avez la possibilité d’ajouter plus de vidéos à ce projet à tout moment en sélectionnant **ajouter des vidéos**. Vous pouvez également ajouter plusieurs occurrences de la même vidéo à votre projet. Vous souhaiterez peut-être le faire si vous souhaitez afficher un élément à partir d’une vidéo, puis un clip à partir d’un autre et un autre élément à partir de la première vidéo. 

### <a name="select-clips-to-use-in-your-project"></a>Sélectionner des éléments à utiliser dans votre projet

Si vous cliquez sur la flèche vers le bas dans la partie droite de chaque vidéo, vous allez ouvrir des informations dans la vidéo selon les horodatages (éléments de la vidéo). 

1. Sélectionnez **afficher les informations** pour personnaliser les informations que vous souhaitez voir et qui vous ne souhaitez pas voir. 

    ![Voir les insights](./media/video-indexer-view-edit/insights.png)
1. Pour créer des requêtes pour les éléments spécifiques, utilisez la zone de recherche qui indique « Rechercher dans la transcription, visuelle du texte, des personnes et des étiquettes ».
1. Ajoutez des filtres pour affiner les détails sur les scènes que vous cherchez en sélectionnant **options de filtre**.

    ![Options de filtre](./media/video-indexer-view-edit/filter-options.png)

    Par exemple, voulez-vous voir des éléments où GitHub est mentionné tandis que Donovan Brown est sur l’écran. Pour ce faire, vous devez ajouter un filtre « include » qui a « People » comme type d’information. Vous devez ensuite à taper « Donovan Brown » dans la zone de recherche pour le filtre.
    
    ![Inclure](./media/video-indexer-view-edit/include.png)
    
    Si vous souhaitez que les clips où GitHub est mentionné alors que Donovan Brown est _pas_ sur l’écran, vous modifieriez simplement le filtre « include » dans un filtre de « exclude » à l’aide de la liste déroulante. 

1. Ajouter un élément à votre projet en sélectionnant le segment que vous souhaitez ajouter. Vous pouvez désélectionner cette image clipart en cliquant sur le segment à nouveau.
    
    Ajouter tous les segments d’une vidéo en cliquant sur l’option de menu de liste en regard de la vidéo et en sélectionnant **sélectionner tous les segments**. 

    ![Ajouter tout](./media/video-indexer-view-edit/add-all.png)

    Vous pouvez effacer tout de votre sélection en sélectionnant Effacer la sélection.

> [!TIP]
> Que vous sélectionnez et ordre de vos éléments, vous pouvez afficher un aperçu de la vidéo dans le lecteur sur le côté droit de la page. 

![VERSION PRÉLIMINAIRE](./media/video-indexer-view-edit/preview.png)

Pensez à enregistrer votre projet lorsque vous apportez des modifications en sélectionnant **enregistrer le projet**. 

### <a name="render-and-download-the-project"></a>Afficher et télécharger le projet

> [!NOTE]
> Pour Video Indexer payé des comptes, rendu de votre projet a les coûts d’encodage. Des comptes d’évaluation indexeur vidéo sont limitées à 5 heures du rendu.

1. Une fois que vous avez terminé, assurez-vous que votre projet a été enregistré. Vous pouvez maintenant afficher ce projet. Sélectionnez **afficher et télécharger**. 

    ![Enregistrer](./media/video-indexer-view-edit/save.png)

    Il y aura une fenêtre contextuelle qui vous indique que Video indexer affichera un fichier, puis le lien de téléchargement sera envoyé à votre adresse e-mail. Sélectionnez Continuer. 
    
    Vous verrez également une notification indiquant que le projet est rendu en haut de la page. Est ensuite rendu, vous verrez une nouvelle notification le projet a été correctement restitué. Cliquez sur la notification pour télécharger le projet. Il télécharge le projet au format mp4.

    ![Rendu effectué](./media/video-indexer-view-edit/rendering-done.png)

1. Vous pouvez accéder à des projets enregistrés à partir de la **projets** onglet. 

    Si vous sélectionnez ce projet, vous voyez toutes les informations et la chronologie de ce projet. Si vous sélectionnez **éditeur de vidéo**, vous pouvez continuer à apporter des modifications à ce projet. Modifications incluent l’ajout ou la suppression des vidéos et des clips ou la modification du nom du projet.

    ![Éditeur de vidéo](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>Créer un projet à partir de votre vidéo

Vous pouvez créer un nouveau projet directement à partir d’une vidéo dans votre compte. 

1. Accédez à la **bibliothèque** onglet du site Web de Video Indexer.
1. Ouvrez la vidéo que vous souhaitez utiliser pour créer votre projet. Sur la page de la chronologie et un insights, sélectionnez le **éditeur de vidéo** bouton.

    Vous accédez alors à la même page que vous avez utilisé pour créer un nouveau projet. Contrairement au nouveau projet, vous voyez les segments d’insights horodaté de la vidéo, ce qui vous aviez commencé la modification précédemment.

## <a name="see-also"></a>Voir aussi

[Présentation de Video Indexer](video-indexer-overview.md)

