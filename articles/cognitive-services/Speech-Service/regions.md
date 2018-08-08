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
ms.openlocfilehash: 082002b25b02e1e496221f4686d0e636630dd438
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324389"
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

Région| Valeur du paramètre de région dans le Kit de développement logiciel (SDK) de reconnaissance vocale
-|-
USA Ouest| `westus`
Asie Est| `eastasia`
Europe Nord| `northeurope`

### <a name="regions-for-intent-recognition"></a>Régions de reconnaissance de l’intention

Les régions disponibles pour la **reconnaissance de l’intention** via le Kit de développement logiciel (SDK) de reconnaissance vocale sont répertoriées dans la [page des régions proposant le service de reconnaissance vocale](/azure/cognitive-services/luis/luis-reference-regions).
Pour chaque région de publication affichée, le paramètre de région du Kit de développement logiciel (SDK) de reconnaissance vocale correspondant est défini comme étant la première partie du nom de domaine du point de terminaison.
Par exemple, utilisez `westus` pour spécifier la région de publication USA Ouest.
