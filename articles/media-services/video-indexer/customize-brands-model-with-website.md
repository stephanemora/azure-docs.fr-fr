---
title: Utiliser le site web Video Indexer pour personnaliser des modèles de marques - Azure
titleSuffix: Azure Media Services
description: Cet article explique comment personnaliser un modèle de marques avec le site web Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 956ca7af055768398392045ecf9b383d2eb1060f
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513896"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Personnaliser un modèle de marques avec le site web Video Indexer

Video Indexer prend en charge la détection de marques dans les messages vocaux et visuels lors de l’indexation et de la réindexation de contenu vidéo et audio. La fonctionnalité de détection de marques identifie les noms de produits, services et entreprises suggérés par la base de données de marques Bing. Par exemple, si le nom de Microsoft est mentionné dans du contenu vidéo ou audio, ou s’il apparaît sous forme de texte visuel dans une vidéo, Video Indexer le détecte et l’interprète comme un nom de marque dans le contenu. Un modèle de marques personnalisé vous permet de choisir si votre instance de Video Indexer doit détecter ou non les marques répertoriées dans la base de données de marques Bing, ne pas détecter certaines marques (création d'une liste rouge de marques) et inclure d'autres marques en plus de celles répertoriées dans la base de données de marques Bing (création d'une liste verte de marques).

Pour un aperçu détaillé, consultez [Vue d’ensemble](customize-brands-model-overview.md).

Vous pouvez utiliser le site web Video Indexer pour créer, utiliser et modifier des modèles personnalisés de marques détectées dans une vidéo, comme décrit dans cette rubrique. Vous pouvez également utiliser l'API, comme décrit dans [Personnaliser le modèle de marques à l'aide des API](customize-brands-model-with-api.md).

## <a name="edit-the-settings-of-the-brands-model"></a>Modifier les paramètres du modèle de marques  

Vous pouvez déterminer si vous souhaitez ou non que les marques de la base de données de marques Bing soient détectées. Pour ce faire, vous devez modifier les paramètres de votre modèle de marques.

1. Accédez au site web [Video Indexer](https://www.videoindexer.ai/) et connectez-vous.
2. Pour personnaliser un modèle à partir de votre compte, cliquez sur le bouton **Personnalisation du modèle de contenu** en haut à droite de la page.
 
   ![Personnaliser un modèle de contenu](./media/content-model-customization/content-model-customization.png) 
3. Pour modifier des marques, sélectionnez l'onglet **Marques**.

    ![Personnaliser un modèle de marques](./media/customize-brand-model/customize-brand-model.png)
4. Cochez la case **Afficher les marques suggérées par Bing** si vous souhaitez que Video Indexer inclue les marques suggérées par Bing. Ne la cochez pas si vous ne souhaitez pas que Video Indexer détecte les marques suggérées par Bing dans votre contenu. 

## <a name="include-brands-in-the-model"></a>Inclure des marques dans le modèle

La section **Inclure des marques** représente les marques personnalisées que vous souhaitez que Video Indexer détecte, même si elles ne sont pas suggérées par Bing.  

### <a name="add-a-brand"></a>Ajouter une marque

1. Cliquez sur « + Ajouter une marque ».

    ![Personnaliser un modèle de marques](./media/customize-brand-model/add-brand.png)

    Indiquez un nom (obligatoire), une catégorie (facultatif), une description (facultatif) et une URL de référence (facultatif).
    Le champ Catégorie a pour but de vous aider à étiqueter vos marques. Ce champ apparaît sous la forme d'*étiquettes* de la marque lors de l'utilisation des API Video Indexer. Par exemple, la marque « Azure » peut être étiquetée ou classée dans la catégorie « Cloud ».

    Le champ URL de référence peut correspondre à n'importe quel site web de référence de la marque, comme un lien vers sa page Wikipédia.
2. Cliquez sur « Ajouter une marque ». La marque est alors ajoutée à la liste **Inclure des marques**.

### <a name="edit-a-brand"></a>Modifier une marque

1. Cliquez sur l'icône Crayon en regard de la marque que vous souhaitez modifier.

    Vous pouvez mettre à jour la catégorie, la description ou l'URL de référence d'une marque. Vous ne pouvez pas changer le nom d'une marque car les noms de marques sont uniques. Pour changer le nom d'une marque, vous devez supprimer celle-ci (voir la section suivante) puis en créer une nouvelle et lui attribuer le nouveau nom.
2. Cliquez sur le bouton **Mettre à jour** pour mettre la marque à jour avec les nouvelles informations.

### <a name="delete-a-brand"></a>Supprimer une marque

1. Cliquez sur l'icône Corbeille en regard de la marque que vous souhaitez supprimer.
2. Cliquez sur « Supprimer » pour effacer la marque de votre liste *Inclure des marques*.

## <a name="exclude-brands-from-the-model"></a>Exclure des marques du modèle

La section **Exclure des marques** représente les marques que vous souhaitez que Video Indexer ignore.

### <a name="add-a-brand"></a>Ajouter une marque

1. Cliquez sur « + Ajouter une marque ».

    Indiquez un nom (obligatoire) et une catégorie (facultatif).
2. Cliquez sur « Ajouter une marque ». La marque est alors ajoutée à la liste *Exclure des marques*.

### <a name="edit-a-brand"></a>Modifier une marque

1. Cliquez sur l'icône Crayon en regard de la marque que vous souhaitez modifier.

    Seule la catégorie d'une marque peut être mise à jour. Vous ne pouvez pas changer le nom d'une marque car les noms de marques sont uniques. Pour changer le nom d'une marque, vous devez supprimer celle-ci (voir la section suivante) puis en créer une nouvelle et lui attribuer le nouveau nom.
2. Cliquez sur le bouton **Mettre à jour** pour mettre la marque à jour avec les nouvelles informations.

### <a name="delete-a-brand"></a>Supprimer une marque

1. Cliquez sur l'icône Corbeille en regard de la marque que vous souhaitez supprimer.
2. Cliquez sur « Supprimer » pour effacer la marque de votre liste *Exclure des marques*.

## <a name="next-steps"></a>Étapes suivantes

[Personnaliser le modèle de marques à l’aide des API](customize-brands-model-with-api.md)
