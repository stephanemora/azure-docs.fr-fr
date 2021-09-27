---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 64b7021132196887440373f343626b43836a8b91
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128700483"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

## <a name="record-calls"></a>Enregistrer les appels
> [!WARNING]
> Jusqu’à la version 1.1.0 et bêta, la version 1.1.0-beta.1 de ACS Calling Android SDK possède le `isRecordingActive` et les `addOnIsRecordingActiveChangedListener` font partie de l’`Call`objet. Pour les nouvelles versions bêta, ces API ont été déplacées comme une fonctionnalité étendue de `Call` tout comme décrit ci-dessous.

> [!NOTE]
> Cet API est fourni en tant qu’aperçu pour les développeurs et peut être modifié en fonction des commentaires que nous avons reçus. N’utilisez pas cet API dans un environnement de production. Pour utiliser cette API, utilisez la version bêta de ACS Calling Android SDK

L’enregistrement des appels est une fonctionnalité étendue de l’API `Call` principale. Vous devez d’abord obtenir l’objet d’API de la fonctionnalité d’enregistrement :

```java
RecordingFeature callRecordingFeature = call.api(RecordingFeature.class);
```

Ensuite, pour vérifier si l’appel est en train d’être enregistré, inspectez la propriété `isRecordingActive` de `callRecordingFeature`. Elle retourne `boolean`.

```java
boolean isRecordingActive = callRecordingFeature.isRecordingActive();
```

Vous pouvez également vous abonner à l’enregistrement des modifications :

```java
private void handleCallOnIsRecordingChanged(PropertyChangedEvent args) {
    boolean isRecordingActive = callRecordingFeature.isRecordingActive();
}

callRecordingFeature.addOnIsRecordingActiveChangedListener(handleCallOnIsRecordingChanged);
```

Si vous souhaitez commencer l’enregistrement à partir de votre application, suivez d'abord la [présentation de l’enregistrement des appels](../../../../concepts/voice-video-calling/call-recording.md) pour savoir comment configurer l’enregistrement des appels.

Une fois que vous avez configuré l’enregistrement des appels sur votre serveur, à partir de votre application Android, vous devez obtenir la `ServerCallId` valeur de l’appel, puis l’envoyer à votre serveur pour démarrer le processus d’enregistrement. La `ServerCallId` valeur peut être trouvée à l’aide de `getServerCallId()` à partir de la classe `CallInfo`, laquelle peut être trouvée dans l’objet de classe à l’aide de `getInfo()`.

```java
try {
    String serverCallId = call.getInfo().getServerCallId().get();
    // Send serverCallId to your recording server to start the call recording.
} catch (ExecutionException | InterruptedException e) {

} catch (UnsupportedOperationException unsupportedOperationException) {

}
```

Lorsque l’enregistrement débute à partir du serveur, l’événement `handleCallOnIsRecordingChanged` se déclenche et la valeur de `callRecordingFeature.isRecordingActive()` sera `true`.

Tout comme le démarrage de l’enregistrement des appels, si vous souhaitez arrêter l’enregistrement des appels, vous devez obtenir le `ServerCallId` et l’envoyer à votre serveur d’enregistrement afin qu’il puisse arrêter l’enregistrement des appels.

```java
try {
    String serverCallId = call.getInfo().getServerCallId().get();
    // Send serverCallId to your recording server to stop the call recording.
} catch (ExecutionException | InterruptedException e) {

} catch (UnsupportedOperationException unsupportedOperationException) {

}
```

Lorsque l’enregistrement est stoppé à partir du serveur, l’événement `handleCallOnIsRecordingChanged` se déclenche et la valeur de `callRecordingFeature.isRecordingActive()` sera `false`.