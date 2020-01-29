---
title: Personnaliser un modèle de langage avec le site Web Video Indexer - Azure
titleSuffix: Azure Media Services
description: Cet article explique comment personnaliser un modèle de langage avec le site web Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 329da39914ef957d3a5376ba59e0c7103ad6a5dd
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513913"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Personnaliser un modèle de langage avec le site web Video Indexer

Video Indexer vous permet de créer des modèles de langage personnalisés pour la reconnaissance vocale en chargeant un texte d’adaptation, à savoir un texte extrait du domaine de vocabulaire auquel vous souhaitez que le moteur s’adapte. Une fois votre modèle entraîné, les nouveaux mots qui apparaissent dans le texte d’adaptation seront reconnus. 

Pour obtenir une présentation détaillée et les meilleures pratiques pour les modèles de langage personnalisés, consultez [Personnaliser un modèle de langage avec Video Indexer](customize-language-model-overview.md).

Vous pouvez utiliser le site web Video Indexer pour créer et modifier des modèles de langage personnalisés dans votre compte, comme décrit dans cette rubrique. Vous pouvez également utiliser l'API, comme décrit dans [Personnaliser le modèle de langage à l'aide des API](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Créer un modèle de langage

1. Accédez au site web [Video Indexer](https://www.videoindexer.ai/) et connectez-vous.
2. Pour personnaliser un modèle à partir de votre compte, cliquez sur le bouton **Personnalisation du modèle de contenu** en haut à droite de la page.

   ![Personnaliser un modèle de contenu](./media/content-model-customization/content-model-customization.png)

3. Sélectionnez l'onglet **Langage**.

    La liste des langues prises en charge s'affiche. 

    ![Personnaliser un modèle de langage](./media/customize-language-model/customize-language-model.png)

4. Sous le langage de votre choix, cliquez sur **Ajouter un modèle**.
5. Entrez le nom du modèle de langage, puis appuyez sur Entrée.

    Une fois le modèle créé, vous pouvez y charger des fichiers texte.
6. Pour ajouter un fichier texte, cliquez sur **Ajouter un fichier**. Votre Explorateur de fichiers s’ouvre.

7. Accédez et sélectionnez le fichier texte de votre choix. Vous pouvez ajouter plusieurs fichiers texte à un modèle de langage.

    Vous pouvez également ajouter un fichier texte en cliquant sur le bouton **...** situé à droite du modèle de langage et en sélectionnant **Ajouter un fichier**.
8. Une fois les fichiers texte chargés, cliquez sur l'option verte **Entraîner**.

    ![Entraîner un modèle de langage](./media/customize-language-model/train-model.png)

Le processus d'apprentissage peut prendre plusieurs minutes. Une fois l'apprentissage terminé, le texte **Entraîné** apparaît en regard du modèle. Vous pouvez afficher un aperçu, télécharger et supprimer le fichier à partir du modèle.

![Modèle de langage entraîné](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Utiliser un modèle de langage sur une nouvelle vidéo

Pour utiliser votre modèle de langage sur une nouvelle vidéo, effectuez l’une des opérations suivantes :

* Cliquez sur le bouton **Charger** en haut de la page. 

    ![Télécharger](./media/customize-language-model/upload.png)
* Déposez votre fichier audio ou vidéo dans le cercle ou accédez à votre fichier.

    ![Télécharger](./media/customize-language-model/upload2.png)

Vous pourrez ainsi sélectionner le **langage source de la vidéo**. Cliquez sur la liste déroulante et sélectionnez un modèle de langage que vous avez créé. Le langage de votre modèle de langage ainsi que le nom que vous lui avez donné y sont indiqués entre parenthèses.

Cliquez sur l'option **Charger** située en bas de la page, et votre nouvelle vidéo sera indexée à l’aide de votre modèle de langage.

### <a name="using-a-language-model-to-reindex"></a>Utiliser un modèle de langage pour réindexer

Pour utiliser votre modèle de langage pour réindexer une vidéo de votre collection, accédez aux **Vidéo du compte** sur la page d'accueil de [Video Indexer](https://www.videoindexer.ai/) et pointez sur le nom de la vidéo que vous souhaitez réindexer.

Les options vous permettant de modifier, de supprimer ou de réindexer votre vidéo s'affichent. Cliquez sur l’option vous permettant de réindexer votre vidéo.

![Réindexer](./media/customize-language-model/reindex1.png)

Sélectionnez le **langage source de la vidéo**  afin de réindexer votre vidéo. Cliquez sur la liste déroulante et sélectionnez un modèle de langage que vous avez créé. Le langage de votre modèle de langage ainsi que le nom que vous lui avez donné y sont indiqués entre parenthèses.

![Réindexer](./media/customize-language-model/reindex.png)

Cliquez sur le bouton **Réindexer** et votre vidéo sera réindexée à l'aide de votre modèle de langage.

## <a name="edit-a-language-model"></a>Modifier un modèle de langage

Vous pouvez modifier un modèle de langage en modifiant son nom, en ajoutant ou en supprimant des fichiers.

Si vous ajoutez ou supprimez des fichiers du modèle de langage, vous devez l'entraîner à nouveau en cliquant sur l'option verte **Entraîner**.

### <a name="rename-the-language-model"></a>Renommer le modèle de langage

Pour modifier le nom du modèle de langage, cliquez sur **...** , à droite du modèle, puis sélectionnez **Renommer**. 

Entrez le nouveau nom, puis appuyez sur Entrée.

### <a name="add-files"></a>Ajouter des fichiers

Pour ajouter un fichier texte, cliquez sur **Ajouter un fichier**. Votre Explorateur de fichiers s’ouvre.

Accédez et sélectionnez le fichier texte de votre choix. Vous pouvez ajouter plusieurs fichiers texte à un modèle de langage.

Vous pouvez également ajouter un fichier texte en cliquant sur le bouton **...** situé à droite du modèle de langage et en sélectionnant **Ajouter un fichier**.

### <a name="delete-files"></a>Supprimer des fichiers

Pour supprimer un fichier du modèle de langage, cliquez sur le bouton **...** , à droite du fichier texte, et sélectionnez **Supprimer**. Une nouvelle fenêtre apparaît pour vous indiquer que cette suppression est définitive. Cliquez sur l'option **Supprimer** dans la nouvelle fenêtre.

Le fichier est complètement supprimé du modèle de langage.

## <a name="delete-a-language-model"></a>Supprimer un modèle de langage

Pour supprimer un modèle de langage de votre compte, cliquez sur le bouton **...** , à droite du modèle de langage, et sélectionnez **Supprimer**.

Une nouvelle fenêtre apparaît pour vous indiquer que cette suppression est définitive. Cliquez sur l'option **Supprimer** dans la nouvelle fenêtre.

Le modèle de langage est complètement supprimé de votre compte. Toute vidéo qui utilisait le modèle de langage supprimé conservera le même index jusqu'à la réindexation de la vidéo. Si vous réindexez la vidéo, vous pouvez lui assigner un nouveau modèle de langage. Sinon, Video Indexer utilisera son modèle par défaut pour réindexer la vidéo. 

## <a name="customize-language-models-by-correcting-transcripts"></a>Personnaliser des modèles de langage en corrigeant les transcriptions

Video Indexer prend en charge la personnalisation automatique de modèles de langage en fonction des corrections apportées par les utilisateurs aux transcriptions de leurs vidéos.

1. Pour apporter des corrections à une transcription, ouvrez la vidéo que vous voulez modifier à partir des vidéos du compte. Sélectionnez l’onglet **Chronologie**.

    ![Personnaliser un modèle de langage](./media/customize-language-model/timeline.png)
1. Cliquez sur l'icône en forme de crayon pour modifier votre transcription. 

    ![Personnaliser un modèle de langage](./media/customize-language-model/edits.png)

    Video Indexer capture toutes les lignes que vous corrigez dans la transcription de votre vidéo et les ajoute automatiquement à un fichier texte appelé « From transcript edits ». Ces modifications servent à former à nouveau le modèle de langage spécifique utilisé pour indexer cette vidéo. 
    
    Si vous n'avez pas spécifié de modèle de langage lors de l'indexation de cette vidéo, toutes les modifications apportées à cette vidéo seront stockées dans un modèle de langage par défaut appelé Account adaptations (Adaptations de compte) dans le langage détecté dans la vidéo. 
    
    Si plusieurs modifications ont été apportées à la même ligne, seule la dernière version de la ligne corrigée sera utilisée pour mettre à jour le modèle de langage.  
    
    > [!NOTE]
    > Seules les corrections textuelles sont utilisées pour la personnalisation. Cela signifie que les corrections qui n'impliquent pas de mots réels (par exemple, des signes de ponctuation ou des espaces) ne sont pas incluses. 
    
1. Vous verrez les corrections de la transcription apparaître dans l'onglet Langage de la page de personnalisation du modèle de contenu.

    ![Personnaliser un modèle de langage](./media/customize-language-model/customize.png)

   Pour consulter le fichier « From transcript edits » pour chacun de vos modèles de langage, cliquez dessus pour l'ouvrir. 

    ![From transcript edits](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Étapes suivantes

[Personnaliser le modèle de langue à l’aide des API](customize-language-model-with-api.md)
