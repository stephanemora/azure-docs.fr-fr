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
ms.openlocfilehash: a5fce6f9547a96da3ce482ce388e5ba2093f2af4
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49468124"
---
# <a name="regions-of-the-speech-service"></a>Régions du service Speech

Le service Speech est disponible dans différentes régions.
Lorsque vous créez un abonnement, vous pouvez sélectionner l’une des régions disponibles, selon vos besoins.

Lorsque vous utilisez votre abonnement, vous devez prendre en compte la région que vous avez sélectionnée.

## <a name="rest-api"></a>API REST

Utilisez l’API REST pour sélectionner les points de terminaison correspondant à la région choisie.
Consultez [API REST](rest-apis.md) pour plus d’informations.

## <a name="speech-sdk"></a>Kit de développement logiciel (SDK) de reconnaissance vocale

Dans le [Kit de développement logiciel (SDK) Speech](speech-sdk.md), les régions sont spécifiées sous forme de chaîne (par exemple, en tant que paramètre `SpeechConfig.FromSubscription` dans le Kit de développement logiciel (SDK) Speech pour C#).

### <a name="regions-for-speech-recognition-and-translation"></a>Régions dédiées à la reconnaissance vocale et à la traduction

Le tableau suivant liste les régions disponibles pour la **reconnaissance vocale** et la **traduction** :

  Région | Paramètre du SDK Speech | Portail
 ------|-------|--------
 USA Ouest | `westus` | https://westus.cris.ai
 Ouest des États-Unis 2 | `westus2` | https://westus2.cris.ai 
 USA Est | `eastus` | https://eastus.cris.ai
 Est des États-Unis 2 | `eastus2` | https://eastus2.cris.ai
 Asie Est | `eastasia` | https://eastasia.cris.ai
 Asie Sud-Est | `southeastasia` | https://southeastasia.cris.ai
 Europe Nord | `northeurope` | https://northeurope.cris.ai
 Europe Ouest | `westeurope` | https://westeurope.cris.ai


### <a name="regions-for-intent-recognition"></a>Régions de reconnaissance de l’intention

Les régions disponibles pour la **reconnaissance de l’intention** via le SDK Speech sont listées dans la [page des régions LUIS](/azure/cognitive-services/luis/luis-reference-regions).
Pour chaque région de publication affichée, le paramètre de région du Kit de développement logiciel (SDK) de reconnaissance vocale correspondant est défini comme étant la première partie du nom de domaine du point de terminaison.
Par exemple, utilisez `westus` pour spécifier la région de publication USA Ouest.
