---
title: Régions - Services Speech
titlesuffix: Azure Cognitive Services
description: Informations de référence pour les régions du service Speech.
services: cognitive-services
author: mahilleb-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mahilleb
ms.custom: seodec18
ms.openlocfilehash: d302fc67225213ad55687797c97c7a2a8a17cc6d
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54329505"
---
# <a name="speech-service-supported-regions"></a>Régions prises en charge pour le service Speech

Le service Speech permet à votre application de convertir de l’audio en texte, d’effectuer une traduction vocale et de convertir du texte par synthèse vocale. Le service est disponible dans plusieurs régions avec des points de terminaison uniques pour le SDK et les API REST Speech.

Vérifiez que vous utilisez le point de terminaison qui correspond à la région de votre abonnement.

## <a name="speech-sdk"></a>Kit de développement logiciel (SDK) de reconnaissance vocale

Dans le [Kit de développement logiciel (SDK) Speech](speech-sdk.md), les régions sont spécifiées sous forme de chaîne (par exemple, en tant que paramètre `SpeechConfig.FromSubscription` dans le Kit de développement logiciel (SDK) Speech pour C#).

### <a name="speech-recognition-and-translation"></a>Reconnaissance vocale et traduction

Le SDK Speech est disponible dans les régions suivantes pour la **reconnaissance vocale** et la **traduction** :

  Région | Paramètre du SDK Speech | Portail de personnalisation de reconnaissance vocale
 ------|-------|--------
 USA Ouest | `westus` | https://westus.cris.ai
 Ouest des États-Unis 2 | `westus2` | https://westus2.cris.ai
 USA Est | `eastus` | https://eastus.cris.ai
 Est des États-Unis 2 | `eastus2` | https://eastus2.cris.ai
 Asie Est | `eastasia` | https://eastasia.cris.ai
 Asie Sud-Est | `southeastasia` | https://southeastasia.cris.ai
 Europe Nord | `northeurope` | https://northeurope.cris.ai
 Europe Ouest | `westeurope` | https://westeurope.cris.ai


### <a name="intent-recognition"></a>Reconnaissance de l’intention

Les régions disponibles pour la **reconnaissance de l'intention** via le kit de développement logiciel (SDK) Speech sont les suivantes :

 Région globale | Région | Paramètre du SDK Speech
 ------|-------|--------
 Asie | Asie Est | `eastasia`
 Asie | Asie Sud-Est | `southeastasia`
 Australie | Australie Est | `australiaeast`
 Europe | Europe Nord | `northeurope`
 Europe | Europe Ouest | `westeurope`
 Amérique du Nord | USA Est | `eastus`
 Amérique du Nord | USA Est 2 | `eastus2`
 Amérique du Nord | USA Centre Sud | `southcentralus`
 Amérique du Nord | USA Centre-Ouest | `westcentralus`
 Amérique du Nord | USA Ouest | `westus`
 Amérique du Nord | USA Ouest 2 | `westus2`
 Amérique du Sud | Brésil Sud | `brazilsouth`

Il s'agit d'un sous-ensemble des régions de publication prises en charge par le [service Language Understanding (LUIS)](/azure/cognitive-services/luis/luis-reference-regions).

## <a name="rest-apis"></a>API REST

Le service Speech expose également des points de terminaison REST pour les requêtes de reconnaissance vocale et de synthèse vocale.

### <a name="speech-to-text"></a>Reconnaissance vocale

Pour obtenir la documentation de référence relative à la reconnaissance vocale, consultez [API REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#speech-to-text).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Synthèse vocale

Pour obtenir la documentation de référence relative à la synthèse vocale, consultez [API REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#speech-to-text).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
