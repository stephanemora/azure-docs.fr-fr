---
title: Régions de service de Speech
description: Informations de référence pour les régions du service de reconnaissance vocale.
services: cognitive-services
author: mahilleb-msft
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 09/24/2018
ms.author: mahilleb
ms.openlocfilehash: 8485caeff3a7c96ed8f7403befac0026fae16e90
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987558"
---
# <a name="regions-of-the-speech-service"></a>Régions du service de reconnaissance vocale

Le service de reconnaissance vocale est disponible dans différentes régions.
Lorsque vous créez un abonnement, vous pouvez sélectionner l’une des régions disponibles, selon vos besoins.

Lorsque vous utilisez votre abonnement, vous devez prendre en compte la région que vous avez sélectionnée.

## <a name="rest-api"></a>API REST

Utilisez l’API REST pour sélectionner les points de terminaison correspondant à la région choisie.
Consultez [API REST](rest-apis.md) pour plus d’informations.

## <a name="speech-sdk"></a>Kit SDK Speech

Dans le [SDK Speech](speech-sdk.md), les régions sont spécifiées sous forme de chaînes (par exemple, en tant que paramètres de `SpeechConfig.FromSubscription`dans le SDK Speech pour C#).

### <a name="regions-for-speech-recognition-and-translation"></a>Régions dédiées à la reconnaissance vocale et à la traduction

Le tableau suivant liste les régions disponibles pour la **reconnaissance vocale** et la **traduction** :

  Région | Paramètre du SDK Speech | Portail
 ------|-------|--------
 USA Ouest | `westus` | https://westus.cris.ai
 USA Ouest 2 | `westus2` | https://westus2.cris.ai 
 USA Est | `eastus` | https://eastus.cris.ai
 USA Est 2 | `eastus2` | https://eastus2.cris.ai
 Asie Est | `eastasia` | https://eastasia.cris.ai
 Asie Sud-Est | `southeastasia` | https://southeastasia.cris.ai
 Europe Nord | `northeurope` | https://northeurope.cris.ai
 Europe Ouest | `westeurope` | https://westeurope.cris.ai


### <a name="regions-for-intent-recognition"></a>Régions de reconnaissance de l’intention

Les régions disponibles pour la **reconnaissance de l’intention** via le SDK Speech sont listées dans la [page des régions LUIS](/azure/cognitive-services/luis/luis-reference-regions).
Pour chaque région de publication affichée, le paramètre de région du kit SDK Speech correspondant est défini comme étant la première partie du nom de domaine du point de terminaison.
Par exemple, utilisez `westus` pour spécifier la région de publication USA Ouest.
