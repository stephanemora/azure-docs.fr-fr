---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: e920ea3cd91f6275c4cb52dc070b0df60312d525
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92470892"
---
Le conteneur fournit des API de point de terminaison de requête s’appuyant sur WebSocket, qui sont accessibles par le biais du [Kit de développement logiciel (SDK) Speech](../index.yml). Par défaut, le Kit de développement logiciel (SDK) Speech utilise des services vocaux en ligne. Pour utiliser le conteneur, vous devez changer la méthode d’initialisation.

> [!TIP]
> Lorsque vous utilisez le Kit de développement logiciel (SDK) Speech avec des conteneurs, vous n’avez pas besoin de fournir de [clé d’abonnement ou de jeton du porteur d’authentification](../rest-speech-to-text.md#authentication) de la ressource Azure Speech.

Considérons les exemples ci-dessous.

# <a name="c"></a>[C#](#tab/csharp)

Passer de l’utilisation de cet appel d’initialisation du cloud Azure :

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Pour utiliser cet appel avec l’[hôte](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet&preserve-view=true) du conteneur :

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```

# <a name="python"></a>[Python](#tab/python)

Passer de l’utilisation de cet appel d’initialisation du cloud Azure :

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

Pour utiliser cet appel avec le [point de terminaison](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python&preserve-view=true) de conteneur :

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/conversation/cognitiveservices/v1"
```

---
