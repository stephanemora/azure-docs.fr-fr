---
title: Utiliser le site web Video Indexer pour personnaliser un modèle de personne - Azure
titleSuffix: Azure Media Services
description: Cet article explique comment personnaliser un modèle de personne avec le site web Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: e5ce43e7cdf70c1f552a8f5bbed28ce47f1826fc
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513216"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Personnaliser un modèle de personne avec le site web Video Indexer

Video Indexer prend en charge la reconnaissance de célébrités dans du contenu vidéo. La fonctionnalité de reconnaissance de célébrités couvre environ un million de visages en fonction de la source de données couramment demandée comme IMDB, Wikipedia et les principaux influenceurs LinkedIn. Pour une présentation détaillée, consultez [Customize a Person model in Video Indexer](customize-person-model-overview.md) (Personnaliser un modèle de personne dans Video Indexer).

Vous pouvez utiliser le site web Video Indexer pour modifier des visages détectés dans une vidéo, comme décrit dans cette rubrique. Vous pouvez également utiliser l’API, comme décrit dans [Personnaliser un modèle de personne avec l’API Video Indexer](customize-person-model-with-api.md).

## <a name="central-management-of-person-models-in-your-account"></a>Gestion centrale des modèles de personne dans votre compte

1. Pour afficher, modifier et supprimer les modèles de personne dans votre compte, accédez au site web de Video Indexer et connectez-vous.
2. Cliquez sur le bouton de personnalisation du modèle de contenu en haut à droite de la page.

    ![Personnalisation du modèle de contenu](./media/customize-face-model/content-model-customization.png)
