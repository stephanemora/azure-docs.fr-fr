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
ms.date: 05/21/2021
ms.author: aahi
ms.openlocfilehash: 4edd78b89ed30d6458eee94e328b8c05f141bfd6
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110457686"
---
# <a name="migrate-to-version-3x-of-the-text-analytics-api"></a>Migrer vers la version 3.x de l’API Analyse de texte

Si vous utilisez la version 2.1 de l’API Analyse de texte, cet article vous aide à mettre à niveau votre application pour utiliser la version 3.x. La version 3.0 est en disponibilité générale et introduit de nouvelles fonctionnalités comme la [Reconnaissance d’entité nommée (NER)](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-features-and-versions) et le [contrôle de version de modèle](concepts/model-versioning.md). Une préversion de v3.1 (v3.1-preview.x) est également disponible, elle ajoute des fonctionnalités comme l’[exploration des opinions](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features). Les modèles utilisés dans v2 ne reçoivent pas les mises à jour ultérieures. 

## <a name="sentiment-analysis"></a>[Analyse des sentiments](#tab/sentiment-analysis)

### <a name="feature-changes"></a>Évolution des fonctionnalités 

L’analyse des sentiments dans la version 2.1 retourne des scores de sentiment entre 0 et 1 pour chaque document envoyé à l’API, les scores plus proches de 1 indiquant un sentiment plus positif. La version 3 à la place retourne des étiquettes de sentiment (par exemple, « positif » ou « négatif ») pour les phrases et le document dans son ensemble, ainsi que les scores de confiance associés. 

### <a name="steps-to-migrate"></a>Étapes de migration

#### <a name="rest-api"></a>API REST

Si votre application utilise l’API REST, mettez à jour son point de terminaison de demande sur le point de terminaison v3 pour l’analyse des sentiments. Par exemple, `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`. Vous devez également mettre à jour l’application pour utiliser les étiquettes de sentiment retournées dans la [réponse de l’API](how-tos/text-analytics-how-to-sentiment-analysis.md#view-the-results). 

Consultez la documentation de référence pour obtenir des exemples de réponse JSON.
* [Version 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)
* [Version 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment) 
* [Version 3.1-preview](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-5/operations/Sentiment)

#### <a name="client-libraries"></a>Bibliothèques clientes

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

## <a name="ner-and-entity-linking"></a>[NER et liaison d’entités](#tab/named-entity-recognition)

### <a name="feature-changes"></a>Évolution des fonctionnalités

Dans la version 2.1, l’API Analyse de texte utilise un seul point de terminaison pour la reconnaissance d’entité nommée (NER) et la liaison d’entités. La version 3 fournit une détection étendue des entités nommées et utilise des points de terminaison séparés pour les demandes NER et de liaison d’entités. À partir de v3.1-preview.1, NER peut également détecter les informations personnelles `pii` et médicales `phi`. 

### <a name="steps-to-migrate"></a>Étapes de migration

#### <a name="rest-api"></a>API REST

Si votre application utilise l’API REST, mettez à jour son point de terminaison de demande sur le point de terminaison v3 pour NER et/ou la liaison d’entités.

Liaison d’entités
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

Vous devez également mettre à jour votre application pour utiliser les [catégories d’entité](named-entity-types.md) retournées dans la [réponse de l’API](how-tos/text-analytics-how-to-entity-linking.md#view-results).

Consultez la documentation de référence pour obtenir des exemples de réponse JSON.
* [Version 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)
* [Version 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral) 
* [Version 3.1-preview](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-5/operations/EntitiesRecognitionGeneral)

#### <a name="client-libraries"></a>Bibliothèques clientes

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

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

### <a name="feature-changes"></a>Évolution des fonctionnalités 

La sortie de la fonctionnalité de détection de langue a changé dans la version 3. La réponse JSON contient `ConfidenceScore` au lieu de `score`. La version 3 ne retourne également qu’une seule langue dans un attribut `detectedLanguage` pour chaque document.

### <a name="steps-to-migrate"></a>Étapes de migration

#### <a name="rest-api"></a>API REST

Si votre application utilise l’API REST, mettez à jour son point de terminaison de demande sur le point de terminaison v3 pour la détection de langue. Par exemple, `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages`. Vous devez également mettre à jour l’application pour utiliser `ConfidenceScore` au lieu de `score` dans la [réponse de l’API](how-tos/text-analytics-how-to-language-detection.md#step-3-view-the-results). 

Consultez la documentation de référence pour obtenir des exemples de réponse JSON.
* [Version 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)
* [Version 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) 
* [Version 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-5/operations/Languages)

#### <a name="client-libraries"></a>Bibliothèques clientes

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

## <a name="key-phrase-extraction"></a>[Extraction de phrases clés](#tab/key-phrase-extraction)

### <a name="feature-changes"></a>Évolution des fonctionnalités 

La fonctionnalité d’extraction d’expressions clés n’a pas changé dans v3 en dehors de la version du point de terminaison.

### <a name="steps-to-migrate"></a>Étapes de migration

#### <a name="rest-api"></a>API REST

Si votre application utilise l’API REST, mettez à jour son point de terminaison de demande sur le point de terminaison v3 pour l’extraction d’expressions clés. Par exemple : `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`

Consultez la documentation de référence pour obtenir des exemples de réponse JSON.
* [Version 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)
* [Version 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) 
* [Version 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-5/operations/KeyPhrases)

#### <a name="client-libraries"></a>Bibliothèques clientes

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

---

## <a name="see-also"></a>Voir aussi

* [Qu’est-ce que l’API Analyse de texte ?](overview.md)
* [Prise en charge linguistique](language-support.md)
* [Contrôle de version de modèle](concepts/model-versioning.md)
