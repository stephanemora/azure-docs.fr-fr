---
title: inclure fichier
description: Automatisation d’appel Java
services: azure-communication-services
author: ravithanneeru
manager: joseys
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: joseys
ms.openlocfilehash: dc674a7e013ed39bd04161cb7e96dc969eaf47eb
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114473188"
---
## <a name="prerequisites"></a>Prérequis
Avant de commencer, assurez-vous de :
- Créer un compte Azure avec un abonnement actif. Pour plus d’informations, consultez [Créer un compte gratuitement](https://azure.microsoft.com/free/).
- [Java Development Kit (JDK)](/azure/developer/java/fundamentals/java-jdk-install) version 11 ou ultérieure.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Créer une ressource Azure Communication Services. Pour plus d’informations, consultez [Créer des ressources Azure Communication Services](https://docs.microsoft.com/azure/communication-services/quickstarts/create-communication-resource). Vous devez noter la **chaîne de connexion** de votre ressource pour cet exemple.
- Obtenir un numéro de téléphone pour votre nouvelle ressource Azure Communication Services. Pour plus d’informations, consultez [Obtenir un numéro de téléphone](https://docs.microsoft.com/azure/communication-services/quickstarts/telephony-sms/get-phone-number?pivots=platform-azp).
- Télécharger et installer [ngrok](https://www.ngrok.com/download). L’exemple étant exécuté localement, ngrok autorisera la réception de tous les événements.
- (Facultatif) Créer une ressource Azure Speech pour générer un message personnalisé qui doit être lu par l’application. Suivez [ce guide](../../../../../cognitive-services/speech-service/overview.md#try-the-speech-service-for-free) pour créer la ressource.

> [!NOTE]
> Vous trouverez le code finalisé pour ce guide de démarrage rapide sur [GitHub](https://github.com/Azure-Samples/communication-services-java-quickstarts/tree/main/OutboundCallReminder). Cet exemple utilise le kit SDK Microsoft Cognitive Services Speech. En téléchargeant le SDK Microsoft Cognitive Services Speech, vous reconnaissez son utilisation sous [licence](https://aka.ms/csspeech/license201809).

## <a name="add-the-package-references-for-the-calling-server-sdk"></a>Ajouter les références de package pour le SDK du serveur appelant

Dans votre fichier POM, référencez le package `azure-communication-callingserver` avec les API d’appel :

```java
<dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-communication-callingserver</artifactId>
      <version>1.0.0-beta.2</version>
</dependency>
```

Pour créer l’identité utilisateur, votre application doit référencer le package `azure-communication-identity` :

```java
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-communication-identity</artifactId>
      <version>1.1.1</version>
      <exclusions>
        <exclusion>
          <groupId>com.azure</groupId>
          <artifactId>azure-communication-common</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
```

## <a name="object-model"></a>Modèle objet

Les classes suivantes gèrent quelques-unes des principales fonctionnalités du SDK Azure Communication Calling Server pour Java.

| Nom                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallingServerClientBuilder | Cette classe sert à créer une instance de CallingServerClient.|
| CallingServerClient | Cette classe est nécessaire à la fonctionnalité d’appel. Vous obtenez une instance via CallingServerClientBuilder, et vous l’utilisez pour démarrer/terminer un appel, lire/annuler l’audio et ajouter/supprimer des participants. |
| CommunicationIdentityClient | Cette classe est nécessaire pour créer ou supprimer une identité d’utilisateur de communication. |

## <a name="create-a-call-client"></a>Créer un client d’appel

Une chaîne de connexion Communication Services et un objet httpClient doivent être passés au `CallingServerClientBuilder`, respectivement par le biais de fonctions `connectionString()` et `httpClient()`. Pour créer un objet client d’appel, utilisez la fonction `buildClient()` de l’objet `CallingServerClientBuilder`.

```java
NettyAsyncHttpClientBuilder httpClientBuilder = new NettyAsyncHttpClientBuilder();
CallingServerClientBuilder callClientBuilder = new CallingServerClientBuilder().httpClient(httpClientBuilder.build())
        .connectionString(this.callConfiguration.ConnectionString);

this.callingServerClient = callClientBuilder.buildClient();
```

## <a name="create-user-identity"></a>Créer une identité utilisateur

Utilisez l’objet `CommunicationIdentityClientBuilder` et définissez la chaîne de connexion de la ressource Communication Services par le biais de la fonction `connectionString()` pour créer un objet `CommunicationIdentityClient`, puis utilisez la fonction `createUser` pour créer une identité utilisateur source.

```java
CommunicationIdentityClient client = new CommunicationIdentityClientBuilder()
        .connectionString("<Connection_String>")
        .buildClient();
CommunicationUserIdentifier user = client.createUser();
```

## <a name="delete-user-identity"></a>Supprimer une identité utilisateur

Utilisez la fonction `deleteUser` de l’objet `CommunicationIdentityClient` pour supprimer une identité utilisateur :

```java
CommunicationIdentityClient client = new CommunicationIdentityClientBuilder()
        .connectionString("<Connection_String>")
        .buildClient();
client.deleteUser(new CommunicationUserIdentifier(source));    
```

## <a name="create-a-call"></a>Créer un appel

Utilisez la méthode `createCallConnectionWithResponse` de l’objet `CallingServerClient` pour démarrer un appel. Les paramètres suivants sont pris en charge par la méthode `createCallConnectionWithResponse` :
- `source` est l’identité utilisateur source de l’appelant de type `CommunicationUserIdentifier`.
- `targets` est la liste des identités cibles de type `Iterable<CommunicationIdentifier>`.
- `options` correspond aux options pour l’appel de type `CreateCallOptions`. Les paramètres de la méthode `CreateCallOptions` sont les suivants :

    - `callbackUri` est l’URI de rappel de l’application.
    - `requestedModalities` est la modalité d’appel de requête. Le type est `Iterable<MediaType>`. Les valeurs peuvent être Audio ou Video.
    - `requestedCallEvents` correspond aux événements de rappel demandés de type `Iterable<EventSubscriptionType>`. Utilisez ce paramètre pour vous abonner aux événements `EventSubscriptionType.PARTICIPANTS_UPDATED` et `EventSubscriptionType.DTMF_RECEIVED`.

- Définissez l’ID d’appelant source en tant qu’ID d’appelant de substitution à l’aide de la fonction `setAlternateCallerId()` de l’objet `CreateCallOptions`.

```java
CommunicationUserIdentifier source = new CommunicationUserIdentifier(this.callConfiguration.SourceIdentity);
PhoneNumberIdentifier target = new PhoneNumberIdentifier(targetPhoneNumber);
List<MediaType> callModality = new ArrayList<>() { {add(MediaType.AUDIO);} };
List<EventSubscriptionType> eventSubscriptionType = new ArrayList<>() {
        {add(EventSubscriptionType.PARTICIPANTS_UPDATED); add(EventSubscriptionType.DTMF_RECEIVED);}};

CreateCallOptions createCallOption = new CreateCallOptions(this.callConfiguration.appCallbackUrl,
        callModality, eventSubscriptionType);
createCallOption.setAlternateCallerId(new PhoneNumberIdentifier(this.callConfiguration.sourcePhoneNumber));

Logger.logMessage(Logger.MessageType.INFORMATION,"Performing CreateCall operation");

List<CommunicationIdentifier> targets = new ArrayList<>() { {add(target);} };

Response<CallConnection> response = this.callingServerClient.createCallConnectionWithResponse(source, targets, createCallOption, null);
callConnection = response.getValue();
```

La méthode `createCallConnectionWithResponse` retourne l’objet de `Response<CallConnection>`, qui est utilisé pour d’autres opérations d’appel comme lire l’audio, annuler l’audio et raccrocher.

## <a name="play-audio"></a>Lire un fichier audio

Une fois qu’un appel est créé, vous pouvez utiliser la méthode `playAudioWithResponse` de l’objet `CallConnection` afin de lire un message audio pour l’appelé. Les paramètres suivants sont pris en charge par la méthode `playAudioWithResponse` :

- `audioFileUri` est l’URI du fichier audio contenant le message à lire.
- `playAudioOptions` correspond aux options de lecture audio. Le type est `PlayAudioOptions`. Les paramètres suivants sont pris en charge pour l’objet `PlayAudioOptions` :
    - `loop` ; affectez la valeur true pour répéter le message audio.
    - `audioFileId` est l’ID du média dans AudioFileUri, à l’aide duquel nous mettons en cache le média.
    - `operationContext` est l’ID unique du contexte de la requête.

```java
// Preparing data for request
String audioFileUri = callConfiguration.audioFileUrl;
Boolean loop = true;
String operationContext = UUID.randomUUID().toString();
String audioFileId = UUID.randomUUID().toString();
PlayAudioOptions playAudioOptions = new PlayAudioOptions();
playAudioOptions.setLoop(loop);
playAudioOptions.setAudioFileId(audioFileId);
playAudioOptions.setOperationContext(operationContext);

Logger.logMessage(Logger.MessageType.INFORMATION, "Performing PlayAudio operation");
Response<PlayAudioResult> playAudioResponse = this.callConnection.playAudioWithResponse(audioFileUri, playAudioOptions, null);

PlayAudioResult response = playAudioResponse.getValue();
```

La méthode `playAudio` retourne `Response<PlayAudioResult>`, qui peut être utilisé pour obtenir `OperationStatus` à l’aide de la méthode `getStatus()`. `OperationStatus` peut avoir les valeurs suivantes : `NotStarted`, `Running`, `Completed` ou `Failed`.

```java
Logger.logMessage(Logger.MessageType.INFORMATION, "playAudioWithResponse -- > " + GetResponse(playAudioResponse) +
", Id: " + response.getOperationId() + ", OperationContext: " + response.getOperationContext() + ", OperationStatus: " +
response.getStatus().toString());
```

## <a name="add-a-participant-to-the-call"></a>Ajouter un participant à l’appel

Utilisez la méthode `addParticipantWithResponse` de l’objet `CallConnection` pour ajouter un participant à l’appel. Les paramètres suivants sont pris en charge par la méthode `addParticipantWithResponse` :

- `participant` est l’identificateur du participant de type `CommunicationUserIdentifier` ou `PhoneNumberIdentifier`.
- `alternateCallerId` est l’ID de l’appelant source.
- `operationContext` est l’ID unique du contexte de la requête.

```java
// Preparing data for request
CommunicationIdentifier participant = null;
String operationContext = UUID.randomUUID().toString();

if (identifierKind == CommunicationIdentifierKind.UserIdentity) {
    participant = new CommunicationUserIdentifier(addedParticipant);

} else if (identifierKind == CommunicationIdentifierKind.PhoneIdentity) {
    participant = new PhoneNumberIdentifier(addedParticipant);
}

Response<AddParticipantResult> response = callConnection.addParticipantWithResponse(participant, this.callConfiguration.sourcePhoneNumber, operationContext, null);
```

## <a name="cancel-media-processing"></a>Annuler le traitement multimédia

Utilisez la méthode `cancelAllMediaOperationsWithResponse` de l’objet `CallConnection` pour annuler l’opération de lecture audio. Les paramètres suivants sont pris en charge par la méthode `cancelAllMediaOperationsWithResponse` :

- `operationContext` est l’ID unique du contexte de la requête.

```java
String operationContext = UUID.randomUUID().toString();
Response<CancelAllMediaOperationsResult> cancelmediaresponse = this.callConnection.cancelAllMediaOperationsWithResponse(operationContext, null);
```

## <a name="hang-up-the-call"></a>Mettre fin à l’appel

Utilisez la méthode `hangupWithResponse` de l’objet `CallConnection` pour mettre fin à l’appel.

```java
Response<Void> response = this.callConnection.hangupWithResponse(null);
```