---
title: Nouveautés de l’API Analyse de texte
titleSuffix: Text Analytics - Azure Cognitive Services
description: Cet article fournit des informations sur les nouvelles versions et fonctionnalités de l’API Analyse de texte Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: aahi
ms.openlocfilehash: f79152dbfa5a0576cd13484f9b3704eaed3901ea
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92015922"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Nouveautés de l’API Analyse de texte

L’API Analyse de texte est mise à jour de manière continue. Pour vous informer des développements récents, cet article vous fournit des informations sur les nouvelles versions et fonctionnalités.

## <a name="october-2020"></a>Octobre 2020

* Prise en charge de la langue Hindi pour la fonctionnalité Analyse des sentiments v3.x, à partir de la version `2020-04-01` du modèle. 
* La version `2020-09-01` du modèle pour le point de terminaison /languages de la v3 améliore la détection de la langue et renforce la précision.

## <a name="september-2020"></a>Septembre 2020

### <a name="general-api-updates"></a>Mises à jour générales de l’API

* Publication d’une nouvelle URL pour la préversion publique de l’API Analyse de texte v3.1 permettant la prise en charge des mises à jour des points de terminaison suivants de l’API Reconnaissance d'entité nommée v3 : 
    * Le point de terminaison `/pii` comprend désormais la nouvelle propriété `redactedText` dans la réponse JSON où les entités PII détectées dans le texte d’entrée sont remplacées par un `*` pour chaque caractère de ces entités.
    * Le point de terminaison `/linking` comprend désormais la propriété `bingID` dans la réponse JSON pour les entités liées.
* Les points de terminaison de l’API Analyse de texte (préversion) suivants ont été retirés le 4 septembre 2020 :
    * v2.1-preview
    * v3.0-preview
    * v3.0-preview.1
    
> [!div class="nextstepaction"]
> [En savoir plus sur l’API Analyse de texte v3.1-Preview.2](quickstarts/text-analytics-sdk.md)

### <a name="text-analytics-for-health-container-updates"></a>Mises à jour du conteneur Analyse de texte pour la santé

Les mises à jour suivantes sont spécifiques à la version du mois de septembre du conteneur Analyse de texte pour la santé uniquement.
* Une nouvelle image conteneur avec la balise `1.1.013530001-amd64-preview` avec la nouvelle version `2020-09-03` du modèle a été publiée dans le référentiel containerpreview. 
* Cette version de modèle apporte des améliorations en matière de reconnaissance d’entités, de détection d’abréviation et de latence.

> [!div class="nextstepaction"]
> [En savoir plus sur l’API Analyse de texte pour la santé](how-tos/text-analytics-for-health.md)

## <a name="august-2020"></a>Août 2020

### <a name="general-api-updates"></a>Mises à jour générales de l’API

* Version de modèle `2020-07-01` pour les points de terminaison v3 `/keyphrases`, `/pii` et `/languages`, qui ajoute ce qui suit :
    * Des [catégories d’entités](named-entity-types.md?tabs=personal) supplémentaires spécifiques à un gouvernement ou un pays pour la Reconnaissance d’entité nommée.
    * Prise en charge du norvégien et du turc dans Analyse des sentiments v3.
* Une erreur HTTP 400 est désormais retournée pour les demandes d’API v3 qui dépassent les [limites de données](concepts/data-limits.md) publiées. 
* Les points de terminaison qui retournent un offset prennent désormais en charge le paramètre facultatif `stringIndexType`, qui ajuste les valeurs `offset` et `length` retournées pour qu’elles correspondent à un schéma d’index de chaîne [ pris en charge](concepts/text-offsets.md).

### <a name="text-analytics-for-health-container-updates"></a>Mises à jour du conteneur Analyse de texte pour la santé

Les mises à jour suivantes sont spécifiques à la version du mois d’août de l’Analyse de texte pour le conteneur d’intégrité uniquement.

