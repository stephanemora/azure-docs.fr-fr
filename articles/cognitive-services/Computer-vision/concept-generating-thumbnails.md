---
title: Génération de miniatures - Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Concepts liés à la génération de miniatures d’images à l’aide de l’API Vision par ordinateur.
services: cognitive-services
author: deken
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: ff16fa4d30fb60e6b1376027a40242af23be04d1
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985343"
---
# <a name="generating-thumbnails"></a>Génération de miniatures

Une miniature est une petite représentation d’une image en taille réelle. Divers appareils tels que les téléphones, les tablettes et les PC nécessitent l’utilisation de mises en page et de tailles de miniatures différentes pour leur expérience utilisateur. La fonctionnalité de rognage intelligent de l’API Vision par ordinateur permet de résoudre le problème.

Après avoir chargé une image, le service Vision par ordinateur génère une miniature de haute qualité, puis analyse les objets contenus dans l’image pour identifier la *zone d’intérêt*. Il peut éventuellement rogner l’image pour l’adapter aux exigences de la zone d’intérêt. La miniature générée peut être présentée à l’aide de proportions différentes de celles de l’image d’origine pour répondre à vos besoins.

L’algorithme des miniatures fonctionne de la façon suivante :

1. Il supprime les éléments parasites de l’image et identifie l’objet principal, la « zone d’intérêt ».
2. Il rogne l’image en fonction de la zone d’intérêt identifiée.
3. Il modifie les proportions afin de s’adapter aux dimensions cibles de la miniature.

La miniature générée varie considérablement selon les paramètres de hauteur, de largeur et de rognage intelligent que vous définissez, comme illustré dans l’image suivante.

![Miniatures](./Images/thumbnail-demo.png)

## <a name="thumbnail-generation-examples"></a>Exemples de génération de miniatures

Le tableau suivant montre les miniatures standard générées par l’API Vision par ordinateur pour les exemples d’images. Les miniatures ont été générées pour une hauteur et une largeur cibles de 50 pixels, avec l’option de rognage intelligent activée.

| Image | Miniature |
|-------|-----------|
|![Montagne extérieur](./Images/mountain_vista.png) | ![Miniature Montagne extérieur](./Images/mountain_vista_thumbnail.png) |
|![Analyse Vision fleur](./Images/flower.png) | ![Miniature Analyse Vision fleur](./Images/flower_thumbnail.png) |
|![Toit femme](./Images/woman_roof.png) | ![Miniature Toit femme](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>Étapes suivantes

Découvrez les concepts de [balisage d’images](concept-tagging-images.md) et de [catégorisation des images](concept-categorizing-images.md).