---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: be7eee7c8de5cae201660b4d165ca51bb40b3c19
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114339520"
---
Commencez à utiliser Azure Communication Services avec la bibliothèque de client d’appel Communication Services pour ajouter des appels vidéo 1 à 1 à votre application. Vous allez découvrir comment démarrer un appel vidéo et y répondre à l’aide du SDK d’appel Azure Communication Services pour Android.

## <a name="sample-code"></a>Exemple de code

Si vous souhaitez passer à la fin, vous pouvez télécharger l’exemple d’application sur [GitHub](https://github.com/Azure-Samples/communication-services-android-quickstarts/tree/main/videoCallingQuickstart).

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Android Studio](https://developer.android.com/studio), pour la création de votre application Android
- Une ressource Communication Services déployée. [Créer une ressource Communication Services](../../../create-communication-resource.md)
- Un [jeton d’accès utilisateur](../../../access-tokens.md) pour votre service Azure Communication

### <a name="create-an-android-app-with-an-empty-activity"></a>Créer une application Android avec une activité vide

Dans Android Studio, sélectionnez Start a new Android Studio project (Commencer un nouveau projet Android Studio).

:::image type="content" source="../../media/android/studio-new-project.png" alt-text="Capture d’écran montrant le bouton « Start a new Android Studio Project » sélectionné dans Android Studio.":::

Sélectionnez le modèle de projet « Empty Activity » sous « Phone and Tablet ».

:::image type="content" source="../../media/android/studio-blank-activity.png" alt-text="Capture d’écran montrant l’option « Empty Activity » sélectionnée dans l’écran Project Template.":::

Sélectionnez le kit de développement logiciel (SDK) minimal « API 26 : Android 8.0 (Oreo) » ou version ultérieure.

:::image type="content" source="../../media/android/studio-calling-min-api.png" alt-text="Capture d’écran montrant l’option « Empty Activity » sélectionnée dans l’écran 2 Project Template.":::

### <a name="install-the-package"></a>Installer le package

Recherchez votre niveau de projet `build.gradle` et veillez à ajouter `mavenCentral()` à la liste des référentiels sous `buildscript` et `allprojects`.
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
Ensuite, dans votre niveau de module `build.gradle`, ajoutez les lignes suivantes aux sections dependencies et android :

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0'
    ...
}
```
### <a name="add-permissions-to-application-manifest"></a>Ajouter des autorisations au manifeste de l’application

Afin de pouvoir demander les autorisations nécessaires pour effectuer un appel, vous devez d’abord les déclarer dans le manifeste de l’application (`app/src/main/AndroidManifest.xml`). Remplacez le contenu du fichier par ce qui suit :

```xml
    <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.contoso.acsquickstart">

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.CAMERA" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <!--Our Calling SDK depends on the Apache HTTP SDK.
When targeting Android SDK 28+, this library needs to be explicitly referenced.
See https://developer.android.com/about/versions/pie/android-9.0-changes-28#apache-p-->
        <uses-library android:name="org.apache.http.legacy" android:required="false"/>
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
    
