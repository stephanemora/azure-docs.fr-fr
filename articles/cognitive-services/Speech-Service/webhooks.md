---
title: Webhooks - Services de reconnaissance vocale
titlesuffix: Azure Cognitive Services
description: Webhooks sont HTTP idéale pour l’optimisation de votre solution lors du traitement de longue durée de rappels en cours d’exécution des processus tels que les importations, adaptation, tests de précision ou transcriptions des fichiers en cours d’exécution longue.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 3ceaed2b1e27a1f5b910865f6e9d0e70ef347b71
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60515388"
---
# <a name="webhooks-for-speech-services"></a>Webhooks pour les Services de reconnaissance vocale

Webhooks sont comme des rappels HTTP qui permettent à votre application accepter des données à partir des Services de reconnaissance vocale lorsqu’elle est disponible. À l’aide de webhooks, vous pouvez optimiser votre utilisation de nos API REST en éliminant le besoin d’interroger en continu pour une réponse. Dans les sections suivantes, vous allez apprendre à utiliser des webhooks avec les Services de reconnaissance vocale.

## <a name="supported-operations"></a>Opérations prises en charge

Les Services de reconnaissance vocale prend en charge les webhooks pour toutes les opérations de longue durée. Chacune des opérations ci-dessous peut déclencher un rappel HTTP à l’achèvement. 

* DataImportCompletion
* ModelAdaptationCompletion
* AccuracyTestCompletion
* TranscriptionCompletion
* EndpointDeploymentCompletion
* EndpointDataCollectionCompletion

Ensuite, nous allons créer un webhook.

## <a name="create-a-webhook"></a>Créer un webhook

Nous allons créer un webhook pour une transcription en mode hors connexion. Le scénario : un utilisateur dispose d’un fichier audio de longue durée qu’ils souhaiteraient de transcription de façon asynchrone avec l’API de Transcription de Batch. 

Pour créer un web raccordement POST https://<region>.cris.ai/api/speechtotext/v2.1/transcriptions/hooks

Paramètres de configuration de la demande sont fournies au format JSON :

```json
{
  "configuration": {
    "url": "https://your.callback.url/goes/here",
    "secret": "<my_secret>"
  },
  "events": [
    "TranscriptionCompletion"
  ],
  "active": true,
  "name": "TranscriptionCompletionWebHook",
  "description": "This is a Webhook created to trigger an HTTP POST request when my audio file transcription is completed.",
  "properties": {
      "Active" : "True"
  }

}
```
Toutes les demandes POST envoyées à l’API de Transcription de lot nécessitent un `name`. Le `description` et `properties` paramètres sont facultatifs.

Le `Active` propriété est utilisée pour basculer en rappelant dans votre URL sur et hors tension sans devoir supprimer et recréer l’inscription du webhook. Si vous devez uniquement effectuer un rappel une fois une fois le processus terminé, puis supprimer le webhook et le commutateur le `Active` false à la propriété.

Le type d’événement `TranscriptionCompletion` est fourni dans le tableau d’événements. Il rappelle à votre point de terminaison quand une transcription Obtient dans un état terminal (`Succeeded` ou `Failed`). Lors de l’appel précédent à l’URL inscrit, la demande contient un `X-MicrosoftSpeechServices-Event` en-tête contenant un des types d’événements inscrits. Il existe une demande par type d’événement enregistré. 

Il est impossible de s’abonner à un type d’événement. Il s’agit du `Ping` type d’événement. Une demande avec ce type est envoyée à l’URL une fois que la création d’un webhook lors de l’utilisation de l’URL de ping (voir ci-dessous).  

Dans la configuration, le `url` propriété est requise. Les requêtes POST sont envoyées à cette URL. Le `secret` est utilisé pour créer un hachage SHA256 de la charge utile, avec la clé secrète comme une clé HMAC. Le hachage est défini comme le `X-MicrosoftSpeechServices-Signature` en-tête lors de l’appel précédent à l’URL inscrit. Cet en-tête est codé en Base64.

Cet exemple montre comment valider un à l’aide de la charge utile C#:

