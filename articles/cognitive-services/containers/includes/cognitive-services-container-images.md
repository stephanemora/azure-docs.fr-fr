---
title: Images et référentiels de conteneurs
services: cognitive-services
author: aahill
manager: nitinme
description: Deux tableaux représentant les registres de conteneurs, les dépôts et les noms d’images pour toutes les offres Cognitive Services.
ms.service: cognitive-services
ms.topic: include
ms.date: 09/03/2020
ms.author: aahi
ms.openlocfilehash: d514460f3cd80b5b85604251743abbbbcd1adc8c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906989"
---
### <a name="container-repositories-and-images"></a>Images et référentiels de conteneurs

Les tableaux ci-dessous répertorient les images conteneurs offertes par Azure Cognitive Services. Pour obtenir la liste complète de tous les noms d’image conteneur disponibles et leurs étiquettes utilisables, consultez [Étiquettes des images conteneurs Cognitive Services](../container-image-tags.md). 

#### <a name="generally-available"></a>Mise à la disposition générale 

Le registre de conteneurs Microsoft (MCR) regroupe tous les conteneurs généralement disponibles publiquement pour Cognitive Services. Les conteneurs sont également accessibles directement à partir du [Docker Hub](https://hub.docker.com/_/microsoft-azure-cognitive-services).

**LUIS**

| Conteneur | Nom de registre de conteneurs / référentiel / image |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/language/luis` |

Pour plus d’informations, consultez [Comment exécuter et installer des conteneurs LUIS](../../LUIS/luis-container-howto.md).

**Analyse de texte**

| Conteneur | Nom de registre de conteneurs / référentiel / image |
|--|--|
| Analyse des sentiments v3 (anglais) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en` |
| Analyse des sentiments v3 (espagnol) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-es` |
| Analyse des sentiments v3 (français) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-fr` |
| Analyse des sentiments v3 (italien) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-it` |
| Analyse des sentiments v3 (allemand) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-de` |
| Analyse des sentiments v3 (chinois – simplifié) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zh` |
| Analyse des sentiments v3 (chinois – traditionnel) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zht` |
| Analyse des sentiments v3 (japonais) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-ja` |
| Analyse des sentiments v3 (portugais) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-pt` |
| Analyse des sentiments v3 (néerlandais) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-nl` |

Pour plus d’informations, consultez [Comment exécuter et installer des conteneurs Analyse de texte](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md).

**Détecteur d’anomalies** 

| Conteneur | Nom de registre de conteneurs / référentiel / image |
|--|--|
| Détecteur d’anomalies | `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` |

Pour plus d’informations, consultez [Comment exécuter et installer des conteneurs de détection d’anomalies](../../anomaly-detector/anomaly-detector-container-howto.md).

**Speech Service**

> [!NOTE]
> Pour utiliser des conteneurs Speech, vous devez remplir un [formulaire de requête en ligne](https://aka.ms/csgate).

| Conteneur | Nom de registre de conteneurs / référentiel / image |
|--|--|
| [Reconnaissance vocale](../../speech-service/speech-container-howto.md?tab=stt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` |
| [Reconnaissance vocale personnalisée](../../speech-service/speech-container-howto.md?tab=cstt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` |
| [Synthèse vocale](../../speech-service/speech-container-howto.md?tab=tts) | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` |

#### <a name="ungated-preview"></a>Préversion « décontrôlée » 

Les conteneurs en préversion sont à la disposition du public. Le registre de conteneurs Microsoft (MCR) regroupe tous les conteneurs non contrôlés disponibles publiquement pour Cognitive Services. Les conteneurs sont également accessibles directement à partir du [Docker Hub](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Service | Conteneur | Nom de registre de conteneurs / référentiel / image |
|--|--|--|
| [Analyse de texte](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Extraction d’expressions clés | `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` |
| [Analyse de texte](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Détection de la langue | `mcr.microsoft.com/azure-cognitive-services/textanalytics/language` |


#### <a name="gated-preview"></a>Préversion « contrôlée »

Précédemment, les conteneurs de préversion contrôlés étaient hébergés sur le référentiel `containerpreview.azurecr.io`. À compter du 22 septembre 2020, ces conteneurs (à l’exception de Analyse de texte pour l’intégrité) sont hébergés sur le Container Registry Microsoft (MCR) et leur téléchargement ne nécessite pas l’utilisation de la commande dockr login. Pour utiliser le conteneur, vous devez :

1. Remplissez un [formulaire de requête](https://aka.ms/csgate) avec votre ID d’abonnement Azure et votre scénario utilisateur. 
2. Une fois l’approbation terminée, téléchargez le conteneur à partir du MCR. 
3. Utilisez la clé et le point de terminaison d’une ressource Azure appropriée pour authentifier le conteneur au moment de l’exécution. 

| Service | Conteneur | Nom de registre de conteneurs / référentiel / image |
|--|--|--|
| [Vision par ordinateur](../../Computer-vision/computer-vision-how-to-install-containers.md) | Lire v3.0 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview` |
| [Vision par ordinateur](../../Computer-vision/computer-vision-how-to-install-containers.md) | Lire v3.1 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview` |
| [Vision par ordinateur](https://docs.microsoft.com/azure/cognitive-services/computer-vision/spatial-analysis-container) | Analyse spatiale | `mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis` |
| [API Speech Service](../../speech-service/speech-container-howto.md?tab=ctts) | Synthèse vocale personnalisée | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` |
| [API Speech Service](../../speech-service/speech-container-howto.md?tab=lid) | Détection de la langue | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` |
| [API Speech Service](../../speech-service/speech-container-howto.md?tab=ntts) | Synthèse vocale neuronale | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` |
| [Analyse de texte pour la santé](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health) | Analyse de texte pour la santé | `containerpreview.azurecr.io/microsoft/cognitive-services-healthcare` |

