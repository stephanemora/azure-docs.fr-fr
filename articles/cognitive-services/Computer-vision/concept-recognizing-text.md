---
title: Reconnaissance optique de caractères (OCR) – Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Concepts liés à la reconnaissance optique de caractères (OCR) à partir d’images et de documents comportant du texte imprimé et manuscrit à l’aide de l’API Vision par ordinateur.
services: cognitive-services
author: msbbonsu
manager: netahw
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: t-bebon
ms.custom: seodec18
ms.openlocfilehash: d1c642a660b24cfc54c9c4308b8956582e13d50a
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85954738"
---
# <a name="optical-character-recognition-ocr"></a>Reconnaissance optique des caractères (OCR)

Vision par ordinateur comprend de nouvelles fonctionnalités de reconnaissance optique de caractères (OCR), basées sur le Deep Learning, qui extraient du texte imprimé ou manuscrit d’images et de documents PDF. L’extraction peut être effectuée à partir de documents analogiques (images et documents numérisés) et de documents numérisés. Il peut s’agir d’images libres, comme des photos de contenants ou de conteneurs indiquant des numéros de série, ainsi que de documents (factures, rapports financiers, articles, etc.). Cette fonctionnalité OCR est disponible dans le cadre du service géré dans le cloud ou localement (conteneurs). Par ailleurs, elle prend en charge les réseaux virtuels et les points de terminaison privés pour répondre aux besoins de conformité et de confidentialité de votre entreprise.

## <a name="read-api"></a>API Lire 

[L’API Lire](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) de Vision par ordinateur, toute dernière technologie OCR de Microsoft, extrait du texte imprimé (dans sept langues), du texte manuscrit (en anglais uniquement), des chiffres et des symboles monétaires à partir d’images et de documents PDF multipages. Elle est optimisée pour extraire le texte d’images libres à forte composante textuelle et de documents PDF multipages en langue mixte. Elle prend en charge la détection de texte imprimé et manuscrit (en anglais uniquement) dans la même image ou le même document. La liste complète des langues prises en charge est disponible sur la page [Prise en charge des langues pour Vision par ordinateur](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#text-recognition).

### <a name="how-it-works"></a>Fonctionnement

Dans la mesure où [l’API Lire](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) prend en charge les documents contenant beaucoup de texte et jusqu’à 2 000 pages, elle s’exécute de manière asynchrone. La première étape consiste à appeler l’opération Lire. Celle-ci prend en entrée une image ou un document PDF et retourne un ID d’opération. 

La deuxième étape consiste à appeler l’opération [Obtenir les résultats](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750). Celle-ci prend l’ID d’opération créé par l’opération Lire. Elle retourne ensuite le contenu texte extrait de votre image ou document au format JSON. La réponse JSON conserve les regroupements de lignes d’origine des mots reconnus. Elle comprend les lignes de texte extraites et les coordonnées de leur cadre englobant. Chaque ligne de texte inclut tous les mots extraits avec leurs coordonnées et un score de confiance.

Si nécessaire, Lire corrige la rotation de la page reconnue en retournant le décalage de rotation en degrés autour de l'axe horizontal de l'image de la façon suivante :

![Rotation d’une image en vue de la lecture et de la délimitation de son texte](./Images/vision-overview-ocr-read.png)

Suivez le guide de démarrage rapide [Extraire du texte imprimé et manuscrit](./QuickStarts/CSharp-hand-text.md) pour mettre en œuvre la reconnaissance optique de caractères à l’aide de C# et de l’API REST.

### <a name="input-requirements"></a>Critères des entrées

L’API Lire accepte les entrées suivantes :
* Formats de fichiers pris en charge : JPEG, PNG, BMP, PDF et TIFF.
* PDF et TIFF : jusqu’à 2 000 pages sont traitées. Abonnés du niveau Gratuit : seules les deux premières pages sont traitées.
* La taille de fichier doit être inférieure à 50 Mo, et les dimensions comprises entre 50 × 50 pixels et 10 000 × 10 000 pixels.
* Les dimensions des PDF ne doivent pas dépasser 17 × 17 pouces, ce qui correspond aux formats de papier Legal ou A3 (maximum).


### <a name="text-from-images"></a>Texte issu d’images

La sortie suivante de l’API Lire présente les lignes de texte et les mots extraits d’une image comportant du texte écrit à différents angles et dans différentes couleurs et polices.

![Rotation d’une image en vue de la lecture et de la délimitation de son texte](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>Texte issu de documents

En plus des images, l’API Lire prend en entrée des documents PDF.

![Rotation d’une image en vue de la lecture et de la délimitation de son texte](./Images/text-from-documents-example.png)


### <a name="handwritten-text-in-english"></a>Texte manuscrit en anglais

Pour l’instant, l’opération Lire prend en charge l’extraction de texte manuscrit en anglais exclusivement.

![Rotation d’une image en vue de la lecture et de la délimitation de son texte](./Images/handwritten-example.png)

### <a name="printed-text-in-supported-languages"></a>Texte imprimé dans les langues prises en charge

L’API Lire gère l’extraction de texte imprimé en anglais, en espagnol, en allemand, en français, en italien, en portugais et en néerlandais. Si votre scénario réclame la prise en charge d’autres langues, référez-vous à la vue d’ensemble de l’API OCR dans ce document. Consultez la liste des [langues prises en charge](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#text-recognition).

![Rotation d’une image en vue de la lecture et de la délimitation de son texte](./Images/supported-languages-example.png)

### <a name="mixed-languages-support"></a>Prise en charge des langues mixtes

L’API Lire prend en charge les images et les documents en plusieurs langues, communément appelés documents en langue mixte. Pour cela, elle classe chaque ligne de texte du document dans la langue détectée avant d’extraire le contenu textuel.

![Rotation d’une image en vue de la lecture et de la délimitation de son texte](./Images/mixed-language-example.png)

### <a name="data-privacy-and-security"></a>Sécurité et confidentialité des données

Comme pour tous les services Cognitive Services, les développeurs qui utilisent le service Lire doivent connaître les stratégies de Microsoft concernant les données client. Pour plus d’informations, consultez la page Cognitive Services dans le [Centre de gestion de la confidentialité Microsoft](https://www.microsoft.com/en-us/trust-center/product-overview).

### <a name="deploy-on-premises"></a>Déploiement en local

Lire est également disponible sous la forme d’un conteneur Docker (préversion), ce qui vous permet de déployer les nouvelles fonctionnalités OCR dans votre propre environnement. Les conteneurs conviennent particulièrement bien à certaines exigences de sécurité et de gouvernance des données. Consultez [Guide pratique pour installer et exécuter des conteneurs Lire](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers).


## <a name="ocr-api"></a>API OCR

[L’API OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) applique un modèle de reconnaissance plus ancien, ne prend en charge que les images et s’exécute de façon synchrone, retournant immédiatement le texte détecté. Elle gère [plus de langues](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#text-recognition) que l’API Lire.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez [l’API REST Lire 3.0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005).
- Suivez le guide de démarrage rapide [Extraction de texte](./QuickStarts/CSharp-hand-text.md) pour mettre en œuvre la reconnaissance optique de caractères en C# , en Java, en JavaScript ou en Python avec l’API REST.
