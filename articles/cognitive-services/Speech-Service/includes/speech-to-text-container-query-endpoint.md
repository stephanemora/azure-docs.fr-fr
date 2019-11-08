---
title: Interroger le point de terminaison de conteneur Reconnaissance vocale
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: a55cf5ea6aa696d0cf0cdd619dc22839d748d83c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491156"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Reconnaissance vocale ou Reconnaissance vocale personnalisée

Le conteneur fournit des API de point de terminaison de requête s’appuyant sur WebSocket, qui sont accessibles par le biais du [Kit de développement logiciel (SDK) Speech](../index.md). Par défaut, le Kit de développement logiciel (SDK) Speech utilise des services vocaux en ligne. Pour utiliser le conteneur, vous devez changer la méthode d’initialisation.

> [!TIP]
> Lorsque vous utilisez le Kit de développement logiciel (SDK) Speech avec des conteneurs, vous n’avez pas besoin de fournir de [clé d’abonnement ou de jeton du porteur d’authentification](../rest-speech-to-text.md#authentication) de la ressource Azure Speech.

Consultez les exemples ci-dessous.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Passer de l’utilisation de cet appel d’initialisation du cloud Azure :

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

à cet appel à l’aide du [point de terminaison](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet) de conteneur :

```csharp
var config = SpeechConfig.FromEndpoint(
    new Uri("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1"));
```
# <a name="pythontabpython"></a>[Python](#tab/python)

Passer de l’utilisation de cet appel d’initialisation du cloud Azure :

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

à cet appel à l’aide du [point de terminaison](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) de conteneur :

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
```

***
