---
title: Nouveautés de Form Recognizer
titleSuffix: Azure Cognitive Services
description: Comprenez les dernières modifications apportées à l’API Form Recognizer.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: pafarley
ms.openlocfilehash: 2109d25d3962063c711dcab491855d9ebf1cf694
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901883"
---
# <a name="whats-new-in-form-recognizer"></a>Nouveautés de Form Recognizer

Cet article présente les modifications principales apportées aux nouvelles versions de l’API Form Recognizer.

> [!NOTE]
> Les démarrages rapides et les guides dans cet ensemble de documentations utilisent toujours la dernière version de l’API, sauf si indiqué différemment.

## <a name="form-recognizer-20-preview"></a>Form Recognizer 2.0 (préversion)

### <a name="new-features"></a>Nouvelles fonctionnalités

* **Modèle personnalisé**
  * **Effectuer l'apprentissage avec les étiquettes** Vous pouvez maintenant effectuer l'apprentissage d’un modèle personnalisé avec des données étiquetées manuellement. Cette approche aboutit à des modèles plus performants et peut engendrer des modèles qui fonctionnent avec des formulaires complexes ou des formulaires contenant des valeurs sans clés.
  * **API asynchrone** Vous pouvez utiliser des appels d’API asynchrone pour effectuer l'apprentissage et analyser des fichiers et des ensembles de données volumineux.
  * **Prise en charge du fichier TIFF** Vous pouvez effectuer l'apprentissage et extraire des données de documents TIFF.
  * **Améliorations de la précision d’extraction**

* **Modèle de reçu préconstruit**
  * **Montants des pourboires** Vous pouvez à présent extraire les montants des pourboires et d’autres valeurs écrites à la main.
  * **Extraction d’élément de ligne** Vous pouvez extraire des valeurs d’élément de ligne des reçus.
  * **Valeurs de confiance** Vous pouvez afficher la confiance du modèle pour chaque valeur extraite.
  * **Améliorations de la précision d’extraction**

* **Extraction de la disposition** Vous pouvez maintenant utiliser l’API de disposition pour extraire les données texte et les données de tableau à partir de vos formulaires.

### <a name="custom-model-api-changes"></a>Modifications de l’API du modèle personnalisé

Toutes les API pour l’apprentissage et l’utilisation de modèles personnalisés ont été renommées, et certaines méthodes synchrones sont maintenant asynchrones. Les modifications principales sont les suivantes :

* Le processus d’apprentissage d’un modèle est maintenant asynchrone. Vous initiez l’apprentissage via l’appel d'API **/custom/models**. Cet appel renvoie un ID d’opération, que vous pouvez passer dans **custom/models/{modelID}** pour revenir aux résultats de l’apprentissage.
* L’extraction de clé/valeur est maintenant initiée par l’appel d'API **/custom/models/{modelID}/analyze**. Cet appel renvoie un ID d’opération, que vous pouvez passer dans **custom/models/{modelID}/analyzeResults/{resultID}** pour renvoyer les résultats d’extraction.
* Les ID d’opération pour l’opération d’apprentissage se trouvent maintenant dans l’en-tête **Location** des réponses HTTP, non dans l’en-tête **Operation-Location**.

### <a name="receipt-api-changes"></a>Modifications de l’API de reçu

Les API pour lire les reçus ont été renommées.

* L’extraction de données de reçu est maintenant initiée par l’appel d'API **/prebuilt/receipt/analyze**. Cet appel renvoie un ID d’opération, que vous pouvez passer dans **/prebuilt/receipt/analyzeResults/{resultID}** pour renvoyer les résultats d’extraction.

### <a name="output-format-changes"></a>Modifications du format de sortie

Les réponses JSON pour tous les appels d’API ont de nouveaux formats. Certaines clés et valeurs ont été ajoutées, supprimées et renommées. Consultez les démarrages rapides pour des exemples des formats JSON actuels.

## <a name="next-steps"></a>Étapes suivantes

Suivez un guide de [démarrage rapide](quickstarts/curl-train-extract.md) pour commencer à utiliser les [API Form Recognizer](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm).