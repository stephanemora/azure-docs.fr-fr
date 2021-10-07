---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 029e4ce2f699eb66f0807f47cc80a893052f7a57
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129585324"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

## <a name="record-calls"></a>Enregistrer les appels
> [!WARNING]
> Jusqu’à la version 1.1.0 et bêta, la version 1.1.0-beta.1 du Kit de développement logiciel (SDK) iOS Appel d’ACS possède le `isRecordingActive` dans le cadre de `Call` objet et `didChangeRecordingState`fait partie de `CallDelegate` délégué. Pour les nouvelles versions bêta, ces API ont été déplacées comme une fonctionnalité étendue de `Call` tout comme décrit ci-dessous.
> [!NOTE]
> Cet API est fourni en tant qu’aperçu pour les développeurs et peut être modifié en fonction des commentaires que nous avons reçus. N’utilisez pas cet API dans un environnement de production. Pour utiliser cette API, utilisez la version bêta du Kit de développement logiciel (SDK) iOS Appel d’ACS.

L’enregistrement des appels est une fonctionnalité étendue de l’API `Call` principale. Vous devez d’abord obtenir l’objet d’API de la fonctionnalité d’enregistrement :

```swift
let callRecordingFeature = call.api(Features.recording)
```

Ensuite, pour vérifier si l’appel est en train d’être enregistré, inspectez la propriété `isRecordingActive` de `callRecordingFeature`. Elle retourne `Bool`.

```swift
let isRecordingActive = callRecordingFeature.isRecordingActive;
```

Vous pouvez également vous abonner à l’enregistrement des modifications en implémentant `RecordingCallFeatureDelegate` le délégué sur votre classe avec l’événement `didChangeRecordingState` :

```swift
callRecordingFeature.delegate = self

// didChangeRecordingState is a member of RecordingCallFeatureDelegate
public func recordingCallFeature(_ recordingCallFeature: RecordingCallFeature, didChangeRecordingState args: PropertyChangedEventArgs) {
    let isRecordingActive = recordingFeature.isRecordingActive
}
```

Si vous souhaitez commencer l’enregistrement à partir de votre application, suivez d'abord la [présentation de l’Enregistrement des appels](../../../../concepts/voice-video-calling/call-recording.md) pour savoir comment configurer l’enregistrement des appels.

Une fois que vous avez configuré l’enregistrement des appels sur votre serveur, à partir de votre application iOS, vous devez obtenir la `ServerCallId` valeur de l’appel, puis l’envoyer à votre serveur pour démarrer le processus d’enregistrement. La `ServerCallId` valeur peut être trouvée à l’aide de `getServerCallId()` à partir de la classe `CallInfo`, laquelle peut être trouvée dans l’objet de classe à l’aide de `getInfo()`.

```swift
// Send serverCallId to your recording server to start the call recording.
let serverCallId = call.info.getServerCallId(){ (serverId, error) in }
```

Lorsque l’enregistrement débute à partir du serveur, l’événement `didChangeRecordingState` se déclenche et la valeur de `recordingFeature.isRecordingActive` sera `true`.

Tout comme le démarrage de l’enregistrement des appels, si vous souhaitez arrêter l’enregistrement des appels, vous devez obtenir le `ServerCallId` et l’envoyer à votre serveur d’enregistrement afin qu’il puisse arrêter l’enregistrement des appels.

```swift
// Send serverCallId to your recording server to stop the call recording.
let serverCallId = call.info.getServerCallId(){ (serverId, error) in }
```

Lorsque l’enregistrement est stoppé à partir du serveur, l’événement `didChangeRecordingState` se déclenche et la valeur de `recordingFeature.isRecordingActive` sera `false`.
