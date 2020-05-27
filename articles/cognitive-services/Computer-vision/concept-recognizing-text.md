---
title: Reconnaissance du texte imprimé et manuscrit - Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Concepts liés à la reconnaissance du texte imprimé et manuscrit dans des images à l’aide de l’API Vision par ordinateur.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5d0a9771e5b999028996676ea72f8def3c5d63cf
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589854"
---
# <a name="recognize-printed-and-handwritten-text"></a>Reconnaître le texte imprimé et manuscrit

L’API Vision par ordinateur fournit un certain nombre de services pour détecter et extraire le texte imprimé ou manuscrit qui s’affiche dans les images. C’est utile dans divers scénarios tels que la prise de notes, les dossiers médicaux, la sécurité et les transactions bancaires. Les trois sections suivantes détaillent trois différentes API de reconnaissance de texte, chacune étant optimisée pour différents cas d’usage.

## <a name="read-api"></a>API Lire

L’API Lire détecte le contenu textuel d'une image à l’aide de nos derniers modèles de reconnaissance et convertit le texte identifié en flux de caractères lisibles par ordinateur. Elle est optimisée pour les images comportant beaucoup de texte (tels que les documents numérisés) et pour les images comportant beaucoup de bruit visuel. Elle détermine le modèle de reconnaissance à utiliser pour chaque ligne de texte et prend en charge les images contenant à la fois du texte imprimé et manuscrit. L’API Lire s’exécute de façon asynchrone, car les documents volumineux peuvent prendre plusieurs minutes pour retourner un résultat.

L’opération de lecture conserve les groupements de lignes d’origine des mots reconnus dans sa sortie. Chaque ligne est accompagnée des coordonnées de son cadre englobant, et chaque mot dans la ligne a également ses propres coordonnées. Si un mot a été reconnu avec une faible confiance, cette information est également transmise. Pour en savoir plus, consultez la [documentation de référence de l’API Lire v2.0](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) ou la [documentation de référence de l’API Lire v3.0](https://aka.ms/computer-vision-v3-ref).

L’API Lire peut reconnaître du texte en anglais, espagnol, allemand, français, italien, portugais et néerlandais.

### <a name="image-requirements"></a>Exigences des images

L’API Lire fonctionne avec des images qui répondent aux exigences suivantes :

- L’image doit être au format JPEG, PNG, BMP, PDF ou TIFF.
- Les dimensions de l’image doivent être comprises entre 50 x 50 et 10 000 x 10 000 pixels. Les pages PDF doivent être de 17 x 17 pouces ou moins.
- La taille de fichier de l’image doit être inférieure à 20 mégaoctets (Mo).

### <a name="limitations"></a>Limites

Si vous utilisez un abonnement de niveau gratuit, l’API Lire traite uniquement les deux premières pages d’un document PDF ou TIFF. Avec un abonnement payant, elle traite jusqu'à 200 pages. Notez également que l’API détecte un maximum de 300 lignes par page.

## <a name="ocr-optical-character-recognition-api"></a>API OCR (reconnaissance optique de caractères)

L’API de reconnaissance optique de caractères (OCR) de Vision par ordinateur est similaire à l’API Lire, mais elle s’exécute de façon synchrone et n’est pas optimisée pour les documents volumineux. Elle utilise un modèle de reconnaissance antérieur mais fonctionne avec plusieurs langues. Consultez [Prise en charge linguistique](language-support.md#text-recognition) pour obtenir la liste complète des langues prises en charge.

Si nécessaire, l'OCR corrige la rotation du texte reconnu en renvoyant le décalage de rotation en degrés autour de l'axe horizontal de l'image. L'API OCR fournit également les coordonnées du cadre de chaque mot, comme illustré ci-dessous.

![Rotation d’une image en vue de la lecture et de la délimitation de son texte](./Images/vision-overview-ocr.png)

Consultez les [documents de référence de l’API OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) pour en savoir plus.

### <a name="image-requirements"></a>Exigences des images

L’API OCR fonctionne sur des images qui répondent aux exigences suivantes :

* L’image doit être au format JPEG, PNG, GIF ou BMP.
* La taille de l’image d’entrée doit être comprise entre 50 x 50 et 4200 x 4200 pixels.
* Le texte de l'image peut être pivoté par multiples de 90 degrés, plus un faible angle de 40 degrés maximum.

### <a name="limitations"></a>Limites

Sur les photographies où le texte est dominant, de faux positifs peuvent provenir de mots partiellement reconnus. Sur certaines photographies, en particulier les photos sans texte, la précision peut varier selon le type d’image.

## <a name="recognize-text-api"></a>API Reconnaître le texte

> [!NOTE]
> L’API Reconnaître le texte est déconseillée en faveur de l’API Lire. L’API Lire a des fonctionnalités similaires et est mise à jour pour gérer les fichiers PDF, TIFF et de plusieurs pages.

L’API Reconnaître le texte est similaire à la reconnaissance optique de caractères, mais elle s’exécute de façon asynchrone et utilise des modèles de reconnaissance mis à jour. Consultez les [documents de référence de l’API Reconnaître le texte](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) pour en savoir plus.

### <a name="image-requirements"></a>Exigences des images

L’API Reconnaître le texte fonctionne avec des images qui répondent aux exigences suivantes :

- L’image doit être au format JPEG, PNG ou BMP.
- Les dimensions de l’image doivent être comprises entre 50 x 50 et 4200 x 4200 pixels.
- La taille de fichier de l’image doit être inférieure à 4 mégaoctets (Mo).

## <a name="limitations"></a>Limites

La précision des opérations de reconnaissance du texte dépend de la qualité des images. Les facteurs suivants peuvent provoquer une lecture inexacte :

* Images floues.
* Texte manuscrit ou lié.
* Styles de police artistiques.
* Taille de police trop petite.
* Arrière-plans complexes, ombres ou reflets sur le texte ou distorsion de perspective.
* Lettres majuscules trop grandes ou manquantes au début des mots.
* Texte barré, exposant ou indice.

## <a name="next-steps"></a>Étapes suivantes

Suivez le guide de démarrage rapide [Extraire du texte (Lire)](./QuickStarts/CSharp-hand-text.md) pour implémenter la reconnaissance de texte dans une application C# simple.
