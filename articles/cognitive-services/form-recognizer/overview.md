---
title: Qu’est-ce que Form Recognizer ?
titleSuffix: Azure Cognitive Services
description: Form Recognizer est un service Azure Cognitive Services qui permet d’identifier et d’extraire des paires clé-valeur et des données de table à partir de documents de formulaire.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 241d91a8c8670c32461684ed964b6fc0fd7c1672
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85206408"
---
# <a name="what-is-form-recognizer"></a>Qu’est-ce que Form Recognizer ?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Form Recognizer est un service cognitif qui utilise la technologie de machine learning pour identifier et extraire du texte, des données de paires clé-valeur et des tableaux à partir de documents de formulaire. Il ingère du texte à partir de formulaires et restitue des données structurées qui incluent les relations du fichier d’origine. Vous pouvez obtenir des résultats rapidement, avec précision et en adéquation avec votre contenu particulier, sans la nécessité d’une intervention manuelle lourde ou de compétences approfondies en science des données. Form Recognizer est constitué de modèles personnalisés, du modèle de reçu prédéfini et de l’API de disposition. Vous pouvez appeler les modèles Form Recognizer au moyen d’une API REST, afin de réduire la complexité et l’intégrer à votre workflow ou application.

Form Recognizer est constitué des services suivants :
* **Modèles personnalisés** : extrayez des paires clé/valeur et des données de tableau à partir de formulaires. Ces modèles étant entraînés avec vos propres données, ils sont adaptés à vos formulaires.
* **Modèle de reçu prédéfini** : extrayez les données des reçus de ventes aux États-Unis à l’aide d’un modèle prédéfini.
* **API de disposition** : extrayez les structures du texte et des tableaux, ainsi que les coordonnées de leur cadre englobant, à partir de documents.

<!-- add diagram -->

## <a name="custom-models"></a>Modèles personnalisés

Les modèles personnalisés Form Recognizer effectuent l’entraînement avec vos propres données, et il vous suffit de cinq exemples de formulaires d’entrée pour démarrer. Un modèle entraîné peut restituer des données structurées qui incluent les relations du document de formulaire d’origine. Une fois que le modèle est entraîné, vous pouvez le tester, le réentraîner et l’utiliser ensuite pour extraire de manière fiable des données d’autres formulaires, en fonction de vos besoins.

Quand vous entraînez des modèles personnalisés, vous disposez des options suivantes : entraînement avec des données étiquetées et sans données étiquetées.

### <a name="train-without-labels"></a>Effectuer l’entraînement sans étiquettes

Par défaut, Form Recognizer utilise l’apprentissage non supervisé pour comprendre la disposition et les relations entre les champs et les entrées de vos formulaires. Quand vous soumettez vos formulaires d’entrée, l’algorithme groupe les formulaires par types, découvre les clés et les tableaux présents, et associe les valeurs aux clés et les entrées aux tableaux. Cette méthode ne nécessitant pas d’étiquetage manuel des données ni de programmation et de maintenance intensives, nous vous recommandons de l’essayer en premier.

### <a name="train-with-labels"></a>Effectuer l’entraînement avec des étiquettes

Quand vous effectuez l’entraînement avec des données étiquetées, le modèle supervise l’apprentissage afin d’extraire les valeurs dignes d’intérêt, à l’aide des formulaires étiquetés que vous fournissez. Cette approche aboutit à des modèles plus performants et peut engendrer des modèles qui fonctionnent avec des formulaires complexes ou des formulaires contenant des valeurs sans clés.

