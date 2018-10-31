---
title: 'Reconnaissance du texte imprimé et manuscrit : Vision par ordinateur'
titleSuffix: Azure Cognitive Services
description: Concepts liés à la reconnaissance du texte imprimé et manuscrit dans des images à l’aide de l’API Vision par ordinateur.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 6827bf5f983834dc5222a3f3028386f8bbcb253a
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49338098"
---
# <a name="recognizing-printed-and-handwritten-text"></a>Reconnaissance du texte imprimé et manuscrit

Le service Vision par ordinateur peut détecter et extraire un texte imprimé ou manuscrit à partir d’images d’objets divers avec différents arrière-plans et surfaces, tels que des reçus, des affiches, des cartes de visite, des courriers ou des tableaux blancs.

La reconnaissance de texte permet d’économiser du temps et d’éviter des efforts inutiles. Vous pouvez être plus productif en prenant une image du texte au lieu de le retranscrire. Elle permet de numériser des notes. Cette numérisation vous permet d’implémenter une recherche simple et rapide. Elle réduit également l’encombrement papier.

## <a name="text-recognition-requirements"></a>Critères requis pour la reconnaissance de texte

Vision par ordinateur peut reconnaître un texte imprimé et manuscrit dans des images qui répondent aux critères suivants :

- L’image doit être au format JPEG, PNG ou BMP.
- La taille de fichier de l’image doit être inférieure à 4 mégaoctets (Mo)
- Les dimensions de l’image doivent être comprises entre 50 x 50 et 4200 x 4200 pixels.

> [!NOTE]
> Cette technologie est actuellement en préversion et n’est disponible que pour du texte écrit en anglais.

## <a name="next-steps"></a>Étapes suivantes

Découvrez les concepts relatifs à [l’extraction d’un texte avec reconnaissance optique de caractères](concept-extracting-text-ocr.md).