* Nouvelle version de modèle pour l’Analyse de texte pour l’intégrité : `2020-07-24`
* Nouvelle URL pour l’envoi d’Analyse de texte pour des demandes de contrôle d’intégrité : `http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health` (Veuillez noter qu’un effacement du cache du navigateur sera nécessaire pour pouvoir utiliser l’application web de démonstration incluse dans cette nouvelle image de conteneur)

Les propriétés suivantes de la réponse JSON ont changé :

* `type` a été renommé en `category` 
* `score` a été renommé en `confidenceScore`
* Les entités dans le champ `category` de la sortie JSON sont maintenant en casse Pascal. Les entités suivantes ont été renommées :
    * `EXAMINATION_RELATION` a été renommé en `RelationalOperator`.
    * `EXAMINATION_UNIT` a été renommé en `MeasurementUnit`.
    * `EXAMINATION_VALUE` a été renommé en `MeasurementValue`.
    * `ROUTE_OR_MODE` a été renommé en `MedicationRoute`.
    * L’entité relationnelle `ROUTE_OR_MODE_OF_MEDICATION` a été renommée en `RouteOfMedication`.

Les entités suivantes ont été ajoutées :

* NER
    * `AdministrativeEvent`
    * `CareEnvironment`
    * `HealthcareProfession`
    * `MedicationForm` 

* Extraction de relations
    * `DirectionOfCondition`
    * `DirectionOfExamination`
    * `DirectionOfTreatment`

> [!div class="nextstepaction"]
> [En savoir plus sur l’Analyse de texte pour le conteneur d’intégrité](how-tos/text-analytics-for-health.md)

## <a name="july-2020"></a>Juillet 2020 

### <a name="text-analytics-for-health-container---public-gated-preview"></a>Analyse de texte pour le conteneur d’intégrité – Préversion publique contrôlée

L’Analyse de texte pour le conteneur d’intégrité est désormais en préversion publique contrôlée, ce qui vous permet d’extraire des informations à partir de texte en anglais non structuré dans des documents cliniques tels que les formulaires d’admission des patients, les notes du médecin, les documents de recherche et les bilans de sortie d’hospitalisation. Actuellement, vous ne serez pas facturé pour l’utilisation de l’Analyse de texte pour le conteneur d’intégrité.

Le conteneur offre les fonctionnalités suivantes :

* Reconnaissance d’entité nommée
* Extraction des relations
* Liaison d’entités
* Négation

## <a name="may-2020"></a>Mai 2020

### <a name="text-analytics-api-v3-general-availability"></a>Disponibilité générale de l’API Analyse de texte v3

L’API Analyse de texte v3 est désormais en disponibilité générale avec les mises à jour suivantes :

* Version de modèle `2020-04-01`
* Nouvelles [limites de données](concepts/data-limits.md) pour chaque fonctionnalité
* Mise à jour de la [prise en charge linguistique](language-support.md) pour [Analyse des sentiments v3](how-tos/text-analytics-how-to-sentiment-analysis.md)
* Point de terminaison distinct pour la liaison d’entités 
* Nouvelle catégorie d’entité « Address » dans [Reconnaissance d’entité nommée (NER) v3](how-tos/text-analytics-how-to-entity-linking.md).
* Nouvelles sous-catégories dans NER v3 :
   * Location - Geographical
   * Location - Structural
   * Organization - Stock Exchange
   * Organization - Medical
   * Organization - Sports
   * Event - Cultural
   * Event - Natural
   * Event - Sports

Les propriétés suivantes de la réponse JSON ont été ajoutées :
   * `SentenceText` dans Analyse des sentiments
   * `Warnings` pour chaque document 

Les noms des propriétés suivantes dans la réponse JSON ont été changés, le cas échéant :

* `score` a été renommé en `confidenceScore`
    * `confidenceScore` a une précision à deux décimales. 
* `type` a été renommé en `category`
* `subtype` a été renommé en `subcategory`

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

