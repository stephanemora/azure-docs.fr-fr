---
title: Prise en charge des langues – Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Cet article donne la liste des langages naturels pris en charge par les fonctionnalités Vision par ordinateur, OCR et Analyse d’image.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: b5c263506db68ea62b0d65b7b866cfab33a36236
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91976876"
---
# <a name="language-support-for-computer-vision"></a>Prise en charge des langues pour la Vision par ordinateur

Certaines des fonctionnalités de la Vision par ordinateur prennent en charge plusieurs langues ; celles qui ne sont pas mentionnées ici ne gèrent que l’anglais.

## <a name="optical-character-recognition-ocr"></a>Reconnaissance optique des caractères (OCR)

Les API OCR de Vision par ordinateur prennent en charge plusieurs langues. Elles n’exigent pas de spécifier un code de langue. Pour plus d’informations, consultez [Reconnaissance optique de caractères (OCR)](concept-recognizing-text.md).

|Langage| Code langue | API OCR | Lire v3.1 | Lire v3.1-preview.2 |
|:-----|:----:|:-----:|:---:|:---:|
|Arabe | `ar`|✔ | | |
|Chinois (simplifié) | `zh-Hans`|✔ | |✔ |
|Chinois (traditionnel) | `zh-Hant`|✔ | | |
|Tchèque | `cs` |✔ | | |
|Danois | `da` |✔ | | |
|Néerlandais | `nl` |✔ |✔ |✔ |
|Anglais | `en` |✔ |✔ |✔ |
|Finnois | `fi` |✔ | | |
|Français | `fr` |✔ |✔ |✔ |
|Allemand | `de` |✔ |✔ |✔ |
|Grec | `el` |✔ | | |
|Hongrois | `hu` |✔ | | |
|Italien | `it` |✔ |✔ |✔ |
|Japonais | `ja` |✔ | |✔ |
|Coréen | `ko` |✔ | | |
|Norvégien | `nb` |✔ | | |
|Polonais | `pl` |✔ | | |
|Portugais | `pt` |✔ |✔ |✔ |
|Roumain | `ro` |✔ | | |
|Russe | `ru` |✔ | | |
|Serbe (cyrillique) | `sr-Cyrl` |✔ | | |
|Serbe (latin) | `sr-Latn` |✔ | | |
|Slovaque | `sk` |✔ | | |
|Espagnol | `es` |✔ |✔ |✔ |
|Suédois | `sw` |✔ | | |
|Turc | `tr` |✔ | | |

## <a name="image-analysis"></a>Analyse d’image

Certaines actions de l’API [Analyser – Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) peuvent retourner des résultats dans d’autres langues, spécifiées avec le paramètre de requête `language`. D’autres donnent des résultats en anglais indépendamment de la langue spécifiée ; les dernières lèvent une exception pour les langues non prises en charge. Les actions sont spécifiées avec les paramètres de requête `visualFeatures` et `details` ; pour connaître la liste des actions possibles avec l’analyse d’image, voir la [Vue d’ensemble](overview.md).

|Langage | Code langue | Catégories | Balises | Description | Adulte | Marques | Couleur | Visages | ImageType | Objets | Célébrités | Points de repère |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Chinois | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Anglais | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japonais | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portugais | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Espagnol | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment utiliser les fonctionnalités Vision par ordinateur mentionnées dans ce guide.

* [Analyser une image locale (REST)](./quickstarts/csharp-analyze.md)
* [Extraire du texte imprimé (REST)](./quickstarts/csharp-print-text.md)
