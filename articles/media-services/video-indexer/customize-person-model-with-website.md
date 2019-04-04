---
title: Utiliser le site web Video Indexer pour personnaliser un modèle de personne - Azure
titlesuffix: Azure Media Services
description: Cet article explique comment personnaliser un modèle de personne avec le site web Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 03/19/2019
ms.author: anzaman
ms.openlocfilehash: 8dd535d97e40fe1dd4358d782db60940af1dd95d
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58892836"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Personnaliser un modèle de personne avec le site web Video Indexer

Video Indexer prend en charge la reconnaissance de célébrités pour le contenu vidéo. La fonctionnalité de reconnaissance de célébrités couvre environ un million de visages en fonction de la source de données couramment demandée comme IMDB, Wikipedia et les principaux influenceurs LinkedIn. Pour une présentation détaillée, consultez [personnaliser un modèle de personne dans Video Indexer](customize-person-model-overview.md).

Vous pouvez utiliser le site web Video Indexer pour modifier des visages détectés dans une vidéo, comme décrit dans cette rubrique. Vous pouvez également utiliser l’API, comme décrit dans [personnaliser un modèle de personne à l’aide des API](customize-person-model-with-api.md).

## <a name="central-management-of-person-models-in-your-account"></a>Gestion centralisée des modèles de personne dans votre compte

1. Pour afficher, modifier et supprimer les modèles de personne dans votre compte, accédez au site Web de Video Indexer et la connexion.
2. Cliquez sur le bouton de personnalisation de modèle de contenu dans l’angle supérieur droit de la page.

    ![Personnalisation du modèle de contenu](./media/customize-face-model/content-model-customization.png)
