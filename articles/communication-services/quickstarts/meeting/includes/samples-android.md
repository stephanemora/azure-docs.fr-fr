---
title: Utilisation de la bibliothèque Azure Communication Services Teams Embed pour Android
description: Dans cette présentation, vous allez apprendre à utiliser la bibliothèque Azure Communication Services Teams Embed pour Android.
author: palatter
ms.author: palatter
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 5f74ab05517c2859cecb9913c19a9ab4c454c85c
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113215134"
---
## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Une ressource Communication Services déployée. [Créez une ressource Communication Services](../../create-communication-resource.md).
- Un `User Access Token` pour activer le client d’appel. Pour en savoir plus, consultez [Comment obtenir un `User Access Token`](../../access-tokens.md)
- Suivez le démarrage rapide pour [prendre en main l’ajout de Teams Embed à votre application](../getting-started-with-teams-embed.md).

## <a name="joining-a-group-call"></a>Rejoindre un appel de groupe

L’appel de groupe peut être rejoint en fournissant `MeetingUIClientGroupCallLocator` et `MeetingUIClientJoinOptions` à l'API `meetingUIClient.join`. L’appel de groupe ne sonnera pas pour les autres participants. L’utilisateur va rejoindre l’appel en mode silencieux.

Accédez au fichier de disposition principal (`app/src/main/res/layout/activity_main.xml`) pour configurer le bouton afin de rejoindre un appel de groupe. Créez un bouton avec un ID `join_groupCall`.

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/join_groupCall"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Join Group Call"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

Accédez à **MainActivityjava** et ajoutez le bouton sur l’action de clic dans la méthode `onCreate` :

```java
package com.yourcompany.teamsembedandroidgettingstarted;

import com.azure.android.communication.ui.meetings.MeetingUIClient;
import com.azure.android.communication.ui.meetings.MeetingUIClientCall;
import com.azure.android.communication.ui.meetings.MeetingUIClientGroupCallLocator;
import com.azure.android.communication.ui.meetings.MeetingUIClientJoinOptions;

import java.util.UUID;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        
        Button joinGroupCallButton = findViewById(R.id.join_groupCall);
        joinGroupCallButton.setOnClickListener(l -> joinGroupCall());
    }
}
```

La configuration du client et la fourniture du jeton s’effectuent de la même façon que pour l’API d’accès à la réunion, qui est décrite dans le [guide de démarrage rapide](../getting-started-with-teams-embed.md).

La méthode `joinGroupCall` est définie en tant qu’action qui sera exécutée lors d’un appui sur le bouton *Rejoindre l’appel de groupe*. Il est possible de rejoindre un appel de groupe via le `MeetingUIClient`. Créez un `MeetingUIClientGroupCallLocator` et configurez les options d’accès à l’aide du `MeetingUIClientJoinOptions`.
Remarque pour remplacer `<GROUP_ID>` par une chaîne UUID. La chaîne d’ID de groupe doit être au format GUID ou UUID.

