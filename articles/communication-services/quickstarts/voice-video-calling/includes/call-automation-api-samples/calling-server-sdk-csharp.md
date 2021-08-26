---
title: inclure fichier
description: C#
services: azure-communication-services
author: ravithanneeru
manager: joseys
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: joseys
ms.openlocfilehash: 1dae62ae0ab1fa03dd4c5e1128699cd43a241984
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802169"
---
## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous de :

- Créer un compte Azure avec un abonnement actif. Pour plus d’informations, consultez [Créer un compte gratuitement](https://azure.microsoft.com/free/).
- [Visual Studio (2019 et versions ultérieures)](https://visualstudio.microsoft.com/vs/).
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download/dotnet-framework/net472) (Veillez à installer la version qui correspond à votre instance de Visual Studio, 32 ou 64 bits).
- Créer une ressource Azure Communication Services. Pour plus d’informations, consultez [Créer des ressources Azure Communication Services](../../../create-communication-resource.md). Vous devez noter la **chaîne de connexion** de votre ressource pour cet exemple.
- Obtenir un numéro de téléphone pour votre nouvelle ressource Azure Communication Services. Pour plus d’informations, consultez [Obtenir un numéro de téléphone](../../../telephony-sms/get-phone-number.md?pivots=platform-azp).
- Télécharger et installer [ngrok](https://www.ngrok.com/download). L’exemple étant exécuté localement, ngrok autorisera la réception de tous les événements.
- (Facultatif) Créer une ressource Azure Speech pour générer un message personnalisé qui doit être lu par l’application. Suivez [ce guide](../../../../../cognitive-services/speech-service/overview.md#try-the-speech-service-for-free) pour créer la ressource.

> [!NOTE]
> Vous trouverez le code finalisé pour ce guide de démarrage rapide sur [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/OutboundCallReminder). Cet exemple utilise le kit SDK Microsoft Cognitive Services Speech. En téléchargeant le SDK Microsoft Cognitive Services Speech, vous reconnaissez son utilisation sous [licence](https://aka.ms/csspeech/license201809).

## <a name="object-model"></a>Modèle objet

Les classes suivantes gèrent quelques-unes des principales fonctionnalités du SDK Azure Communication Calling Server pour C#.

| Nom                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallingServerClient | Cette classe est nécessaire à la fonctionnalité d’appel. Vous créez une instance de CallingServerClient à l’aide de votre chaîne de connexion de ressource Communication Services, et vous l’utilisez pour démarrer/terminer un appel, lire/annuler l’audio et ajouter/supprimer des participants |
| CommunicationIdentityClient | Cette classe est nécessaire pour créer ou supprimer une identité d’utilisateur de communication. |

## <a name="create-a-call-client"></a>Créer un client d’appel

Pour créer un client d’appel, vous allez utiliser votre chaîne de connexion Communication Services et la passer à votre objet client d’appel.

```csharp
this.CallClient = new CallingServerClient("<Connection_String>");
```

## <a name="create-user-identity"></a>Créer une identité utilisateur

Utilisez la chaîne de connexion de la ressource Communication Services pour créer un objet `CommunicationIdentityClient`, puis utilisez la méthode `CreateUserAsync` pour créer une identité utilisateur source.

```csharp
var client = new CommunicationIdentityClient("<Connection_String>");
var user = await client.CreateUserAsync().ConfigureAwait(false);
```

## <a name="delete-user-identity"></a>Supprimer une identité utilisateur

Utilisez la méthode `DeleteUserAsync` de l’objet `CommunicationIdentityClient` pour supprimer une identité utilisateur :

```csharp
var client = new CommunicationIdentityClient("<Connection_String>");
await client.DeleteUserAsync(new CommunicationUserIdentifier(source)).ConfigureAwait(false);
```

## <a name="create-a-call"></a>Créer un appel

Utilisez la méthode `CreateCallConnectionAsync` de l’objet `CallingServerClient` pour démarrer un appel. Les paramètres de la méthode `CreateCallConnectionAsync` sont les suivants :
- `source` est l’identité utilisateur source de l’appelant de type `CommunicationIdentifier`.
- `targets` est la liste des identités cibles de type `IEnumerable<CommunicationIdentifier>`.
- `options` correspond aux options pour l’appel de type `CreateCallOptions`. Les paramètres de la méthode `CreateCallOptions` sont les suivants :

    - `callbackUri` est l’URI de rappel de l’application.
    - `requestedModalities` est la modalité d’appel de requête. Le type est `IEnumerable<MediaType>`. Les valeurs peuvent être Audio ou Video.
    - `requestedCallEvents` correspond aux événements de rappel demandés. Il s’agit du type de `IEnumerable<EventSubscriptionType>`. Utilisez ce paramètre pour vous abonner aux événements `EventSubscriptionType.ParticipantsUpdated` et `EventSubscriptionType.DtmfReceived`.

- `cancellationToken` est le jeton d'annulation. Le type est `CancellationToken`.

```csharp
var source = new CommunicationUserIdentifier(callConfiguration.SourceIdentity);
var target = new PhoneNumberIdentifier(targetPhoneNumber);
var createCallOption = new CreateCallOptions(
    new Uri(callConfiguration.AppCallbackUrl),
    new List<MediaType> { MediaType.Audio },
    new List<EventSubscriptionType> { EventSubscriptionType.ParticipantsUpdated, EventSubscriptionType.DtmfReceived }
    );
createCallOption.AlternateCallerId = new PhoneNumberIdentifier(callConfiguration.SourcePhoneNumber);

Logger.LogMessage(Logger.MessageType.INFORMATION, "Performing CreateCall operation");
var call = await callClient.CreateCallConnectionAsync(source,
    new List<CommunicationIdentifier>() { target },
    createCallOption, reportCancellationToken)
    .ConfigureAwait(false);
```

La méthode `CreateCallConnectionAsync` retourne un objet `CallConnection`, qui peut être utilisé pour d’autres opérations d’appel comme lire l’audio, annuler l’audio et raccrocher.

## <a name="play-audio"></a>Lire un fichier audio

Une fois qu’une connexion d’appel est créée, vous pouvez utiliser la méthode `PlayAudioAsync` de l’objet `CallConnection` afin de lire un message audio pour l’appelé. Les paramètres suivants sont pris en charge par la méthode `PlayAudioAsync` :

- `options`, obligatoire, correspond aux options de lecture audio. Le type est `PlayAudioOptions`. Les paramètres suivants sont pris en charge par l’objet `PlayAudioOptions` :
    - `AudioFileUri`, obligatoire, est l’URI du fichier audio contenant le message à lire.
    - `OperationContext`, obligatoire, est l’ID unique du contexte de la requête.
    - `Loop` (facultatif) ; affectez la valeur true pour répéter le message audio.
- `cancellationToken` est le jeton d'annulation. Le type est `CancellationToken`.

```csharp
// Preparing data for request
var playAudioRequest = new PlayAudioOptions()
{
    AudioFileUri = new Uri(callConfiguration.AudioFileUrl),
    OperationContext = Guid.NewGuid().ToString(),
    Loop = true,
};

Logger.LogMessage(Logger.MessageType.INFORMATION, "Performing PlayAudio operation");
var response = await callConnection.PlayAudioAsync(playAudioRequest, reportCancellationToken).ConfigureAwait(false);
```

La méthode `PlayAudioAsync` retourne `PlayAudioResponse`, qui peut être utilisé pour obtenir `OperationStatus`. `OperationStatus` peut avoir les valeurs suivantes : `NotStarted`, `Running`, `Completed` ou `Failed`.

```csharp
Logger.LogMessage(Logger.MessageType.INFORMATION, $"Play Audio state: {response.Value.Status}");
```

## <a name="add-a-participant-to-the-call"></a>Ajouter un participant à l’appel

Utilisez la méthode `AddParticipantAsync` de l’objet `CallConnection` pour ajouter un participant à l’appel. Les paramètres suivants sont pris en charge par la méthode `AddParticipantAsync` :

- `participant` est l’identificateur du participant de type `CommunicationUserIdentifier` ou `PhoneNumberIdentifier`.
- `alternateCallerId` est l’ID de l’appelant source.
- `operationContext` est l’ID unique du contexte de la requête.
- `cancellationToken`, facultatif, est le jeton d’annulation.


```csharp
// Preparing data for request
var operationContext = Guid.NewGuid().ToString();

if (identifierKind == CommunicationIdentifierKind.UserIdentity)
{
    var response = await callConnection.AddParticipantAsync(new CommunicationUserIdentifier(addedParticipant), null, operationContext).ConfigureAwait(false);
    Logger.LogMessage(Logger.MessageType.INFORMATION, $"PlayAudioAsync response --> {response}");
}
else if (identifierKind == CommunicationIdentifierKind.PhoneIdentity)
{
    var response = await callConnection.AddParticipantAsync(new PhoneNumberIdentifier(addedParticipant), callConfiguration.SourcePhoneNumber, operationContext).ConfigureAwait(false);
    Logger.LogMessage(Logger.MessageType.INFORMATION, $"PlayAudioAsync response --> {response}");
}
```

## <a name="cancel-media-processing"></a>Annuler le traitement multimédia

Utilisez la méthode `CancelAllMediaOperationsAsync` de l’objet `CallConnection` pour annuler l’opération de lecture audio. Les paramètres suivants sont pris en charge pour la méthode `CancelAllMediaOperationsAsync` :

- `operationContext` est l’ID unique du contexte de la requête.
- `reportCancellationToken` est le jeton d'annulation. Le type est `CancellationToken`.

```csharp
var operationContext = Guid.NewGuid().ToString();
var response = await callConnection.CancelAllMediaOperationsAsync(operationContext, reportCancellationToken).ConfigureAwait(false);
```

## <a name="hang-up-the-call"></a>Mettre fin à l’appel

Utilisez la méthode `HangupAsync` de l’objet `CallConnection` pour mettre fin à l’appel. Les paramètres suivants sont pris en charge pour la méthode `HangupAsync` :

- `reportCancellationToken` est le jeton d'annulation. Le type est `CancellationToken`.

```csharp
var hangupResponse = await callConnection.HangupAsync(reportCancellationToken).ConfigureAwait(false);
```