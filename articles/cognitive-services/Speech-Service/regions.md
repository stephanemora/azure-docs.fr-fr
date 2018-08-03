---
title: Régions du service de reconnaissance vocale | Microsoft Docs
description: Informations de référence pour les régions du service de reconnaissance vocale.
services: cognitive-services
author: mahilleb-msft
manager: wolmfa61
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/28/2018
ms.author: mahilleb
ms.openlocfilehash: 11360d163fdba057d373d091d46903cde7789a8b
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071417"
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

Le tableau ci-dessous répertorie les régions disponibles pour la **reconnaissance vocale** et la **traduction** :

Région| Valeur du paramètre de région dans le Kit de développement logiciel (SDK) de reconnaissance vocale
-|-
USA Ouest| `westus`
Est de l'Asie| `eastasia`
Europe Nord| `northeurope`

Les régions disponibles pour la **reconnaissance de l’intention** via le Kit de développement logiciel (SDK) de reconnaissance vocale sont répertoriées dans la [page des régions proposant le service de reconnaissance vocale](/azure/cognitive-services/luis/luis-reference-regions).
Pour chaque région de publication affichée, le paramètre de région du Kit de développement logiciel (SDK) de reconnaissance vocale correspondant est défini comme étant la première partie du nom de domaine du point de terminaison.
Par exemple, utilisez `westus` pour spécifier la région de publication USA Ouest.
