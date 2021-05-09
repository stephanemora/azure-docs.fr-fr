---
title: 'Démarrage rapide : Ajouter la participation à une réunion Teams à une application Android avec Azure Communication Services'
description: Dans ce guide de démarrage rapide, vous allez apprendre à utiliser la bibliothèque Azure Communication Services Teams Embed pour Android.
author: palatter
ms.author: palatter
ms.date: 01/25/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 331d8eb6ed74880a855934fad4d3e1afc9b29109
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108313453"
---
Dans ce guide de démarrage rapide, vous allez découvrir comment rejoindre une réunion Microsoft Teams en utilisant la bibliothèque Azure Communication Services Teams Embed pour Android.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Android Studio](https://developer.android.com/studio), pour la création de votre application Android
- Une ressource Communication Services déployée. [Créer une ressource Communication Services](../../create-communication-resource.md)
- Un [jeton d’accès utilisateur](../../access-tokens.md) pour votre service Azure Communication

## <a name="setting-up"></a>Configuration

### <a name="create-an-android-app-with-an-empty-activity"></a>Créer une application Android avec une activité vide

Dans Android Studio, sélectionnez Start a new Android Studio project (Commencer un nouveau projet Android Studio).

:::image type="content" source="../media/android/studio-new-project.png" alt-text="Capture d’écran montrant le bouton « Start a new Android Studio Project » sélectionné dans Android Studio.":::

Sélectionnez le modèle de projet « Empty Activity » sous « Phone and Tablet ».

:::image type="content" source="../media/android/studio-blank-activity.png" alt-text="Capture d’écran montrant l’option « Empty Activity » sélectionnée dans l’écran Project Template.":::

Nommez le projet `TeamsEmbedAndroidGettingStarted` , définissez le langage sur Java et sélectionnez Kit de développement logiciel (SDK) minimal « API 21 : Android 5.0 (Lollipop) » ou version ultérieure.

:::image type="content" source="../media/android/studio-calling-min-api.png" alt-text="Capture d’écran montrant l’option « Empty Activity » sélectionnée dans l’écran 2 Project Template.":::


### <a name="install-the-azure-package"></a>Installer le package Azure

Dans votre niveau d’application (**dossier d’application**) `build.gradle`, ajoutez les lignes suivantes aux sections android et dependencies.

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
}
```

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-common:1.0.0-beta.8'
    ...
}
```

Mettez à jour les valeurs sur le fichier `build.gradle`

```groovy
 buildTypes {
        release {
        ...
            minifyEnabled true
            shrinkResources true
        ...
    }
}
```


### <a name="install-the-teams-embed-package"></a>Installer le package Teams Embed

Téléchargez le package [`MicrosoftTeamsSDK`.](https://github.com/Azure/communication-teams-embed/releases)

Décompressez ensuite le dossier `MicrosoftTeamsSDK` dans le dossier d’application du projet. Ex. `TeamsEmbedAndroidGettingStarted/app/MicrosoftTeamsSDK`.

### <a name="add-teams-embed-package-to-your-buildgradle"></a>Ajouter le package Teams Embed à build.gradle

Dans `build.gradle` au niveau de l’application, ajoutez la ligne suivante à la fin du fichier :

```groovy
apply from: 'MicrosoftTeamsSDK/MicrosoftTeamsSDK.gradle'
```

Synchronisez le projet avec les fichiers gradle.

### <a name="create-application-class"></a>Créer une classe d’application

Créez un fichier de classe d’application Java appelé `TeamsEmbedAndroidGettingStarted`. Cette classe sera la classe d’application qui doit étendre `TeamsSDKApplication`. [Documentation Android](https://developer.android.com/reference/android/app/Application)

:::image type="content" source="../media/android/application-class-location.png" alt-text="Capture d’écran montrant où créer la classe d’application dans Android Studio":::

```java
package com.microsoft.teamsembedandroidgettingstarted;

import com.microsoft.teamssdk.app.TeamsSDKApplication;

public class TeamsEmbedAndroidGettingStarted extends TeamsSDKApplication {
}
```

### <a name="update-themes"></a>Mettre à jour les thèmes

Définissez le nom du style sur `AppTheme` dans vos fichiers `theme.xml` et `theme.xml (night)`.

:::image type="content" source="../media/android/theme-settings.png" alt-text="Capture d’écran montrant les fichiers theme.xml dans Android Studio":::

```xml
<style name="AppTheme" parent="Theme.AppCompat.DayNight.DarkActionBar">
```

```xml
<style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
```

### <a name="add-permissions-to-application-manifest"></a>Ajouter des autorisations au manifeste de l’application

Ajoutez l'autorisation `RECORD_AUDIO` au manifeste de votre application (`app/src/main/AndroidManifest.xml`) :  


```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.yourcompany.TeamsEmbedAndroidGettingStarted">
    <uses-permission android:name="android.permission.RECORD_AUDIO"/>
    <application
```

### <a name="add-app-name-and-theme-to-application-manifest"></a>Ajouter le nom et le thème de l’application au manifeste de l’application

Ajoutez xmlns:tools=« http://schemas.android.com/tools  » au manifeste.

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    package="com.yourcompany.TeamsEmbedAndroidGettingStarted">
```

Ajoutez `.TeamsEmbedAndroidGettingStarted` à `android:name`, `android:name` à `tools:replace` et remplacez `android:theme` par `@style/AppTheme`.

```xml
<application
    android:name=".TeamsEmbedAndroidGettingStarted"
    tools:replace="android:name"
    android:theme="@style/AppTheme"
    android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:roundIcon="@mipmap/ic_launcher_round"
    android:supportsRtl="true">
```

### <a name="set-up-the-layout-for-the-app"></a>Configurer la disposition de l’application

Créez un bouton avec un ID `join_meeting`. Accédez au fichier de disposition (`app/src/main/res/layout/activity_main.xml`) et remplacez le contenu du fichier par ce qui suit :

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/join_meeting"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Join Meeting"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>Créer la génération de modèles automatique et les liaisons de l’activité principale

La mise en page étant créée, la génération de modèles automatique de base de l’activité, de même que les liaisons requises peuvent être ajoutées. L’activité gère les demandes d’autorisations au moment de l’exécution, la création du client de réunion et la participation à une réunion en cas d’appui sur le bouton. Chacune de ces opérations est traitée dans sa propre section. 

La méthode `onCreate` sera substituée pour appeler `getAllPermissions` et `createAgent`, ainsi que pour ajouter les liaisons pour le bouton `Join Meeting`. Cela ne se produit qu’une seule fois lors de la création de l’activité. Pour plus d’informations sur `onCreate`, consultez le guide intitulé [Understand the Activity Lifecycle](https://developer.android.com/guide/components/activities/activity-lifecycle) (Présentation du cycle de vie des activités).

Accédez à **MainActivity.java** et remplacez le contenu par le code suivant :

```java
package com.yourcompany.teamsembedandroidgettingstarted;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.Toast;

import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.common.CommunicationTokenRefreshOptions;
import com.azure.android.communication.ui.meetings.MeetingUIClientJoinOptions;
import com.azure.android.communication.ui.meetings.MeetingUIClient;
import com.azure.android.communication.ui.meetings.MeetingUIClientTeamsMeetingLinkLocator;

import java.util.ArrayList;
import java.util.concurrent.Callable;

public class MainActivity extends AppCompatActivity {

    private final String displayName = "John Smith";

    private MeetingUIClient meetingUIClient;
    private MeetingUIClientJoinOptions meetingJoinOptions;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);
        
        getAllPermissions();
        createMeetingClient();

        Button joinMeeting = findViewById(R.id.join_meeting);
        joinMeeting.setOnClickListener(l -> joinMeeting());
    }

    private void createMeetingClient() {
        // See section on creating meeting client
    }

    private void joinMeeting() {
        // See section on joining a meeting
    }

    private void getAllPermissions() {
        // See section on getting permissions
    }
}
```

### <a name="request-permissions-at-runtime"></a>Demander des autorisations au moment de l’exécution

Pour Android 6.0 et ultérieur (niveau d’API 23) et `targetSdkVersion` 23 ou plus, les autorisations sont accordées au moment de l’exécution et non lors de l’installation de l’application. Pour prendre cela en charge, vous pouvez implémenter `getAllPermissions` afin d’appeler `ActivityCompat.checkSelfPermission` et `ActivityCompat.requestPermissions` pour chaque autorisation requise.

```java
/**
 * Request each required permission if the app doesn't already have it.
 */
private void getAllPermissions() {
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 202);
    }
}
```

> [!NOTE]
> Lorsque vous concevez votre application, tenez compte du moment où ces autorisations doivent être demandées. Les autorisations doivent être demandées lorsqu’elles sont nécessaires, et non à l’avance. Pour plus d’informations, consultez le [guide des autorisations Android](https://developer.android.com/training/permissions/requesting).

## <a name="object-model"></a>Modèle objet

Les classes et les interfaces suivantes gèrent certaines des principales fonctionnalités de la bibliothèque Azure Communication Services Teams Embed :

| Nom                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| MeetingUIClient| L’élément MeetingUIClient correspond au point d’entrée principal de la bibliothèque Teams Embed. |
| MeetingUIClientJoinOptions | Les éléments MeetingUIClientJoinOptions sont utilisés pour les options configurables telles que le nom d’affichage. |
| MeetingUIClientTeamsMeetingLinkLocator | MeetingUIClientTeamsMeetingLinkLocator est utilisé pour définir l’URL de réunion pour rejoindre une réunion. |
| MeetingUIClientGroupCallLocator | MeetingUIClientGroupCallLocator est utilisé pour définir l’ID de groupe à rejoindre. |
| MeetingUIClientCallState | L’élément MeetingUIClientCallState est utilisé pour signaler les changements d’état d’appel. Les options disponibles sont les suivantes : `connecting`, `waitingInLobby`, `connected` et `ended`. |
| MeetingUIClientEventListener | L’élément MeetingUIClientEventListener est utilisé pour recevoir des événements, tels que les changements d’état d’appel. |
| MeetingUIClientIdentityProvider | L’élément MeetingUIClientIdentityProvider est utilisé pour mapper les détails d’utilisateur sur les utilisateurs d’une réunion. |
| MeetingUIClientUserEventListener | L’élément MeetingUIClientUserEventListener est utilisé pour fournir des informations sur les actions de l’utilisateur dans l’interface utilisateur. |

## <a name="create-a-meetingclient-from-the-user-access-token"></a>Créer un élément MeetingClient à partir du jeton d’accès utilisateur

Un client de réunion authentifié peut être instancié avec un jeton d’accès utilisateur. Ce jeton est généré par un service avec une authentification spécifique à l’application. Pour plus d’informations sur les jetons d’accès utilisateur, consultez le guide [Jetons d’accès utilisateur](../../access-tokens.md). Pour les besoins de ce guide de démarrage rapide, remplacez `<USER_ACCESS_TOKEN>` par un jeton d’accès utilisateur généré pour votre ressource Azure Communication Services.

```java
private void createMeetingClient() { 
    try {
        CommunicationTokenRefreshOptions refreshOptions = new CommunicationTokenRefreshOptions(tokenRefresher, true, "<USER_ACCESS_TOKEN>");
        CommunicationTokenCredential credential = new CommunicationTokenCredential(refreshOptions);
        meetingUIClient = new MeetingUIClient(credential);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="setup-token-refreshing"></a>Configurer l’actualisation du jeton

Créez une méthode `tokenRefresher` pouvant être appelée. Créez ensuite une méthode `fetchToken` pour obtenir le jeton utilisateur. [Vous trouverez des instructions sur la manière de procéder ici](../../access-tokens.md?pivots=programming-language-java)

```java
Callable<String> tokenRefresher = () -> {
    return fetchToken();
};

public String fetchToken() {
    // Get token
    return USER_ACCESS_TOKEN;
}
```

## <a name="get-the-teams-meeting-link"></a>Obtenir le lien de réunion Teams

Le lien de réunion Teams peut être récupéré par le biais des API Graph. Cette procédure est détaillée dans la [documentation de Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true).
Le kit SDK Communication Services Calling accepte un lien de réunion Teams complet. Ce lien est retourné comme faisant partie de la ressource `onlineMeeting`, accessible sous la [propriété `joinWebUrl`](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true). Vous pouvez également récupérer les informations de réunion nécessaires à partir de l’URL **Rejoindre la réunion** dans l’invite de réunion Teams elle-même.

## <a name="start-a-meeting-using-the-meeting-client"></a>Démarrer une réunion à l’aide du client de réunion

Rejoindre une réunion peut se faire via `MeetingUIClient` , et nécessite simplement un `MeetingUIClientTeamsMeetingLinkLocator` et le `MeetingUIClientJoinOptions` . Remplacez `<MEETING_URL>` par une URL de réunion Teams.

```java
/**
 * Join a meeting with a meetingURL.
 */
private void joinMeeting() {
    try {
        MeetingUIClientTeamsMeetingLinkLocator meetingUIClientTeamsMeetingLinkLocator = new MeetingUIClientTeamsMeetingLinkLocator(<MEETING_URL>);
        meetingUIClient.join(meetingUIClientTeamsMeetingLinkLocator, meetingJoinOptions);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="launch-the-app-and-join-a-meeting"></a>Lancer l’application et rejoindre une réunion

L’application peut maintenant être lancée à l’aide du bouton « Run App » de la barre d’outils (Maj+F10). 

:::image type="content" source="../media/android/quickstart-android-join-meeting.png" alt-text="Capture d’écran montrant l’application terminée.":::

:::image type="content" source="../media/android/quickstart-android-joined-meeting.png" alt-text="Capture d’écran montrant l’application terminée une fois la réunion rejointe.":::

## <a name="sample-code"></a>Exemple de code

Vous pouvez télécharger l’exemple d’application à partir de [GitHub](https://github.com/Azure-Samples/teams-embed-android-getting-started).