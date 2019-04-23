---
title: Prise en charge linguistique - vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Une liste de langages naturels pris en charge par les fonctionnalités de vision par ordinateur.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 1a70d1b2ea504d0ccfba925810a2d19d0c7583cc
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60012646"
---
# <a name="language-support-for-computer-vision"></a>Prise en charge linguistique pour la vision par ordinateur

Certaines fonctionnalités de vision par ordinateur prend en charge plusieurs langues ; toutes les fonctionnalités non mentionnées ici prennent uniquement en charge l’anglais.

## <a name="text-recognition"></a>Reconnaissance de texte

Vision par ordinateur peut reconnaître le texte dans de nombreux langages. Plus précisément, le [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) API prend en charge une variété de langues, tandis que le [en lecture](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) API et [reconnaître le texte](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) API prennent uniquement en charge l’anglais. Consultez [reconnaître le texte imprimé et manuscrit](concept-recognizing-text.md) pour plus d’informations sur cette fonctionnalité et les avantages de chaque API.

Reconnaissance optique de caractères détecte automatiquement la langue du matériel d’entrée, par conséquent, il est inutile de spécifier un code de langue dans l’appel d’API. Toutefois, les codes de langue sont toujours retournés comme valeur de la `"language"` nœud dans la réponse JSON.

|Langage| Code de langue | API DE RECONNAISSANCE OPTIQUE DE CARACTÈRES |
|:-----|:----:|:-----:|
|Arabe | `ar`|✔ |
|Chinois (simplifié) | `zh-Hans`|✔ |
|Chinois (traditionnel) | `zh-Hant`|✔ |
|Tchèque | `cs` |✔ |
|Danois | `da` |✔ |
|Néerlandais | `nl` |✔ |
|Anglais | `en` |✔ |
|Finnois | `fi` |✔ |
|Anglais | `fr` |✔ |
|Allemand | `de` |✔ |
|Grec | `el` |✔ |
|Hongrois | `hu` |✔ |
|Italien | `it` |✔ |
|Japonais | `ja` |✔ |
|Coréen | `ko` |✔ |
|Norvégien | `nb` |✔ |
|Polonais | `pl` |✔ |
|Portugais | `pt` |✔ |
|Roumain | `ro` |✔ |
|Russe | `ru` |✔ |
|Serbe (cyrillique) | `sr-Cyrl` |✔ |
|Serbe (latin) | `sr-Latn` |✔ |
|Slovaque | `sk` |✔ |
|Espagnol | `es` |✔ |
|Suédois | `sw` |✔ |
|Turc | `tr` |✔ |

## <a name="image-analysis"></a>Analyse d’image

Certaines actions de la [analyser - Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API peut retourner des résultats dans d’autres langages, spécifiés avec le `language` paramètre de requête. Autres actions retournent des résultats en anglais, quel que soit le langage est spécifié, et autres lèvent une exception pour les langages non pris en charge. Actions sont spécifiées avec le `visualFeatures` et `details` paramètres de requête ; consultez la [vue d’ensemble](home.md) pour obtenir la liste de toutes les actions que vous pouvez faire avec l’analyse d’image.

|Langage | Code de langue | Categories | Balises | Description | Adulte | Marques | Couleur | Visages | ImageType | Objets | Célébrités | Points de repère |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Chinois | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Anglais | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japonais | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portugais | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Espagnol | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment utiliser les fonctionnalités de vision par ordinateur mentionnées dans ce guide.

* [Analyser une image locale (REST)](./quickstarts/csharp-analyze.md)
* [Extraire le texte imprimé (REST)](./quickstarts/csharp-print-text.md)