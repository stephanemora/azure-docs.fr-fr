---
title: Utilisation de la bibliothèque Azure Communication Services Teams Embed pour Android
description: Dans cette présentation, vous allez apprendre à utiliser la bibliothèque Azure Communication Services Teams Embed pour Android.
author: palatter
ms.author: palatter
ms.date: 24/02/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 73174081d416f8ff1ab532baaf62b5b466aeb178
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110164125"
---
## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Une ressource Communication Services déployée. [Créez une ressource Communication Services](../../create-communication-resource.md).
- Un `User Access Token` pour activer le client d’appel. Pour en savoir plus, consultez [Comment obtenir un `User Access Token`](../../access-tokens.md)
- Suivez le démarrage rapide pour [prendre en main l’ajout de Teams Embed à votre application](../getting-started-with-teams-embed.md).

## <a name="teams-embed-events"></a>Événements Teams Embed

Ajoutez le code suivant à votre fichier `MainActivity.java`.

```java
import androidx.core.content.ContextCompat;
import com.azure.android.communication.ui.meetings.MeetingUIClientCallState;
import com.azure.android.communication.ui.meetings.MeetingUIClientEventListener;
import com.azure.android.communication.ui.meetings.MeetingUIClientIdentityProvider;
import com.azure.android.communication.ui.meetings.MeetingUIClientIdentityProviderCallback;
```

Ajoutez `MeetingUIClientEventListener` à votre classe.

```java
public class MainActivity extends AppCompatActivity implements MeetingUIClientEventListener {

    private MeetingUIClient meetingUIClient;
```

Définissez `MeetingUIClientEventListener` sur `this`.

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    createMeetingClient();
}

private void createMeetingClient() {
    try {
        CommunicationTokenCredential credential = new CommunicationTokenCredential(tokenRefresher, true, ACS_TOKEN);
        meetingUIClient = new MeetingUIClient(credential);
        meetingUIClient.setMeetingUIClientEventListener(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

Implémentez les méthodes `onCallStateChanged` et `onRemoteParticipantCountChanged`.

```java
@Override
public void onCallStateChanged(MeetingUIClientCallState callState) {
    switch(callState) {
        case CONNECTING:
            System.out.println("Call state changed to 'Connecting'");
            break;
        case CONNECTED:
            System.out.println("Call state changed to 'Connected'");
            break;
        case WAITING_IN_LOBBY:
            System.out.println("Call state changed to 'Waiting in Lobby'");
            break;
        case ENDED:
            System.out.println("Call state changed to 'Ended'");
            break;
    }
}

@Override
public void onRemoteParticipantCountChanged(int newCount) {
    System.out.println("Remote participant count changed: " + newCount);
}
```

## <a name="assigning-avatars-for-users"></a>Attribution d’avatars aux utilisateurs

Ajoutez `MeetingUIClientIdentityProvider` à votre classe.

```java
public class MainActivity extends AppCompatActivity implements MeetingUIClientIdentityProvider {

    private MeetingUIClient meetingUIClient;
```

Définissez `MeetingUIClientIdentityProvider` sur `this`.

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    createMeetingClient();
}

private void createMeetingClient() {
    try {
        CommunicationTokenCredential credential = new CommunicationTokenCredential(tokenRefresher, true, ACS_TOKEN);
        meetingUIClient = new MeetingUIClient(credential);
        meetingUIClient.setMeetingUIClientIdentityProvider(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

Mappez chaque `userMri` avec l’avatar correspondant.

```java
@Override
public void provideAvatarFor(String userIdentifier, MeetingUIClientIdentityProviderCallback meetingIdentityProviderCallback) {
    try {
        System.out.println("MeetingUIClientIdentityProvider.provideAvatarFor called for userIdentifier: " + userIdentifier);
        if (userIdentifier.startsWith("8:teamsvisitor:")) {
            meetingIdentityProviderCallback.onAvatarAvailable(ContextCompat.getDrawable(this, R.drawable.nodpi_avatar_placeholder_large_pink));
        } else if (userIdentifier.startsWith("8:orgid:")) {
            meetingIdentityProviderCallback.onAvatarAvailable(ContextCompat.getDrawable(this, R.drawable.nodpi_doctor_avatar));
        } else if (userIdentifier.startsWith("8:acs:")) {
            meetingIdentityProviderCallback.onAvatarAvailable(ContextCompat.getDrawable(this, R.drawable.nodpi_avatar_placeholder_large_green));
        }
    } catch (Exception e) {
        System.out.println("MeetingUIClientIdentityProvider: Exception while provideAvatarFor for userIdentifier: " + userIdentifier + e.getMessage());
    }
}
```


Ajoutez d’autres méthodes d’interface MeetingUIClientIdentityProvider obligatoires à la classe. Elles peuvent être laissées avec une implémentation vide.

```java
@Override
public void provideDisplayNameFor(String userIdentifier, MeetingUIClientIdentityProviderCallback meetingUIClientIdentityProviderCallback) {
}

@Override
public void provideSubTitleFor(String userIdentifier, MeetingUIClientIdentityProviderCallback meetingUIClientIdentityProviderCallback) {
}
```
