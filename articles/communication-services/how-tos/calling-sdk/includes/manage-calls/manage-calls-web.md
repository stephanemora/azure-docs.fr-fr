---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 3ab9331dddb90803867f6cfb5118aa33bdd313d0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128700474"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-web.md)]

## <a name="place-a-call"></a>Passer un appel

Pour créer et démarrer un appel, utilisez l’une des API sur `callAgent` et fournissez un utilisateur que vous avez créé par le biais du SDK Identité Communication Services.

La création et le démarrage de l’appel sont synchrones. L’instance `call` vous permet de vous abonner à des événements d’appel.

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>Passer un appel 1:n à un utilisateur ou à un RTC

Pour appeler un autre utilisateur de Communication Services, utilisez la méthode `startCall` sur `callAgent` et transmettez le `CommunicationUserIdentifier` du destinataire que vous avez [créé avec la bibliothèque d’administration de Communication Services](../../../../quickstarts/access-tokens.md).

Pour un appel 1:1 à un utilisateur, utilisez le code suivant :

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

Pour passer un appel à un réseau téléphonique commuté (RTC) public, utilisez la méthode `startCall` sur `callAgent` et transmettez le `PhoneNumberIdentifier` du destinataire. Votre ressource Communication Services doit être configurée pour autoriser l’appel RTC.

Quand vous appelez un numéro RTC, spécifiez votre ID d’appelant de substitution. Un ID d’appelant de substitution est un numéro de téléphone (basé sur la norme E.164) qui identifie l’appelant dans un appel RTPC. Il s’agit du numéro de téléphone que le destinataire de l’appel voit en tant qu’appel entrant.

