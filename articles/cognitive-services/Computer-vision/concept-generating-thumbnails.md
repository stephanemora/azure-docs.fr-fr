---
title: Génération de miniatures - Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Concepts liés à la génération de miniatures d’images à l’aide de l’API Vision par ordinateur.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: pafarley
ms.openlocfilehash: 7d914f394ecfcf02ed26f41cd8fe2ef799cf6103
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52966736"
---
# <a name="generating-thumbnails"></a>Génération de miniatures

Une miniature est une représentation à taille réduite d’une image. Les miniatures sont utilisées pour représenter des images et d’autres données de manière plus économique et plus pratique au niveau disposition. L’API Vision par ordinateur utilise le rognage intelligent, en plus du redimensionnement d’image, afin de créer des miniatures intuitives pour une image donnée.

L’algorithme de génération de miniatures de Vision par ordinateur fonctionne comme suit :
1. Il supprime les éléments parasites de l’image et identifie la _zone d’intérêt_, autrement dit la zone de l’image où apparaissent les principaux objets.
1. Il rogne l’image en fonction de la _zone d’intérêt_ identifiée.
1. Il modifie les proportions afin de s’adapter aux dimensions cibles de la miniature.

## <a name="area-of-interest"></a>Zone d’intérêt

Quand vous chargez une image, l’API Vision par ordinateur l’analyse pour déterminer la *zone d’intérêt*. Elle peut ensuite utiliser cette région pour déterminer comment rogner l’image. L’opération de rognage, toutefois, correspond toujours aux proportions souhaitées, si elles sont spécifiées.

Vous pouvez également obtenir les coordonnées brutes du cadre englobant de cette même *zone d’intérêt* en appelant l’API **areaOfInterest** à la place. Vous pouvez ensuite utiliser ces informations pour modifier l’image d’origine comme vous le souhaitez.

## <a name="examples"></a>Exemples

La miniature générée varie considérablement selon les paramètres de hauteur, de largeur et de rognage intelligent que vous définissez, comme illustré dans l’image suivante.

![Miniatures](./Images/thumbnail-demo.png)

Le tableau suivant montre les miniatures standard générées par l’API Vision par ordinateur pour les exemples d’images. Les miniatures ont été générées pour une hauteur et une largeur cibles de 50 pixels, avec l’option de rognage intelligent activée.

| Image | Miniature |
|-------|-----------|
|![Montagne extérieur](./Images/mountain_vista.png) | ![Miniature Montagne extérieur](./Images/mountain_vista_thumbnail.png) |
|![Analyse Vision fleur](./Images/flower.png) | ![Miniature Analyse Vision fleur](./Images/flower_thumbnail.png) |
|![Toit femme](./Images/woman_roof.png) | ![Miniature Toit femme](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur le [balisage d’images](concept-tagging-images.md) et la [catégorisation des images](concept-categorizing-images.md).