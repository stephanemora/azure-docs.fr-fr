---
title: Guide de migration pour la version 2 de l’API Analyse de texte
titleSuffix: Azure Cognitive Services
description: Découvrez comment déplacer vos applications pour utiliser la version 3 de l’API Analyse de texte.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/06/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 396cac23548eb951e47c4ec3a602aadcdfa304f4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096349"
---
# <a name="migrate-from-version-2-of-the-text-analytics-api"></a>Effectuer une migration depuis la version 2 de l’API Analyse de texte

Si vos applications utilisent la version 2.1 de l’API Analyse de texte, cet article vous aidera à les mettre à niveau afin qu’elles utilisent la dernière version des fonctionnalités, qui font désormais partie d’[Azure Cognitive Service for Language](../overview.md).

## <a name="features"></a>Fonctionnalités

Sélectionnez l’une des fonctionnalités ci-dessous pour voir les informations que vous pouvez utiliser afin de mettre à jour votre application.

## <a name="sentiment-analysis"></a>[Analyse des sentiments](#tab/sentiment-analysis)

> [!TIP]
> Vous souhaitez utiliser la dernière version de l’API dans votre application ? Pour plus d’informations sur la version actuelle de l’API, consultez l’article de guide pratique [analyse des sentiments](../sentiment-opinion-mining/how-to/call-api.md) et le [guide de démarrage rapide](../sentiment-opinion-mining/quickstart.md). 

## <a name="feature-changes"></a>Évolution des fonctionnalités 

L’analyse des sentiments dans la version 2.1 retourne des scores de sentiment entre 0 et 1 pour chaque document envoyé à l’API, les scores plus proches de 1 indiquant un sentiment plus positif. La version actuelle de cette fonctionnalité retourne des étiquettes de sentiment (par exemple, « positif » ou « négatif ») pour les phrases et le document dans son ensemble ainsi que les scores de confiance associés. 

## <a name="steps-to-migrate"></a>Étapes de migration

### <a name="rest-api"></a>API REST

Si votre application utilise l’API REST, mettez à jour son point de terminaison de demande afin d’utiliser le [point de terminaison actuel](../sentiment-opinion-mining/quickstart.md?pivots=rest-api) pour l’analyse des sentiments. Par exemple, `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/sentiment`. Vous devez également mettre à jour l’application pour utiliser les étiquettes de sentiment retournées dans la [réponse de l’API](../sentiment-opinion-mining/how-to/call-api.md). 

Consultez la documentation de référence pour obtenir des exemples de réponse JSON.
* [Version 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)
* [Version 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment) 
* [Version 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/Sentiment)

### <a name="client-libraries"></a>Bibliothèques clientes

Pour utiliser la dernière version de la bibliothèque de client de l’analyse des sentiments, vous devez télécharger le dernier package logiciel dans l’espace de noms `Azure.AI.TextAnalytics`. L’[article de démarrage rapide](../sentiment-opinion-mining/quickstart.md) liste les commandes que vous pouvez utiliser pour le langage de votre choix, avec un exemple de code.

## <a name="ner-and-entity-linking"></a>[NER et liaison d’entités](#tab/named-entity-recognition)

> [!TIP]
> Vous souhaitez utiliser la dernière version de l’API dans votre application ? Pour plus d’informations sur la version actuelle des API, consultez les articles suivants : NER :
> * [Démarrage rapide](../named-entity-recognition/quickstart.md)
> * [Comment appeler l’API](../named-entity-recognition/how-to-call.md) Liaison d’entités
> * [Démarrage rapide](../entity-linking/quickstart.md)
> * [Comment appeler l’API](../entity-linking/how-to/call-api.md)

## <a name="feature-changes"></a>Évolution des fonctionnalités

Dans la version 2.1, l’API Analyse de texte utilise un seul point de terminaison pour la reconnaissance d’entité nommée (NER) et la liaison d’entités. La version actuelle de cette fonctionnalité fournit une détection étendue des entités nommées et utilise des points de terminaison séparés pour les demandes NER et de liaison d’entités. En outre, vous pouvez utiliser une autre fonctionnalité offerte par le service de langage, qui vous permet de détecter les [informations personnelles (PII) et médicales (PHI)](../personally-identifiable-information/overview.md). 

## <a name="steps-to-migrate"></a>Étapes de migration

### <a name="rest-api"></a>API REST

Si votre application utilise l’API REST, mettez à jour son point de terminaison de demande sur les [points de terminaison actuels](../named-entity-recognition/quickstart.md?pivots=rest-api) pour NER et/ou la liaison d’entités. Par exemple :