3. Sélectionnez l’onglet Personne.

    Vous verrez le modèle de personne par défaut dans votre compte. Le modèle de personne par défaut conserve les visages vous avez modifiés dans les insights de vos vidéos pour lequel vous n’avez pas spécifié de modèle de personne personnalisé lors de l’indexation. 

    Si vous avez créé d’autres modèles de personne, ils seront également affichés sur cette page. 

    ![Personnalisation du modèle de contenu](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>Créer un nouveau modèle de personne

1. Cliquez sur le bouton **+ Ajouter un modèle**.

    ![Ajouter une nouvelle personne](./media/customize-face-model/add-new-person.png)
2. Entrez le nom du modèle, puis cliquez sur la coche en regard du nom.

    ![Ajouter une nouvelle personne](./media/customize-face-model/add-new-person2.png)

    Vous avez créé un nouveau modèle de personne. Vous pouvez maintenant ajouter des visages au nouveau modèle de personne.
3. Cliquez sur le bouton de liste déroulante et choisissez **+ Ajouter une personne**.

    ![Ajouter une nouvelle personne](./media/customize-face-model/add-new-person3.png)
    
## <a name="add-a-new-person-to-a-person-model"></a>Ajouter une nouvelle personne à un modèle de personne

> [!NOTE]
> Video Indexer vous permet d’ajouter plusieurs personnes portant le même nom dans un modèle de personne. Toutefois, nous vous recommandons de donner des noms à chaque personne de votre modèle pour plus de praticité et de clarté.

1. Pour ajouter un nouveau visage à un modèle de personne, cliquez sur le bouton de menu de liste en regard du modèle de personne à qui vous souhaitez ajouter le visage.
1. Cliquez sur **+ Ajouter une personne** à partir du menu.

    ![Ajouter un nouveau visage à la personne](./media/customize-face-model/add-new-face.png)
 
    Une fenêtre contextuelle vous invitera à fournir les informations relatives à la personne. Tapez le nom de la personne et cliquez sur la coche.

    ![Ajouter un nouveau visage à la personne](./media/customize-face-model/add-new-face2.png)
 
Vous pouvez ensuite choisir à partir de l’Explorateur de fichiers ou glisser et déplacer les images de visage du visage concerné. Video Indexer accepte tous les types de fichiers d’image standard (p. ex. : JPG, PNG, etc.).

Video Indexer doit être en mesure de détecter les occurrences de cette personne dans les futures vidéos que vous indexez et les vidéos actuelles que vous aviez déjà indexées, à l’aide du modèle de personne auquel vous avez ajouté ce nouveau visage. La reconnaissance de la personne dans vos vidéos actuelles peut prendre un certain temps pour entrer en vigueur, car il s’agit d’un processus de traitement par lots.


## <a name="rename-a-person-model"></a>Renommer un modèle de personne

Vous pouvez renommer tous les modèles de personne dans votre compte, y compris le modèle de personne par défaut. Même si vous renommez votre modèle de personne par défaut, il sera toujours considéré comme le modèle de personne par défaut dans votre compte.

1. Cliquez sur le bouton de menu de liste en regard du modèle de personne que vous souhaitez renommer.
2. Cliquez sur **Renommer** dans le menu.

    ![Renommer une personne](./media/customize-face-model/rename-person.png)
3. Cliquez sur le nom actuel du modèle et tapez votre nouveau nom.

    ![Renommer une personne](./media/customize-face-model/rename-person2.png)
4. Cliquez sur la coche du modèle à renommer.

## <a name="delete-a-person-model"></a>Supprimer un modèle de personne

Vous pouvez supprimer les modèles de personne que vous avez créés dans votre compte. Toutefois, vous ne pouvez pas supprimer votre modèle de personne par défaut.

1. Cliquez sur **Supprimer** dans le menu.

    ![Supprimer une personne](./media/customize-face-model/delete-person.png)
    
    Une fenêtre contextuelle s’affiche et vous informe que cette action va supprimer le modèle de personne et toutes les personnes et les fichiers qu’il contient. Il est impossible d’annuler cette opération. 

    ![Supprimer une personne](./media/customize-face-model/delete-person2.png)
1. Si vous êtes sûr, cliquez à nouveau sur Supprimer.

> [!NOTE]
> Les vidéos existantes qui ont été indexées à l’aide de ce modèle de personne (désormais supprimé) ne prendront pas en charge la possibilité de mettre à jour les noms des visages qui s’affichent dans la vidéo. Vous pourrez modifier les noms des visages dans ces vidéos uniquement après les avoir réindexées à l’aide d’un autre modèle de personne. Si vous réindexez sans spécifier un modèle de personne, le modèle par défaut est utilisé. 

## <a name="manage-existing-people-in-a-person-model"></a>Gérer la personne existante dans un modèle de personne

Pour consulter le contenu d’un de vos modèles de personne, cliquez sur la flèche en regard du nom du modèle de personne.
La liste déroulante affiche toutes les personnes de ce modèle de personne particulier. Si vous cliquez sur le bouton de menu de liste en regard de chaque personne, vous pouvez voir les options gérer, renommer et supprimer.  

![Ajouter un nouveau visage à la personne](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Renommer une personne

1. Pour renommer une personne dans votre modèle de personne, cliquez sur le bouton de menu de liste, puis choisissez **Renommer** dans le menu de liste.
1. Cliquez sur le nom actuel de la personne et tapez votre nouveau nom.
1. Cliquez sur la coche, et la personne sera renommée.

### <a name="delete-a-person"></a>Supprimer une personne

1. Pour supprimer une personne dans votre modèle de personne, cliquez sur le bouton de menu de liste, puis choisissez **Supprimer** dans le menu de liste.
1. Une fenêtre contextuelle vous indique que cette action va supprimer la personne et qu’il est impossible d’annuler cette opération.
1. Cliquez une nouvelle fois sur Supprimer pour supprimer la personne du modèle de personne.

### <a name="manage-a-person"></a>Gérer une personne

Si vous cliquez sur **Gérer**, vous voyez tous les visages à partir desquels ce modèle de personne est formé. Ces visages proviennent des occurrences de cette personne dans des vidéos qui utilisent ce modèle de personne ou d’images que vous avez chargées manuellement. 

Vous pouvez ajouter plus de visages à la personne en cliquant sur Ajouter des images.

Vous pouvez utiliser le volet de gestion pour renommer la personne et supprimer la personne du modèle de personne.

## <a name="use-a-person-model-to-index-a-video"></a>Utiliser un modèle de personne pour indexer une vidéo

Vous pouvez utiliser un modèle de personne pour indexer votre nouvelle vidéo en attribuant le modèle de personne lors du chargement de la vidéo.

Pour utiliser votre modèle de personne sur une nouvelle vidéo, procédez comme suit :

1. Cliquez sur le bouton **Charger** en haut de la page.

    ![Télécharger](./media/customize-face-model/upload.png)
1. Déposez votre fichier vidéo dans le cercle ou accédez à votre fichier.
1. Cliquez sur la flèche des options avancées.

    ![Télécharger](./media/customize-face-model/upload2.png)
1. Cliquez sur la liste déroulante et sélectionnez le modèle de personne que vous avez créé.

    ![Télécharger](./media/customize-face-model/upload3.png)
1. Cliquez sur l’option Charger située en bas de la page, et votre nouvelle vidéo sera indexée à l’aide de votre modèle de personne.

Si vous ne spécifiez pas de modèle de personne au cours du chargement, Video Indexer indexera la vidéo à l’aide du modèle de personne par défaut dans votre compte.

## <a name="use-a-person-model-to-reindex-a-video"></a>Utiliser un modèle de personne pour réindexer une vidéo 

Pour utiliser un modèle de personne pour réindexer une vidéo de votre collection, accédez aux vidéos du compte sur la page d’accueil de Video Indexer et pointez sur le nom de la vidéo que vous souhaitez réindexer.

Les options vous permettant de modifier, de supprimer et de réindexer votre vidéo s’affichent. 

1. Cliquez sur l’option vous permettant de réindexer votre vidéo.

    ![Réindexer](./media/customize-face-model/reindex.png)

    Vous pouvez maintenant sélectionner le modèle de personne avec lequel réindexer votre vidéo.
1. Cliquez sur la liste déroulante et sélectionnez le modèle de personne que vous souhaitez utiliser. 

    ![Réindexer](./media/customize-face-model/reindex2.png)

1. Cliquez sur le bouton **Réindexer** et votre vidéo sera réindexée à l’aide de votre modèle de personne.

Toutes les modifications que vous apportez aux visages détectés et reconnus dans la vidéo que vous venez de réindexer seront enregistrées dans le modèle de personne que vous utilisez pour réindexer la vidéo.

## <a name="managing-people-in-your-videos"></a>Gestion des personnes de vos vidéos

Vous pouvez gérer les visages détectés et les personnes reconnues dans les vidéos que vous indexez en modifiant et en supprimant les visages.

La suppression d’un visage supprime un visage spécifique des insights de la vidéo.

La modification d’un visage renomme un visage qui est détecté et éventuellement reconnu dans votre vidéo. Lorsque vous modifiez un visage dans votre vidéo, ce nom est enregistré comme une entrée de la personne dans le modèle de personne qui a été attribué à la vidéo pendant le chargement et l’indexation.

Si vous n’attribuez pas de modèle de personne à la vidéo lors du chargement, votre modification est enregistrée dans le modèle de personne par défaut de votre compte.

### <a name="edit-a-face"></a>Modifier un visage


> [!NOTE]
> Si un modèle de personne a deux ou plusieurs personnes différentes portant le même nom, vous ne serez pas en mesure de marquer ce nom dans les vidéos qui utilisent ce modèle de personne. Vous pourrez uniquement apporter des modifications aux personnes qui partagent ce nom dans l’onglet Personne de la page de personnalisation du modèle de contenu dans Video Indexer. C’est pour cette raison qu’il est recommandé d’attribuer un nom unique à chaque personne dans votre modèle de personne.

1. Accédez au site web Video Indexer et connectez-vous.
1. Recherchez une vidéo que vous souhaitez afficher et modifier dans votre compte.
1. Pour modifier un visage dans votre vidéo, accédez à l’onglet Insights, puis cliquez sur l’icône de crayon en haut à droite de la fenêtre.

    ![Modifier un visage dans votre vidéo](./media/customize-face-model/edit-face.png)
1. Cliquez sur l’un des visages détectés et modifiez le nom de la zone Unknown #X (Inconnu n°X) (ou le nom précédemment affecté au visage). 
1. Après avoir tapé le nouveau nom, cliquez sur l’icône de coche en regard du nouveau nom. Cela permet d’enregistrer le nouveau nom, ainsi que de reconnaître et de nommer toutes les occurrences de ce visage dans les autres vidéos actuelles et les vidéos que vous chargerez par la suite. La reconnaissance du visage dans vos autres vidéos actuelles peut prendre un certain temps pour entrer en vigueur, car il s’agit d’un processus de traitement par lots.

Si vous nommez un visage d’après une personne qui existe dans le modèle de personne utilisé par la vidéo, les images des visages détectés dans cette vidéo de cette personne seront fusionnées avec ce qui se trouve déjà dans le modèle. Si vous attribuez un nom entièrement nouveau à un visage, cela crée une nouvelle entrée de personne dans le modèle de personne que la vidéo utilise. 

    ![Edit a face in your video](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>Supprimer un visage

Pour supprimer un visage détecté dans votre vidéo, accédez au volet Insights, puis cliquez sur l’icône de crayon en haut à droite du volet. Cliquez sur l’option Supprimer sous le nom du visage. Ce visage détecté est supprimé de la vidéo. Le visage de la personne sera toujours détecté dans les autres vidéos dans lesquelles il apparaît, mais vous pouvez aussi le supprimer de ces vidéos une fois qu’elles ont été indexées. La personne, si elle avait été nommée, continue également d’exister dans le modèle de personne qui a été utilisé pour indexer la vidéo à partir de laquelle vous avez supprimé le visage, sauf si vous supprimez spécifiquement la personne à partir du modèle de personne.

![Supprimer un visage dans votre vidéo](./media/customize-face-model/delete-face.png)


## <a name="next-steps"></a>Étapes suivantes

[Personnaliser le modèle de personne à l’aide des API](customize-person-model-with-api.md)
