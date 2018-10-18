---
title: Utiliser un point de terminaison vocal personnalisé avec Custom Speech Service sur Azure | Microsoft Docs
description: Découvrez comment créer un point de terminaison de reconnaissance vocale personnalisé avec Custom Speech Service dans Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: 9f1a21d6f099bc27c81e8c254f7df73d6c3e585d
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341798"
---
# <a name="use-a-custom-speech-to-text-endpoint"></a>Utiliser un point de terminaison de reconnaissance vocale personnalisé

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Vous pouvez envoyer des requêtes à un point de terminaison de reconnaissance vocale Azure Custom Speech Service de la même façon que vous pouvez le faire sur le point de terminaison vocal Cognitive Services par défaut. Ces points de terminaison sont identiques en termes de fonction aux points de terminaison par défaut de l’API Microsoft Speech. Ainsi, cette même fonctionnalité, disponible via la bibliothèque de client ou l’API REST pour l’API Microsoft Speech, est également disponible pour votre point de terminaison personnalisé.

Les points de terminaison que vous créez à l’aide de ce service peuvent traiter différentes quantités de requêtes simultanées. Le volume varie selon le niveau tarifaire de votre abonnement. Si trop de requêtes sont reçues, une erreur se produit. Le niveau gratuit a une limite mensuelle des requêtes.

Le service suppose que les données sont transmises en temps réel. Si elles sont envoyées plus rapidement, la requête est considérée comme en cours d’exécution jusqu’à la fin de sa durée audio en temps réel.

> [!NOTE]
> Nous prenons déjà en charge les [nouveaux sockets web](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol). Si vous envisagez d’utiliser des sockets web avec votre point de terminaison vocal personnalisé, suivez les instructions présentées ici.
>
> La prise en charge de la nouvelle [API REST](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest) sera bientôt disponible. Si vous envisagez d’appeler votre point de terminaison vocal personnalisé via HTTP, suivez les instructions présentées ici.
>

## <a name="send-requests-by-using-the-speech-client-library"></a>Envoyer des requêtes à l’aide de la bibliothèque de client Speech

