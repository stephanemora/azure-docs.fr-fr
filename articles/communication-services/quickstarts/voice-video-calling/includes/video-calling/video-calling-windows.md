---
ms.openlocfilehash: 05d7ac0fc46ddbe279208e9d60fb9f039985ad06
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111560681"
---
Ce démarrage rapide explique comment démarrer un appel vidéo à deux avec le Kit de développement logiciel (SDK) Appel d’Azure Communication Services pour Windows.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vous devez disposer des prérequis suivants :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Installez [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) avec la charge de travail de développement pour la plateforme Windows universelle. 
- Une ressource Communication Services déployée. [Créer une ressource Communication Services](../../../create-communication-resource.md)
- Un [jeton d’accès utilisateur](../../../access-tokens.md) pour votre service Azure Communication

## <a name="setting-up"></a>Configuration

### <a name="creating-the-project"></a>Création du projet

Dans Visual Studio, créez un projet avec le modèle **Application vide (Windows universel)** pour configurer une application de plateforme Windows universelle (UWP) monopage.

:::image type="content" source="../../media/windows/create-a-new-project.png" alt-text="Capture d’écran montrant la fenêtre Nouveau projet dans Visual Studio.":::

### <a name="install-the-package"></a>Installer le package

Cliquez avec le bouton droit sur votre projet, puis accédez à `Manage Nuget Packages` pour installer `Azure.Communication.Calling`. 

### <a name="request-access"></a>Demander l'accès

Accédez à `Package.appxmanifest`, puis cliquez sur `Capabilities`.
Cochez la case `Internet (Client & Server)` pour obtenir un accès entrant et sortant à Internet. Cochez la case `Microphone` pour accéder au flux audio du microphone. Cochez la case `WebCam` pour accéder à la caméra de l’appareil. 

Ajoutez le code suivant à votre fichier `Package.appxmanifest`. 
```
<Extensions>
<Extension Category="windows.activatableClass.inProcessServer">
<InProcessServer>
<Path>RtmMvrUap.dll</Path>
<ActivatableClass ActivatableClassId="VideoN.VideoSchemeHandler" ThreadingModel="both" />
</InProcessServer>
</Extension>
</Extensions>
```

### <a name="set-up-the-app-framework"></a>Configurer le framework d’application

Nous devons configurer une disposition de base pour attacher notre logique. Afin de passer un appel sortant, nous avons besoin d’une `TextBox` pour fournir l’ID d’utilisateur de l’appelé. Nous avons également besoin d’un bouton `Start Call` et d’un bouton `Hang Up`. En outre, nous avons besoin de voir un aperçu de la vidéo locale et d’afficher la vidéo à distance de l’autre participant. Nous avons donc besoin de deux éléments pour afficher les flux vidéo.

Ouvrez le fichier `MainPage.xaml` de votre projet et remplacez le contenu par l’implémentation suivante. 

```C#
<Page
    x:Class="CallingQuickstart.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:CallingQuickstart"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <StackPanel>
        <TextBox Text="Who would you like to call?" TextWrapping="Wrap" x:Name="CalleeTextBox" Margin="10,10,10,10"></TextBox>
        <Button Content="Start Call" Click="CallButton_ClickAsync" x:Name="CallButton" Margin="10,10,10,10"></Button>
        <Button Content="Hang Up" Click="HangupButton_Click" x:Name="HangupButton" Margin="10,10,10,10"></Button>
    </StackPanel>
    <StackPanel Orientation="Vertical" HorizontalAlignment="Center">
        <MediaElement x:Name="RemoteVideo" AutoPlay="True" Stretch="UniformToFill"/>
        <MediaElement x:Name="LocalVideo" AutoPlay="True"  Stretch="UniformToFill" HorizontalAlignment="Right"  VerticalAlignment="Bottom"/>
    </StackPanel>   
</Page>
```