```
### <a name="set-up-the-layout-for-the-app"></a>Configurer la disposition de l’application

Nous avons besoin d’une entrée de texte pour l’ID de l’appelé, d’un bouton pour lancer un appel et d’un autre bouton pour raccrocher. Nous avons aussi besoin de deux boutons pour activer et désactiver la vidéo locale. Nous devons placer deux conteneurs pour les flux vidéo locaux et distants. Vous pouvez les ajouter par le biais du concepteur ou en modifiant le fichier xml de disposition. Accédez à `app/src/main/res/layout/activity_main.xml` et remplacez le contenu du fichier par ceci :

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical">

            <EditText
                android:id="@+id/callee_id"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:ems="10"
                android:gravity="center"
                android:hint="Callee Id"
                android:inputType="textPersonName"
                app:layout_constraintBottom_toTopOf="@+id/call_button"
                app:layout_constraintEnd_toEndOf="parent"
                app:layout_constraintStart_toStartOf="parent"
                app:layout_constraintTop_toTopOf="parent"
                app:layout_constraintVertical_bias="0.064" />
            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content">
                <Button
                    android:id="@+id/call_button"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_marginBottom="16dp"
                    android:gravity="center"
                    android:text="Call"
                    app:layout_constraintBottom_toBottomOf="parent"
                    app:layout_constraintEnd_toEndOf="parent"
                    app:layout_constraintStart_toStartOf="parent" />
                <Button
                    android:id="@+id/show_preview"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_marginBottom="16dp"
                    android:gravity="center"
                    android:text="Show Video"
                    app:layout_constraintBottom_toBottomOf="parent"
                    app:layout_constraintEnd_toEndOf="parent"
                    app:layout_constraintStart_toStartOf="parent" />
                <Button
                    android:id="@+id/hide_preview"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_marginBottom="16dp"
                    android:gravity="center"
                    android:text="Hide Video"
                    app:layout_constraintBottom_toBottomOf="parent"
                    app:layout_constraintEnd_toEndOf="parent"
                    app:layout_constraintStart_toStartOf="parent" />
                <Button
                    android:id="@+id/hang_up"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_marginBottom="16dp"
                    android:gravity="center"
                    android:text="Hang Up"
                    app:layout_constraintBottom_toBottomOf="parent"
                    app:layout_constraintEnd_toEndOf="parent"
                    app:layout_constraintStart_toStartOf="parent" />
            </LinearLayout>
            <FrameLayout
                android:layout_width="match_parent"
                android:layout_height="match_parent">
                <LinearLayout
                    android:id="@+id/remotevideocontainer"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:gravity="end"
                    android:orientation="horizontal"
                    android:padding="10dp"></LinearLayout>
                <LinearLayout
                    android:id="@+id/localvideocontainer"
                    android:layout_width="180dp"
                    android:layout_height="320dp"
                    android:layout_gravity="right|bottom"
                    android:orientation="horizontal"
                    android:padding="10dp"></LinearLayout>

            </FrameLayout>
        </LinearLayout>
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>Créer la génération de modèles automatique et les liaisons de l’activité principale

La mise en page étant créée, nous pouvons ajouter les liaisons ainsi que la génération de modèles automatique de base de l’activité. L’activité gère les demandes d’autorisations au moment de l’exécution, la création de l’agent d’appel et l’établissement de l’appel en cas d’appui sur le bouton. Chacune de ces opérations est traitée dans sa propre section. La méthode `onCreate` est substituée pour appeler `getAllPermissions` et `createAgent`, ainsi que pour ajouter les liaisons pour le bouton d’appel. Cela ne se produit qu’une seule fois lors de la création de l’activité. Pour plus d’informations sur `onCreate`, consultez le guide intitulé [Understand the Activity Lifecycle](https://developer.android.com/guide/components/activities/activity-lifecycle) (Présentation du cycle de vie des activités).

Accédez à **MainActivity.java** et remplacez le contenu par le code suivant :

```java
package com.example.acsquickstart;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.media.AudioManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;
import android.widget.LinearLayout;
import android.content.Context;
import com.azure.android.communication.calling.CallState;
import com.azure.android.communication.calling.CallingCommunicationException;
import com.azure.android.communication.calling.CameraFacing;
import com.azure.android.communication.calling.PropertyChangedEvent;
import com.azure.android.communication.calling.VideoDeviceInfo;
import com.azure.android.communication.common.CommunicationUserIdentifier;
import com.azure.android.communication.common.CommunicationIdentifier;
import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.calling.CallAgent;
import com.azure.android.communication.calling.CallClient;
import com.azure.android.communication.calling.StartCallOptions;
import com.azure.android.communication.calling.DeviceManager;
import com.azure.android.communication.calling.VideoOptions;
import com.azure.android.communication.calling.LocalVideoStream;
import com.azure.android.communication.calling.VideoStreamRenderer;
import com.azure.android.communication.calling.VideoStreamRendererView;
import com.azure.android.communication.calling.CreateViewOptions;
import com.azure.android.communication.calling.ScalingMode;
import com.azure.android.communication.calling.IncomingCall;
import com.azure.android.communication.calling.Call;
import com.azure.android.communication.calling.AcceptCallOptions;
import com.azure.android.communication.calling.ParticipantsUpdatedEvent;
import com.azure.android.communication.calling.RemoteParticipant;
import com.azure.android.communication.calling.RemoteVideoStream;
import com.azure.android.communication.calling.RemoteVideoStreamsEvent;
import com.azure.android.communication.calling.RendererListener;

