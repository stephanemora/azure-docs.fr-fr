---
title: Effectuer une reconnaissance vocale à l’aide de l’API REST
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser l’API Reconnaissance vocale dans le service vocal Cognitive Services.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: erhopf
ms.openlocfilehash: ceb8e929520becd2bf097fefc9c6fe8959b1bf85
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51709812"
---
# <a name="recognize-speech-by-using-the-rest-api"></a>Effectuer une reconnaissance vocale à l’aide de l’API REST

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

L’API REST peut être utilisée pour reconnaître de courts énoncés à l’aide d’une requête HTTP POST.

L’API REST est la méthode la plus simple pour reconnaître la voix si le langage utilisé n’est pas pris en charge par le [Kit de développement logiciel (SDK)](speech-sdk.md). Vous effectuez une requête HTTP POST auprès du point de terminaison de service et transmettez l’intégralité de l’énoncé dans le corps de la requête. Vous recevez une réponse contenant le texte reconnu.

> [!NOTE]
> Les énoncés sont limités à 15 secondes maximum avec l’API REST.
> Découvrez le [kit de développement logiciel (SDK) Speech](how-to-recognize-speech-csharp.md) pour reconnaître les énoncés longs.

Pour plus d’informations sur l’API REST **Reconnaissance vocale**, consultez l’article [API REST](rest-apis.md#speech-to-text-api). Pour la voir en action, téléchargez les [Exemples API REST](https://github.com/Azure-Samples/SpeechToText-REST) sur GitHub.

## <a name="next-steps"></a>Étapes suivantes

- Voir [Vue d’ensemble de l’API REST](rest-apis.md).