> [!div class="nextstepaction"]
> [En savoir plus sur l’API Analyse de texte v3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>Préversion publique de l’API Analyse de texte v3.1
   * Nouvelle fonctionnalité Analyse des sentiments - [Exploration des opinions](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * Nouveau [filtre de domaine (`PII`) Personnel](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) pour les informations médicales protégées (`PHI`).

> [!div class="nextstepaction"]
> [En savoir plus sur la préversion de l’API Analyse de texte v3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>Février 2020

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>Prise en charge du Kit de développement logiciel (SDK) pour l’API Analyse de texte v3 (Préversion publique)

Dans le cadre de la [version unifiée du Kit de développement logiciel (SDK) Azure](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290), le Kit de développement logiciel (SDK) de l’API Analyse de texte v3 est désormais disponible en préversion publique pour les langages de programmation suivants :
   * [C#](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (Node.js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)
   
   > [!div class="nextstepaction"]
> [En savoir plus sur le Kit de développement logiciel (SDK) de l’API Analyse de texte v3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Préversion publique de la reconnaissance d’entité nommée v3

D’autres types d’entités sont désormais disponibles dans le service Reconnaissance d’entité nommée (NER) v3 en préversion publique, car nous développons la détection des entités d’informations générales et personnelles trouvées dans le texte. Cette mise à jour introduit le [modèle version](concepts/model-versioning.md) `2020-02-01`, qui inclut :

* Reconnaissance des types suivants d’entités générales (en anglais uniquement) :
    * PersonType
    * Produit
    * Événement
    * Geopolitical Entity (GPE) comme sous-type de Location
    * Compétence

* Reconnaissance des types suivants d’entités d’informations personnelles (en anglais uniquement) :
    * Personne
    * Organisation
    * Age comme sous-type de Quantity
    * Date comme sous-type de DateTime
    * E-mail 
    * Phone Number (États-Unis uniquement)
    * URL
    * Adresse IP

> [!div class="nextstepaction"]
> [En savoir plus sur la reconnaissance d’entité nommée v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>2 octobre 2019

#### <a name="named-entity-recognition-ner"></a>Reconnaissance d’entité nommée (NER)

* Un [nouveau point de terminaison](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionPii) pour reconnaître les types d’entités d’informations personnelles (en anglais uniquement)

* Points de terminaison distincts pour la [reconnaissance d’entité](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionGeneral) et la [liaison d’entité](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesLinking).

* [Modèle version](concepts/model-versioning.md) `2019-10-01`, qui inclut :
    * Détection et catégorisation élargies des entités trouvées dans le texte. 
    * Reconnaissance des nouveaux types d’entités suivants :
        * Numéro de téléphone
        * Adresse IP

La liaison d’entités prend en charge l’anglais et l’espagnol. Le support du langage NER varie selon le type d’entité.

#### <a name="sentiment-analysis-v3-public-preview"></a>Analyse des sentiments v3 - Préversion publique

* Un [nouveau point de terminaison](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/Sentiment) pour l’analyse des sentiments.
* [Modèle version](concepts/model-versioning.md) `2019-10-01`, qui inclut :

    * Améliorations significatives en matière d’exactitude et de détail du score et de la catégorisation du texte de l’API.
    * Étiquetage automatique pour différents sentiments dans le texte.
    * Analyse des sentiments et sortie au niveau du document et de la phrase. 

Elle prend en charge l’anglais (`en`), le japonnais (`ja`), le chinois simplifié (`zh-Hans`),  le chinois traditionnel (`zh-Hant`), le français (`fr`), l’italien (`it`), l’espagnol (`es`), le néerlandais (`nl`), la portugais (`pt`) et l’allemand (`de`) et est disponible dans les régions suivantes : `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe` et `West US 2`. 

> [!div class="nextstepaction"]
> [En savoir plus sur Analyse des sentiments v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Étapes suivantes

* [Qu’est-ce que l’API Analyse de texte ?](overview.md)  
* [Exemples de scénarios utilisateur](text-analytics-user-scenarios.md)
* [Analyse des sentiments](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Détection de la langue](how-tos/text-analytics-how-to-language-detection.md)
* [Reconnaissance d’entités](how-tos/text-analytics-how-to-entity-linking.md)
* [Extraction de phrases clés](how-tos/text-analytics-how-to-keyword-extraction.md)
