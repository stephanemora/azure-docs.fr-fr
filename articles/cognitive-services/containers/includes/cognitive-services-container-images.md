---
title: Images et référentiels de conteneurs
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Deux tableaux représentant les registres de conteneurs, les dépôts et les noms d’images pour toutes les offres Cognitive Services.
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2019
ms.author: dapine
ms.openlocfilehash: c1593cb3dad7ee1370a66747fa3fe47e93c19957
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499149"
---
### <a name="container-repositories-and-images"></a>Images et référentiels de conteneurs

Les tableaux ci-dessous sont une liste complète des images de conteneur offertes par Azure Cognitive Services.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Public « non contrôlé » (registre de conteneurs : `mcr.microsoft.com`)

Le Registre de conteneurs Microsoft héberge tous les conteneurs « non contrôlés » disponibles publiquement pour Cognitive Services.

| Service | Conteneur | Nom de registre de conteneurs / référentiel / image |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Analyse de texte](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Extraction d’expressions clés | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Analyse de texte](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Détection de la langue | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Analyse de texte](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Analyse des sentiments | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Préversion « contrôlée » publique (registre de conteneurs : `containerpreview.azurecr.io`)

Le registre des conteneurs en préversion héberge tous les conteneurs « contrôlés » disponibles publiquement pour Cognitive Services. Ces conteneurs doivent faire l’objet d’une demande formelle d’accès pour pouvoir être utilisés.

| Service | Conteneur | Nom de registre de conteneurs / référentiel / image |
|--|--|--|
| [Détecteur d’anomalies](../../anomaly-detector/anomaly-detector-container-howto.md) | Le détecteur d’anomalies | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Vision par ordinateur](../../Computer-vision/computer-vision-how-to-install-containers.md) | Lire | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Visage](../../face/face-how-to-install-containers.md) | Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Form recognizer](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
<<<<<<< TITRE | [API Service de reconnaissance vocale](../../speech-service/speech-container-howto.md?tab=stt) | Reconnaissance vocale | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` | | [API Service de reconnaissance vocale](../../speech-service/speech-container-howto.md?tab=cstt) | Reconnaissance vocale personnalisée | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` | | [API Service de reconnaissance vocale](../../speech-service/speech-container-howto.md?tab=tts) | Synthèse vocale| `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` | | [API Service de reconnaissance vocale](../../speech-service/speech-container-howto.md?tab=ctts) | Synthèse vocale personnalisée | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` | ======= | [API Service de reconnaissance vocale I](../../speech-service/speech-container-howto.md) | Reconnaissance vocale | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` | | [API Service de reconnaissance vocale](../../speech-service/speech-container-howto.md) | Synthèse vocale | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` | | [Traduction de texte Translator Text](../../translator/how-to-install-containers.md) | Traduction de texte Translator Text | `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` |
>>>>>>> refs/remotes/MicrosoftDocs/master