Ouvrez le fichier `MainPage.xaml.cs` et remplacez le contenu par l’implémentation suivante : 
```C#
using System;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;

using Azure.Communication;
using Azure.Communication.Calling;

namespace CallingQuickstart
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        public MainPage()
        {
            this.InitializeComponent();
            this.InitCallAgentAndDeviceManager();
        }
        
        private async void InitCallAgentAndDeviceManager()
        {
            // Initialize call agent and Device Manager
        }

        private async void CallButton_ClickAsync(object sender, RoutedEventArgs e)
        {
            // Authenticate the client and start call
        }
        
        private async void Agent_OnIncomingCall(object sender, IncomingCall incomingcall)
        {
            // Accept an incoming call
        }

        private async void HangupButton_Click(object sender, RoutedEventArgs e)
        {
            // End the current call
        }

        CallClient callClient;
        CallAgent callAgent;
        Call call;
        DeviceManager deviceManager;
        LocalVideoStream[] localVideoStream;
    }
}
```

## <a name="object-model"></a>Modèle objet

Les classes et les interfaces suivantes gèrent certaines des principales fonctionnalités du SDK Azure Communication Services Calling :

| Nom                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | CallClient est le point d’entrée principal du SDK Calling.|
| CallAgent | CallAgent sert à démarrer et à gérer les appels. |
| CommunicationTokenCredential | CommunicationTokenCredential est utilisé comme informations d’identification du jeton pour instancier CallAgent.| 
| CommunicationUserIdentifier | CommunicationUserIdentifier sert à représenter l’identité de l’utilisateur qui peut être l’une des suivantes : CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication. |

## <a name="authenticate-the-client"></a>Authentifier le client

Initialisez une instance de `CallAgent` avec un jeton d’accès utilisateur qui nous permettra d’établir et de recevoir des appels. Pour accéder aux caméras de l’appareil, nous avons également besoin d’une instance du Gestionnaire de périphériques. 

```C#
private async void InitCallAgentAndDeviceManager()
{
    CallClient callClient = new CallClient();
    deviceManager = await callClient.GetDeviceManager();

    CommunicationTokenCredential token_credential = new CommunicationTokenCredential("<USER_ACCESS_TOKEN>");
    callClient = new CallClient();

    CallAgentOptions callAgentOptions = new CallAgentOptions()
    {
        DisplayName = "<DISPLAY_NAME>"
    };
    callAgent = await callClient.CreateCallAgent(token_credential, callAgentOptions);
    callAgent.OnCallsUpdated += Agent_OnCallsUpdated;
    callAgent.OnIncomingCall += Agent_OnIncomingCall;
}
```

## <a name="start-a-call-with-video"></a>Démarrer un appel vidéo

Pour démarrer un appel vidéo, nous devons énumérer les caméras avec l’instance du gestionnaire de périphériques et la construction `LocalVideoStream`. Nous devons définir la valeur `VideoOptions` avec `LocalVideoStream` et la passer avec `startCallOptions` pour définir les options initiales de l’appel. En joignant un `LocalVideoStream` à un `MediaElement`, nous pouvons voir l’aperçu de la vidéo locale. 

```C#
private async void CallButton_ClickAsync(object sender, RoutedEventArgs e)
{
    Debug.Assert(deviceManager.Microphones.Count > 0);
    Debug.Assert(deviceManager.Speakers.Count > 0);
    Debug.Assert(deviceManager.Cameras.Count > 0);

    if (deviceManager.Cameras.Count > 0)
    {
        VideoDeviceInfo videoDeviceInfo = deviceManager.Cameras[0];
        localVideoStream = new LocalVideoStream[1];
        localVideoStream[0] = new LocalVideoStream(videoDeviceInfo);

        Uri localUri = await localVideoStream[0].CreateBindingAsync();

        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            LocalVideo.Source = localUri;
            LocalVideo.Play();
        });

    }

    StartCallOptions startCallOptions = new StartCallOptions();
    startCallOptions.VideoOptions = new VideoOptions(localVideoStream);
    ICommunicationIdentifier[] callees = new ICommunicationIdentifier[1]
    {
        new CommunicationUserIdentifier(CalleeTextBox.Text)
    };

    call = await callAgent.StartCallAsync(callees, startCallOptions);
}
```

## <a name="accept-an-incoming-call"></a>Acceptation d’un appel entrant

Pour répondre à un appel vidéo entrant, transmettez le `LocalVideoStream` à `acceptCallOptions`. 

