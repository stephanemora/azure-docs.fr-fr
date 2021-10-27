---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 7d2a6415a2cc03513606183c290443c453a82577
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130143653"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

## <a name="place-a-call"></a>Passer un appel

Pour créer et démarrer un appel, vous devez appeler la méthode `CallAgent.startCall()` et fournir le `Identifier` du ou des appelé(s).
Pour rejoindre un appel de groupe, vous devez appeler la méthode `CallAgent.join()` et fournir l’ID de groupe. Les ID de groupe doivent être au format GUID ou UUID.

La création et le démarrage de l’appel sont synchrones. L’instance d’appel vous permet de vous abonner à tous les événements de l’appel.

### <a name="place-a-11-call-to-a-user"></a>Passer un appel 1:1 à un utilisateur
Pour passer un appel à un autre utilisateur de Communication Services, appelez la méthode `call` sur `callAgent` et transmettez un objet avec la clé `communicationUserId`.
```java
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
CommunicationUserIdentifier acsUserId = new CommunicationUserIdentifier(<USER_ID>);
CommunicationUserIdentifier participants[] = new CommunicationUserIdentifier[]{ acsUserId };
call oneToOneCall = callAgent.startCall(appContext, participants, startCallOptions);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Passer un appel 1:n avec des utilisateurs et un RTC
> [!WARNING]
> Les appels RTC ne sont pas disponibles actuellement

Pour passer un appel 1:n à un utilisateur et un numéro RTC, vous devez spécifier le numéro de téléphone de l’appelé.
Votre ressource Communication Services doit être configurée de manière à autoriser l’appel RTC :
```java
CommunicationUserIdentifier acsUser1 = new CommunicationUserIdentifier(<USER_ID>);
PhoneNumberIdentifier acsUser2 = new PhoneNumberIdentifier("<PHONE_NUMBER>");
CommunicationIdentifier participants[] = new CommunicationIdentifier[]{ acsUser1, acsUser2 };
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
Call groupCall = callAgent.startCall(participants, startCallOptions);
```

## <a name="accept-a-call"></a>Acceptation d’un appel
Pour accepter un appel, appelez la méthode « accept » sur un objet call.

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
Call call = incomingCall.accept(context).get();
```

Pour accepter un appel avec la caméra vidéo activée, procédez comme suit :

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
AcceptCallOptions acceptCallOptions = new AcceptCallOptions();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameraList().get(0);
acceptCallOptions.setVideoOptions(new VideoOptions(new LocalVideoStream(desiredCamera, appContext)));
Call call = incomingCall.accept(context, acceptCallOptions).get();
```

L’appel entrant peut être obtenu en s’abonnant à l’événement `onIncomingCall` sur l’objet `callAgent` :

```java
// Assuming "callAgent" is an instance property obtained by calling the 'createCallAgent' method on CallClient instance 
public Call retrieveIncomingCall() {
    IncomingCall incomingCall;
    callAgent.addOnIncomingCallListener(new IncomingCallListener() {
        void onIncomingCall(IncomingCall inboundCall) {
            // Look for incoming call
            incomingCall = inboundCall;
        }
    });
    return incomingCall;
}
```

## <a name="join-a-group-call"></a>Rejoindre un appel de groupe
Pour démarrer un nouvel appel de groupe ou rejoindre un appel de groupe en cours, vous devez appeler la méthode « join » et transmettre un objet avec une propriété `groupId`. La valeur doit être un GUID.
```java
Context appContext = this.getApplicationContext();
GroupCallLocator groupCallLocator = new GroupCallLocator("<GUID>");
JoinCallOptions joinCallOptions = new JoinCallOptions();

