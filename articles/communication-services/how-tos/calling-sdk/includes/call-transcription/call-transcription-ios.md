---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: d2011720b203493b5b17fabb3d65ca912e7ec37d
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129584988"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

> [!WARNING]
> Jusqu’à la version 1.1.0 et bêta, la version 1.1.0-beta.1 du Kit de développement logiciel (SDK) iOS Appel d’ACS possède le `isTranscriptionActive` dans le cadre de `Call` objet et `didChangeTranscriptionState`fait partie de `CallDelegate` délégué. Pour les nouvelles versions bêta, ces API ont été déplacées comme une fonctionnalité étendue de `Call` tout comme décrit ci-dessous.
> [!NOTE]
> Cet API est fourni en tant qu’aperçu pour les développeurs et peut être modifié en fonction des commentaires que nous avons reçus. N’utilisez pas cet API dans un environnement de production. Pour utiliser cette API, utilisez la version bêta du Kit de développement logiciel (SDK) iOS Appel d’ACS.

La transcription des appels est une fonctionnalité étendue de l’API `Call` principale. Vous devez d’abord obtenir l’objet d’API de la fonctionnalité de transcription :

```swift
let callTranscriptionFeature = call.api(Features.transcription)
```

Ensuite, pour vérifier si l’appel est en cours d’enregistrement, vérifiez la `isTranscriptionActive` propriété de `callTranscriptionFeature`. Elle retourne `Bool`.

```swift
let isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive;
```

Vous pouvez également vous abonner à l’enregistrement des modifications en implémentant le délégué `TranscriptionCallFeatureDelegate` sur votre classe avec l’événement `didChangeTranscriptionState` :

```swift
callTranscriptionFeature.delegate = self

// didChangeTranscriptionState is a member of TranscriptionCallFeatureDelegate
public func transcriptionCallFeature(_ transcriptionCallFeature: TranscriptionCallFeature, didChangeTranscriptionState args: PropertyChangedEventArgs) {
    let isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive
}
```