```java
/**
 * Join a group call with a groupID.
 */
private void joinGroupCall() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);

    try {
        UUID groupUUID = UUID.fromString("<GROUP_ID>");
        MeetingUIClientGroupCallLocator meetingUIClientGroupCallLocator = new MeetingUIClientGroupCallLocator(groupUUID);
        meetingUIClientCall = meetingUIClient.join(meetingUIClientGroupCallLocator, meetingJoinOptions);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join group call: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="teams-embed-call-or-meeting-status-events-capturing"></a>Capture d’événements relatifs à l’état des appels ou des réunions de Teams Embed

L’état de l’appel de groupe qui a été rejoint ou de la réunion peut être capturé à partir de la classe `MeetingUIClientCallEventListener`. L’état comprend les états de connexion, le nombre de participants et les modalités comme l’état du micro ou de la caméra.

Ajoutez le code suivant à votre fichier `MainActivity.java`.

```java
import androidx.core.content.ContextCompat;
import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.common.CommunicationTokenRefreshOptions;
import com.azure.android.communication.ui.meetings.MeetingUIClient;
import com.azure.android.communication.ui.meetings.MeetingUIClientCall;
import com.azure.android.communication.ui.meetings.MeetingUIClientCallState;
import com.azure.android.communication.ui.meetings.MeetingUIClientCallEventListener;
import com.azure.android.communication.ui.meetings.MeetingUIClientCallIdentityProvider;
import com.azure.android.communication.ui.meetings.MeetingUIClientCallIdentityProviderCallback;
import com.azure.android.communication.ui.meetings.MeetingUIClientCallUserEventListener;
import com.azure.android.communication.ui.meetings.MeetingUIClientGroupCallLocator;
import com.azure.android.communication.ui.meetings.MeetingUIClientIconType;
import com.azure.android.communication.ui.meetings.MeetingUIClientJoinOptions;
import com.azure.android.communication.ui.meetings.MeetingUIClientTeamsMeetingLinkLocator;
```

Ajoutez `MeetingUIClientCallEventListener` à votre classe.

```java
public class MainActivity extends AppCompatActivity implements MeetingUIClientEventListener {
```

Appelez `setMeetingUIClientCallEventListener` avec le paramètre `this` après avoir rejoint l’appel ou après que la réunion ait démarré avec succès.

```java
private void joinMeeting() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientTeamsMeetingLinkLocator meetingUIClientTeamsMeetingLinkLocator = new MeetingUIClientTeamsMeetingLinkLocator(<MEETING_URL>);
    
    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);
    
    try {
        MeetingUIClientCall meetingUIClientCall = meetingUIClient.join(meetingUIClientTeamsMeetingLinkLocator, meetingJoinOptions);
        meetingUIClientCall.setMeetingUIClientCallEventListener(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

Implémentez les méthodes `MeetingUIClientCallEventListener` dont votre application a besoin et ajoutez des stubs pour celles qui ne sont pas nécessaires.

```java
@Override
public void onCallStateChanged(MeetingUIClientCallState callState) {
    switch(callState) {
        case CONNECTING:
            System.out.println("Call state changed to Connecting");
            break;
        case CONNECTED:
            System.out.println("Call state changed to Connected");
            break;
        case WAITING_IN_LOBBY:
            System.out.println("Call state changed to Waiting in Lobby");
            break;
        case ENDED:
            System.out.println("Call state changed to Ended");
            break;
    }
}

@Override
public void onRemoteParticipantCountChanged(int newCount) {
    System.out.println("Remote participant count changed: " + newCount);
}

@Override
public void onIsMutedChanged() {
}

@Override
public void onIsSendingVideoChanged() {
}

@Override
public void onIsHandRaisedChanged(List<String> participantIds) {
}
```

## <a name="bring-your-own-identity-from-the-app-to-the-participants-in-the-sdk-call"></a>Apportez votre propre identité de l’application aux participants dans l’appel du Kit de développement logiciel (SDK).

L’application peut attribuer les valeurs d’identité de ses utilisateurs aux participants à l’appel ou à la réunion et remplacer les valeurs par défaut. Ces valeurs comprennent l’avatar, le nom et le sous-titre.

### <a name="assigning-avatars-for-call-participants"></a>Attribution d’avatars aux participants à l’appel

Ajoutez `MeetingUIClientCallIdentityProvider` à votre classe.

```java
public class MainActivity extends AppCompatActivity implements MeetingUIClientCallIdentityProvider {
```

Appelez `setMeetingUIClientCallIdentityProvider` avec le paramètre `this`.

```java
private void joinMeeting() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientTeamsMeetingLinkLocator meetingUIClientTeamsMeetingLinkLocator = new MeetingUIClientTeamsMeetingLinkLocator(<MEETING_URL>);

    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);

    try {
        MeetingUIClientCall meetingUIClientCall = meetingUIClient.join(meetingUIClientTeamsMeetingLinkLocator, meetingJoinOptions);
        meetingUIClientCall.setMeetingUIClientCallIdentityProvider(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

Implémentez la méthode `provideAvatarFor` et mappez chaque `userIdentifier` à l’avatar correspondant.

```java
@Override
public void provideAvatarFor(CommunicationIdentifier communicationIdentifier, MeetingUIClientAvatarSize avatarSize, MeetingUIClientCallIdentityProviderCallback callback) {
    if(communicationIdentifier instanceof CommunicationUserIdentifier) {
        CommunicationUserIdentifier userIdentifier = (CommunicationUserIdentifier) communicationIdentifier;
        System.out.println("MeetingUIClientIdentityProvider.provideAvatarFor called for userIdentifier: " + userIdentifier.getId());
        if (userIdentifier.getId().startsWith("8:teamsvisitor:")) {
            callback.onAvatarAvailable(ContextCompat.getDrawable(this, R.drawable.nodpi_avatar_placeholder_large_pink));
        } else if (userIdentifier.getId().startsWith("8:orgid:")) {
            callback.onAvatarAvailable(ContextCompat.getDrawable(this, R.drawable.nodpi_doctor_avatar));
        } else if (userIdentifier.getId().startsWith("8:acs:")) {
            callback.onAvatarAvailable(ContextCompat.getDrawable(this, R.drawable.nodpi_avatar_placeholder_large_green));
        }
    }
}
```

Ajoutez d’autres méthodes d’interface MeetingUIClientCallIdentityProvider obligatoires à la classe. Elles peuvent être laissées avec des implémentations vides.

```java
@Override
public void provideDisplayNameFor(CommunicationIdentifier communicationIdentifier, MeetingUIClientCallIdentityProviderCallback callback) {
}

@Override
public void provideSubTitleFor(CommunicationIdentifier communicationIdentifier, MeetingUIClientCallIdentityProviderCallback callback) {
}
```
## <a name="receive-information-about-user-actions-in-the-ui-and-add-your-own-custom-functionalities"></a>Recevez des informations sur les actions des utilisateurs dans l’interface utilisateur et ajoutez vos propres fonctionnalités personnalisées.

### <a name="call-screen"></a>Écran d’appel

Les méthodes d’interface `MeetingUIClientCallUserEventListener` sont appelées à la suite d’actions de l’utilisateur dans le profil du participant distant.

Ajoutez `MeetingUIClientCallUserEventListener` à votre classe.

```java
public class MainActivity extends AppCompatActivity implements MeetingUIClientCallUserEventListener {
```

Appelez `setMeetingUIClientCallUserEventListener` avec le paramètre `this`.

```java
private void joinMeeting() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientTeamsMeetingLinkLocator meetingUIClientTeamsMeetingLinkLocator = new MeetingUIClientTeamsMeetingLinkLocator(<MEETING_URL>);

    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);

    try {
        MeetingUIClientCall meetingUIClientCall = meetingUIClient.join(meetingUIClientTeamsMeetingLinkLocator, meetingJoinOptions);
        meetingUIClientCall.setMeetingUIClientCallUserEventListener(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```
Ajoutez et implémentez la méthode `onNamePlateOptionsClicked` et mappez chaque `userIdentifier` à l’utilisateur participant à l’appel qui correspond.
Cette méthode est appelée en appuyant sur le texte du titre de l’utilisateur dans l’écran d’appel principal.

```java
@Override
public void onNamePlateOptionsClicked(@NonNull Activity activity, @NonNull CommunicationIdentifier communicationIdentifier) {
    if(communicationIdentifier instanceof CommunicationUserIdentifier) {
        CommunicationUserIdentifier userIdentifier = (CommunicationUserIdentifier) communicationIdentifier;
        if (userIdentifier.getId().startsWith("8:acs:")) {
            // Custom behavior based on the user here.
            System.out.println("On user name plate clicked");
        }
    }
}
```

Ajoutez et implémentez la méthode `onParticipantViewLongPressed` et mappez chaque `userIdentifier` à l’utilisateur participant à l’appel qui correspond.
Cette méthode est appelée en appuyant longuement sur la vignette de l’utilisateur dans l’écran d’appel principal. Renvoie `true` pour la gestion personnalisée ou `false` pour la gestion par défaut de l’appui long.

```java
@Override
public boolean onParticipantViewLongPressed(@NonNull Activity activity, @NonNull CommunicationIdentifier communicationIdentifier) {
    if(communicationIdentifier instanceof CommunicationUserIdentifier) {
        CommunicationUserIdentifier userIdentifier = (CommunicationUserIdentifier) communicationIdentifier;
        if (userIdentifier.getId().startsWith("8:acs:")) {
            // Custom behavior based on the user here.
            System.out.println("On participant tile long pressed");
            return true;
        }
        return false;
    }
}
```

### <a name="call-roster"></a>Liste d’appels

Les méthodes d’interface `MeetingUIClientCallRosterDelegate` sont appelées à la suite d’actions de l’utilisateur dans la liste d’appels.

Ajoutez `MeetingUIClientCallRosterDelegate` à votre classe.

```java
public class MainActivity extends AppCompatActivity implements MeetingUIClientCallRosterDelegate {
```

Appelez `setMeetingUIClientCallRosterDelegate` avec le paramètre `this`.

```java
private void joinMeeting() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientTeamsMeetingLinkLocator meetingUIClientTeamsMeetingLinkLocator = new MeetingUIClientTeamsMeetingLinkLocator(<MEETING_URL>);

    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);

    try {
        MeetingUIClientCall meetingUIClientCall = meetingUIClient.join(meetingUIClientTeamsMeetingLinkLocator, meetingJoinOptions);
        meetingUIClientCall.setMeetingUIClientCallRosterDelegate(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

Ajoutez et implémentez la méthode `onCallParticipantCellTapped` et mappez chaque `userIdentifier` à l’utilisateur participant à l’appel qui correspond.
Cette méthode est appelée sur un seul appui sur la cellule de participant à distance à partir de l’écran de liste d’appels. Renvoie `true` pour la gestion personnalisée ou `false` pour la gestion par défaut de l’appui unique.

```java
@Override
public boolean onCallParticipantCellTapped(@NonNull Activity activity, @NonNull CommunicationIdentifier communicationIdentifier) {
    if(communicationIdentifier instanceof CommunicationUserIdentifier) {
        CommunicationUserIdentifier userIdentifier = (CommunicationUserIdentifier) communicationIdentifier;
        if (userIdentifier.getId().startsWith("8:acs:")) {
            // Custom behavior based on the user here.
            System.out.println("On call participant cell tapped");
            return true;
        }
        return false;
    }
}
```
## <a name="user-experience-customization"></a>Personnalisation de l’expérience utilisateur

L’expérience utilisateur dans le Kit de développement logiciel (SDK) peut être personnalisée en fournissant des icônes spécifiques à l’application. 

### <a name="customize-ui-icons-in-a-call-or-meeting"></a>Personnaliser les icônes de l’interface utilisateur dans un appel ou une réunion
Les icônes affichées dans l’appel ou la réunion peuvent être personnalisées par le biais de la méthode `public void setIconConfig(Map<MeetingUIClientIconType, Integer> iconConfig)` exposée dans `MeetingUIClient`.

Après la création du meetingUIClient, définissez la configuration des icônes `meetingUIClient.setIconConfig(getIconConfig())` pour les icônes d’appel prises en charge dans `MeetingUIClientIconType`.

```java
private MeetingUIClient createMeetingUIClient() {
    MeetingUIClient meetingUIClient = new MeetingUIClient(credential);
    meetingUIClient.setIconConfig(getIconConfig());
}

private Map<IconType, Integer> getIconConfig() {
    Map<IconType, Integer> iconConfig = new HashMap<>();
    iconConfig.put(MeetingUIClientIconType.VIDEO_OFF, R.drawable.video_camera_off);
    iconConfig.put(MeetingUIClientIconType.VIDEO_ON, R.drawable.video_camera);
    iconConfig.put(MeetingUIClientIconType.MIC_ON, R.drawable.microphone_fill);
    iconConfig.put(MeetingUIClientIconType.MIC_OFF, R.drawable.microphone_off);
    iconConfig.put(MeetingUIClientIconType.MIC_PROHIBITED, R.drawable.mic_none);
    iconConfig.put(MeetingUIClientIconType.DEVICE_AUDIO, R.drawable.device_filled);
    iconConfig.put(MeetingUIClientIconType.SPEAKER, R.drawable.volume_high);
    iconConfig.put(MeetingUIClientIconType.SPEAKER_OFF, R.drawable.speaker_off);
    iconConfig.put(MeetingUIClientIconType.HEADSET, R.drawable.headset);
    iconConfig.put(MeetingUIClientIconType.BLUETOOTH, R.drawable.bluetooth_audio);
    iconConfig.put(MeetingUIClientIconType.HANGUP, R.drawable.close_app_bar);
    return iconConfig;
}
```

## <a name="perform-operations-with-the-call"></a>Effectuer des opérations pendant l’appel

Les actions de contrôle de l’appel sont exposées par le biais des méthodes présentes dans `MeetingUIClientCall`.
Ces méthodes sont utiles pour contrôler les actions d’appel.

Ajouter une variable pour meetingUIClientCall

```java
public class MainActivity extends AppCompatActivity {
    private MeetingUIClientCall meetingUIClientCall;
```
Assigner une variable meetingUIClientCall à partir de la valeur de retour de la méthode d’accès

```java
private void joinGroupCall() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);

    try {
        UUID groupUUID = UUID.fromString("<GROUP_ID>");
        MeetingUIClientGroupCallLocator meetingUIClientGroupCallLocator = new MeetingUIClientGroupCallLocator(groupUUID);
        meetingUIClientCall = meetingUIClient.join(meetingUIClientGroupCallLocator, meetingJoinOptions);
        meetingUIClientCall.setMeetingUIClientCallEventListener(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join group call: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```
### <a name="mute-and-unmute"></a>Activer et désactiver le son

Appelez la méthode `mute` pour désactiver le micro pendant un appel actif, s’il en existe un.
Les changements d’état du micro sont notifiés dans la méthode `onIsMutedChanged` de `MeetingUIClientCallEventListener`.

```java
// Mute the microphone for an active call.
public void mute() {
    try {
        meetingUIClientCall.mute();
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Mute call failed: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

Appelez la méthode `unmute` pour réactiver le micro pendant un appel actif, s’il en existe un.

```java
// Unmute the microphone for an active call.
public void unmute() {
    try {
        meetingUIClientCall.unmute();
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Unmute call failed: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```
### <a name="other-operations-available-in-from-the--meetinguiclientcall-class"></a>D’autres opérations sont disponibles dans la classe `MeetingUIClientCall`.

```java
// Start the video for an active call.
public void startVideo()

// Stop the video for an active call.
public void stopVideo()

// Set the preferred audio route in the call for self user.
public void setAudioRoute(MeetingUIClientAudioRoute audioRoute)

// Raise the hand of current user for an active call.
public void raiseHand()

// Lower the hand of current user for an active call.
public void lowerHand()

// Change the layout in the call for self user.
public List<MicrosoftTeamsSDKLayoutMode> getSupportedLayoutModes()
public void changeLayout(MeetingUIClientLayoutMode microsoftTeamsSDKLayoutMode)

// Hang up the call or leave the meeting.
public void hangUp()
```

## <a name="use-teams-embed-sdk-and-azure-communication-calling-sdk-in-the-same-app"></a>Utiliser le Kit de développement logiciel (SDK) Teams Embed et le Kit de développement logiciel (SDK) Appel d’Azure Communication Services dans la même application

Le Kit de développement logiciel (SDK) Teams Embed contient également le Kit de développement logiciel (SDK) Appel d’Azure Communication Services (ACS), ce qui permet d’utiliser les fonctionnalités des deux SDK dans la même application. Un seul Kit de développement logiciel (SDK) peut être initialisé et utilisé à la fois. Si les deux Kit de développement logiciel (SDK) sont initialisés et utilisés en même temps, cela se traduira par un comportement inattendu. 

Ajoutez les importations suivantes à votre classe.
```java
import com.azure.android.communication.calling.Call;
import com.azure.android.communication.calling.CallAgent;
import com.azure.android.communication.calling.CallClient;
import com.azure.android.communication.calling.CallState;
import com.azure.android.communication.calling.GroupCallLocator;
import com.azure.android.communication.calling.JoinCallOptions;
import com.azure.android.communication.common.CommunicationTokenCredential;
```

Déclarez des variables pour l’utilisation d’ACS.
```java
public class MainActivity extends AppCompatActivity {
    private CallAgent agent;
    private CallClient mCallClient;
    private Call mCall;
```

L’initialisation s’effectue en créant un nouveau `CallClient`. Ajoutez la création à `joinAcsCall` ou à toute autre méthode.

```java
private void joinAcsCall() {
    if (mCallClient == null) {
        mCallClient = new CallClient();
    }
}
```
Utilisez toutes les API ACS comme elles sont décrites dans la documentation. L’utilisation de l’API n’est pas traitée dans cette documentation. 

Supprimez le Kit de développement logiciel (SDK) ACS et définissez `null` sur ses variables une fois que son utilisation n’est plus nécessaire ou que l’application a besoin d’utiliser le Kit de développement logiciel (SDK) Teams Embed.
```java
private void stopAcs() {
    mCall = null;
    agent.dispose();
    agent = null;
    mCallClient.dispose();
    mCallClient = null;
}
```

L’initialisation du Kit de développement logiciel (SDK) Teams Embed s’effectue également lors de la création de `MeetingUIClient`. Ajoutez la création à `createMeetingUIClient` ou à toute autre méthode.
```java
private MeetingUIClient createMeetingUIClient() { 
    try {
        CommunicationTokenRefreshOptions refreshOptions = new CommunicationTokenRefreshOptions(tokenRefresher, true, "<USER_ACCESS_TOKEN>");
        CommunicationTokenCredential credential = new CommunicationTokenCredential(refreshOptions);
        return new MeetingUIClient(credential);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting ui client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```
Utilisez les API du Kit de développement logiciel (SDK) Teams Embed comme elles sont décrites dans la documentation. L’utilisation de l’API n’est pas traitée dans cette documentation. 

Supprimez le Kit de développement logiciel (SDK) Teams Embed et définissez `null` sur ses variables une fois que son utilisation n’est plus nécessaire ou que l’application a besoin d’utiliser le Kit de développement logiciel (SDK) ACS.
```java
private void disposeTeamsSdk() {
    try {
        meetingUIClient.dispose();
        meetingUIClientCall = null;
        meetingUIClient = null;
    } catch (Exception ex) {
        Toast.makeText(getContext(), "Failed to teardown Teams Sdk: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

La suppression du Kit de développement logiciel (SDK) Teams Embed n’est possible que s’il n’y a pas d’appels actifs. Le `meetingUIClient.dispose()` retourne une exception si un appel est actif. Raccrochez l’appel actif, puis appelez `disposeTeamsSdk()`.

```java
private void endMeeting() {
    try {
        meetingUIClientCall.hangUp();
        disposeTeamsSdk();
    } catch (Exception ex) {
        Toast.makeText(getContext(), "Failed to end meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

Implémentez la méthode d’interface `MeetingUIClientCallEventListener` `onCallStateChanged(MeetingUIClientCallState callState)` pour obtenir la mise à jour de l’état de l’appel actif qui se termine et supprimez le Kit de développement logiciel (SDK) Teams Embed.
```java
@Override
public void onCallStateChanged(MeetingUIClientCallState callState) {
    switch(callState) {
        case ENDED:
            disposeTeamsSdk();
            break;
        default:
        System.out.println("Call state changed to: " + callState.toString());
    }
}
```

Ajoutez d’autres méthodes d’interface `MeetingUIClientCallEventListener` obligatoires à la classe.

```java
@Override
public void onRemoteParticipantCountChanged(int newCount) {
    System.out.println("Remote participant count has changed to: " + newCount);
}

@Override
public void onIsMutedChanged() {
    System.out.println("Mute state changed to: " + meetingUIClientCall.isMuted());
}

@Override
public void onIsSendingVideoChanged() {
    System.out.println("Sending video state changed to: " + meetingUIClientCall.isSendingVideo());
}

@Override
public void onIsHandRaisedChanged(List<String> participantIds) {
    System.out.println("Self participant raise hand status changed to: " + meetingUIClientCall.isHandRaised());
}
```