call = callAgent.join(context, groupCallLocator, joinCallOptions);
```

## <a name="call-properties"></a>Propriétés d’appel

Obtenir l’ID unique de cet appel :

```java
String callId = call.getId();
```

Pour en savoir plus sur les autres participants de l’appel, examinez la collection `remoteParticipant` sur l’instance `call` :

```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants();
```

L’identité de l’appelant si l’appel est entrant :

```java
CommunicationIdentifier callerId = call.getCallerInfo().getIdentifier();
```

Obtenir l’état de l’appel : 

```java
CallState callState = call.getState();
```

Retourne une chaîne représentant l’état actuel d’un appel :
* « NONE » : État initial de l’appel
* « EARLY_MEDIA » : Indique un état dans lequel une annonce est lue avant la connexion de l’appel
* « CONNECTING » : État de transition initial une fois l’appel passé ou accepté
* « RINGING » : Pour un appel sortant, indique que l’appel sonne pour les participants distants
* « CONNECTED » : L’appel est connecté
* « LOCAL_HOLD »  L’appel est mis en attente par un participant local, aucun média ne circule entre le point de terminaison local et les participants distants
* « REMOTE_HOLD » : L’appel est mis en attente par un participant distant, aucun média ne circule entre le point de terminaison local et les participants distants
* « DISCONNECTING » : État de transition avant que l’appel passe à l’état « Déconnecté »
* « DISCONNECTED » : État d’appel final
* « IN_LOBBY » : En salle d’attente pour l’interopérabilité d’une réunion Teams

Pour connaître la raison de l’arrêt de l’appel, examinez la propriété `callEndReason`. Il contient le code/sous-code : 

```java
CallEndReason callEndReason = call.getCallEndReason();
int code = callEndReason.getCode();
int subCode = callEndReason.getSubCode();
```

Pour voir si l’appel en cours est un appel entrant ou sortant, inspectez la propriété `callDirection` :

```java
CallDirection callDirection = call.getCallDirection(); 
// callDirection == CallDirection.INCOMING for incoming call
// callDirection == CallDirection.OUTGOING for outgoing call
```

Pour voir si le microphone actuel est désactivé, inspectez la propriété `muted` :

```java
boolean muted = call.isMuted();
```

Pour inspecter les flux vidéo actifs, vérifiez la collection `localVideoStreams` :

```java
List<LocalVideoStream> localVideoStreams = call.getLocalVideoStreams();
```

## <a name="mute-and-unmute"></a>Activer et désactiver le son

Pour activer ou désactiver le son du point de terminaison local, vous pouvez utiliser les API asynchrones `mute` et `unmute` :

```java
Context appContext = this.getApplicationContext();
call.mute(appContext).get();
call.unmute(appContext).get();
```
## <a name="change-the-volume-of-the-call"></a>Modifier le volume de l’appel

Pendant que vous êtes en communication, les clés de volume matériel sur le téléphone doivent autoriser l’utilisateur à modifier le volume d’appel.
Cette opération est effectuée en utilisant la méthode `setVolumeControlStream` avec le type de flux `AudioManager.STREAM_VOICE_CALL` sur l’Activité d’où est passé l’appel.
Cela permet aux clés de volume matériel de modifier le volume de l’appel (indiqué par une icône de téléphone ou une valeur similaire sur le curseur de volume), empêchant ainsi de modifier le volume pour d’autres profils audio, comme les alarmes, les médias ou le volume pour l’ensemble du système. Pour plus d’informations, vous pouvez vérifier la [Gestion des modifications dans la sortie audio | Développeurs Android](https://developer.android.com/guide/topics/media-apps/volume-and-earphones).

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    ...
    setVolumeControlStream(AudioManager.STREAM_VOICE_CALL);
}
```

## <a name="remote-participants-management"></a>Gestion des participants distants

Tous les participants distants sont représentés par le type `RemoteParticipant` et sont disponibles via la collection `remoteParticipants` sur une instance d’appel.

### <a name="list-participants-in-a-call"></a>Répertorier les participants à un appel
La collection `remoteParticipants` renvoie une liste de participants distants dans un appel donné :
```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants(); // [remoteParticipant, remoteParticipant....]
```

### <a name="add-a-participant-to-a-call"></a>Ajouter un participant à un appel

Pour ajouter un participant à un appel (un utilisateur ou un numéro de téléphone), vous pouvez appeler `addParticipant`. Cette opération renvoie de façon synchrone l’instance de participant distant.

```java
const acsUser = new CommunicationUserIdentifier("<acs user id>");
const acsPhone = new PhoneNumberIdentifier("<phone number>");
RemoteParticipant remoteParticipant1 = call.addParticipant(acsUser);
AddPhoneNumberOptions addPhoneNumberOptions = new AddPhoneNumberOptions(new PhoneNumberIdentifier("<alternate phone number>"));
RemoteParticipant remoteParticipant2 = call.addParticipant(acsPhone, addPhoneNumberOptions);
```

### <a name="remove-participant-from-a-call"></a>Supprimer un participant d’un appel
Pour supprimer un participant d’un appel (un utilisateur ou un numéro de téléphone), vous pouvez appeler `removeParticipant`.
Le problème sera résolu de façon asynchrone une fois le participant supprimé de l’appel.
Le participant sera également supprimé de la collection `remoteParticipants`.
```java
RemoteParticipant acsUserRemoteParticipant = call.getParticipants().get(0);
RemoteParticipant acsPhoneRemoteParticipant = call.getParticipants().get(1);
call.removeParticipant(acsUserRemoteParticipant).get();
call.removeParticipant(acsPhoneRemoteParticipant).get();
```

