---
title: Personnaliser un modèle de marques avec le site web Video Indexer
titleSuffix: Azure Media Services
description: Découvrez comment personnaliser un modèle de marques avec le site web Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/15/2019
ms.author: kumud
ms.openlocfilehash: a2de9dbb479f43d6b646cd9f6cf604d6a08c8b6a
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586100"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Personnaliser un modèle de marques avec le site web Video Indexer

Video Indexer prend en charge la détection de marques dans les messages vocaux et visuels lors de l’indexation et de la réindexation de contenu vidéo et audio. La fonctionnalité de détection de marques identifie les noms de produits, services et entreprises suggérés par la base de données de marques Bing. Par exemple, si le nom de Microsoft est mentionné dans du contenu vidéo ou audio, ou s’il apparaît sous forme de texte visuel dans une vidéo, Video Indexer le détecte et l’interprète comme un nom de marque dans le contenu.

Un modèle de marques personnalisé vous permet d’effectuer les opérations suivantes :

- Sélectionnez cette option si vous souhaitez que Video Indexer détecte des marques dans la base de données des marques Bing.
- Sélectionnez cette option si vous souhaitez que Video Indexer empêche certaines marques d’être détectées (en créant essentiellement une liste rouge de marques).
- Sélectionnez cette option si vous souhaitez que Video Indexer inclue les marques qui doivent faire partie de votre modèle qui ne se trouvent pas nécessairement dans la base de données des marques de Bing (en créant essentiellement une liste verte de marques).

Pour un aperçu détaillé, consultez cette [Vue d’ensemble](customize-brands-model-overview.md).

Vous pouvez utiliser le site web Video Indexer pour créer, utiliser et modifier des modèles personnalisés de marques détectées dans une vidéo, comme décrit dans cette rubrique. Vous pouvez également utiliser l'API, comme décrit dans [Personnaliser le modèle de marques à l'aide des API](customize-brands-model-with-api.md).

> [!NOTE]
> Si votre vidéo a été indexée avant l’ajout d’une marque, vous devez la réindexer. Vous trouverez l’élément **Réindexer** dans le menu déroulant associé à la vidéo. Sélectionnez **Options avancées** -> **Catégories de marques** et cochez **Toutes les marques**.

## <a name="edit-brands-model-settings"></a>Modifier les paramètres du modèle de marques

Vous pouvez déterminer si vous souhaitez ou non que les marques de la base de données de marques Bing soient détectées. Pour définir cette option, vous devez modifier les paramètres de votre modèle de marques. Procédez comme suit :

1. Accédez au site web [Video Indexer](https://www.videoindexer.ai/) et connectez-vous.
1. Pour personnaliser un modèle dans votre compte, sélectionnez le bouton **Personnalisation du modèle de contenu** à gauche de la page.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="Personnaliser le modèle de contenu dans Video Indexer":::
1. Pour modifier des marques, sélectionnez l'onglet **Marques**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-brand-model/customize-brand-model.png" alt-text="Capture d’écran montrant l’onglet Marques de la boîte de dialogue Personnalisation du modèle de contenu":::
1. Activez la case à cocher **Afficher les marques suggérées par Bing** si vous souhaitez que Video Indexer détecte les marques suggérées par Bing : laissez l’option désactivée dans le cas contraire.

## <a name="include-brands-in-the-model"></a>Inclure des marques dans le modèle

La section **Inclure des marques** représente les marques personnalisées que vous souhaitez que Video Indexer détecte, même si elles ne sont pas suggérées par Bing.  

### <a name="add-a-brand-to-include-list"></a>Ajouter une personnalisation à la liste d’inclusion

1. Sélectionnez **+ Créer une marque**.

    Indiquez un nom (obligatoire), une catégorie (facultatif), une description (facultatif) et une URL de référence (facultatif).
    Le champ Catégorie a pour but de vous aider à étiqueter vos marques. Ce champ apparaît sous la forme d'*étiquettes* de la marque lors de l'utilisation des API Video Indexer. Par exemple, la marque « Azure » peut être étiquetée ou classée dans la catégorie « Cloud ».

    Le champ URL de référence peut correspondre à n'importe quel site web de référence de la marque (comme un lien vers sa page Wikipédia).

2. Sélectionnez **Enregistrer** et vous verrez que la marque a été ajoutée à la liste **Inclure des marques**.

### <a name="edit-a-brand-on-the-include-list"></a>Modifier une marque dans la liste d’inclusion

1. Sélectionnez l'icône Crayon en regard de la marque que vous souhaitez modifier.

    Vous pouvez mettre à jour la catégorie, la description ou l'URL de référence d'une marque. Vous ne pouvez pas changer le nom d'une marque car les noms de marques sont uniques. Pour changer le nom d'une marque, vous devez supprimer celle-ci (voir la section suivante) puis en créer une nouvelle et lui attribuer le nouveau nom.

2. Sélectionnez le bouton **Mettre à jour** pour mettre la marque à jour avec les nouvelles informations.

### <a name="delete-a-brand-on-the-include-list"></a>Supprimer une marque de la liste d’inclusion

1. Sélectionnez l'icône Corbeille en regard de la marque que vous souhaitez supprimer.
2. Sélectionnez **Supprimer** pour effacer la marque de votre liste *Inclure des marques*.

## <a name="exclude-brands-from-the-model"></a>Exclure des marques du modèle

La section **Exclure des marques** représente les marques que vous souhaitez que Video Indexer ignore.

### <a name="add-a-brand-to-exclude-list"></a>Ajouter une marque à la liste d’exclusion

1. Sélectionnez **+ Créer une marque**.

    Indiquez un nom (obligatoire) et une catégorie (facultatif).

2. Sélectionnez **Enregistrer** et vous verrez que la marque a été ajoutée à la liste *Exclure des marques*.

### <a name="edit-a-brand-on-the-exclude-list"></a>Modifier une marque dans la liste d’exclusion

1. Sélectionnez l'icône Crayon en regard de la marque que vous souhaitez modifier.

    Seule la catégorie d'une marque peut être mise à jour. Vous ne pouvez pas changer le nom d'une marque car les noms de marques sont uniques. Pour changer le nom d'une marque, vous devez supprimer celle-ci (voir la section suivante) puis en créer une nouvelle et lui attribuer le nouveau nom.

2. Sélectionnez le bouton **Mettre à jour** pour mettre la marque à jour avec les nouvelles informations.

### <a name="delete-a-brand-on-the-exclude-list"></a>Supprimer une marque de la liste d’exclusion

1. Sélectionnez l'icône Corbeille en regard de la marque que vous souhaitez supprimer.
2. Sélectionnez **Supprimer** pour effacer la marque de votre liste *Exclure des marques*.

## <a name="next-steps"></a>Étapes suivantes

[Personnaliser le modèle de marques à l’aide des API](customize-brands-model-with-api.md)
