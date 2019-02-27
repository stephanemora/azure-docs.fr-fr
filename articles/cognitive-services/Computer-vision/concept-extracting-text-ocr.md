---
title: Extraire du texte à l'aide de la reconnaissance optique de caractères (OCR) - Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Découvrez les concepts liés à l’extraction de texte avec l’OCR (reconnaissance optique de caractères) dans l’API Vision par ordinateur.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: deb73eb9fdd6879a5fbe1fed820bf92b2d627b65
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310438"
---
# <a name="extract-text-with-optical-character-recognition"></a>Extraire du texte à l'aide de la reconnaissance optique de caractères (OCR)

La fonctionnalité de reconnaissance optique de caractères (OCR) de Vision par ordinateur détecte le contenu textuel d'une image et convertit le texte identifié en flux de caractères lisibles par ordinateur. Vous pouvez utiliser le résultat à diverses fins (recherche, dossiers médicaux, sécurité, opérations bancaires, etc.). 

L'OCR prend en charge 25 langues : arabe, chinois simplifié, chinois traditionnel, tchèque, danois, néerlandais, anglais, finnois, français, allemand, grec, hongrois, italien, japonais, coréen, norvégien, polonais, portugais, roumain, russe, serbe (cyrillique et latin), slovaque, espagnol, suédois et turc. L'OCR identifie automatiquement la langue du texte détecté.

Si nécessaire, l'OCR corrige la rotation du texte reconnu en renvoyant le décalage de rotation en degrés autour de l'axe horizontal de l'image. L'OCR fournit également les coordonnées du cadre de chaque mot, comme illustré ci-dessous.

![Diagramme illustrant une image en rotation et le texte en cours de lecture et délimité](./Images/vision-overview-ocr.png)

## <a name="image-requirements"></a>Exigences des images

Vision par ordinateur peut utiliser l’OCR pour extraire le texte des images qui remplissent les conditions suivantes :

* L’image doit être au format JPEG, PNG, GIF ou BMP
* La taille de l’image d’entrée doit être comprise entre 50 x 50 et 4 200 x 4 200 pixels
* Le texte de l'image peut être pivoté par multiples de 90 degrés, plus un faible angle de 40 degrés maximum.

## <a name="improving-ocr-accuracy"></a>Amélioration de la précision de l’OCR

La précision de la reconnaissance du texte dépend de la qualité de l’image. Une lecture inexacte peut être due à ce qui suit :

* Images floues.
* Texte manuscrit ou lié.
* Styles de police artistiques.
* Taille de police trop petite.
* Arrière-plans complexes, ombres ou reflets sur le texte ou distorsion de perspective.
* Lettres majuscules trop grandes ou manquantes au début des mots.
* Texte barré, exposant ou indice.

### <a name="ocr-limitations"></a>Limitations de l’OCR

Sur les images où le texte est dominant, de faux positifs peuvent provenir de mots partiellement reconnus. Sur certaines images, en particulier les photos sans texte, la précision peut considérablement varier selon le type d'image.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez la [documentation de référence sur l'OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc).
