---
title: Images et référentiels de conteneurs
services: cognitive-services
author: aahill
manager: nitinme
description: Deux tableaux représentant les registres de conteneurs, les dépôts et les noms d’images pour toutes les offres Cognitive Services.
ms.service: cognitive-services
ms.topic: include
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: 9025e016725a966061c557f16b610d8897c04c11
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590659"
---
### <a name="container-repositories-and-images"></a>Images et référentiels de conteneurs

Les tableaux ci-dessous répertorient les images conteneurs offertes par Azure Cognitive Services. Pour obtenir la liste complète de tous les noms d’image conteneur disponibles et leurs étiquettes utilisables, consultez [Étiquettes des images conteneurs Cognitive Services](../container-image-tags.md). 

#### <a name="generally-available"></a>Mise à la disposition générale 

Le registre de conteneurs Microsoft (MCR) regroupe tous les conteneurs généralement disponibles publiquement pour Cognitive Services. Les conteneurs sont également accessibles directement à partir du [Docker Hub](https://hub.docker.com/_/microsoft-azure-cognitive-services).

#### <a name="luis"></a>[LUIS](#tab/luis)

| Conteneur LUIS | Nom de registre de conteneurs / référentiel / image |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |

Pour plus d’informations, consultez [Comment exécuter et installer des conteneurs LUIS](../../LUIS/luis-container-howto.md).

#### <a name="text-analytics"></a>[Analyse de texte](#tab/text-analytics)

| Conteneur Analyse de texte | Nom de registre de conteneurs / référentiel / image |
|--|--|
| Analyse des sentiments v3 (anglais) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en` |
| Analyse des sentiments v3 (espagnol) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-es` |
| Analyse des sentiments v3 (français) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-fr` |
| Analyse des sentiments v3 (italien) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-it` |
| Analyse des sentiments v3 (allemand) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-de` |
| Analyse des sentiments v3 (chinois - simplifié) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zh` |
| Analyse des sentiments v3 (chinois - traditionnel) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zht` |
| Analyse des sentiments v3 (japonais) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-ja` |
| Analyse des sentiments v3 (portugais) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-pt` |
| Analyse des sentiments v3 (néerlandais) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-nl` |

Pour plus d’informations, consultez [Comment exécuter et installer des conteneurs Analyse de texte](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md).

---

#### <a name="public-ungated-preview-container-registry-mcrmicrosoftcom"></a>Préversion « non contrôlée » publique (registre de conteneurs : `mcr.microsoft.com`)

Les conteneurs en préversion sont à la disposition du public. Le registre de conteneurs Microsoft (MCR) regroupe tous les conteneurs non contrôlés disponibles publiquement pour Cognitive Services. Les conteneurs sont également accessibles directement à partir du [Docker Hub](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Service | Conteneur | Nom de registre de conteneurs / référentiel / image |
|--|--|--|
| [Analyse de texte](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Extraction d’expressions clés | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Analyse de texte](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Détection de la langue | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Détecteur d’anomalies](../../anomaly-detector/anomaly-detector-container-howto.md) | Le détecteur d’anomalies | `mcr.microsoft.com/azure-cognitive-services/anomaly-detector` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Préversion « contrôlée » publique (registre de conteneurs : `containerpreview.azurecr.io`)

Les conteneurs en préversion contrôlée suivants sont hébergés dans le registre Container Preview et nécessitent une application pour y accéder. Pour plus d’informations, consultez [Processus de contrôle Cognitive Services](../../cognitive-services-gating-process.md).

| Service | Conteneur | Nom de registre de conteneurs / référentiel / image |
|--|--|--|
| [Vision par ordinateur](../../Computer-vision/computer-vision-how-to-install-containers.md) | Lire | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Visage](../../face/face-how-to-install-containers.md) | Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Form recognizer](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [API Speech Service](../../speech-service/speech-container-howto.md?tab=stt) | Reconnaissance vocale | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [API Speech Service](../../speech-service/speech-container-howto.md?tab=cstt) | Reconnaissance vocale personnalisée | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [API Speech Service](../../speech-service/speech-container-howto.md?tab=tts) | Synthèse vocale | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [API Speech Service](../../speech-service/speech-container-howto.md?tab=ctts) | Synthèse vocale personnalisée | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
