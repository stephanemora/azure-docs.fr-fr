---
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 6041f49f613b5eb1367dce0982eaaf041285ffea
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525535"
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

Pour utiliser cet appel avec l’[hôte](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost) du conteneur :

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

Pour utiliser cet appel avec le [point de terminaison](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) de conteneur :

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/conversation/cognitiveservices/v1"
```

---