import java.util.ArrayList;
import java.util.List;
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;

public class MainActivity extends AppCompatActivity {

    private CallAgent callAgent;
    private LocalVideoStream currentVideoStream;
    private DeviceManager deviceManager;
    private IncomingCall incomingCall;
    private Call call;
    VideoStreamRenderer previewRenderer;
    VideoStreamRendererView preview;
    final Map<Integer, StreamData> streamData = new HashMap<>();
    private boolean renderRemoteVideo = true;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        getAllPermissions();
        createAgent();

        handleIncomingCall();

        Button callButton = findViewById(R.id.call_button);
        callButton.setOnClickListener(l -> startCall());
        Button hangupButton = findViewById(R.id.hang_up);
        hangupButton.setOnClickListener(l -> hangUp());
        Button startVideo = findViewById(R.id.show_preview);
        startVideo.setOnClickListener(l -> turnOnLocalVideo());
        Button stopVideo = findViewById(R.id.hide_preview);
        stopVideo.setOnClickListener(l -> turnOffLocalVideo());
        
        setVolumeControlStream(AudioManager.STREAM_VOICE_CALL);
    }

    /**
     * Request each required permission if the app doesn't already have it.
     */
    private void getAllPermissions() {
        // See section on requesting permissions
    }

    /**
      * Create the call agent for placing calls
      */
    private void createAgent() {
        // See section on creating the call agent
    }

    /**
     * Place a call to the callee id provided in `callee_id` text input.
     */
    private void startCall() {
        // See section on starting the call
    }

    /**
     * Handle incoming calls
     */
    private void handleIncomingCall() {
        // See section on answering incoming call
    }

    /**
     * Mid-call operations
     */
    public void turnOnLocalVideo() {
        // See setion on 
    }
    public void turnOffLocalVideo() {
        
    }

    /**
     * End calls
     */
    private void hangUp() {
        // See section on ending the call
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
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO, Manifest.permission.CAMERA, Manifest.permission.WRITE_EXTERNAL_STORAGE, Manifest.permission.READ_PHONE_STATE};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 1);
    }
}
```

> [!NOTE]
> Lorsque vous concevez votre application, tenez compte du moment où ces autorisations doivent être demandées. Les autorisations doivent être demandées lorsqu’elles sont nécessaires, et non à l’avance. Pour plus d’informations, consultez le [guide des autorisations Android](https://developer.android.com/training/permissions/requesting).

## <a name="object-model"></a>Modèle objet

Les classes et les interfaces suivantes gèrent certaines des principales fonctionnalités du SDK Azure Communication Services Calling :

| Nom                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| CallClient est le point d’entrée principal du SDK Calling.|
| CallAgent | CallAgent sert à démarrer et à gérer les appels. |
| CommunicationTokenCredential | CommunicationTokenCredential est utilisé comme informations d’identification du jeton pour instancier CallAgent.|
| CommunicationIdentifier | CommunicationIdentifier est utilisé comme type de participant différent susceptible de faire partie d’un appel.|

## <a name="create-an-agent-from-the-user-access-token"></a>Créer un agent à partir du jeton d’accès utilisateur

Vous avez besoin d’un jeton d’utilisateur pour créer un agent d’appel authentifié. En général, ce jeton est généré à partir d’un service avec une authentification propre à l’application. Pour plus d’informations sur les jetons d’accès utilisateur, consultez le [guide des jetons d’accès utilisateur](../../../access-tokens.md). 

Pour les besoins de ce guide de démarrage rapide, remplacez `<User_Access_Token>` par un jeton d’accès utilisateur généré pour votre ressource Azure Communication Services.

```java
/**
 * Create the call agent for placing calls
 */
