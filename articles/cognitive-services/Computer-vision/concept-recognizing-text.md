---
title: Reconnaître le texte imprimé/main, vision par ordinateur
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
ms.openlocfilehash: bcaa990cc2186a5f1eecdbbca91804c92370277c
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357181"
---
# <a name="recognize-printed-and-handwritten-text"></a>Reconnaître le texte imprimé et manuscrit

Vision par ordinateur fournit un certain nombre de services pour détecter et extraire le texte imprimé ou manuscrit qui s’affiche dans les images. Cela est utile dans divers scénarios tels que la prise de notes, de dossiers médicaux, de sécurité et d’opérations bancaires. La suivant trois sections détail trois différents reconnaissance de texte API, chacun étant optimisé pour différents cas d’usage.

## <a name="read-api"></a>API de lecture

L’API de lecture détecte le contenu de texte dans une image à l’aide de nos derniers modèles de reconnaissance et convertit le texte identifié dans un flux de caractères exploitable automatiquement. Il est optimisé pour les images comportant beaucoup de texte (tels que les documents ont été analysés numériquement) et pour les images avec beaucoup de bruit visuel. Elle permet de déterminer quel modèle de reconnaissance à utiliser pour chaque ligne de texte, en prenant en charge les images avec texte imprimé ou manuscrit. L’API de lecture exécute de façon asynchrone, car les documents plus volumineux peuvent prendre plusieurs minutes pour retourner un résultat.

L’opération de lecture conserve le regroupement d’origine de la ligne des mots reconnus dans sa sortie. Chaque ligne est fourni avec les coordonnées de la zone de délimitation, et chaque mot dans la ligne a également sa propre coordonnées. Si un mot a été reconnu avec confiance faible, cette information est également transmise. Consultez le [documents de référence des API de lecture](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) pour en savoir plus.

> [!NOTE]
> Cette fonctionnalité est uniquement disponible pour le texte en anglais.

### <a name="image-requirements"></a>Exigences des images

L’API de lecture fonctionne avec des images qui répondent aux exigences suivantes :

- L’image doit être présentée au format JPEG, PNG, BMP, PDF ou TIFF.
- Les dimensions de l’image doivent être comprise entre 50 x 50 et 4200 x 4200 pixels. Pages PDF doivent être 17 x 17 pouces ou plus petits.
- La taille du fichier de l’image doit être inférieure à 20 mégaoctets (Mo).

### <a name="limitations"></a>Limites

Si vous utilisez un abonnement gratuit de couche, l’API de lecture traitera uniquement les deux premières pages d’un document PDF ou TIFF. Avec un abonnement payant, il traite jusqu'à 200 pages. Notez également que l’API détecte un maximum de 300 lignes par page.

## <a name="ocr-optical-character-recognition-api"></a>OCR (reconnaissance optique de caractères) API

API de reconnaissance optique de caractères (OCR) de vision par ordinateur est similaire à l’API de lecture, mais il s’exécute de façon synchrone et n’est pas optimisée pour les documents volumineux. Il utilise un modèle de reconnaissance antérieur mais fonctionne avec plusieurs langues ; consultez [prise en charge linguistique](language-support.md#text-recognition) pour obtenir une liste complète des langues prises en charge.

Si nécessaire, l'OCR corrige la rotation du texte reconnu en renvoyant le décalage de rotation en degrés autour de l'axe horizontal de l'image. OCR fournit également les coordonnées de cadre de chaque mot, comme indiqué dans l’illustration suivante.

![Une image en rotation et son texte en cours et des lectures délimitée](./Images/vision-overview-ocr.png)

Consultez le [documents de référence de reconnaissance optique de caractères](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) pour en savoir plus.

### <a name="image-requirements"></a>Exigences des images

L’API de reconnaissance optique de caractères fonctionne sur les images qui répondent aux exigences suivantes :

* L’image doit être présentée au format JPEG, PNG, GIF ou BMP.
* La taille de l’image d’entrée doit être comprise entre 50 x 50 et 4200 x 4200 pixels.
* Le texte de l'image peut être pivoté par multiples de 90 degrés, plus un faible angle de 40 degrés maximum.

### <a name="limitations"></a>Limites

Sur les photographies où le texte est dominant, de faux positifs peuvent provenir de mots partiellement reconnus. Sur certains photographies, en particulier les photos sans texte, la précision peut varier selon le type d’image.

## <a name="recognize-text-api"></a>Reconnaître le texte API

> [!NOTE]
> L’API texte reconnaître est déconseillé en faveur de l’API de lecture. L’API de lecture a des fonctionnalités similaires et est mis à jour pour gérer les PDF, TIFF et les fichiers de plusieurs pages.

L’API texte reconnaître est similaire à la reconnaissance optique de caractères, mais il s’exécute de façon asynchrone et utilise des modèles de reconnaissance mis à jour. Consultez le [documents de référence des API de texte reconnaître](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) pour en savoir plus.

### <a name="image-requirements"></a>Exigences des images

L’API texte reconnaître fonctionne avec des images qui répondent aux exigences suivantes :

- L’image doit être présentée au format JPEG, PNG ou BMP.
- Les dimensions de l’image doivent être comprise entre 50 x 50 et 4200 x 4200 pixels.
- La taille du fichier de l’image doit être inférieure à 4 mégaoctets (Mo).

## <a name="limitations"></a>Limites

La précision des opérations de reconnaissance de texte dépend de la qualité des images. Les facteurs suivants peuvent provoquer une lecture inexacte :

* Images floues.
* Texte manuscrit ou lié.
* Styles de police artistiques.
* Taille de police trop petite.
* Arrière-plans complexes, ombres ou reflets sur le texte ou distorsion de perspective.
* Lettres majuscules au début de mots surdimensionnées ou manquants.
* Texte barré, exposant ou indice.

## <a name="next-steps"></a>Étapes suivantes

Suivez le [extraire le texte imprimé (OCR)](./quickstarts/csharp-print-text.md) Guide de démarrage rapide pour implémenter la reconnaissance du texte dans une simple C# application.