### <a name="remote-participant-properties"></a>Propriétés du participant distant
Tout participant distant donné dispose d’un ensemble de propriétés et de collections qui lui sont associées :

* Obtenez l’identificateur de ce participant distant.
L’identité est l’un des types d’identificateurs
    ```java
    CommunicationIdentifier participantIdentifier = remoteParticipant.getIdentifier();
    ```

* Obtenez l’état de ce participant distant.
    ```java
    ParticipantState state = remoteParticipant.getState();
    ```
Il peut s’agir de l’un des états suivants :
* « IDLE » : État initial
* « EARLY_MEDIA » : Une annonce est lue avant que le participant soit connecté à l’appel
* « RINGING » : Sonnerie en cours chez le participant
* « CONNECTING » : État transitoire pendant que le participant se connecte à l’appel
* « CONNECTED » : Le participant est connecté à l’appel
* « HOLD » : Le participant est en attente
* « IN_LOBBY » : Le participant est en salle d’attente et attend son éventuelle admission. Actuellement utilisé uniquement dans le scénario d’interopération Teams
* « DISCONNECTED » : État final, le participant est déconnecté de l’appel

* Pour savoir pourquoi un participant a quitté l’appel, inspectez la propriété `callEndReason` :
    ```java
    CallEndReason callEndReason = remoteParticipant.getCallEndReason();
    ```

* Pour vérifier si le son de ce participant distant est activé ou non, inspectez la propriété `isMuted` :
    ```java
    boolean isParticipantMuted = remoteParticipant.isMuted();
    ```

* Pour vérifier si ce participant parle ou non, inspectez la propriété `isSpeaking` :
    ```java
    boolean isParticipantSpeaking = remoteParticipant.isSpeaking();
    ```

* Pour inspecter tous les flux vidéo qu’un participant donné envoie dans cet appel, vérifiez la collection `videoStreams` :
    ```java
    List<RemoteVideoStream> videoStreams = remoteParticipant.getVideoStreams(); // [RemoteVideoStream, RemoteVideoStream, ...]
    ```
## <a name="using-foreground-services"></a>Utilisation de services de premier plan

Dans les cas où vous souhaitez exécuter une tâche visible par l’utilisateur même lorsque votre application est en arrière-plan, vous pouvez utiliser des [services de premier plan](https://developer.android.com/guide/components/foreground-services).

Grâce aux services de premier plan, vous pouvez, par exemple, conserver une notification visible par l’utilisateur lorsque un appel est actif sur votre application. Ainsi, même si l’utilisateur accède à l’écran d’accueil ou supprime l’application de l’[écran des applications récentes](https://developer.android.com/guide/components/activities/recents), l’appel restera actif.

Si vous n’utilisez pas de service de premier plan pendant un appel, le fait de naviguer vers l’écran d’accueil peut maintenir l’appel actif, mais le fait de supprimer l’application de l’écran des applications récentes peut arrêter l’appel si le système d’exploitation Android tue le processus de votre application.

Vous devez démarrer le service de premier plan lorsque vous démarrez ou joignez un appel, par exemple :

```java
call = callAgent.startCall(context, participants, options);
startService(yourForegroundServiceIntent);
```

Et arrêtez le service de premier plan lorsque vous raccrochez l’appel ou que l’état de l’appel est Déconnecté, par exemple :

```java
call.hangUp(new HangUpOptions()).get();
stopService(yourForegroundServiceIntent);
```

### <a name="notes-on-using-foreground-services"></a>Notes sur l’utilisation de services de premier plan

Gardez à l’esprit que des scénarios tels que l’arrêt d’un service de premier plan déjà en cours d’exécution lorsque l’application est supprimée de la liste des applications récentes supprimeront la notification visible par l’utilisateur et que le système d’exploitation Android peut maintenir le processus de votre application en vie pendant une période supplémentaire, ce qui signifie que l’appel peut encore être actif pendant cette période.

Si votre application arrête le service de premier plan sur la méthode `onTaskRemoved` du service, par exemple, votre application peut démarrer ou arrêter l’audio et la vidéo en fonction du [cycle de vie de votre activité](https://developer.android.com/guide/components/activities/activity-lifecycle), comme en arrêtant l’audio et la vidéo lorsque votre activité est détruite avec le remplacement de la méthode `onDestroy`.
