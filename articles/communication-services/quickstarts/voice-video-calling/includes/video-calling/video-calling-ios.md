---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: e134173fdcd72c637be990b92a278d848ce6527b
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112535747"
---
Commencez à utiliser Azure Communication Services avec le SDK d’appel Communication Services pour ajouter des appels vidéo 1 à 1 à votre application. Vous allez découvrir comment démarrer un appel vidéo et y répondre à l’aide du SDK d’appel Azure Communication Services pour iOS.

## <a name="sample-code"></a>Exemple de code

Si vous souhaitez passer à la fin, vous pouvez télécharger ce guide de démarrage rapide en guise d’exemple sur [GitHub](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/Add%20Video%20Calling).

## <a name="prerequisites"></a>Prérequis
- Obtenez un compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un Mac exécutant [Xcode](https://developer.apple.com/xcode/), ainsi qu’un certificat de développeur valide installé dans votre trousseau
- Créez une ressource Communication Services active. [Créez une ressource Communication Services](../../../create-communication-resource.md?tabs=windows&pivots=platform-azp).
- Créez un jeton d’accès utilisateur pour instancier le client d’appel. [Découvrez comment créer et gérer des jetons d’accès utilisateur](../../../access-tokens.md?pivots=programming-language-csharp).

## <a name="setting-up"></a>Configuration
### <a name="creating-the-xcode-project"></a>Création du projet Xcode
Dans Xcode, créez un projet iOS et sélectionnez le modèle Single View App. Ce tutoriel utilise le [framework SwiftUI](https://developer.apple.com/xcode/swiftui/). Vous devez donc définir Swift comme langage et SwiftUI comme interface utilisateur. Vous n’allez pas créer de tests au cours de ce guide démarrage rapide. N’hésitez pas à décocher Inclure des tests.

:::image type="content" source="../../media/ios/xcode-new-ios-project.png" alt-text="Capture d’écran représentant la fenêtre Nouveau projet dans Xcode.":::

### <a name="installing-cocoapods"></a>Installation de CocoaPods
Utilisez ce guide pour [installer CocoaPods](https://guides.cocoapods.org/using/getting-started.html) sur votre Mac. 

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Installer le package et les dépendances avec CocoaPods
1. Pour créer un Podfile pour votre application, ouvrez le terminal et accédez au dossier du projet, puis exécutez pod init.

2. Ajoutez le code suivant au Podfile et enregistrez-le :

```
platform :ios, '13.0'
use_frameworks!

target 'VideoCallingQuickstart' do
  pod 'AzureCommunicationCalling', '~> 1.0.0'
end
```

3. Exécutez pod install.

4. Ouvrez le fichier .xcworkspace avec Xcode.


### <a name="request-access-to-the-microphone-and-camera"></a>Demandez l’accès au microphone et à la caméra.
Pour accéder au microphone et à la caméra de l’appareil, vous devez mettre à jour la liste des propriétés d’informations de votre application avec des `NSMicrophoneUsageDescription` et `NSCameraUsageDescription`. Vous affectez la valeur associée à une chaîne qui sera incluse dans la boîte de dialogue affichée par le système pour demander l’accès à l’utilisateur.

Cliquez avec le bouton droit sur l’entrée `Info.plist` de l’arborescence du projet, puis sélectionnez Ouvrir comme > Code source. Ajoutez les lignes suivantes dans la section `<dict>` tout en haut, puis enregistrez le fichier.

```XML
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
<key>NSCameraUsageDescription</key>
<string>Need camera access for video calling</string>
```

### <a name="set-up-the-app-framework"></a>Configurer le framework d’application
Ouvrez le fichier `ContentView.swift` de votre projet et ajoutez une déclaration d’importation en haut du fichier pour importer la bibliothèque `AzureCommunicationCalling` et `AVFoundation`. AVFoundation est utilisé pour capturer l’autorisation audio à partir du code.

```Swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="object-model"></a>Modèle objet
Les classes et les interfaces suivantes gèrent certaines des principales fonctionnalités du kit de développement logiciel (SDK) Azure Communication Services Calling pour iOS.

| Nom                         | Description                                                                                                                                                                        |
| :--------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                   | CallClient est le point d’entrée principal du SDK Calling.                                                                                                                         |
| CallAgent                    | CallAgent sert à démarrer et à gérer les appels.                                                                                                                                   |
| CommunicationTokenCredential | CommunicationTokenCredential est utilisé comme informations d’identification du jeton pour instancier CallAgent.                                                                                     |
| CommunicationIdentifier      | CommunicationIdentifier sert à représenter l’identité de l’utilisateur qui peut être l’une des suivantes : CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication. |

## <a name="create-the-call-agent"></a>Créer l’agent d’appel
Remplacez l’implémentation du struct ContentView par des contrôles d’interface utilisateur simples qui permettent à un utilisateur de lancer un appel et d’y mettre fin. Dans ce guide de démarrage rapide, nous allons attacher une logique métier à ces contrôles.

```Swift
struct ContentView: View {
    @State var callee: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?
    @State var deviceManager: DeviceManager?
    @State var localVideoStream:[LocalVideoStream]?
    @State var incomingCall: IncomingCall?
    @State var sendingVideo:Bool = false
    @State var errorMessage:String = "Unknown"

    @State var remoteVideoStreamData:[Int32:RemoteVideoStreamData] = [:]
    @State var previewRenderer:VideoStreamRenderer? = nil
    @State var previewView:RendererView? = nil
    @State var remoteRenderer:VideoStreamRenderer? = nil
    @State var remoteViews:[RendererView] = []
    @State var remoteParticipant: RemoteParticipant?
    @State var remoteVideoSize:String = "Unknown"
    @State var isIncomingCall:Bool = false
    
    @State var callObserver:CallObserver?
    @State var remoteParticipantObserver:RemoteParticipantObserver?

    var body: some View {
        NavigationView {
            ZStack{
                Form {
                    Section {
                        TextField("Who would you like to call?", text: $callee)
                        Button(action: startCall) {
                            Text("Start Call")
                        }.disabled(callAgent == nil)
                        Button(action: endCall) {
                            Text("End Call")
                        }.disabled(call == nil)
                        Button(action: toggleLocalVideo) {
                            HStack {
                                Text(sendingVideo ? "Turn Off Video" : "Turn On Video")
                            }
                        }
                    }
                }
                // Show incoming call banner
                if (isIncomingCall) {
                    HStack() {
                        VStack {
                            Text("Incoming call")
                                .padding(10)
                                .frame(maxWidth: .infinity, alignment: .topLeading)
                        }
                        Button(action: answerIncomingCall) {
                            HStack {
                                Text("Answer")
                            }
                            .frame(width:80)
                            .padding(.vertical, 10)
                            .background(Color(.green))
                        }
                        Button(action: declineIncomingCall) {
                            HStack {
                                Text("Decline")
                            }
                            .frame(width:80)
                            .padding(.vertical, 10)
                            .background(Color(.red))
                        }
                    }
                    .frame(maxWidth: .infinity, alignment: .topLeading)
                    .padding(10)
                    .background(Color.gray)
                }
                ZStack{
                    VStack{
                        ForEach(remoteViews, id:\.self) { renderer in
                            ZStack{
                                VStack{
                                    RemoteVideoView(view: renderer)
                                        .frame(width: .infinity, height: .infinity)
                                        .background(Color(.lightGray))
                                }
                            }
                            Button(action: endCall) {
                                Text("End Call")
                            }.disabled(call == nil)
                            Button(action: toggleLocalVideo) {
                                HStack {
                                    Text(sendingVideo ? "Turn Off Video" : "Turn On Video")
                                }
                            }
                        }
                        
                    }.frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .topLeading)
                    VStack{
                        if(sendingVideo)
                        {
                            VStack{
                                PreviewVideoStream(view: previewView!)
                                    .frame(width: 135, height: 240)
                                    .background(Color(.lightGray))
                            }
                        }
                    }.frame(maxWidth:.infinity, maxHeight:.infinity,alignment: .bottomTrailing)
                }
            }
     .navigationBarTitle("Video Calling Quickstart")
        }.onAppear{
            // Authenticate the client
            
            // Initialize the CallAgent and access Device Manager
            
            // Ask for permissions
        }
    }
}

//Functions and Observers

struct PreviewVideoStream: UIViewRepresentable {
    let view:RendererView
    func makeUIView(context: Context) -> UIView {
        return view
    }
    func updateUIView(_ uiView: UIView, context: Context) {}
}

struct RemoteVideoView: UIViewRepresentable {
    let view:RendererView
    func makeUIView(context: Context) -> UIView {
        return view
    }
    func updateUIView(_ uiView: UIView, context: Context) {}
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```

### <a name="authenticate-the-client"></a>Authentifier le client
Pour initialiser une instance CallAgent, nous avons besoin d’un jeton d’accès utilisateur qui nous permettra d’effectuer et de recevoir des appels. Consultez la documentation sur les [jetons d’accès utilisateur](../../../access-tokens.md?pivots=programming-language-csharp) si vous n’avez pas encore de jeton disponible.

Une fois que vous avez le jeton, ajoutez le code suivant au rappel `onAppear` dans `ContentView.swift`. Vous devez remplacer `<USER ACCESS TOKEN>` par un jeton d’accès utilisateur valide** pour votre ressource :

```Swift
var userCredential: CommunicationTokenCredential?
do {
    userCredential = try CommunicationTokenCredential(token: "<USER ACCESS TOKEN>")
} catch {
    print("ERROR: It was not possible to create user credential.")
    return
}
```

### <a name="initialize-the-callagent-and-access-device-manager"></a>Initialiser l’instance CallAgent et accéder au Gestionnaire de périphériques
Pour créer une instance CallAgent à partir d’un CallClient, utilisez la méthode `callClient.createCallAgent` qui retourne un objet CallAgent de manière asynchrone une fois qu’elle est lancée. DeviceManager permet d’énumérer les périphériques locaux qui peuvent être utilisés dans un appel pour transmettre des flux audio/vidéo. Il vous permet également de demander l’autorisation à un utilisateur d’accéder au microphone/à la caméra. 

```Swift
self.callClient = CallClient()
self.callClient?.createCallAgent(userCredential: userCredential!) { (agent, error) in
    if error != nil {
        print("ERROR: It was not possible to create a call agent.")
        return
    }

    else {
        self.callAgent = agent
        print("Call agent successfully created.")
        self.callAgent!.delegate = incomingCallHandler
        self.callClient?.getDeviceManager { (deviceManager, error) in
            if (error == nil) {
                print("Got device manager instance")
                self.deviceManager = deviceManager
            } else {
                print("Failed to get device manager instance")
            }
        }
    }
}
```

### <a name="ask-for-permissions"></a>Demander des autorisations
Nous avons besoin d’ajouter le code suivant au rappel `onAppear` pour demander des autorisations pour l’audio et la vidéo. 

```Swift
AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
    if granted {
        AVCaptureDevice.requestAccess(for: .video) { (videoGranted) in
            /* NO OPERATION */
        }
    }
}
```

## <a name="display-local-video"></a>Afficher la vidéo locale
Avant de démarrer un appel, vous pouvez gérer les paramètres liés à la vidéo. Dans ce guide de démarrage rapide, nous allons présenter l’implémentation du basculement de la vidéo locale avant ou pendant un appel. 

Tout d’abord, nous avons besoin d’accéder aux caméras locales avec `deviceManager`. Une fois que la caméra souhaitée sélectionnée, nous pouvons construire `LocalVideoStream` et créer un `VideoStreamRenderer`, avant de l’attacher à `previewView`. Pendant l’appel, nous pouvons utiliser `startVideo` ou `stopVideo` pour démarrer ou arrêter l’envoi de `LocalVideoStream` aux participants distants. Cette fonction s’applique aussi à la gestion des appels entrants. 

```Swift
func toggleLocalVideo() {
    // toggling video before call starts
    if (call == nil)
    {
        if(!sendingVideo)
        {
            self.callClient = CallClient()
            self.callClient?.getDeviceManager { (deviceManager, error) in
                if (error == nil) {
                    print("Got device manager instance")
                    self.deviceManager = deviceManager
                } else {
                    print("Failed to get device manager instance")
                }
            }
            guard let deviceManager = deviceManager else {
                return
            }
            let camera = deviceManager.cameras.first
            let scalingMode = ScalingMode.fit
            if (self.localVideoStream == nil) {
                self.localVideoStream = [LocalVideoStream]()
            }
            localVideoStream!.append(LocalVideoStream(camera: camera!))
            previewRenderer = try! VideoStreamRenderer(localVideoStream: localVideoStream!.first!)
            previewView = try! previewRenderer!.createView(withOptions: CreateViewOptions(scalingMode:scalingMode))
            self.sendingVideo = true
        }
        else{
            self.sendingVideo = false
            self.previewView = nil
            self.previewRenderer!.dispose()
            self.previewRenderer = nil
        }
    }
    // toggle local video during the call
    else{
        if (sendingVideo) {
            call!.stopVideo(stream: localVideoStream!.first!) { (error) in
                if (error != nil) {
                    print("cannot stop video")
                }
                else {
                    self.sendingVideo = false
                    self.previewView = nil
                    self.previewRenderer!.dispose()
                    self.previewRenderer = nil
                }
            }
        }
        else {
            guard let deviceManager = deviceManager else {
                return
            }
            let camera = deviceManager.cameras.first
            let scalingMode = ScalingMode.fit
            if (self.localVideoStream == nil) {
                self.localVideoStream = [LocalVideoStream]()
            }
            localVideoStream!.append(LocalVideoStream(camera: camera!))
            previewRenderer = try! VideoStreamRenderer(localVideoStream: localVideoStream!.first!)
            previewView = try! previewRenderer!.createView(withOptions: CreateViewOptions(scalingMode:scalingMode))
            call!.startVideo(stream:(localVideoStream?.first)!) { (error) in
                if (error != nil) {
                    print("cannot start video")
                }
                else {
                    self.sendingVideo = true
                }
            }
        }
    }
}
```

## <a name="place-an-outgoing-call"></a>Passer un appel sortant
La méthode `startCall` est définie en tant qu’action qui sera exécutée lors d’un appui sur le bouton Start Call. Dans ce guide de démarrage rapide, les appels sortants sont uniquement audio par défaut. Pour démarrer un appel avec la vidéo, vous avez besoin de définir `VideoOptions` avec `LocalVideoStream` et de le passer avec `startCallOptions` pour définir les options initiales de l’appel.

```Swift
func startCall() {
        let startCallOptions = StartCallOptions()
        if(sendingVideo)
        {
            if (self.localVideoStream == nil) {
                self.localVideoStream = [LocalVideoStream]()
            }
            let videoOptions = VideoOptions(localVideoStreams: localVideoStream!)
            startCallOptions.videoOptions = videoOptions
        }
        let callees:[CommunicationIdentifier] = [CommunicationUserIdentifier(self.callee)]
        self.callAgent?.startCall(participants: callees, options: startCallOptions) { (call, error) in
            setCallAndObersever(call: call, error: error)
        }
    }
```

`CallObserver` et `RemotePariticipantObserver` sont utilisés pour gérer les événements durant l’appel et les participants distants. Nous allons définir les observateurs dans la fonction `setCallAndOberserver`. 

```Swift
func setCallAndObersever(call:Call!, error:Error?) {
    if (error == nil) {
        self.call = call
        self.callObserver = CallObserver(self)
        self.call!.delegate = self.callObserver
        self.remoteParticipantObserver = RemoteParticipantObserver(self)
    } else {
        print("Failed to get call object")
    }
}
```

## <a name="answer-an-incoming-call"></a>Répondre à un appel entrant
Pour répondre à un appel entrant, implémentez un `IncomingCallHandler` pour afficher la bannière d’appel entrant afin de répondre à cet appel ou de le refuser. Placez l’implémentation suivante dans `IncomingCallHandler.swift`. 

```Swift
final class IncomingCallHandler: NSObject, CallAgentDelegate, IncomingCallDelegate {
    public var contentView: ContentView?
    private var incomingCall: IncomingCall?

    private static var instance: IncomingCallHandler?
    static func getOrCreateInstance() -> IncomingCallHandler {
        if let c = instance {
            return c
        }
        instance = IncomingCallHandler()
        return instance!
    }

    private override init() {}
    
    public func callAgent(_ callAgent: CallAgent, didRecieveIncomingCall incomingCall: IncomingCall) {
        self.incomingCall = incomingCall
        self.incomingCall?.delegate = self
        contentView?.showIncomingCallBanner(self.incomingCall!)
    }
    
    public func callAgent(_ callAgent: CallAgent, didUpdateCalls args: CallsUpdatedEventArgs) {
        if let removedCall = args.removedCalls.first {
            contentView?.callRemoved(removedCall)
            self.incomingCall = nil
        }
    }
}
```

Nous avons besoin de créer une instance de `IncomingCallHandler` en ajoutant le code suivant au rappel `onAppear` dans `ContentView.swift` :

```Swift
let incomingCallHandler = IncomingCallHandler.getOrCreateInstance()
incomingCallHandler.contentView = self
```

Définissez un délégué sur le CallAgent une fois que ce dernier est correctement créé :

```Swift
self.callAgent!.delegate = incomingCallHandler
```

Une fois qu’il y a un appel entrant, le `IncomingCallHandler` appelle la fonction `showIncomingCallBanner` pour afficher les boutons `answer` et `decline`. 

```Swift
func showIncomingCallBanner(_ incomingCall: IncomingCall?) {
    isIncomingCall = true
    self.incomingCall = incomingCall
}
```

Les actions attachées à `answer` et `decline` sont implémentées comme indiqué ci-dessous. Pour répondre à l’appel avec la vidéo, nous avons besoin d’activer la vidéo locale et de définir les options de `AcceptCallOptions` avec `localVideoStream`. 

```Swift
func answerIncomingCall() {
    isIncomingCall = false
    let options = AcceptCallOptions()
    if (self.incomingCall != nil) {
        guard let deviceManager = deviceManager else {
            return
        }        
        if (self.localVideoStream == nil) {
            self.localVideoStream = [LocalVideoStream]()
        }
        if(sendingVideo)
        {
            let camera = deviceManager.cameras.first
            localVideoStream!.append(LocalVideoStream(camera: camera!))
            let videoOptions = VideoOptions(localVideoStreams: localVideoStream!)
            options.videoOptions = videoOptions
        }
        self.incomingCall!.accept(options: options) { (call, error) in
            setCallAndObersever(call: call, error: error)
        }
    }
}

func declineIncomingCall(){
    self.incomingCall!.reject { (error) in }
    isIncomingCall = false
}
```

## <a name="remote-participant-video-streams"></a>Flux vidéo des participants distants
Nous pouvons créer une classe `RemoteVideoStreamData` pour gérer le rendu des flux vidéo d’un participant distant. 

```Swift
public class RemoteVideoStreamData : NSObject, RendererDelegate {
    public func videoStreamRenderer(didFailToStart renderer: VideoStreamRenderer) {
        owner.errorMessage = "Renderer failed to start"
    }
    
    private var owner:ContentView
    let stream:RemoteVideoStream
    var renderer:VideoStreamRenderer? {
        didSet {
            if renderer != nil {
                renderer!.delegate = self
            }
        }
    }
    
    var views:[RendererView] = []
    init(view:ContentView, stream:RemoteVideoStream) {
        owner = view
        self.stream = stream
    }
    
    public func videoStreamRenderer(didRenderFirstFrame renderer: VideoStreamRenderer) {
        let size:StreamSize = renderer.size
        owner.remoteVideoSize = String(size.width) + " X " + String(size.height)
    }
}
```

## <a name="subscribe-to-events"></a>S’abonner à des événements
Nous pouvons implémenter une classe `CallObserver` pour nous abonner à une collecte d’événements afin d’être avertis quand des valeurs changent pendant l’appel. 

```Swift
public class CallObserver: NSObject, CallDelegate, IncomingCallDelegate {
    private var owner: ContentView
    init(_ view:ContentView) {
            owner = view
    }
        
    public func call(_ call: Call, didChangeState args: PropertyChangedEventArgs) {
        if(call.state == CallState.connected) {
            initialCallParticipant()
        }
    }

    // render remote video streams when remote participant changes
    public func call(_ call: Call, didUpdateRemoteParticipant args: ParticipantsUpdatedEventArgs) {
        for participant in args.addedParticipants {
            participant.delegate = owner.remoteParticipantObserver
            for stream in participant.videoStreams {
                if !owner.remoteVideoStreamData.isEmpty {
                    return
                }
                let data:RemoteVideoStreamData = RemoteVideoStreamData(view: owner, stream: stream)
                let scalingMode = ScalingMode.fit
                data.renderer = try! VideoStreamRenderer(remoteVideoStream: stream)
                let view:RendererView = try! data.renderer!.createView(withOptions: CreateViewOptions(scalingMode:scalingMode))
                data.views.append(view)
                self.owner.remoteViews.append(view)
                owner.remoteVideoStreamData[stream.id] = data
            }
            owner.remoteParticipant = participant
        }
    }

    // Handle remote video streams when the call is connected
    public func initialCallParticipant() {
        for participant in owner.call!.remoteParticipants {
            participant.delegate = owner.remoteParticipantObserver
            for stream in participant.videoStreams {
                renderRemoteStream(stream)
            }
            owner.remoteParticipant = participant
        }
    }
    
    //create render for RemoteVideoStream and attach it to view
    public func renderRemoteStream(_ stream: RemoteVideoStream!) {
        if !owner.remoteVideoStreamData.isEmpty {
            return
        }
        let data:RemoteVideoStreamData = RemoteVideoStreamData(view: owner, stream: stream)
        let scalingMode = ScalingMode.fit
        data.renderer = try! VideoStreamRenderer(remoteVideoStream: stream)
        let view:RendererView = try! data.renderer!.createView(withOptions: CreateViewOptions(scalingMode:scalingMode))
        self.owner.remoteViews.append(view)
        owner.remoteVideoStreamData[stream.id] = data
    }
}
```

## <a name="remote-participant-management"></a>Gestion des participants distants
Tous les participants distants sont représentés par le type `RemoteParticipant` et sont disponibles via la collection `remoteParticipants` sur une instance d’appel. Nous pouvons implémenter une classe `RemoteParticipantObserver` pour nous abonner aux mises à jour effectuées sur les flux vidéo distants des participants distants. 

```Swift
public class RemoteParticipantObserver : NSObject, RemoteParticipantDelegate {
    private var owner:ContentView
    init(_ view:ContentView) {
        owner = view
    }

    public func renderRemoteStream(_ stream: RemoteVideoStream!) {
        let data:RemoteVideoStreamData = RemoteVideoStreamData(view: owner, stream: stream)
        let scalingMode = ScalingMode.fit
        data.renderer = try! VideoStreamRenderer(remoteVideoStream: stream)
        let view:RendererView = try! data.renderer!.createView(withOptions: CreateViewOptions(scalingMode:scalingMode))
        self.owner.remoteViews.append(view)
        owner.remoteVideoStreamData[stream.id] = data
    }
    
    // render RemoteVideoStream when remote participant turns on the video, dispose the renderer when remote video is off
    public func remoteParticipant(_ remoteParticipant: RemoteParticipant, didUpdateVideoStreams args: RemoteVideoStreamsEventArgs) {
        for stream in args.addedRemoteVideoStreams {
            renderRemoteStream(stream)
        }
        for stream in args.removedRemoteVideoStreams {
            for data in owner.remoteVideoStreamData.values {
                data.renderer?.dispose()
            }
            owner.remoteViews.removeAll()
        }
    }
}
```

## <a name="run-the-code"></a>Exécuter le code
Vous pouvez générer et exécuter votre application sur un simulateur iOS en sélectionnant Product > Run ou en utilisant le raccourci clavier (⌘-R).
