---
title: Webhooks – Service Speech
titleSuffix: Azure Cognitive Services
description: Les webhooks sont des rappels HTTP parfaits pour optimiser votre solution quand vous avez affaire à des traitements de longue durée comme les importations, les adaptations, les tests de précision ou les transcriptions de fichiers volumineux.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 3d07e540bf88c956f61b5d3b2a98702cad616985
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558805"
---
# <a name="webhooks-for-speech-services"></a>Webhooks pour Speech Services

Les webhooks sont comparables à des rappels HTTP qui permettent à votre application d’accepter des données en provenance de Speech Services quand celles-ci deviennent disponibles. En utilisant des webhooks, vous pouvez optimiser l’utilisation de nos API REST sans être contraint de chercher à obtenir une réponse par des interrogations constantes. Dans les sections suivantes, vous allez découvrir comment utiliser les webhooks avec Speech Services.

## <a name="supported-operations"></a>Opérations prises en charge

Speech Services prend en charge les webhooks pour toutes les opérations de longue durée. Chacune des opérations listées ci-dessous peut déclencher un rappel HTTP une fois qu’elle a abouti.

* DataImportCompletion
* ModelAdaptationCompletion
* AccuracyTestCompletion
* TranscriptionCompletion
* EndpointDeploymentCompletion
* EndpointDataCollectionCompletion

Maintenant, créons un webhook.

## <a name="create-a-webhook"></a>Créer un webhook

Nous allons créer un webhook pour une transcription en mode hors connexion. Le scénario : un utilisateur dispose d’un fichier audio de longue durée qu’il souhaite transcrire de façon asynchrone avec l’API de transcription Batch.

Des webhooks peuvent être créés en adressant une requête POST à https://\<région\>.cris.ai/api/speechtotext/v2.1/transcriptions/hooks.

Les paramètres de configuration de la requête sont fournis au format JSON :

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
Toutes les requêtes POST adressées à l’API de transcription Batch nécessitent un nom (`name`). Les paramètres `description` et `properties` sont facultatifs.

La propriété `Active` sert à activer et à désactiver le rappel dans votre URL sans avoir à supprimer et recréer l’inscription du webhook. Si vous n’avez besoin que d’un seul rappel à l’issue du processus, supprimez le webhook et définissez la propriété `Active` sur false.

Le type d’événement `TranscriptionCompletion` est fourni dans le tableau des événements. Il rappelle votre point de terminaison quand une transcription obtient un état terminal (`Succeeded` ou `Failed`). Quand le rappel s’adresse à l’URL inscrite, la requête comporte un en-tête `X-MicrosoftSpeechServices-Event` contenant un des types d’événement inscrits. À chaque type d’événement inscrit correspond une requête.

Il y a un type d’événement auquel vous ne vous pouvez pas vous abonner. Il s’agit du type d’événement `Ping`. Une requête associée à ce type est envoyée à l’URL une fois qu’un webhook est créé et que l’URL ping est utilisée (voir ci-dessous).  

Dans la configuration, la propriété `url` est obligatoire. Les requêtes POST sont envoyées à cette URL. Le `secret` est utilisé pour créer un hachage SHA256 de la charge utile, le secret étant une clé HMAC. Le hachage est défini comme en-tête `X-MicrosoftSpeechServices-Signature` lors d’un rappel à l’URL inscrite. Cet en-tête est encodé en Base64.

Cet exemple montre comment valider une charge utile en utilisant C# :

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
Dans cet extrait de code, le `secret` est décodé et validé. Vous remarquerez aussi que le type d’événement webhook a basculé. Pour l’heure, à chaque transcription terminée correspond un événement. Le code effectue une cinq nouvelles tentatives pour chaque événement (avec un décalage d’une seconde) avant d’abandonner.

### <a name="other-webhook-operations"></a>Autres opérations de webhook

Pour obtenir tous les webhooks inscrits : GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks

Pour obtenir un webhook spécifique : GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

Pour supprimer un webhook spécifique : DELETE https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

> [!Note]
> Dans l’exemple ci-dessus, la région est « westus ». Cette valeur doit être remplacée par la région dans laquelle vous avez créé votre ressource Speech Services sur le portail Azure.

POST https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/ping Body : empty

Envoie une requête POST à l’URL inscrite. La requête contient un en-tête `X-MicrosoftSpeechServices-Event` avec une valeur ping. Si le webhook a été inscrit avec un secret, il contient un en-tête `X-MicrosoftSpeechServices-Signature` avec un hachage SHA256 de la charge utile, le secret étant une clé HMAC. Le hachage est encodé en Base64.

POST https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/test Body : empty

Envoie une requête POST à l’URL inscrite si une entité correspondant au type d’événement abonné (transcription) est présente dans le système et qu’elle est dans l’état approprié. La charge utile est générée à partir de la dernière entité qui aurait appelé le webhook. Si aucune entité n’est présente, la requête POST répond par 204. Si une requête de test peut être effectuée, elle répond par 200. Le corps de la requête présente la même forme que dans la requête GET pour une entité spécifique à laquelle le webhook s’est abonné (par exemple, la transcription). La requête comporte les en-têtes `X-MicrosoftSpeechServices-Event` et `X-MicrosoftSpeechServices-Signature` comme indiqué précédemment.

### <a name="run-a-test"></a>Exécuter un test

Un test rapide peut être effectué via le site web https://bin.webhookrelay.com. De là, vous pouvez obtenir les URL de rappel à transmettre comme paramètre à la requête POST HTTP pour créer un webhook décrit précédemment dans le document.

Cliquez sur « Créer un compartiment » et suivez les instructions à l’écran pour obtenir un webhook. Utilisez ensuite les informations fournies dans cette page pour inscrire le webhook auprès du service Speech. La charge utile d’un message de relais – en réponse à l’achèvement d’une transcription – se présente comme suit :

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
Le message contient l’URL d’enregistrement et les modèles utilisés pour transcrire cet enregistrement.

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