private void createAgent() {
    Context context = this.getApplicationContext();
    String userToken = "<User_Access_Token>";
    try {
        CommunicationTokenCredential credential = new CommunicationTokenCredential(userToken);
        CallClient callClient = new CallClient();
        deviceManager = callClient.getDeviceManager(context).get();
        callAgent = callClient.createCallAgent(getApplicationContext(), credential).get(); 
    } catch (Exception ex) {
        Toast.makeText(context, "Failed to create call agent.", Toast.LENGTH_SHORT).show();
    }
}
```
## <a name="start-a-video-call-using-the-call-agent"></a>Démarrer un appel vidéo à l’aide de l’agent d’appel

L’appel peut être établi par le biais de l’agent d’appel, et cela nécessite simplement de fournir une liste d’ID d’appelés et les options d’appel. 

Pour passer un appel avec la vidéo, vous devez énumérer les caméras locales à l’aide de l’API `deviceManager` `getCameras`. Une fois la caméra sélectionnée, utilisez-la pour créer une instance `LocalVideoStream` et la transmettre à `videoOptions` en tant qu’élément dans le tableau `localVideoStream` vers une méthode d’appel. Une fois l’appel connecté, il commence automatiquement à envoyer un flux vidéo à l’autre participant à partir de la caméra sélectionnée.

```java
private void startCall() {
    Context context = this.getApplicationContext();
    EditText calleeIdView = findViewById(R.id.callee_id);
    String calleeId = calleeIdView.getText().toString();
    ArrayList<CommunicationIdentifier> participants = new ArrayList<CommunicationIdentifier>();
    StartCallOptions options = new StartCallOptions();
    List<VideoDeviceInfo> cameras = deviceManager.getCameras();
    if(!cameras.isEmpty()) {
        VideoDeviceInfo camera = chooseCamera(cameras);
        currentVideoStream = new LocalVideoStream(camera, context);
        LocalVideoStream[] videoStreams = new LocalVideoStream[1];
        videoStreams[0] = currentVideoStream;
        VideoOptions videoOptions = new VideoOptions(videoStreams);
        options.setVideoOptions(videoOptions);
        showPreview(currentVideoStream);
    }
    participants.add(new CommunicationUserIdentifier(calleeId));

    call = callAgent.startCall(
            context,
            participants,
            options);
    
    //Subcribe to events on updates of call state and remote participants
    call.addOnRemoteParticipantsUpdatedListener(this::handleRemoteParticipantsUpdate);
    call.addOnStateChangedListener(this::handleCallOnStateChanged);
}
```

Dans ce guide de démarrage rapide, nous préférons utiliser la caméra avant de l’appareil. La fonction `chooseCamera` prend l’énumération des caméras en tant qu’entrée. Si la caméra avant n’est pas disponible, nous utilisons la première caméra disponible. S’il n’y a aucune caméra disponible lorsque nous appuyons sur `Start Call`, un appel audio est lancé. Mais si le participant distant a répondu avec la vidéo, nous pouvons quand même voir le flux vidéo distant. 

```java
VideoDeviceInfo chooseCamera(List<VideoDeviceInfo> cameras) {
    for (VideoDeviceInfo camera : cameras) {
        if (camera.getCameraFacing() == CameraFacing.FRONT) {
            return camera;
        }
    }
    return cameras.get(0);
}
```

Une fois que nous avons construit une instance `LocalVideoStream`, nous pouvons créer un renderer pour l’afficher dans l’interface utilisateur. 

```java
private void showPreview(LocalVideoStream stream) {
    previewRenderer = new VideoStreamRenderer(stream, this);
    LinearLayout layout = ((LinearLayout)findViewById(R.id.localvideocontainer));
    preview = previewRenderer.createView(new CreateViewOptions(ScalingMode.FIT));
    runOnUiThread(() -> {
        layout.addView(preview);
    });
}
```

## <a name="accept-an-incoming-call"></a>Acceptation d’un appel entrant

L’appel entrant peut être obtenu par abonnement à `addOnIncomingCallListener` sur `callAgent`. 

```java
private void handleIncomingCall() {
    callAgent.addOnIncomingCallListener((incomingCall) -> {
        this.incomingCall = incomingCall;
        Executors.newCachedThreadPool().submit(this::answerIncomingCall);
    });
}
```

Pour accepter un appel avec une caméra vidéo activée, énumérez les caméras locales à l’aide de l’API deviceManager getCameras, choisissez une caméra, puis construisez une instance `LocalVideoStream` et transmettez-la à `acceptCallOptions` avant d’appeler la méthode « accept » sur un objet `call`. 
```java
private void answerIncomingCall() {
    Context context = this.getApplicationContext();
    if (incomingCall == null){
        return;
    }
    AcceptCallOptions acceptCallOptions = new AcceptCallOptions();
    List<VideoDeviceInfo> cameras = deviceManager.getCameras();
    if(!cameras.isEmpty()) {
        VideoDeviceInfo camera = chooseCamera(cameras);
        currentVideoStream = new LocalVideoStream(camera, context);
        LocalVideoStream[] videoStreams = new LocalVideoStream[1];
        videoStreams[0] = currentVideoStream;
        VideoOptions videoOptions = new VideoOptions(videoStreams);
        acceptCallOptions.setVideoOptions(videoOptions);
        showPreview(currentVideoStream);
    }
    try {
        call = incomingCall.accept(context, acceptCallOptions).get();
    } catch (InterruptedException e) {
        e.printStackTrace();
    } catch (ExecutionException e) {
        e.printStackTrace();
    }

    //Subcribe to events on updates of call state and remote participants
    call.addOnRemoteParticipantsUpdatedListener(this::handleRemoteParticipantsUpdate);
    call.addOnStateChangedListener(this::handleCallOnStateChanged);
}
```

## <a name="remote-participant-and-remote-video-streams"></a>Le participant distant et les flux vidéo distants

Quand nous lançons un appel ou répondons à un appel entrant, nous avons besoin de nous abonner à l’événement `addOnRemoteParticipantsUpdatedListener` pour gérer les participants distants. 

```java
call.addOnRemoteParticipantsUpdatedListener(this::handleRemoteParticipantsUpdate);
```

### <a name="remote-participant-and-remote-video-stream-update"></a>Mise à jour du participant distant et des flux vidéo distants

Dans ce guide de démarrage rapide, nous gérons uniquement les participants ajoutés, car nous implémentons un appel 1:1. Quand le participant distant est supprimé, l’appel est terminé. Pour un participant ajouté, nous nous abonnons à `addOnVideoStreamsUpdatedListener` pour gérer les mises à jour de flux vidéo. 

```java
public void handleRemoteParticipantsUpdate(ParticipantsUpdatedEvent args) {
    LinearLayout participantVideoContainer = findViewById(R.id.remotevideocontainer);
    handleAddedParticipants(args.getAddedParticipants(),participantVideoContainer);
}