> [!NOTE]
> L’appel RTC est actuellement en préversion privée. Pour l’accès, [appliquez le programme Utilisateur précoce](https://aka.ms/ACS-EarlyAdopter).

Pour un appel 1:1 à un numéro de réseau téléphonique commuté, utilisez le code suivant :
```js
const pstnCallee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<ALTERNATE_CALLER_ID>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

Pour un appel 1:n à un utilisateur et un numéro de réseau téléphonique commuté, utilisez le code suivant :

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const pstnCallee = { phoneNumber: '<PHONE_NUMBER>'};
const alternateCallerId = {alternateCallerId: '<ALTERNATE_CALLER_ID>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});
```

### <a name="join-a-group-call"></a>Rejoindre un appel de groupe

> [!NOTE]
> Considéré comme des métadonnées système, le paramètre `groupId` peut être utilisé par Microsoft pour les opérations nécessaires à l’exécution du système. N’incluez pas de données personnelles dans la valeur `groupId`. Microsoft ne traite pas ce paramètre comme des données personnelles et son contenu peut être visible pour les employés de Microsoft ou stocké à long terme.
>
> Le paramètre `groupId` exige que les données soient au format GUID. Nous vous recommandons d’utiliser des GUID générés de manière aléatoire qui ne sont pas considérés comme des données personnelles dans vos systèmes.
>

Pour démarrer un nouvel appel de groupe ou rejoindre un appel de groupe, utilisez la méthode `join` et transmettez un objet avec une propriété `groupId`. La valeur `groupId` doit être un GUID.

```js
const context = { groupId: '<GUID>'};
const call = callAgent.join(context);
```

## <a name="receive-an-incoming-call"></a>Recevoir un appel entrant

L’instance de `callAgent` émet un événement `incomingCall` quand l’identité connectée reçoit un appel entrant. Pour écouter cet événement, abonnez-vous en utilisant l’une des options suivantes :

```js
const incomingCallHandler = async (args: { incomingCall: IncomingCall }) => {
    const incomingCall = args.incomingCall; 

    // Get incoming call ID
    var incomingCallId = incomingCall.id

    // Get information about this Call. This API is provided as a preview for developers
    // and may change based on feedback that we receive. Do not use this API in a production environment.
    // To use this api please use 'beta' release of ACS Calling Web SDK
    var callInfo = incomingCall.info;

    // Get information about caller
    var callerInfo = incomingCall.callerInfo

    // Accept the call
    var call = await incomingCall.accept();

    // Reject the call
    incomingCall.reject();

    // Subscribe to callEnded event and get the call end reason
     incomingCall.on('callEnded', args => {
        console.log(args.callEndReason);
    });

    // callEndReason is also a property of IncomingCall
    var callEndReason = incomingCall.callEndReason;
};
callAgentInstance.on('incomingCall', incomingCallHandler);
```

L’événement `incomingCall` comprend une instance `incomingCall` que vous pouvez accepter ou rejeter.

Lorsque vous démarrez/rejoignez/acceptez un appel avec la vidéo activée, si la caméra spécifiée est utilisée par un autre processus ou si elle est désactivée dans le système, l’appel commence sans vidéo, et un diagnostic d’appel cameraStartFailed : true est déclenché.

## <a name="mute-and-unmute"></a>Activer et désactiver le son

Pour activer ou désactiver le son du point de terminaison local, vous pouvez utiliser les API asynchrones `mute` et `unmute` :

```js
//mute local device
await call.mute();

//unmute local device
await call.unmute();
```

## <a name="manage-remote-participants"></a>Gérer les participants distants

Tous les participants distants sont représentés par le type `RemoteParticipant`, et disponibles via la collection `remoteParticipants` sur une instance d’appel.

### <a name="list-the-participants-in-a-call"></a>Lister les participants à un appel

La collection `remoteParticipants` retourne une liste de participants distants à un appel :

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
```

### <a name="add-a-participant-to-a-call"></a>Ajouter un participant à un appel

Pour ajouter un participant (un utilisateur ou un numéro de téléphone) à un appel, vous pouvez appeler `addParticipant`. Fournissez l’un des types `Identifier`. Il renvoie l’instance `remoteParticipant` de manière synchrone. L’événement `remoteParticipantsUpdated` à partir de l’appel est déclenché lorsqu’un participant est correctement ajouté à l’appel.

```js
const userIdentifier = { communicationUserId: '<ACS_USER_ID>' };
const pstnIdentifier = { phoneNumber: '<PHONE_NUMBER>' }
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<ALTERNATE_CALLER_ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>Supprimer un participant d’un appel

Pour supprimer un participant (un utilisateur ou un numéro de téléphone) d’un appel, vous pouvez appeler `removeParticipant`. Vous devez passer l’un des types `Identifier`. Cette méthode résout le problème de façon asynchrone une fois le participant supprimé de l’appel. Le participant est également supprimé de la collection `remoteParticipants`.

```js
const userIdentifier = { communicationUserId: '<ACS_USER_ID>' };
const pstnIdentifier = { phoneNumber: '<PHONE_NUMBER>' }
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

### <a name="access-remote-participant-properties"></a>Accéder aux propriétés des participants distants

Un ensemble de propriétés et de collections sont associées aux participants distants :

- `CommunicationIdentifier` : obtenez l’identificateur d’un participant distant. L’identité correspond à l’un des types `CommunicationIdentifier` :

    ```js
    const identifier = remoteParticipant.identifier;
    ```

Les types `CommunicationIdentifier` possibles sont les suivants :

- `{ communicationUserId: '<ACS_USER_ID'> }` : objet représentant l’utilisateur d’ACS.
- `{ phoneNumber: '<E.164>' }` : objet représentant le numéro de téléphone au format E.164.
- `{ microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" }` : objet représentant l’utilisateur de Teams.
- `{ id: string }` : objet représentant un identificateur qui ne correspond à aucun autre type d’identificateur

- `state` : obtenez l’état d’un participant distant.

    ```js
    const state = remoteParticipant.state;
    ```

L’état peut être :

- `Idle` : état initial.
- `Connecting` : état transitoire pendant qu’un participant se connecte à l’appel.
- `Ringing` : sonnerie en cours chez le participant.
- `Connected` : le participant est connecté à l’appel.
- `Hold` : le participant est en attente.
- `EarlyMedia` : annonce qui est lue avant qu’un participant ne se connecte à l’appel.
- `InLobby` : indique que le participant distant est en salle d’attente.
- `Disconnected` : état final. Le participant est déconnecté de l’appel. Si le participant distant perd sa connectivité réseau, son état passe à `Disconnected` au bout de deux minutes.

- `callEndReason` : pour savoir pourquoi un participant a quitté l’appel, vérifiez la propriété `callEndReason` :

    ```js
    const callEndReason = remoteParticipant.callEndReason;
    const callEndReasonCode = callEndReason.code // (number) code associated with the reason
    const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
    ```

- État `isMuted` : pour savoir si le son d’un participant est désactivé, vérifiez la propriété `isMuted`. Elle retourne `Boolean`.

    ```js
    const isMuted = remoteParticipant.isMuted;
    ```

- État `isSpeaking` : pour savoir si un participant parle, vérifiez la propriété `isSpeaking`. Elle retourne `Boolean`.

    ```js
    const isSpeaking = remoteParticipant.isSpeaking;
    ```

- `videoStreams` : pour inspecter tous les flux vidéo qu’un participant donné envoie dans cet appel, vérifiez la collection `videoStreams`. Elle contient des objets `RemoteVideoStream`.

    ```js
    const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]
    ```
- `displayName` : pour obtenir le nom complet de ce participant distant, inspectez la propriété `displayName`. Elle retourne une chaîne. 

    ```js
    const displayName = remoteParticipant.displayName;
    ```

## <a name="check-call-properties"></a>Vérifier les propriétés de l’appel

Obtenez l’ID unique (string) d’un appel :

```js
const callId: string = call.id;
```
Obtenir des informations sur l’appel :
> [!NOTE]
> Cet API est fourni en tant qu’aperçu pour les développeurs et peut être modifié en fonction des commentaires que nous avons reçus. N’utilisez pas cet API dans un environnement de production. Pour utiliser cette API, utilisez la version bêta du SDK web Appel ACS
```js
const callInfo = call.info;
```

Découvrez les autres participants à l’appel en examinant la collection `remoteParticipants` sur l’instance « call » :

```js
const remoteParticipants = call.remoteParticipants;
```

Identifiez l’appelant d’un appel entrant :
```js
const callerIdentity = call.callerInfo.identifier;
```

`identifier` correspond à l’un des types `CommunicationIdentifier`.

Obtenez l’état d’un appel :

```js
const callState = call.state;
```

Retourne une chaîne représentant l’état actuel d’un appel :

- `None` : état initial de l’appel.
- `Connecting` : état de transition initial quand un appel est passé ou accepté.
- `Ringing` : pour un appel sortant, indique qu’il sonne pour les participants distants. Il s’agit de `Incoming` de leur côté.
- `EarlyMedia` : indique un état dans lequel une annonce est lue avant la connexion de l’appel.
- `Connected` : indique que l’appel est connecté.
- `LocalHold` : indique que l’appel est mis en attente par un participant local. Aucun média ne circule entre le point de terminaison local et les participants distants.
- `RemoteHold` : indique que l’appel a été mis en attente par un participant distant. Aucun média ne circule entre le point de terminaison local et les participants distants.
- `InLobby` : indique que l’utilisateur est en salle d’attente.
- `Disconnecting` : état de transition avant que l’appel ne passe à l’état `Disconnected`.
- `Disconnected` : état d’appel final. Si la connexion réseau est perdue, l’état passe à `Disconnected` au bout de deux minutes.

Découvrez pourquoi un appel s’est terminé en examinant la propriété `callEndReason` :

```js
const callEndReason = call.callEndReason;
const callEndReasonCode = callEndReason.code // (number) code associated with the reason
const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
```

Découvrez si l’appel en cours est entrant ou sortant en inspectant la propriété `direction`. Elle retourne `CallDirection`.

```js
const isIncoming = call.direction == 'Incoming';
const isOutgoing = call.direction == 'Outgoing';
```

Vérifiez si le microphone actuel est désactivé. Elle retourne `Boolean`.

```js
const muted = call.isMuted;
```

Découvrez si le flux de partage d’écran est envoyé à partir d’un point de terminaison donné en vérifiant la propriété `isScreenSharingOn`. Elle retourne `Boolean`.

```js
const isScreenSharingOn = call.isScreenSharingOn;
```

Inspectez les flux vidéo actifs en vérifiant la collection `localVideoStreams`. Elle retourne des objets `LocalVideoStream`.

```js
const localVideoStreams = call.localVideoStreams;
```