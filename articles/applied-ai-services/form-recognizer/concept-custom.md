---
title: Modèles personnalisés et composés – Form Recognizer
titleSuffix: Azure Applied AI Services
description: Découvrez comment créer, utiliser et gérer des modèles personnalisés et composés de Form Recognizer, ainsi que leurs utilisation et limites.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/09/2021
ms.author: lajanuar
ms.openlocfilehash: 9c995c5ce63c44473cd3d7b824ef85e64868ae1e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128546633"
---
# <a name="form-recognizer-custom-and-composed-models"></a>Modèles personnalisés et composés de Form Recognizer

Form Recognizer utilise la technologie de Machine Learning avancée pour détecter et extraire des informations à partir d’images de document et retourner les données extraites dans une sortie JSON structurée. Avec Form Recognizer, vous pouvez effectuer l’apprentissage de modèles personnalisés autonomes, créer des modèles composés ou commencer avec nos modèles prédéfinis :

* **Modèles personnalisés**. Les modèles personnalisés Form Recognizer vous permettent d’analyser et d’extraire des données à partir de formulaires et de documents spécifiques à votre entreprise. Les modèles personnalisés sont entraînés pour vos données et cas d’usage spécifiques.

* **Modèles composés**. Un modèle composé est créé sur la base d’une collection de modèles personnalisés qui sont affectés à un modèle unique englobant les types de formulaire. Quand un document est soumis à un modèle composé, le service effectue une étape de classification pour déterminer le modèle personnalisé qui représente précisément le formulaire présenté pour l’analyse.

* **Modèles prédéfinis**. Form Recognizer prend actuellement en charge les modèles prédéfinis pour les [cartes de visite](concept-business-cards.md), [mises en pages](concept-layout.md), [documents d’identité](concept-identification-cards.md), [factures](concept-invoices.md) et [tickets de caisse](concept-receipts.md).