```csharp

private const string EventTypeHeaderName = "X-MicrosoftSpeechServices-Event";
private const string SignatureHeaderName = "X-MicrosoftSpeechServices-Signature";

[HttpPost]
public async Task<IActionResult> PostAsync([FromHeader(Name = EventTypeHeaderName)]WebHookEventType eventTypeHeader, [FromHeader(Name = SignatureHeaderName)]string signature)
{
    string body = string.Empty;
    using (var streamReader = new StreamReader(this.Request.Body))
    {
        body = await streamReader.ReadToEndAsync().ConfigureAwait(false);
        var secretBytes = Encoding.UTF8.GetBytes("my_secret");
        using (var hmacsha256 = new HMACSHA256(secretBytes))
        {
            var contentBytes = Encoding.UTF8.GetBytes(body);
            var contentHash = hmacsha256.ComputeHash(contentBytes);
            var storedHash = Convert.FromBase64String(signature);
            var validated = contentHash.SequenceEqual(storedHash);
        }
    }
 
    switch (eventTypeHeader)
    {
        case WebHookEventType.Ping:
            // Do your ping event related stuff here (or ignore this event)
            break;
        case WebHookEventType.TranscriptionCompletion:
            // Do your subscription related stuff here.
            break;
        default:
            break;
    }
 
    return this.Ok();
}

```
Dans cet extrait de code, le `secret` est décodé et validé. Vous remarquerez également que le type d’événement webhook a été basculé. Actuellement est un événement par transcription terminée. Le code effectue une nouvelle tentative cinq fois pour chaque événement (avec un délai d’une seconde) avant d’abandonner.

### <a name="other-webhook-operations"></a>Autres opérations de webhook

Pour obtenir des webhooks tout inscrits : GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks

Pour obtenir un webhook spécifique : GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

Pour supprimer un webhook spécifique : DELETE https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

> [!Note] 
> Dans l’exemple ci-dessus, la région est 'westus'. Il doit être remplacé par la région où vous avez créé votre ressource de Services de reconnaissance vocale dans le portail Azure.

POST https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/ping corps : vide

Envoie une demande POST à l’URL inscrit. La demande contient un `X-MicrosoftSpeechServices-Event` en-tête avec une commande ping de valeur. Si le webhook a été inscrit avec une clé secrète, il contiendra un `X-MicrosoftSpeechServices-Signature` en-tête avec un hachage SHA256 de la charge utile avec la clé secrète comme clé HMAC. Le hachage est codé en Base64. 

POST https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/test corps : vide

Envoie une demande POST à l’URL inscrit si une entité pour le type d’événement abonné (transcription) est présente dans le système et qu’il est dans l’état approprié. La charge utile est générée à partir de la dernière entité qui peut avoir appelé le raccordement du web. Si aucune entité n’est présente, la publication répond avec 204. Si une demande de test peut être effectuée, il répond avec 200. Le corps de la demande est de la même forme que dans la demande GET pour une entité spécifique pour que le raccordement web s’est abonné à (par exemple la transcription). La demande de la `X-MicrosoftSpeechServices-Event` et `X-MicrosoftSpeechServices-Signature` en-têtes comme décrit précédemment.

### <a name="run-a-test"></a>Exécuter un test

Un test rapide peut être effectué via le site Web https://bin.webhookrelay.com. À partir de là, vous pouvez obtenir appel sauvegarder URL à passer comme paramètre à la requête HTTP POST pour la création d’un webhook décrit précédemment dans le document.

Cliquez sur « Créer un compartiment » et suivez l’à l’écran des instructions pour obtenir un raccordement. Puis utilisez les informations fournies dans cette page pour inscrire le raccordement auprès du service de reconnaissance vocale. La charge utile d’un relais de message - en réponse à l’achèvement d’une transcription-se présente comme suit :

```json
{
    "results": [],
    "recordingsUrls": [
        "my recording URL"
    ],
    "models": [
        {
            "modelKind": "AcousticAndLanguage",
            "datasets": [],
            "id": "a09c8c8b-1090-443c-895c-3b1cf442dec4",
            "createdDateTime": "2019-03-26T12:48:46Z",
            "lastActionDateTime": "2019-03-26T14:04:47Z",
            "status": "Succeeded",
            "locale": "en-US",
            "name": "v4.13 Unified",
            "description": "Unified",
            "properties": {
                "Purpose": "OnlineTranscription,BatchTranscription,LanguageAdaptation",
                "ModelClass": "unified-v4"
            }
        }
    ],
    "statusMessage": "None.",
    "id": "d41615e1-a60e-444b-b063-129649810b3a",
    "createdDateTime": "2019-04-16T09:35:51Z",
    "lastActionDateTime": "2019-04-16T09:38:09Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Simple transcription",
    "description": "Simple transcription description",
    "properties": {
        "PunctuationMode": "DictatedAndAutomatic",
        "ProfanityFilterMode": "Masked",
        "AddWordLevelTimestamps": "True",
        "AddSentiment": "True",
        "Duration": "00:00:02"
    }
}
```
Le message contient l’URL de l’enregistrement et permet de transcrire que l’enregistrement de modèles.

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