Form Recognizer utilise l’[API de disposition](#layout-api) pour connaître les tailles et les positions attendues des éléments de texte imprimés et manuscrits. Ensuite, il utilise des étiquettes spécifiées par l’utilisateur pour connaître les associations clé/valeur dans les documents. Nous vous recommandons d’utiliser cinq formulaires étiquetés manuellement du même type pour démarrer l’entraînement d’un nouveau modèle et d’ajouter des données étiquetées en fonction des besoins afin d’améliorer l’exactitude du modèle.

## <a name="prebuilt-receipt-model"></a>Modèle de reçu préconstruit

Form Recognizer comprend également un modèle de lecture des reçus de vente en anglais émis aux États-Unis&mdash;le type utilisé par les restaurants, les stations-service, la vente au détail, etc. ([exemple de reçu](./media/contoso-receipt-small.png)). Ce modèle extrait des informations clés telles que la date et l’heure de la transaction, les détails du vendeur, le montant des taxes et frais totaux et plus encore. En outre, le modèle de reçu préconstruit est entraîné pour reconnaître et retourner tout le texte d’un reçu.

## <a name="layout-api"></a>API de disposition

Form Recognizer peut également extraire la structure du texte et des tableaux (les numéros de ligne et de colonne associés au texte) à l’aide de la reconnaissance optique de caractères (OCR) haute définition.

## <a name="get-started"></a>Bien démarrer

Suivez un guide de démarrage rapide pour commencer à extraire des données de vos formulaires. Nous vous recommandons d’utiliser le service gratuit pendant que vous apprenez la technologie. N’oubliez pas que le nombre de pages gratuites est limité à 500 par mois.

* Personnaliser : entraîner un modèle par rapport à vos formulaires
  * Effectuer l’entraînement sans étiquettes
    * [Démarrage rapide : Entraîner un modèle Form Recognizer et extraire des données à partir de formulaires au moyen d’une API REST avec cURL](quickstarts/curl-train-extract.md)
    * [Démarrage rapide : Entraîner un modèle Form Recognizer et extraire des données à partir de formulaires au moyen d’une API REST avec Python](quickstarts/python-train-extract.md)
  * Effectuer l’entraînement avec des étiquettes
    * [Entraîner un modèle Form Recognizer avec des étiquettes à l’aide de l’outil d’étiquetage des exemples](quickstarts/label-tool.md)
    * [Entraîner un modèle Form Recognizer avec des étiquettes à l’aide de l’API REST et de Python](quickstarts/python-labeled-data.md)
* Reçus prédéfinis : extraire les données des reçus de ventes aux États-Unis
  * [Démarrage rapide : Extraire des données de reçu à l’aide de cURL](quickstarts/curl-receipts.md)
  * [Démarrage rapide : Extraire des données de reçu à l’aide de Python](quickstarts/python-receipts.md)
* Disposition : extraire la structure du texte et des tableaux à partir de formulaires
  * [Démarrage rapide : Extraire des données de disposition à l’aide de Python](quickstarts/python-layout.md)

### <a name="review-the-rest-apis"></a>Passer en revue les API REST

Vous allez utiliser les API suivantes pour entraîner des modèles et extraire des données structurées à partir de formulaires.

|Nom |Description |
|---|---|
| **Entraîner un modèle personnalisé**| Entraîner un nouveau modèle pour analyser vos formulaires avec 5 formulaires du même type. Définissez le paramètre _useLabelFile_ sur `true` pour effectuer l’entraînement avec des données étiquetées manuellement. |
| **Analyser le formulaire** |Analysez un seul document transmis en tant que flux de données pour extraire du formulaire le texte, les paires clé/valeur et les tableaux avec votre modèle personnalisé.  |
| **Analyser le reçu** |Analysez un document de reçu unique pour extraire les informations clés et les autres éléments textuels du reçu.|
| **Analyser la disposition** |Analysez la disposition d’un formulaire pour extraire la structure du texte et des tableaux.|

Explorez la [documentation de référence sur l’API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-previewoperations/AnalyzeWithCustomForm) pour en savoir plus. Si vous êtes familiarisé avec une version précédente de l’API, consultez l’article [Nouveautés](./whats-new.md) pour en savoir plus sur les modifications récentes.

## <a name="input-requirements"></a>Critères des entrées
### <a name="custom-model"></a>Modèle personnalisé

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt-receipt-model"></a>Modèle de reçu préconstruit

Les exigences d’entrée pour le modèle de reçu sont légèrement différentes.

* Format JPEG, PNG, PDF (texte ou numérisé) ou TIFF.
* La taille du fichier doit être inférieure à 20 Mo.
* Les dimensions des images doivent être comprises entre 50 x 50 et 10 000 x 10 000 pixels.
* Les dimensions des PDF ne doivent pas dépasser 17 x 17 pouces, ce qui correspond aux formats de papier Legal ou A3, et plus petits.
* Pour PDF et TIFF, seules les 200 premières pages sont traitées (avec un abonnement gratuit, seules les deux premières pages sont traitées).

## <a name="data-privacy-and-security"></a>Sécurité et confidentialité des données

Comme c’est le cas pour tous les services Cognitive Services, les développeurs utilisant le service Form Recognizer doivent connaître les politiques de Microsoft relatives aux données client. Pour en savoir plus, consultez la [page Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) dans le Centre de gestion de la confidentialité Microsoft.

## <a name="next-steps"></a>Étapes suivantes

Suivez un guide de [démarrage rapide](quickstarts/curl-train-extract.md) pour commencer à utiliser les [API Form Recognizer](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-previewoperations/AnalyzeWithCustomForm).