Liaison d’entités
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/recognition/general`

Vous devez également mettre à jour votre application pour utiliser les [catégories d’entité](../named-entity-recognition/concepts/named-entity-categories.md) retournées dans la [réponse de l’API](../named-entity-recognition/how-to-call.md).

Consultez la documentation de référence pour obtenir des exemples de réponse JSON.
* [Version 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)
* [Version 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral) 
* [Version 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/EntitiesRecognitionGeneral)

### <a name="client-libraries"></a>Bibliothèques clientes

Pour utiliser la dernière version des bibliothèques de client de liaison d’entités et de NER, vous devez télécharger le dernier package logiciel dans l’espace de noms `Azure.AI.TextAnalytics`. L’article de démarrage rapide pour la [Reconnaissance d’entité nommée](../named-entity-recognition/quickstart.md) et la [liaison d’entités](../entity-linking/quickstart.md) liste les commandes que vous pouvez utiliser pour le langage de votre choix, avec un exemple de code.

#### <a name="version-21-entity-categories"></a>Catégories d’entité version 2.1

Le tableau suivant répertorie les catégories d’entités retournées pour NER v2.1.

| Category   | Description                          |
|------------|--------------------------------------|
| Personne   |   Noms des personnes.  |
|Emplacement    | Points de repère, structures et caractéristiques géographiques et entités géopolitiques naturels et créés par l’homme |
|Organisation | Sociétés, partis politiques, groupes de musique, clubs de sport, organismes gouvernementaux et organisations publiques. Les nationalités et les religions ne sont pas incluses dans ce type d’entité. |
| PhoneNumber | Numéros de téléphone (Numéros de téléphone américains et européens uniquement). |
| E-mail | Adresses e-mail. |
| URL | URL vers des sites web. |
| IP | Adresses IP du réseau. |
| DateTime | Dates et heures du jour.| 
| Date | Dates du calendrier. |
| Temps | Heures de la journée |
| DateRange | Plages de dates. |
| TimeRange | Intervalles de temps. |
| Duration | Durées. |
| Définissez | Définie, répétée tant de fois. |
| Quantité | Nombres et quantités numériques. |
| Number | Nombres. |
| Pourcentage | Pourcentages.|
| Ordinal | Nombres ordinaux. |
| Age | Ages. |
| Devise | Devises. |
| Dimension | Dimensions et mesures. |
| Température | Températures. |

## <a name="language-detection"></a>[Détection de la langue](#tab/language-detection)

> [!TIP]
> Vous souhaitez utiliser la dernière version de l’API dans votre application ? Pour plus d’informations sur la version actuelle de l’API, consultez l’article de guide pratique [détection de la langue](../language-detection/how-to/call-api.md) et le [guide de démarrage rapide](../language-detection/quickstart.md). 

## <a name="feature-changes"></a>Évolution des fonctionnalités 

La sortie de la fonctionnalité de détection de langue a changé dans la version actuelle. La réponse JSON contient `ConfidenceScore` au lieu de `score`. La version actuelle ne retourne également qu’une seule langue dans un attribut `detectedLanguage` pour chaque document.

## <a name="steps-to-migrate"></a>Étapes de migration

### <a name="rest-api"></a>API REST

Si votre application utilise l’API REST, mettez à jour son point de terminaison de demande sur le [point de terminaison actuel](../language-detection/quickstart.md?pivots=rest-api) pour la détection de langue. Par exemple, `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/languages`. Vous devez également mettre à jour l’application pour utiliser `ConfidenceScore` au lieu de `score` dans la [réponse de l’API](../language-detection/how-to/call-api.md). 

Consultez la documentation de référence pour obtenir des exemples de réponse JSON.
* [Version 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)
* [Version 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) 
* [Version 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/Languages)

#### <a name="client-libraries"></a>Bibliothèques clientes

Pour utiliser la dernière version de la bibliothèque de client de l’analyse des sentiments, vous devez télécharger le dernier package logiciel dans l’espace de noms `Azure.AI.TextAnalytics`. L’[article de démarrage rapide](../language-detection/quickstart.md) liste les commandes que vous pouvez utiliser pour le langage de votre choix, avec un exemple de code.

## <a name="key-phrase-extraction"></a>[Extraction de phrases clés](#tab/key-phrase-extraction)

> [!TIP]
> Vous souhaitez utiliser la dernière version de l’API dans votre application ? Pour plus d’informations sur la version actuelle de l’API, consultez l’article de guide pratique [extraction de phrase clé](../key-phrase-extraction/how-to/call-api.md) et le [guide de démarrage rapide](../key-phrase-extraction/quickstart.md). 

## <a name="feature-changes"></a>Évolution des fonctionnalités 

La fonctionnalité d’extraction d’expressions clés n’a pas changé en dehors de la version du point de terminaison.

## <a name="steps-to-migrate"></a>Étapes de migration

### <a name="rest-api"></a>API REST

Si votre application utilise l’API REST, mettez à jour son point de terminaison de demande sur le [point de terminaison actuel](../key-phrase-extraction/quickstart.md?pivots=rest-api) pour l’extraction d’expressions clés. Par exemple : `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.1/keyPhrases`

Consultez la documentation de référence pour obtenir des exemples de réponse JSON.
* [Version 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)
* [Version 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) 
* [Version 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/KeyPhrases)

### <a name="client-libraries"></a>Bibliothèques clientes

Pour utiliser la dernière version de la bibliothèque de client de l’analyse des sentiments, vous devez télécharger le dernier package logiciel dans l’espace de noms `Azure.AI.TextAnalytics`. L’[article de démarrage rapide](../key-phrase-extraction/quickstart.md) liste les commandes que vous pouvez utiliser pour le langage de votre choix, avec un exemple de code.

---

## <a name="see-also"></a>Voir aussi

* [Qu’est-ce qu’Azure Cognitive Service for Language ?](../overview.md)
