---
title: Extraction de texte avec l’OCR - Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Découvrez les concepts liés à l’extraction de texte avec l’OCR (reconnaissance optique de caractères) dans l’API Vision par ordinateur.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 52b6265722d5cfbf8baf54e1785ace627aa0892f
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341665"
---
# <a name="extracting-text-with-ocr"></a>Extraction de texte avec reconnaissance optique de caractères

L’OCR fournie dans Vision par ordinateur est une technologie de reconnaissance optique de caractères qui détecte le contenu textuel dans une image et extrait le texte identifié sous forme de flux de caractères lisibles par ordinateur. Vous pouvez utiliser le résultat pour effectuer des recherches ou l’appliquer à de nombreuses autres applications, comme les dossiers médicaux, la sécurité et le secteur bancaire. Cette technologie détecte automatiquement la langue. Elle permet de gagner du temps, et elle est pratique car elle permet de prendre des photos du texte au lieu de le retranscrire.

L’OCR prend en charge 25 langues. Ces langues sont les suivantes : arabe, chinois simplifié, chinois traditionnel, tchèque, danois, néerlandais, anglais, finnois, français, allemand, grec, hongrois, italien, japonais, coréen, norvégien, polonais, portugais, roumain, russe, serbe (cyrillique et latin), slovaque, espagnol, suédois et turc.

Si nécessaire, l’OCR corrige la rotation du texte reconnu, en degrés, autour de l’axe horizontal de l’image. L’OCR fournit les coordonnées du cadre de chaque mot, comme illustré ci-dessous.

![Vue d’ensemble de l’OCR](./Images/vision-overview-ocr.png)

## <a name="ocr-requirements"></a>Configuration requise pour l’OCR

Vision par ordinateur peut utiliser l’OCR pour extraire le texte des images qui remplissent les conditions suivantes :

* L’image doit être au format JPEG, PNG, GIF ou BMP
* La taille de l’image d’entrée doit être comprise entre 50 x 50 et 4 200 x 4 200 pixels


L’image d’entrée peut être pivotée par multiples de 90 degrés, plus un faible angle de 40 degrés maximum.

## <a name="improving-ocr-accuracy"></a>Amélioration de la précision de l’OCR

La précision de la reconnaissance du texte dépend de la qualité de l’image. Une lecture inexacte peut être due aux situations suivantes :

* Images floues.
* Texte manuscrit ou lié.
* Styles de police artistiques.
* Taille de police trop petite.
* Arrière-plans complexes, ombres ou reflets sur le texte ou distorsion de perspective.
* Lettres majuscules trop grandes ou manquantes au début des mots.
* Texte barré, exposant ou indice.

### <a name="ocr-limitations"></a>Limitations de l’OCR

Sur les photographies où le texte est dominant, de faux positifs peuvent provenir de mots partiellement reconnus. Sur certaines photographies, en particulier les photos sans texte, la précision peut beaucoup varier selon le type d’image.

## <a name="next-steps"></a>Étapes suivantes

Découvrez les concepts de la [reconnaissance du texte imprimé et manuscrit](concept-recognizing-text.md).
