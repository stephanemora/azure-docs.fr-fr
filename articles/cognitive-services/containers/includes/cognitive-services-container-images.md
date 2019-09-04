---
ms.openlocfilehash: fe9879f9574fe1496ebdf20ac76fe5b759d4ea6a
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051239"
---
### <a name="container-repositories-and-images"></a>Images et référentiels de conteneurs

Les tableaux ci-dessous sont une liste complète des images de conteneur offertes par Azure Cognitive Services.

#### <a name="public-container-registry-mcrmicrosoftcom"></a>Public (registre de conteneurs : `mcr.microsoft.com`)

Le registre de conteneurs Microsoft héberge tous les conteneurs en disponibilité générale (GA) pour Cognitive Services.

| de diffusion en continu | Conteneur | Nom de registre de conteneurs / référentiel / image |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Analyse de texte](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Extraction d’expressions clés | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Analyse de texte](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Détection de la langue | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Analyse de texte](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Analyse des sentiments | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-preview-container-registry-containerpreviewazurecrio"></a>Préversion publique (registre de conteneurs : `containerpreview.azurecr.io`)

Le registre Container Preview héberge tous les conteneurs en « préversion publique » pour Cognitive Services qui ne sont pas encore en disponibilité générale (GA). Ces conteneurs doivent faire l’objet d’une demande formelle d’accès pour pouvoir être utilisés.

| de diffusion en continu | Conteneur | Nom de registre de conteneurs / référentiel / image |
|--|--|--|
| [Détecteur d’anomalies](../../anomaly-detector/anomaly-detector-container-howto.md) | Le détecteur d’anomalies | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Vision par ordinateur](../../Computer-vision/computer-vision-how-to-install-containers.md) | Reconnaître le texte | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text` |
| [Visage](../../face/face-how-to-install-containers.md) | Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Form recognizer](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [API Speech Service](../../speech-service/speech-container-howto.md) | Reconnaissance vocale | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [API Speech Service](../../speech-service/speech-container-howto.md) | Synthèse vocale | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
