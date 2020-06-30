---
title: Nouveautés de Form Recognizer
titleSuffix: Azure Cognitive Services
description: Comprenez les dernières modifications apportées à l’API Form Recognizer.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: pafarley
ms.openlocfilehash: 7898fcf9b149f50f8478576d18cd3c3b984eb937
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85209094"
---
# <a name="whats-new-in-form-recognizer"></a>Nouveautés de Form Recognizer

Le service Form Recognizer est régulièrement mis à jour. Lisez cet article pour vous tenir informé des améliorations de fonctionnalités, des correctifs et des mises à jour de la documentation.

> [!NOTE]
> Sauf indication contraire, les démarrages rapides et les guides relatifs à Form Recognizer utilisent toujours la dernière version de l'API.

## <a name="june-2020"></a>Juin 2020

### <a name="new-features"></a>Nouvelles fonctionnalités
* **API CopyModel ajoutée aux kits de développement logiciel (SDK) clients** Vous pouvez désormais utiliser les kits de développement logiciel (SDK) clients pour copier des modèles d’un abonnement à un autre. Consultez [Sauvegarder et récupérer des modèles](./disaster-recovery.md) pour obtenir des informations générales sur cette fonctionnalité.
* **Intégration d’Azure Active Directory**  Vous pouvez maintenant utiliser vos informations d’identification AAD pour authentifier vos objets Form Recognizer clients dans les kits de développement logiciel (SDK).
* **Modifications spécifiques au kit de développement logiciel (SDK)** Inclut des ajouts de fonctionnalités mineures ainsi que des modifications importantes. Consultez les journaux des modifications du kit de développement logiciel (SDK) pour plus d’informations.
  * [Journal des modifications C# SDK Preview 3](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Journal des modifications Python SDK Preview 3](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Journal des modifications Java SDK Preview 3](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Journal des modifications JavaScript SDK Preview 3](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>Avril 2020

### <a name="new-features"></a>Nouvelles fonctionnalités
* **Prise en charge du SDK pour la préversion publique de l’API Form Recognizer v 2.0** Ce mois-ci, nous avons étendu notre support technique pour inclure une préversion du SDK de Form Recognizer v2.0 (préversion). Utilisez les liens ci-dessous pour bien démarrer avec le langage de votre choix : 
   * [Kit de développement logiciel (SDK) .NET](https://docs.microsoft.com/dotnet/api/overview/azure/formrecognizer)
   * [Kit SDK Java](https://docs.microsoft.com/java/api/overview/azure/formrecognizer)
   * [Kit de développement logiciel (SDK) Python](https://docs.microsoft.com/python/api/overview/azure/formrecognizer)
   * [Kit de développement logiciel (SDK) JavaScript](https://docs.microsoft.com/javascript/api/overview/azure/formrecognizer)

  Le nouveau SDK prend en charge toutes les fonctionnalités de l’API REST v2.0 pour Form Recognizer. Par exemple, vous pouvez entraîner un modèle avec ou sans étiquettes pour extraire du texte, des paires clé-valeur et des tables de vos formulaires, extraire des données à partir de reçus avec le service des reçus préintégré et extraire du texte et des tables de vos documents avec le service de disposition. Vous pouvez partager vos commentaires sur les SDK à l’aide du [formulaire de commentaires sur les SDK](https://aka.ms/FR_SDK_v1_feedback).
 
* **Copier un modèle personnalisé** Vous pouvez désormais copier des modèles entre les régions et les abonnements à l’aide de la nouvelle fonctionnalité de copie de modèle personnalisé. Avant d’appeler l’API Copier une modèle personnalisé, vous devez d’abord obtenir l’autorisation de copie dans la ressource cible en appelant l’opération d’autorisation de copie sur le point de terminaison de cette dernière.
   * API REST [Générer une autorisation de copie](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-previewoperations/CopyCustomFormModelAuthorization)
   * API REST [Copier un modèle personnalisé](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-previewoperations/CopyCustomFormModel) 

### <a name="security-improvements"></a>Améliorations de sécurité

* Les clés gérées par le client sont maintenant disponibles pour Form Recognizer. Pour plus d’informations, consultez [Chiffrement des données au repos pour Form Recognizer](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/form-recognizer-encryption-of-data-at-rest).
* Utilisez des identités managées pour accéder aux ressources Azure avec Azure Active Directory. Pour plus d’informations, consultez [Autoriser l’accès aux identités managées](https://docs.microsoft.com/azure/cognitive-services/authentication#authorize-access-to-managed-identities).

## <a name="march-2020"></a>Mars 2020 

### <a name="new-features"></a>Nouvelles fonctionnalités

* **Types de valeurs pour l’étiquetage** : vous pouvez maintenant spécifier les types de valeurs que vous étiquetez avec l’outil d’étiquetage des exemples Form Recognizer. Les types et variantes de valeurs suivants sont actuellement pris en charge :
  * `string`
    * default, `no-whitespaces`, `alphanumeric`
  * `number`
    * default, `currency`
  * `date` 
    * default, `dmy`, `mdy`, `ymd`
  * `time`
  * `integer`

  Pour savoir comment utiliser cette fonctionnalité, consultez le guide de l’[outil d’étiquetage des exemples](./quickstarts/label-tool.md#specify-tag-value-types).


* **Visualisation des tables** : l’outil d’étiquetage des exemples affiche désormais les tables reconnues dans le document. Cela vous permet de visualiser les tables qui ont été reconnues et extraites du document, avant l’étiquetage et l’analyse. Cette fonctionnalité peut être activée/désactivée à l'aide de l'option couches.

  L'exemple suivant illustre la façon dont les tables sont reconnues et extraites :

  > [!div class="mx-imgBorder"]
  > ![Visualisation de table à l'aide de l'outil d'étiquetage des exemples](./media/whats-new/formre-table-viz.png)

    Les tables extraites sont disponibles dans la sortie JSON sous `"pageResults"`.

  > [!IMPORTANT]
  > L'étiquetage des tables n'est pas pris en charge. Si les tables ne sont pas reconnues et extraites automatiquement, vous ne pouvez les étiqueter qu'en tant que paires clé/valeur. Lorsque vous étiquetez des tables en tant que paires clé-valeur, étiquetez chaque cellule en tant que valeur unique.

### <a name="extraction-enhancements"></a>Améliorations apportées à l'extraction

Cette version comprend des améliorations en termes d'extraction et de précision, avec notamment la possibilité d'étiqueter et d'extraire plusieurs paires clé/valeur sur la même ligne de texte. 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>L’outil d’étiquetage des exemples est désormais open source

L’outil d’étiquetage des exemples Form Recognizer est désormais disponible sous forme de projet open source. Vous pouvez l'intégrer à vos solutions et y apporter des modifications pour qu'il réponde à vos besoins.

Pour plus d’informations sur l’outil d’étiquetage des exemples Form Recognizer, consultez la documentation disponible sur [GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

### <a name="tls-12-enforcement"></a>Application de TLS 1.2

La sécurité TLS 1.2 est maintenant appliquée pour toutes les requêtes HTTP adressées à ce service. Pour plus d'informations, consultez [Sécurité Azure Cognitive Services](../cognitive-services-security.md).

## <a name="january-2020"></a>Janvier 2020

Cette version introduit Form Recognizer 2.0 (préversion). Les sections ci-dessous contiennent des informations sur les nouvelles fonctionnalités, améliorations et modifications. 

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

Suivez un guide de [démarrage rapide](quickstarts/curl-train-extract.md) pour commencer à utiliser les [API Form Recognizer](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-previewoperations/AnalyzeWithCustomForm).