Dans cet article, nous allons examiner le processus de création de modèles personnalisés et composés de Form Recognizer à l’aide de notre [outil d’étiquetage des exemples Form Recognizer](label-tool.md), nos [API REST](quickstarts/client-library.md?branch=main&pivots=programming-language-rest-api#train-a-custom-model) ou nos kits [SDK de bibliothèque de client](quickstarts/client-library.md?branch=main&pivots=programming-language-csharp#train-a-custom-model).

## <a name="what-is-a-custom-model"></a>Qu’est-ce qu’un modèle personnalisé ?

Un modèle personnalisé est un programme de Machine Learning dont l’apprentissage a pour but de reconnaître les champs de formulaire dans vos contenus spécifiques et d’extraire des paires clé-valeur et des données de table. Vous avez seulement besoin de cinq exemples du même type de formulaire pour commencer. L’apprentissage de votre modèle personnalisé peut s’effectuer avec ou sans jeux de données étiquetés.

## <a name="what-is-a-composed-model"></a>Qu’est-ce qu’un modèle composé ?

Avec les modèles composés, il est possible d’affecter plusieurs modèles personnalisés à un modèle composé qui est appelé avec un ID de modèle unique. Cela est utile quand vous avez effectué l’apprentissage de plusieurs modèles et que vous souhaitez les regrouper pour analyser des types de formulaire similaires. Par exemple, votre modèle composé peut être constitué de modèles personnalisés dont l’apprentissage a pour but d’analyser vos approvisionnements, vos équipement et vos bons de commande de mobilier. Au lieu d’essayer de sélectionner manuellement le modèle approprié, vous pouvez utiliser un modèle composé pour déterminer le modèle personnalisé approprié pour chaque analyse et extraction.

## <a name="try-it"></a>Essayer

Bien démarrer avec notre Outil d’étiquetage des exemples Form Recognizer :

> [!div class="nextstepaction"]
> [Essayer un modèle personnalisé](https://aka.ms/fott-2.1-ga "Commencez avec Custom pour effectuer l’apprentissage d’un modèle avec des étiquettes et rechercher des paires clé-valeur.")

## <a name="create-your-models"></a>Créer vos modèles

Voici les étapes de création, d’apprentissage et d’utilisation des modèles personnalisés et composés :

* [**Assembler votre jeu de données d’apprentissage**](#assemble-your-training-dataset)
* [**Charger votre set d’apprentissage sur le Stockage Blob Azure**](#upload-your-training-dataset)
* [**Effectuer l’apprentissage de votre modèle personnalisé**](#train-your-custom-model)
* [**Composer des modèles personnalisés**](#create-a-composed-model)
* [**Analyser des documents**](#analyze-documents-with-your-custom-or-composed-model)
* [**Gérer vos modèles personnalisés**](#manage-your-custom-models)

## <a name="assemble-your-training-dataset"></a>Assembler votre jeu de données d’apprentissage

La création d’un modèle personnalisé commence par l’établissement de votre jeu de données d’apprentissage. Vous aurez besoin d’un minimum de cinq formulaires remplis du même type pour votre exemple de jeu de données. Il peut s’agir de différents types de fichiers (jpg, png, pdf, tiff). Ils peuvent contenir à la fois du texte et de l’écriture manuscrite. Vos formulaires doivent respecter les [critères des entrées](build-training-data-set.md#custom-model-input-requirements) de Form Recognizer.

## <a name="upload-your-training-dataset"></a>Charger votre jeu de données d’apprentissage

Vous devez [charger votre jeu de données d’apprentissage](build-training-data-set.md#upload-your-training-data) dans un conteneur Stockage Blob Azure. Si vous ignorez comment créer un compte de stockage Azure avec un conteneur, *consultez* [Démarrage rapide de Stockage Azure pour Portail Azure](../../storage/blobs/storage-quickstart-blobs-portal.md). Vous pouvez utiliser le niveau tarifaire Gratuit (F0) pour tester le service, puis passer par la suite à un niveau payant pour la production.

## <a name="train-your-custom-model"></a>Effectuer l’apprentissage de votre modèle personnalisé

Vous pouvez [entraîner votre modèle](quickstarts/client-library.md#train-a-custom-model) avec des jeux de données étiquetés ou sans. Les jeux de données sans étiquette reposent uniquement sur [l’API de disposition](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync) pour détecter et identifier les informations clés, sans intervention humaine supplémentaire. Les jeux de données étiquetés s’appuient également sur l’API de disposition, mais une autre intervention humaine est incluse, comme vos étiquettes spécifiques et l’emplacement des champs. Pour utiliser à la fois des données avec et sans étiquettes, commencez avec au moins cinq formulaires remplis du même type pour les données d’apprentissage avec étiquettes, puis ajoutez des données sans étiquettes au jeu de données requis.

## <a name="create-a-composed-model"></a>Créer un modèle composé

> [!NOTE]
> **La composition de modèle est disponible uniquement pour les modèles personnalisés entraînés _avec_ des étiquettes.** Toute tentative de composition de modèles sans étiquette génère une erreur.

L’opération de composition de modèle permet d’affecter un ID de modèle unique à jusqu’à 100 modèles personnalisés entraînés. Quand vous appelez la fonction d’analyse avec l’ID de modèle composé, Form Recognizer commence par classifier le formulaire envoyé. Il choisit le modèle affecté qui correspond le mieux, puis retourne les résultats pour ce modèle. Cette opération est utile quand les formulaires entrants sont susceptibles d’appartenir à l’un des différents modèles.

À l’aide de l’outil d’étiquetage des exemples Form Recognizer, de l’API REST ou des kits SDK de la bibliothèque de client, procédez comme suit pour configurer un modèle composé :

1. [**Collecter vos ID de modèles personnalisés**](#gather-your-custom-model-ids)
1. [**Composer vos modèles personnalisés**](#compose-your-custom-models)

#### <a name="gather-your-custom-model-ids"></a>Collecter vos ID de modèles personnalisés

Une fois le processus d’apprentissage réussi, un ID de modèle est affecté à votre modèle personnalisé. Vous pouvez récupérer un ID de modèle comme suit :

### <a name="form-recognizer-sample-labeling-tool"></a>[**Outil d’étiquetage des exemples Form Recognizer**](#tab/fott)

Quand vous effectuez l’apprentissage des modèles à l’aide de [**l’outil d’étiquetage des exemples Form Recognizer**](https://fott-2-1.azurewebsites.net/), l’ID de modèle se trouve dans la fenêtre de résultat de l’apprentissage :

:::image type="content" source="media/fott-training-results.png" alt-text="Capture d’écran : fenêtre des résultats de l’apprentissage.":::

### <a name="rest-api"></a>[**API REST**](#tab/rest-api)

[**L’API REST**](quickstarts/client-library.md?pivots=programming-language-rest-api#train-a-custom-model) retourne une `201 (Success)` réponse avec en-tête **Emplacement**. La valeur du dernier paramètre dans cet en-tête est l’ID de modèle pour le modèle dont l’apprentissage vient d’être réalisé :

:::image type="content" source="media/model-id.png" alt-text="Capture d’écran : en-tête d’emplacement retourné contenant l’ID de modèle.":::

### <a name="client-library-sdks"></a>[**Kits SDK de bibliothèque de client**](#tab/sdks)

 Les kits [**SDK de bibliothèque de client**](quickstarts/client-library.md?pivots=programming-language-csharp#train-a-custom-model) retournent un objet de modèle pouvant être interrogé pour retourner l’ID de modèle entraîné :

* C\#  | [Classe CustomFormModel](/dotnet/api/azure.ai.formrecognizer.training.customformmodel?view=azure-dotnet&preserve-view=true#properties "Kit SDK Azure pour .NET")

* Java | [Classe CustomFormModelInfo](/java/api/com.azure.ai.formrecognizer.training.models.customformmodelinfo?view=azure-java-stable&preserve-view=true#methods "Kit SDK Azure pour Java")

* JavaScript | [Interface CustomFormModelInfo](/javascript/api/@azure/ai-form-recognizer/customformmodelinfo?view=azure-node-latest&preserve-view=true&branch=main#properties "Kit SDK Azure pour JavaScript")

* Python | [Classe CustomFormModelInfo](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.customformmodelinfo?view=azure-python&preserve-view=true&branch=main#variables "Kit SDK Azure pour Python")

---

#### <a name="compose-your-custom-models"></a>Composer vos modèles personnalisés

Une fois que vous avez collecté vos modèles personnalisés correspondant à un type de formulaire unique, vous pouvez les combiner dans un modèle unique.

### <a name="form-recognizer-sample-labeling-tool"></a>[**Outil d’étiquetage des exemples Form Recognizer**](#tab/fott)

**L’outil d’étiquetage des exemples** vous permet de commencer rapidement à effectuer l’apprentissage des modèles et à les combiner pour composer un ID de modèle unique.

Une fois l’apprentissage réalisé, composez vos modèles comme suit :

1. Dans le menu de rail de gauche, sélectionnez **l’icône de composition du modèle** (flèche de fusion).

1. Dans la fenêtre principale, sélectionnez les modèles auxquels vous souhaitez affecter un ID de modèle unique. Les modèles présentant une icône de flèche sont déjà des modèles composés.

1. Choisissez le **bouton Composer** dans le coin supérieur gauche.

1. Dans la fenêtre contextuelle, nommez le modèle que vous venez de composer, puis sélectionnez **Composer**.

Une fois l’opération effectuée, le modèle récemment composé apparaît dans la liste.

  :::image type="content" source="media/custom-model-compose.png" alt-text="Capture d’écran : fenêtre de la composition du modèle." lightbox="media/custom-model-compose-expanded.png":::

### <a name="rest-api"></a>[**API REST**](#tab/rest-api)

À l’aide de **l’API REST**, vous pouvez créer une demande de [**modèle composé personnalisé**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/Compose) pour créer un modèle composé unique à partir de modèles existants. Le corps de la demande requiert un tableau de chaînes de vos ID `modelIds` pour composer et vous pouvez éventuellement définir `modelName`.  *Consultez* [Composer des modèles Async](/rest/api/formrecognizer/2.1preview2/compose-custom-models-async/compose-custom-models-async).

### <a name="client-library-sdks"></a>[**Kits SDK de bibliothèque de client**](#tab/sdks)

Utilisez le code du langage de programmation de votre choix pour créer un modèle composé qui sera appelé avec un ID de modèle unique. Vous trouverez ci-dessous des liens vers des exemples de code qui montrent comment créer un modèle composé à partir de modèles personnalisés existants :

* [**C#/.NET**](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/Sample8_ModelCompose.md).

* [**Java**](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/java/com/azure/ai/formrecognizer/CreateComposedModel.java).

* [**JavaScript**](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/formrecognizer/ai-form-recognizer/samples/v3/javascript/createComposedModel.js).

* [**Python**](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_create_composed_model.py)

---

## <a name="analyze-documents-with-your-custom-or-composed-model"></a>Analyser des documents avec votre modèle personnalisé ou composé

 L’opération **Analyse** de formulaire personnalisée vous oblige à fournir le paramètre `modelID` dans l’appel à Form Recognizer. Vous pouvez fournir un ID de modèle personnalisé unique ou un ID de modèle composé pour le paramètre `modelID`.

### <a name="form-recognizer-sample-labeling-tool"></a>[**Outil d’étiquetage des exemples Form Recognizer**](#tab/fott)

1. Dans le menu du volet de gauche de l’outil, sélectionnez **l’icône Analyse** (ampoule).

1. Choisissez une URL de fichier ou d’image locale à analyser.

1. Sélectionnez le bouton **Exécuter l’analyse**.

1. L’outil applique des étiquettes dans les cadres englobants et signale le pourcentage de confiance de chaque étiquette.

:::image type="content" source="media/analyze.png" alt-text="Capture d’écran : outil Form Recognizer, fenêtre de l’analyse de formulaire personnalisé.":::

### <a name="rest-api"></a>[**API REST**](#tab/rest-api)

À l’aide de l’API REST, vous pouvez effectuer une requête [Analyser le formulaire](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm) pour analyser un document et extraire des paires clé-valeur et des données de table.

### <a name="client-library-sdks"></a>[**Kits SDK de bibliothèque de client**](#tab/sdks)

Utilisation du langage de programmation de votre choix pour analyser un formulaire ou un document avec un modèle personnalisé ou composé. Vous aurez besoin de votre point de terminaison Form Recognizer, de votre clé API et de votre ID de modèle.

* [**C#/.NET**](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/Sample8_ModelCompose.md#recognize-a-custom-form-using-a-composed-model)

* [**Java**](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/java/com/azure/ai/formrecognizer/RecognizeCustomFormsFromUrl.java)

* [**JavaScript**](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/formrecognizer/ai-form-recognizer/samples/v3/javascript/recognizeCustomForm.js)

* [**Python**](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_recognize_custom_forms.py)

---

Testez les modèles dont l’apprentissage vient d’être réalisé en [analysant les formulaires](quickstarts/client-library.md#analyze-forms-with-a-custom-model) qui ne faisaient pas partie du jeu de données d’apprentissage. Selon la justesse signalée, vous souhaiterez peut-être effectuer d’autres entraînements pour améliorer le modèle. Vous pouvez poursuivre l’apprentissage pour [améliorer les résultats](label-tool.md#improve-results).

## <a name="manage-your-custom-models"></a>Gérer vos modèles personnalisés

Vous pouvez [gérer vos modèles personnalisés](quickstarts/client-library.md#manage-custom-models) tout au long de leur cycle de vie en affichant une [liste de tous les modèles personnalisés](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModels) sous votre abonnement, en récupérant des informations sur [un modèle personnalisé spécifique](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModel) et en [supprimant des modèles personnalisés](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/DeleteCustomModel) de votre compte.

Très bien ! Vous avez découvert les étapes permettant de créer des modèles personnalisés et composés, puis de les utiliser dans vos projets et applications Form Recognizer.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la bibliothèque de client Form Recognizer, explorez notre documentation de référence sur les API.

> [!div class="nextstepaction"]
> [Informations de référence sur l’API Form Recognizer](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)
>
