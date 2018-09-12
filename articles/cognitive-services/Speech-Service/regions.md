---
title: Régions de service de Speech
description: Informations de référence pour les régions du service de reconnaissance vocale.
services: cognitive-services
author: mahilleb-msft
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/28/2018
ms.author: mahilleb
ms.openlocfilehash: 1cb00035dc8f1cdeabd1beb22ca69f47bf4bd89e
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44379568"
---
# <a name="regions-of-the-speech-service"></a>Régions du service de reconnaissance vocale

Le service de reconnaissance vocale est disponible dans différentes régions.
Lorsque vous créez un abonnement, vous pouvez choisir une région disponible, selon vos besoins.

Lorsque vous utilisez votre abonnement, vous devez prendre en compte la région que vous avez choisie.

## <a name="rest-api"></a>API REST

À l’aide de l’API REST, choisissez les points de terminaison spécifiques à la région.
Consultez [API REST](rest-apis.md) pour plus d’informations.

## <a name="speech-sdk"></a>Kit de développement logiciel (SDK) de reconnaissance vocale

Dans le [Kit de développement logiciel (SDK) de reconnaissance vocale](speech-sdk.md), les régions sont spécifiées sous forme de chaîne (par exemple, en tant que paramètre de [SpeechFactory.FromSubscription](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechfactory.fromsubscription) dans le Kit de développement logiciel (SDK) de reconnaissance vocale pour C#).

### <a name="regions-for-speech-recognition-and-translation"></a>Régions dédiées à la reconnaissance vocale et à la traduction

Le tableau ci-dessous répertorie les régions disponibles pour la **reconnaissance vocale** et la **traduction** :

Région| Valeur du paramètre de région dans le Kit de développement logiciel (SDK) de reconnaissance vocale| Portail
-|-
USA Ouest| `westus`| https://westus.cris.ai
Ouest des États-Unis 2| `westus2`| https://westus2.cris.ai
USA Est| `eastus`| https://eastus.cris.ai
Est des États-Unis 2| `eastus2`| https://eastus2.cris.ai
Asie Est| `eastasia`| https://eastasia.cris.ai
Asie Sud-Est| `southeastasia`| https://southeastasia.cris.ai
Europe Nord| `northeurope`| https://northeurope.cris.ai
Europe Ouest|  `westeurope`| https://westeurope.cris.ai

### <a name="regions-for-intent-recognition"></a>Régions de reconnaissance de l’intention

Les régions disponibles pour la **reconnaissance de l’intention** via le Kit de développement logiciel (SDK) de reconnaissance vocale sont répertoriées dans la [page des régions proposant le service de reconnaissance vocale](/azure/cognitive-services/luis/luis-reference-regions).
Pour chaque région de publication affichée, le paramètre de région du Kit de développement logiciel (SDK) de reconnaissance vocale correspondant est défini comme étant la première partie du nom de domaine du point de terminaison.
Par exemple, utilisez `westus` pour spécifier la région de publication USA Ouest.
