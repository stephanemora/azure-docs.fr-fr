---
title: Personnaliser un modèle linguistique avec le site web Azure Video Analyzer for Media (anciennement Video Indexer)
titleSuffix: Azure Media Services
description: Découvrez comment personnaliser un modèle linguistique avec le site web Azure Video Analyzer for Media (anciennement Video Indexer).
services: media-services
author: anikaz
manager: johndeu
ms.topic: article
ms.date: 08/10/2020
ms.author: kumud
ms.openlocfilehash: 4f1d5e708755828fb2193ffca6275684418110d5
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110384850"
---
# <a name="customize-a-language-model-with-the-video-analyzer-for-media-website"></a>Personnaliser un modèle linguistique à l’aide du site web Video Analyzer for Media

Azure Video Analyzer for Media (anciennement Video Indexer) vous permet de créer des modèles linguistiques personnalisés pour la reconnaissance vocale en chargeant un texte d’adaptation, à savoir un texte extrait du domaine de vocabulaire auquel vous souhaitez que le moteur s’adapte. Une fois votre modèle entraîné, les nouveaux mots qui apparaissent dans le texte d’adaptation seront reconnus.

Pour obtenir une présentation détaillée et les meilleures pratiques pour les modèles linguistiques personnalisés, consultez [Personnaliser un modèle linguistique avec Video Analyzer for Media](customize-language-model-overview.md).

