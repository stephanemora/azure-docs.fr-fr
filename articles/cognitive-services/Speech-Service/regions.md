---
title: Régions de service Speech
titlesuffix: Azure Cognitive Services
description: Informations de référence pour les régions du service Speech.
services: cognitive-services
author: mahilleb-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mahilleb
ms.openlocfilehash: 8d36036332e939075ffac8763bec9c23d8e4a3f7
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51712889"
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

La **reconnaissance de l’intention** pour le SDK Speech partage la prise en charge des régions avec LUIS. Pour obtenir la liste complète des régions disponibles, consultez [Points de terminaison et régions de publication - LUIS](https://docs.microsoft.comazure/cognitive-services/luis/luis-reference-regions).

Les régions disponibles pour la **reconnaissance de l’intention** via le SDK Speech sont listées dans la [page des régions LUIS](/azure/cognitive-services/luis/luis-reference-regions).

Pour chaque région de publication listée, utilisez le **nom de région d’API** fourni. Par exemple, utilisez `westus` pour les États-Unis de l’Ouest.

## <a name="rest-apis"></a>API REST

Le service Speech expose également des points de terminaison REST pour les requêtes de reconnaissance vocale et de synthèse vocale.

### <a name="speech-to-text"></a>Reconnaissance vocale

Pour obtenir la documentation de référence relative à la reconnaissance vocale, consultez [API REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#speech-to-text).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Synthèse vocale

Pour obtenir la documentation de référence relative à la synthèse vocale, consultez [API REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#speech-to-text).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
