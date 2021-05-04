---
title: Présentation de la Reconnaissance optique de caractères
titleSuffix: Azure Cognitive Services
description: Le service Reconnaissance optique de caractères extrait le texte visible d'une image et le renvoie sous forme de chaînes structurées.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 9eb35c892db113807d3c401306485a836a1d970b
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107947426"
---
# <a name="what-is-optical-character-recognition"></a>Présentation de la Reconnaissance optique de caractères

La reconnaissance optique de caractères (OCR) vous permet d’extraire du texte imprimé ou manuscrit à partir d’images, comme des photos de plaques de rue ou de produits, ainsi qu’à partir de documents (factures, rapports financiers, articles, etc.). Les technologies OCR de Microsoft prennent en charge l’extraction de texte imprimé en [plusieurs langues](./language-support.md). Pour bien démarrer, suivez un [guide de démarrage rapide](./quickstarts-sdk/client-library.md).

![Versions de démonstration OCR](./Images/ocr-demo.gif)

Cette documentation contient les types d’articles suivants :
* Les [guides de démarrage rapide](./quickstarts-sdk/client-library.md) sont des instructions pas à pas qui vous permettent d’effectuer des appels au service et d’obtenir des résultats en peu de temps. 
* Les [guides patiques](./Vision-API-How-to-Topics/call-read-api.md) contiennent des instructions sur l’utilisation du service de manière plus spécifique ou personnalisée.
<!--* The [conceptual articles](Vision-API-How-to-Topics/call-read-api.md) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions. -->

## <a name="read-api"></a>API Lire 

[L’API Lire](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) Vision par ordinateur, toute dernière technologie OCR d’Azure ([découvrez les nouveautés](./whats-new.md)), extrait du texte imprimé (dans plusieurs langues), du texte manuscrit (en anglais uniquement), des chiffres et des symboles monétaires à partir d’images et de documents PDF multipages. Elle est optimisée pour extraire le texte d’images à forte composante textuelle et de documents PDF multipages en langue mixte. Elle prend en charge la détection de texte imprimé et manuscrit dans la même image ou le même document.

![Comment la reconnaissance optique de caractères convertit les images et les documents en une sortie structurée avec du texte extrait](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>Critères des entrées

L’appel **Lire** utilise des images et des documents comme entrée. Les conditions requises sont les suivantes :

* Formats de fichiers pris en charge : JPEG, PNG, BMP, PDF et TIFF.
* Pour les fichiers PDF et TIFF, jusqu’à 2000 pages (seules les deux premières pages pour le niveau gratuit) sont traitées.
* La taille de fichier doit être inférieure à 50 Mo (4 Mo pour le niveau gratuit), et les dimensions comprises entre 50 × 50 pixels et 10000 × 10000 pixels. 

## <a name="supported-languages"></a>Langues prises en charge
L’API Read prend en charge un total de 73 langues pour le texte de style d’impression. Consultez la liste complète des [langues prises en charge par OCR](./language-support.md#optical-character-recognition-ocr). Le style OCR manuscrit est exclusivement pris en charge pour l'anglais.

## <a name="key-features"></a>Fonctionnalités clés

L’API Read comprend les fonctionnalités suivantes. 

* Impression de l’extraction de texte en 73 langues
* Extraction de texte manuscrit en anglais
* Lignes de texte et mots avec scores de localisation et de confiance
* Aucune identification de langue requise
* Prise en charge des langues mixtes et du mode mixte (impression et écriture manuscrite)
* Sélection de pages et de plages de pages à partir de grands documents multipages
* Ordre de lecture naturel pour les lignes de texte
* Classification de l’écriture manuscrite pour les lignes de texte
* Disponible en tant que conteneur Docker Distroless pour un déploiement local

Découvrez [comment utiliser les fonctionnalités OCR](./vision-api-how-to-topics/call-read-api.md).

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>Utiliser l’API cloud ou déployer localement
Les API cloud Read 3.x sont l’option préférée pour la plupart des clients en raison de la facilité d’intégration et de la productivité rapide prête à l’emploi. Azure et le service de Vision par ordinateur gèrent l’évolutivité, les performances, la sécurité des données et les besoins en matière de conformité tout en répondant aux besoins de vos clients.

Pour un déploiement local, le [conteneur Docker Read (préversion)](./computer-vision-how-to-install-containers.md) vous permet de déployer les nouvelles capacités OCR dans votre propre environnement local. Les conteneurs conviennent particulièrement bien à certaines exigences de sécurité et de gouvernance des données.

## <a name="ocr-api"></a>API OCR

L'[API OCR](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f20d) héritée applique un modèle de reconnaissance plus ancien, ne prend en charge que les images et s'exécute de façon synchrone, renvoyant immédiatement le texte détecté. Pour obtenir la liste des langues prises en charge, consultez la colonne OCR des [langues prises en charge](./language-support.md#optical-character-recognition-ocr).

> [!WARNING]
> Les opérations RecognizeText de Vision par ordinateur 2.0 seront prochainement remplacées par la nouvelle [API Read](#read-api) abordée dans cet article. Les clients existants devraient [effectuer la transition vers les opérations de lecture](upgrade-api-versions.md).

## <a name="data-privacy-and-security"></a>Sécurité et confidentialité des données

Comme avec tous les services Cognitive Services, les développeurs utilisant le service Vision par ordinateur doivent connaître les politiques de Microsoft relatives aux données client. Pour en savoir plus, consultez la [page Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) dans le Centre de gestion de la confidentialité Microsoft.

## <a name="next-steps"></a>Étapes suivantes

- Démarrez avec les [guides de démarrage rapide consacrés à la bibliothèque de client ou à l’API REST (Lire) OCR](./quickstarts-sdk/client-library.md).
- Découvrez l’[API REST Lire 3.2](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005).