private void handleAddedParticipants(List<RemoteParticipant> participants, LinearLayout participantVideoContainer) {
    for (RemoteParticipant remoteParticipant : participants) {
        remoteParticipant.addOnVideoStreamsUpdatedListener(videoStreamsEventArgs -> videoStreamsUpdated(videoStreamsEventArgs));
    }
}

private void videoStreamsUpdated(RemoteVideoStreamsEvent videoStreamsEventArgs) {
    for(RemoteVideoStream stream : videoStreamsEventArgs.getAddedRemoteVideoStreams()) {
        StreamData data = new StreamData(stream, null, null);
        streamData.put(stream.getId(), data);
        if (renderRemoteVideo) {
            startRenderingVideo(data);
        }
    }

    for(RemoteVideoStream stream : videoStreamsEventArgs.getRemovedRemoteVideoStreams()) {
        stopRenderingVideo(stream);
    }
}
```

### <a name="render-remote-videos"></a>Afficher des vidéos distantes

Créez un renderer du flux vidéo distant et attachez-le à l’affichage pour commencer à effectuer le rendu de l’affichage distant. Supprimez l’affichage pour en cesser le rendu. 

```java
void startRenderingVideo(StreamData data){
    if (data.renderer != null) {
        return;
    }
    LinearLayout layout = ((LinearLayout)findViewById(R.id.remotevideocontainer));
    data.renderer = new VideoStreamRenderer(data.stream, this);
    data.renderer.addRendererListener(new RendererListener() {
        @Override
        public void onFirstFrameRendered() {
            String text = data.renderer.getSize().toString();
            Log.i("MainActivity", "Video rendering at: " + text);
        }

        @Override
        public void onRendererFailedToStart() {
            String text = "Video failed to render";
            Log.i("MainActivity", text);
        }
    });
    data.rendererView = data.renderer.createView(new CreateViewOptions(ScalingMode.FIT));
    runOnUiThread(() -> {
        layout.addView(data.rendererView);
    });
}