3. Sélectionnez l’onglet de personnes.

    Vous verrez le modèle par défaut une personne dans votre compte. Le modèle par défaut de personne conserve les visages vous avez modifié ou changé dans les analyses de vos vidéos pour lequel vous n’avez pas spécifié un modèle de personne personnalisé lors de l’indexation. 

    Si vous avez créé d’autres modèles de personne, ils seront afficheront sur cette page. 

    ![Personnalisation du modèle de contenu](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>Créer un nouveau modèle de personne

1. Cliquez sur le **+ ajouter un modèle** bouton.

    ![Ajouter une nouvelle personne](./media/customize-face-model/add-new-person.png)
2. Entrez le nom du modèle, puis cliquez sur la coche en regard du nom.

    ![Ajouter une nouvelle personne](./media/customize-face-model/add-new-person2.png)

    Vous avez créé un nouveau modèle de personne. Vous pouvez maintenant ajouter des visages vers le nouveau modèle de personne.
3. Cliquez sur le bouton de menu de liste et choisissez **+ ajouter une personne**.

    ![Ajouter une nouvelle personne](./media/customize-face-model/add-new-person3.png)
    
## <a name="add-a-new-person-to-a-person-model"></a>Ajouter une nouvelle personne à un modèle de personne

> [!NOTE]
> Video Indexer vous permet d’ajouter plusieurs personnes portant le même nom dans un modèle de personne. Toutefois, il est recommandé que vous donnez unque noms à chaque personne dans votre modèle pour la facilité d’utilisation et de clarté.

1. Pour ajouter un nouveau visage à un modèle de la personne, cliquez sur le bouton de menu de liste en regard du modèle de la personne que vous souhaitez ajouter la face à.
1. Cliquez sur **+ ajouter une personne** à partir du menu.

    ![Ajouter un nouveau visage à la personne](./media/customize-face-model/add-new-face.png)
 
    Une fenêtre contextuelle vous invitera à fournir vos informations de la personne. Tapez le nom de la personne et cliquez sur le bouton de vérification.

    ![Ajouter un nouveau visage à la personne](./media/customize-face-model/add-new-face2.png)
 
Vous pouvez ensuite choisir à partir de l’Explorateur de fichiers ou glisser -déplacer les images de la face de la face. Video Indexer prendra une image standard de tous les types de fichiers (ex : JPG, PNG, etc.).

Video Indexer doit être en mesure de détecter les occurrences de cette personne dans les vidéos futures cet index vous et les vidéos actuelles vous aviez déjà indexé, l’aide du modèle de personne à laquelle vous avez ajouté ce nouveau visage à. Reconnaissance de la personne dans vos vidéos actuels peut prendre un certain temps en vigueur, car il s’agit d’un traitement par lots.


## <a name="rename-a-person-model"></a>Renommer un modèle de personne

Vous pouvez renommer n’importe quel modèle de personne dans votre compte, y compris le modèle par défaut de personne. Même si vous renommez votre modèle de personne par défaut, il sera toujours servir le modèle de personne par défaut dans votre compte.

1. Cliquez sur le bouton de menu de liste en regard du modèle de la personne que vous souhaitez renommer.
2. Cliquez sur **renommer** à partir du menu.

    ![Renommer une personne](./media/customize-face-model/rename-person.png)
3. Cliquez sur le nom actuel du modèle et tapez votre nouveau nom.

    ![Renommer une personne](./media/customize-face-model/rename-person2.png)
4. Cliquez sur le bouton de vérification pour votre modèle à renommer.

## <a name="delete-a-person-model"></a>Supprimer un modèle de personne

Vous pouvez supprimer n’importe quel modèle de la personne que vous avez créé dans votre compte. Toutefois, vous ne pouvez pas supprimer votre modèle de personne par défaut.

1. Cliquez sur **supprimer** à partir du menu.

    ![Supprimer une personne](./media/customize-face-model/delete-person.png)
    
    Une fenêtre contextuelle s’affichent et vous informer que cette action va supprimer le modèle de personne et toutes les personnes et les fichiers qu’il contient. Il est impossible d’annuler cette opération. 

    ![Supprimer une personne](./media/customize-face-model/delete-person2.png)
1. Si vous êtes sûr, cliquez à nouveau sur Supprimer.

> [!NOTE]
> Les vidéos existantes qui ont été indexées à l’aide de ce modèle de personne (désormais supprimé) ne prendra pas en charge la possibilité de mettre à jour les noms des faces qui s’affichent dans la vidéo. Vous serez en mesure de modifier les noms de visages dans ces vidéos qu’une fois que vous réindexer à l’aide d’un autre modèle de personne. Si vous réindexer sans spécifier un modèle de la personne, le modèle par défaut sera utilisé. 

## <a name="manage-existing-people-in-a-person-model"></a>Gérer le personnel existant dans un modèle de personne

Pour consulter le contenu d’une de vos modèles de personne, cliquez sur la flèche en regard du nom du modèle de personne.
La liste déroulante affiche toutes les personnes de ce modèle particulier de la personne. Si vous cliquez sur le bouton de menu de liste en regard de chacune des personnes, vous voyez gérer, renommer et supprimer des options.  

![Ajouter un nouveau visage à la personne](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Renommer une personne

1. Pour renommer une personne dans votre modèle de personne, cliquez sur le bouton de menu de liste et choisissez **renommer** dans le menu de liste.
1. Cliquez sur le nom actuel de la personne et le type dans le nouveau nom.
1. Cliquez sur le bouton de vérification, et la personne qui sera renommée.

### <a name="delete-a-person"></a>Supprimer une personne

1. Pour supprimer une personne de votre modèle de personne, cliquez sur le bouton de menu de liste et choisissez **supprimer** dans le menu de liste.
1. Une fenêtre contextuelle vous indique que cette action va supprimer la personne et que cette action ne peut pas être annulée.
1. Cliquez sur Supprimer à nouveau et cela supprimera la personne à partir du modèle de personne.

### <a name="manage-a-person"></a>Gérer une personne

Si vous cliquez sur **gérer**, vous voyez toutes les faces ce modèle personne formée à partir de. Ces faces proviennent des occurrences de cette personne dans des vidéos qui utilisent ce modèle de la personne ou à partir d’images que vous avez téléchargées manuellement. 

Vous pouvez ajouter plusieurs faces à la personne en cliquant sur Ajouter des images.

Vous pouvez utiliser le volet gérer pour renommer la personne et à supprimer de la personne à partir du modèle de personne.

## <a name="use-a-person-model-to-index-a-video"></a>Utiliser un modèle de la personne pour indexer une vidéo

Vous pouvez utiliser un modèle de la personne pour indexer votre nouvelle vidéo en assignant le modèle de personne lors du téléchargement de la vidéo.

Pour utiliser votre modèle de personne sur une nouvelle vidéo, procédez comme suit :

1. Cliquez sur le **télécharger** bouton en haut de la page.

    ![Télécharger](./media/customize-face-model/upload.png)
1. Supprimer votre fichier vidéo dans le cercle ou accédez à votre fichier.
1. Cliquez sur la flèche options avancées.

    ![Télécharger](./media/customize-face-model/upload2.png)
1. Cliquez sur la liste déroulante et sélectionnez le modèle de la personne que vous avez créé.

    ![Télécharger](./media/customize-face-model/upload3.png)
1. Cliquez sur l’option de téléchargement dans la partie inférieure de la page, et votre nouvelle vidéo est indexée à l’aide de votre modèle de personne.

Si vous ne spécifiez pas un modèle de la personne au cours du chargement, vidéo sera d’indexer la vidéo à l’aide du modèle par défaut une personne dans votre compte.

## <a name="use-a-person-model-to-reindex-a-video"></a>Utiliser un modèle de personne réindexation d’une vidéo 

Pour utiliser un modèle de personne réindexer une vidéo dans votre collection, accédez à vos vidéos compte sur la page d’accueil de Video Indexer et le pointage sur le nom de la vidéo que vous souhaitez réindexer.

Vous voyez des options permettant de modifier, supprimer et réindexer votre vidéo. 

1. Cliquez sur l’option de réindexer votre vidéo.

    ![Réindexer](./media/customize-face-model/reindex.png)

    Vous pouvez maintenant sélectionner le modèle de personne réindexer votre vidéo avec.
1. Cliquez sur la liste déroulante et sélectionnez le modèle de la personne que vous souhaitez utiliser. 

    ![Réindexer](./media/customize-face-model/reindex2.png)

1. Cliquez sur le **réindexer** bouton et votre vidéo seront réindexées à l’aide de votre modèle de personne.

Toutes les modifications que vous apportez aux visages détectés et reconnus dans la vidéo que vous venez de réindexées seront enregistrées dans le modèle de la personne qui vous permet de réindexer la vidéo.

## <a name="managing-people-in-your-videos"></a>La gestion des personnes de vos vidéos

Vous pouvez gérer les visages détectés et les personnes qui ont été reconnus dans les vidéos que vous indexez en modifiant et en supprimant les visages.

Suppression d’un visage, supprime un visage spécifique les informations de la vidéo.

Modification d’un visage, renomme un visage qui est détecté et éventuellement reconnu dans votre vidéo. Lorsque vous modifiez un visage dans votre vidéo, ce nom est enregistré comme une entrée de la personne dans le modèle de la personne qui a été affectée à la vidéo pendant le chargement et l’indexation.

Si vous n’affectez pas un modèle de personne à la vidéo lors du chargement, votre modification est enregistrée dans le modèle de personne de votre compte par défaut.

### <a name="edit-a-face"></a>Modifier un visage


> [!NOTE]
> Si un modèle de la personne a deux ou plusieurs personnes différentes portant le même nom, vous ne serez pas en mesure de marquer ce nom dans les vidéos qui utilisent ce modèle de personne. Vous serez uniquement en mesure d’apporter des modifications aux personnes qui partagent ce nom dans l’onglet de personnes de la page de personnalisation du modèle de contenu dans Video Indexer. Pour cette raison, il est recommandé que vous attribuez un nom unique à chaque personne dans votre modèle de personne.

1. Accédez au site Web de Video Indexer et connectez-vous.
1. Recherchez une vidéo que vous souhaitez afficher et modifier dans votre compte.
1. Pour modifier un visage dans votre vidéo, accédez à l’onglet Insights et cliquez sur l’icône de crayon dans l’angle supérieur droit de la fenêtre.

    ![Modifier un visage dans votre vidéo](./media/customize-face-model/edit-face.png)
1. Cliquez sur l’un des visages détectés et modifiez le nom de la zone Unknown #X (Inconnu n°X) (ou le nom précédemment affecté au visage). 
1. Après avoir tapé le nouveau nom, cliquez sur l’icône de coche en regard du nouveau nom. Le nouveau nom est enregistré et reconnaît et noms de toutes les occurrences de cette face dans vos autres vidéos actuelles et à l’avenir vidéos que vous téléchargez. Reconnaissance du visage dans vos autres vidéos actuels peut prendre un certain temps en vigueur, car il s’agit d’un traitement par lots.

Si vous nommez un visage avec le nom d’une personne existante dans le modèle de personne à l’aide de la vidéo, il fusionne les images face détectée à partir de cette vidéo de cette personne avec ce qui existe déjà dans le modèle. Si vous nommez un visage avec un nom entièrement nouveau, cela crée une nouvelle entrée de personne dans le modèle de personne à l’aide de la vidéo. 

    ![Edit a face in your video](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>Supprimer un visage

Pour supprimer un visage détecté dans votre vidéo, accédez au volet d’informations et cliquez sur l’icône de crayon dans l’angle supérieur droit du volet. Cliquez sur l’option de suppression sous le nom de la face. Ce visage détecté est supprimé de la vidéo. Visage de la personne sera toujours détecté dans les autres vidéos dans laquelle elle apparaît, mais vous pouvez supprimer le visage à partir de ces vidéos ainsi une fois qu’ils ont été indexées. La personne, si elle avait été appelée, également continue d’exister dans le modèle de la personne qui a été utilisé pour indexer la vidéo à partir de laquelle vous avez supprimé le visage, sauf si vous supprimez spécifiquement la personne à partir du modèle de personne.

![Supprimer un visage dans votre vidéo](./media/customize-face-model/delete-face.png)


## <a name="next-steps"></a>Étapes suivantes

[Personnaliser le modèle de personne à l’aide des API](customize-person-model-with-api.md)