```C#
private async void Agent_OnIncomingCall(object sender, IncomingCall incomingcall)
{
    Debug.Assert(deviceManager.Microphones.Count > 0);
    Debug.Assert(deviceManager.Speakers.Count > 0);
    Debug.Assert(deviceManager.Cameras.Count > 0);

    if (deviceManager.Cameras.Count > 0)
    {
        VideoDeviceInfo videoDeviceInfo = deviceManager.Cameras[0];
        localVideoStream = new LocalVideoStream[1];
        localVideoStream[0] = new LocalVideoStream(videoDeviceInfo);

        Uri localUri = await localVideoStream[0].CreateBindingAsync();

        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            LocalVideo.Source = localUri;
            LocalVideo.Play();
        });

    }
    AcceptCallOptions acceptCallOptions = new AcceptCallOptions();
    acceptCallOptions.VideoOptions = new VideoOptions(localVideoStream);

    call = await incomingcall.Accept(acceptCallOptions);
}
```

## <a name="remote-participant-and-remote-video-streams"></a>Le participant distant et les flux vidéo distants

Tous les participants distants sont disponibles via la collection `RemoteParticipants` d’une instance d’appel. Une fois l’appel connecté, nous pouvons accéder aux participants distants à l’appel et gérer les flux de vidéo à distance. 

```C#
private async void Agent_OnCallsUpdated(object sender, CallsUpdatedEventArgs args)
{
    foreach (var call in args.AddedCalls)
    {
        foreach (var remoteParticipant in call.RemoteParticipants)
        {
            await AddVideoStreams(remoteParticipant.VideoStreams);
            remoteParticipant.OnVideoStreamsUpdated += async (s, a) => await AddVideoStreams(a.AddedRemoteVideoStreams);
        }
        call.OnRemoteParticipantsUpdated += Call_OnRemoteParticipantsUpdated;
        call.OnStateChanged += Call_OnStateChanged;
    }
}

private async void Call_OnRemoteParticipantsUpdated(object sender, ParticipantsUpdatedEventArgs args)
{
    foreach (var remoteParticipant in args.AddedParticipants)
    {
        await AddVideoStreams(remoteParticipant.VideoStreams);
        remoteParticipant.OnVideoStreamsUpdated += async (s, a) => await AddVideoStreams(a.AddedRemoteVideoStreams);
    }
}
```

### <a name="render-remote-videos"></a>Afficher des vidéos distantes

Attachez chaque flux de vidéo à distance au `MediaElement`. 

```C#
private async Task AddVideoStreams(IReadOnlyList<RemoteVideoStream> streams)
{

    foreach (var remoteVideoStream in streams)
    {
        var remoteUri = await remoteVideoStream.CreateBindingAsync();

        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            RemoteVideo.Source = remoteUri;
            RemoteVideo.Play();
        });
        remoteVideoStream.Start();
    }
}
```

## <a name="call-state-update"></a>Mise à jour de l’état d’appel
Une fois l’appel déconnecté, nous devons nettoyer les convertisseurs vidéo. 

```C#
private async void Call_OnStateChanged(object sender, PropertyChangedEventArgs args)
{
    switch (((Call)sender).State)
    {
        case CallState.Disconnected:
            await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
            {
                LocalVideo.Source = null;
                RemoteVideo = null;
            });
            break;
        default:
            System.Console.WriteLine(((Call)sender).State);
            break;
    }
}
```

## <a name="end-a-call"></a>Terminer un appel

Terminez l’appel en cours quand l’utilisateur clique sur le bouton `Hang Up`. 

```C#
private async void HangupButton_Click(object sender, RoutedEventArgs e)
{
    var hangUpOptions = new HangUpOptions();
    await call.HangUp(hangUpOptions);
}
```

## <a name="run-the-code"></a>Exécuter le code

Vous pouvez générer et exécuter le code sur Visual Studio. Notez que, pour les plateformes de solution, nous prenons en charge `ARM64`, `x64` et `x86`. 

Vous pouvez passer un appel vidéo sortant en entrant un ID d’utilisateur dans le champ de texte, puis en cliquant sur le bouton `Start Call`. 