void stopRenderingVideo(RemoteVideoStream stream) {
    StreamData data = streamData.get(stream.getId());
    if (data == null || data.renderer == null) {
        return;
    }
    runOnUiThread(() -> {
        ((LinearLayout) findViewById(R.id.remotevideocontainer)).removeAllViews();
    });
    data.rendererView = null;
    // Dispose renderer
    data.renderer.dispose();
    data.renderer = null;
}

static class StreamData {
    RemoteVideoStream stream;
    VideoStreamRenderer renderer;
    VideoStreamRendererView rendererView;
    StreamData(RemoteVideoStream stream, VideoStreamRenderer renderer, VideoStreamRendererView rendererView) {
        this.stream = stream;
        this.renderer = renderer;
        this.rendererView = rendererView;
    }
}
```

## <a name="call-state-update"></a>Mise à jour de l’état d’appel
Dans ce guide de démarrage rapide, nous allons gérer les modifications de `CallState`. Quand l’appel est connecté, nous gérons le participant distant et, quand l’appel est déconnecté, nous supprimons le `previewRenderer` pour arrêter la vidéo locale. 

```groovy
private void handleCallOnStateChanged(PropertyChangedEvent args) {
    if (call.getState() == CallState.CONNECTED) {
        LinearLayout participantVideoContainer = findViewById(R.id.remotevideocontainer);
        handleAddedParticipants(call.getRemoteParticipants(),participantVideoContainer);
    }
    if (call.getState() == CallState.DISCONNECTED) {
        if (previewRenderer != null) {
            previewRenderer.dispose();
        }
    }
}
```

## <a name="end-a-call"></a>Terminer un appel
Mettez fin à l’appel en appelant la fonction `hangUp()`sur l’instance d’appel et supprimez le `previewRenderer` pour arrêter la vidéo locale. 
```java
private void hangUp() {
    try {
        call.hangUp().get();
    } catch (ExecutionException | InterruptedException e) {
        e.printStackTrace();
    }
    if (previewRenderer != null) {
        previewRenderer.dispose();
    }
}
```

## <a name="run-the-code"></a>Exécuter le code

L’application peut maintenant être lancée à l’aide du bouton `Run 'App'` de la barre d’outils d’Android Studio. 
