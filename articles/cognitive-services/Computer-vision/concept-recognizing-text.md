---
title: 'Reconnaissance du texte imprimé et manuscrit : Vision par ordinateur'
titleSuffix: Azure Cognitive Services
description: Concepts liés à la reconnaissance du texte imprimé et manuscrit dans des images à l’aide de l’API Vision par ordinateur.
services: cognitive-services
author: deken
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 49cba0e9b6958beb07b6f074e6dc748679514525
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985309"
---
# <a name="recognizing-printed-and-handwritten-text"></a>Reconnaissance du texte imprimé et manuscrit

Le service Vision par ordinateur peut détecter et extraire un texte imprimé ou manuscrit à partir d’images d’objets divers avec différents arrière-plans et surfaces, tels que des reçus, des affiches, des cartes de visite, des courriers ou des tableaux blancs.

La reconnaissance de texte permet d’économiser du temps et d’éviter des efforts inutiles. Vous pouvez être plus productif en prenant une image du texte au lieu de le retranscrire. Elle permet de numériser des notes. Cette numérisation vous permet d’implémenter une recherche simple et rapide. Elle réduit également l’encombrement papier.

## <a name="text-recognition-requirements"></a>Critères requis pour la reconnaissance de texte

Vision par ordinateur peut reconnaître un texte imprimé et manuscrit dans des images qui répondent aux critères suivants :

- L’image doit être au format JPEG, PNG ou BMP.
- La taille de fichier de l’image doit être inférieure à 4 mégaoctets (Mo)
- Les dimensions de l’image doivent être comprises entre 40 x 40 et 3200 x 3200 pixels.

> [!NOTE]
> Cette technologie est actuellement en préversion et n’est disponible que pour du texte écrit en anglais.

## <a name="next-steps"></a>Étapes suivantes

Découvrez les concepts relatifs à [l’extraction d’un texte avec reconnaissance optique de caractères](concept-extracting-text-ocr.md).