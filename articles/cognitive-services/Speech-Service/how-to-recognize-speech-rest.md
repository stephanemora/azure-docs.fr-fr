---
title: Effectuer une reconnaissance vocale à l’aide de l’API REST
description: En savoir plus sur l’utilisation de la reconnaissance vocale du service Speech
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: 15db2d9b872c76d70fd531af07fb55c701e86494
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39331279"
---
# <a name="recognize-speech-by-using-the-rest-api"></a>Effectuer une reconnaissance vocale à l’aide de l’API REST

[!include[Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

L’API REST peut être utilisée pour reconnaître les énoncés courts à l’aide d’une requête HTTP POST.

L’API REST est la méthode la plus simple pour reconnaître la voix si vous n’utilisez pas un langage pris en charge par le Kit de développement logiciel (SDK).
Vous effectuez une requête HTTP POST sur le point de terminaison de service, en transmettant l’intégralité de l’énoncé dans le corps de la requête.
Vous recevez une réponse contenant le texte reconnu.

> [!NOTE]
> Les énoncés sont limités à 15 secondes maximum lors de l’utilisation de l’API REST.
> Découvrez le [kit de développement logiciel (SDK) Speech](how-to-recognize-speech-csharp.md) pour reconnaître les énoncés longs.

Pour plus d’informations sur l’API REST **Reconnaissance vocale**, consultez l’article sur les [API REST](rest-apis.md#speech-to-text). Pour le voir en action, téléchargez les [exemples API REST](https://github.com/Azure-Samples/SpeechToText-REST) à partir de GitHub.

## <a name="next-steps"></a>Étapes suivantes

- [Consulter la section Vue d’ensemble de l’API REST](rest-apis.md)