Vous pouvez utiliser le site web Video Analyzer for Media pour créer et modifier des modèles linguistiques personnalisés dans votre compte, comme décrit dans cette rubrique. Vous pouvez également utiliser l'API, comme décrit dans [Personnaliser le modèle de langage à l'aide des API](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Créer un modèle de langage

1. Accédez au site web [Video Analyzer for Media](https://www.videoindexer.ai/) et connectez-vous.
1. Pour personnaliser un modèle dans votre compte, sélectionnez le bouton **Personnalisation du modèle de contenu** à gauche de la page.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-language-model/model-customization.png" alt-text="Personnaliser un modèle de contenu dans Video Analyzer for Media":::
1. Sélectionnez l'onglet **Langage**.

    La liste des langues prises en charge s'affiche.
1. Sous la langue de votre choix, sélectionnez **Ajouter un modèle**.
1. Entrez le nom du modèle de langage, puis appuyez sur Entrée.

    Cette étape crée le modèle et offre la possibilité d’y charger des fichiers texte.
1. Pour ajouter un fichier texte, sélectionnez **Ajouter un fichier**. Votre explorateur de fichiers s’ouvre.
1. Accédez et sélectionnez le fichier texte de votre choix. Vous pouvez ajouter plusieurs fichiers texte à un modèle de langage.

    Vous pouvez également ajouter un fichier texte en sélectionnant le bouton  **...** situé à droite du modèle de langage, puis en sélectionnant **Ajouter un fichier**.
1. Une fois les fichiers texte chargés, sélectionnez l’option verte **Entraîner**.

Le processus d'apprentissage peut prendre plusieurs minutes. Une fois l'apprentissage terminé, le texte **Entraîné** apparaît en regard du modèle. Vous pouvez afficher un aperçu, télécharger et supprimer le fichier à partir du modèle.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/customize-language-model/customize-language-model.png" alt-text="Formation du modèle":::

### <a name="using-a-language-model-on-a-new-video"></a>Utiliser un modèle de langage sur une nouvelle vidéo

Pour utiliser votre modèle de langage sur une nouvelle vidéo, effectuez l’une des actions suivantes :

* Sélectionnez le bouton **Charger** en haut de la page.

    ![Bouton Charger de Video Analyzer for Media](./media/customize-language-model/upload.png)
* Déposez votre fichier audio ou vidéo, ou accédez à votre fichier.

Vous avez la possibilité de sélectionner la **langue source de la vidéo**. Sélectionnez la liste déroulante, puis un modèle de langage que vous avez créé. Le langage de votre modèle de langage ainsi que le nom que vous lui avez donné y sont indiqués entre parenthèses. Par exemple :

![Choisir la langue de la source vidéo - Réindexer une vidéo avec Video Analyzer for Media](./media/customize-language-model/reindex.png)

Sélectionnez l’option **Charger** située en bas de la page. Votre nouvelle vidéo est alors indexée à l’aide de votre modèle de langage.

### <a name="using-a-language-model-to-reindex"></a>Utiliser un modèle de langage pour réindexer

Pour utiliser votre modèle de langage afin de réindexer une vidéo dans votre collection, procédez comme suit :

1. Connectez-vous à la page d’accueil [Azure Video Analyzer for Media](https://www.videoindexer.ai/).
1. Cliquez sur le bouton **...** de la vidéo et sélectionnez **Réindexer**.
1. Vous avez la possibilité de sélectionner la **langue source de la vidéo** pour réindexer votre vidéo. Sélectionnez la liste déroulante, puis un modèle de langage que vous avez créé. Le langage de votre modèle de langage ainsi que le nom que vous lui avez donné y sont indiqués entre parenthèses.
1. Sélectionnez le bouton **Réindexer**, après quoi votre vidéo sera réindexée à l’aide de votre modèle de langage.

## <a name="edit-a-language-model"></a>Modifier un modèle de langage

Vous pouvez modifier un modèle de langage en modifiant son nom, en ajoutant ou en supprimant des fichiers.

Si vous ajoutez ou supprimez des fichiers dans modèle de langage, vous devez réentraîner ce dernier en sélectionnant l’option verte **Entraîner**.

### <a name="rename-the-language-model"></a>Renommer le modèle de langage

Pour changer le nom du modèle de langage, sélectionnez le bouton de sélection ( **...** ) situé à droite du modèle, puis sélectionnez **Renommer**.

Entrez le nouveau nom, puis appuyez sur Entrée.

### <a name="add-files"></a>Ajouter des fichiers

Pour ajouter un fichier texte, sélectionnez **Ajouter un fichier**. Votre explorateur de fichiers s’ouvre.

Accédez et sélectionnez le fichier texte de votre choix. Vous pouvez ajouter plusieurs fichiers texte à un modèle de langage.

Vous pouvez également ajouter un fichier texte en sélectionnant le bouton de sélection ( **...** ) situé à droite du modèle de langage, puis en sélectionnant **Ajouter un fichier**.

### <a name="delete-files"></a>Supprimer des fichiers

Pour supprimer un fichier du modèle de langage, sélectionnez le bouton de sélection ( **...** ) situé à droite du fichier texte, puis sélectionnez **Supprimer**. Une nouvelle fenêtre s’affiche, vous indiquant que cette suppression ne peut pas être annulée. Sélectionnez l’option **Supprimer** dans la nouvelle fenêtre.

Le fichier est complètement supprimé du modèle de langage.

## <a name="delete-a-language-model"></a>Supprimer un modèle de langage

Pour supprimer un modèle de langage de votre compte, sélectionnez le bouton de sélection ( **...** ) situé à droite du modèle, puis sélectionnez **Supprimer**.

Une nouvelle fenêtre s’affiche, vous indiquant que cette suppression ne peut pas être annulée. Sélectionnez l’option **Supprimer** dans la nouvelle fenêtre.

Le modèle de langage est complètement supprimé de votre compte. Toute vidéo qui utilisait le modèle de langage supprimé conservera le même index jusqu’à ce que vous la réindexiez. Si vous réindexez la vidéo, vous pouvez lui assigner un nouveau modèle de langage. Sinon, Video Analyzer for Media utilisera son modèle par défaut pour réindexer la vidéo.

## <a name="customize-language-models-by-correcting-transcripts"></a>Personnaliser des modèles de langage en corrigeant les transcriptions

Video Analyzer for Media prend en charge la personnalisation automatique de modèles linguistiques en fonction des corrections apportées par les utilisateurs aux transcriptions de leurs vidéos.

1. Pour apporter des corrections à une transcription, ouvrez la vidéo que vous voulez modifier à partir des vidéos du compte. Sélectionnez l’onglet **Chronologie**.

    ![Personnaliser un Video Analyzer for Media - Video Analyzer for Media](./media/customize-language-model/timeline.png)

1. Sélectionnez l’icône de crayon pour modifier votre transcription.

    ![Personnaliser la transcription d’un modèle linguistique - Video Analyzer for Media](./media/customize-language-model/edits.png)

    Video Analyzer for Media capture toutes les lignes que vous corrigez dans la transcription de votre vidéo et les ajoute automatiquement à un fichier texte appelé « From transcript edits ». Ces modifications servent à réentraîner le modèle de langage spécifique utilisé pour indexer cette vidéo. 
    
    Les modifications apportées à la chronologie du [widget](video-indexer-embed-widgets.md) sont également incluses.
    
    Si vous n’avez pas spécifié de modèle de langage lors de l’indexation de cette vidéo, toutes les modifications apportées à cette vidéo seront stockées dans un modèle de langage par défaut appelé « Account adaptations » (Adaptations de compte) dans le langage détecté dans la vidéo.
    
    Si plusieurs modifications ont été apportées à la même ligne, seule la dernière version de la ligne corrigée sera utilisée pour mettre à jour le modèle de langage.  
    
    > [!NOTE]
    > Seules les corrections textuelles sont utilisées pour la personnalisation. Les corrections qui n’impliquent pas de mots réels (par exemple, des signes de ponctuation ou des espaces) ne sont pas incluses.
    
1. Vous verrez les corrections de la transcription apparaître sous l’onglet Langue de la page Personnalisation du modèle de contenu.

   Pour consulter le fichier « À partir des modifications de transcription » pour chacun de vos modèles de langage, sélectionnez-le pour l’ouvrir.

    ![From transcript edits—Video Analyzer for Media](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Étapes suivantes

[Personnaliser le modèle de langue à l’aide des API](customize-language-model-with-api.md)
