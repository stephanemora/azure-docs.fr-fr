---
title: Reconnaître du texte imprimé et manuscrit - Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Concepts liés à la reconnaissance du texte imprimé et manuscrit dans des images à l’aide de l’API Vision par ordinateur.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 48ce15a11c3e3282535420f3e1bb1915276d70f5
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313175"
---
# <a name="recognizing-printed-and-handwritten-text"></a>Reconnaissance du texte imprimé et manuscrit

Le service Vision par ordinateur peut détecter et extraire un texte imprimé ou manuscrit à partir d’images d’objets divers avec différents arrière-plans et surfaces, tels que des reçus, des affiches, des cartes de visite, des courriers ou des tableaux blancs.

La fonctionnalité de reconnaissance de texte est très semblable à la fonctionnalité de [reconnaissance optique de caractères (OCR)](concept-extracting-text-ocr.md), mais contrairement à cette dernière, elle s'exécute de manière asynchrone et utilise des modèles de reconnaissance actualisés.

> [!NOTE]
> Cette technologie est actuellement en préversion et n’est disponible que pour du texte écrit en anglais.

## <a name="text-recognition-requirements"></a>Critères requis pour la reconnaissance de texte

Vision par ordinateur peut reconnaître un texte imprimé et manuscrit dans des images qui répondent aux critères suivants :

- L’image doit être au format JPEG, PNG ou BMP.
- La taille de fichier de l’image doit être inférieure à 4 mégaoctets (Mo)
- Les dimensions de l’image doivent être comprises entre 50 x 50 et 4200 x 4200 pixels.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez la [documentation de référence sur la reconnaissance de texte](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200).