Pour envoyer des requêtes à votre point de terminaison personnalisé à l’aide de la bibliothèque de client Speech, démarrez le client de reconnaissance. Utilisez le Kit de développement logiciel (SDK) Speech du client à partir de [NuGet](http://nuget.org/). Recherchez *la reconnaissance vocale*, puis sélectionnez le package de reconnaissance vocale à partir de Microsoft pour votre plateforme. Des exemples de code source sont disponibles sur [GitHub](https://github.com/Microsoft/Cognitive-Speech-STT-Windows). Le Kit de développement logiciel (SDK) Speech du client fournit une classe de fabrique **SpeechRecognitionServiceFactory** proposant les méthodes suivantes :

  *   ```CreateDataClient(...)``` : un client de reconnaissance de données.
  *   ```CreateDataClientWithIntent(...)``` : un client de reconnaissance de données avec une intention.
  *   ```CreateMicrophoneClient(...)``` : un client de reconnaissance de microphone.
  *   ```CreateMicrophoneClientWithIntent(...)``` : un client de reconnaissance de microphone avec une intention.

Pour avoir accès à la documentation détaillée, reportez-vous à l’article [Vue d’ensemble de l’API Microsoft Speech](https://docs.microsoft.com/azure/cognitive-services/speech/home). Les points de terminaison Custom Speech Service prennent en charge le même Kit de développement logiciel (SDK).

Le client de reconnaissance de données est approprié pour la reconnaissance vocale à partir de données, comme un fichier ou une autre source audio. Le client de reconnaissance de microphone est approprié pour la reconnaissance vocale à partir du microphone. L’utilisation de l’intention dans un client peut renvoyer des résultats d’intention structurés à partir de [Language Understanding Intelligent Service](https://www.luis.ai/) (LUIS), si vous avez créé une application LUIS pour votre scénario.

Les quatre types de clients peuvent être instanciés de deux manières. La première consiste à utiliser l’API Microsoft Speech de Cognitive Services standard. La deuxième consiste à spécifier une URL qui correspond à votre point de terminaison personnalisé créé avec Custom Speech Service.

Par exemple, vous pouvez créer un élément **DataRecognitionClient** qui envoie des requêtes à un point de terminaison personnalisé à l’aide de la méthode suivante :

```csharp
public static DataRecognitionClient CreateDataClient(SpeeechRecognitionMode speechRecognitionMode, string language, string primaryOrSecondaryKey, **string url**);
```

Les éléments **your_subscriptionId** et **endpointURL** font référence à la clé d’abonnement et à l’URL des sockets web respectivement sur la page **Informations de déploiement**.

L’élément **AuthenticationUri** est utilisé pour recevoir un jeton issu du service d’authentification. Cet URI doit être défini séparément, comme indiqué dans l’exemple de code suivant.

Cet exemple de code montre comment utiliser le Kit de développement logiciel (SDK) du client :

```csharp
var dataClient = SpeechRecognitionServiceFactory.CreateDataClient(
  SpeechRecognitionMode.LongDictation,
  "en-us",
  "your_subscriptionId",
  "your_subscriptionId",
  "endpointURL");
// set the authorization Uri
dataClient.AuthenticationUri = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
```

> [!NOTE]
> Lorsque vous utilisez des méthodes **Create** dans le Kit de développement logiciel (SDK), vous devez fournir l’ID d’abonnement à deux reprises en raison de la surcharge des méthodes **Create**.
>

Custom Speech Service utilise deux URL différentes pour la reconnaissance de forme courte et de forme longue. Celles-ci sont répertoriées sur la page **Déploiements**. Utilisez l’URL de point de terminaison adaptée à la forme que vous souhaitez utiliser.

Pour plus d’informations sur l’appel de différents clients de reconnaissance avec votre point de terminaison personnalisé, consultez la classe [SpeechRecognitionServiceFactory](https://www.microsoft.com/cognitive-services/Speech-api/documentation/GetStarted/GetStartedCSharpDesktop). La documentation sur cette page fait référence à l’adaptation du modèle acoustique, mais elle s’applique à tous les points de terminaison créés à l’aide de Custom Speech Service.

## <a name="send-requests-by-using-the-speech-protocol"></a>Envoyer des requêtes à l’aide du protocole Speech

Les points de terminaison indiqués pour le [protocole Speech](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol) sont des points de terminaison pour le protocole Speech de socket web open source.

Actuellement, la seule implémentation de client officielle qui existe est destinée à [JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). Si vous souhaitez vous lancer avec l’exemple fourni ici, modifiez le code comme suit et créez une nouvelle fois l’exemple :

1. Dans _src\sdk\speech.browser\SpeechConnectionFactory.ts_, remplacez le nom d’hôte « wss://speech.platform.bing.com » par le nom d’hôte indiqué sur la page d’informations de votre déploiement. N’insérez pas l’URI complet ici, mais uniquement le nom d’hôte et le schéma de protocole *wss*. Par exemple : 

    ```JavaScript
    private get Host(): string {
        return Storage.Local.GetOrAdd("Host", "wss://<your_key_goes_here>.api.cris.ai");
    }
    ```

2. Définissez le paramètre _recognitionMode_ dans _samples\browser\Samples.html_ selon vos exigences :
    * _RecognitionMode.Interactive_ prend en charge les requêtes de 15 secondes maximum.
    * _RecognitionMode.Conversation_ et _RecognitionMode.Dictation_ (les deux sont équivalents dans Custom Speech Service) prennent en charge les requêtes de 10 minutes maximum.

3. Générez l’exemple à l’aide de « gulp build » avant de l’utiliser.

> [!NOTE]
> Veillez à utiliser l’URI approprié pour ce protocole. Le schéma requis est *wss* (pas *http* comme dans le protocole de client). 

Pour plus d’informations, consultez la documentation [de l’API Reconnaissance vocale Bing](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedclientlibraries).

## <a name="send-requests-by-using-http"></a>Envoyer des requêtes à l’aide de HTTP

L’envoi d’une requête à votre point de terminaison personnalisé à l’aide de HTTP post est identique à l’envoi d’une requête par HTTP à l’API Reconnaissance vocale Bing de Cognitive Services. Modifiez l’URL pour refléter l’adresse de votre déploiement personnalisé.

Il existe certaines restrictions sur les requêtes envoyées via HTTP pour le point de terminaison Speech de Cognitive Services et les points de terminaison personnalisés créés avec ce service. La requête HTTP ne peut pas renvoyer de résultats partiels au cours du processus de reconnaissance. En outre, la durée des requêtes est limitée à 10 secondes pour le contenu audio et à 14 secondes en général.

Pour créer une requête post, suivez le même processus que celui utilisé pour l’API Microsoft Speech de Cognitive Services.

1. Récupérez un jeton d’accès à l’aide de votre ID d’abonnement. Ce jeton est nécessaire pour accéder au point de terminaison de reconnaissance. Il peut être réutilisé pendant 10 minutes.

    ```
    curl -X POST --header "Ocp-Apim-Subscription-Key:<subscriptionId>" --data "" "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    ```
      **subscriptionId** doit être défini sur l’ID de l’abonnement que vous utilisez pour ce déploiement. La réponse est le jeton simple dont vous avez besoin pour la requête suivante.

2. Envoyez une nouvelle fois l’audio au point de terminaison à l’aide de POST.

    ```
    curl -X POST --data-binary @example.wav -H "Authorization: Bearer <token>" -H "Content-Type: application/octet-stream" "<https_endpoint>"
    ```

    Le **jeton** est le jeton d’accès que vous avez reçu avec l’appel précédent. **https_endpoint** correspond à l’adresse complète de votre point de terminaison de reconnaissance vocale personnalisé, présenté sur la page **Informations de déploiement**.

Pour plus d’informations sur les paramètres de HTTP post et le format de réponse, consultez la page sur [l’API HTTP Reconnaissance vocale Bing de Microsoft Cognitive Services](https://www.microsoft.com/cognitive-services/speech-api/documentation/API-Reference-REST/BingVoiceRecognition#SampleImplementation).

## <a name="send-requests-by-using-the-service-library"></a>Envoyer des requêtes à l’aide de la bibliothèque du service
La bibliothèque du service permet à votre service d’utiliser le cloud de transcription Microsoft Speech pour convertir la langue parlée en texte, et ce en temps réel. Votre application de client peut ensuite envoyer l’audio et recevoir des résultats de reconnaissance partiels de façon simultanée et asynchrone. Vous pouvez trouver des informations sur le Kit de développement logiciel (SDK) [ici](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedcsharpservicelibrary)

> [!NOTE]
> Lors de l’utilisation de la bibliothèque du service, vous devez modifier l’URI du fournisseur d’autorisation dans l’implémentation de **IAuthorizationProvider** sur https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken.

## <a name="next-steps"></a>Étapes suivantes
* Améliorez la précision avec votre [modèle acoustique personnalisé](cognitive-services-custom-speech-create-acoustic-model.md).
* Améliorez la précision avec un [modèle de langage personnalisé](cognitive-services-custom-speech-create-language-model.md).
