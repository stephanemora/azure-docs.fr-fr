---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: b880f8a8f84247c14a0f1e81577d9e252b2b78c8
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129585146"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

> [!WARNING]
> Jusqu’à la version 1.1.0 et bêta, la version 1.1.0-beta.1 de ACS Calling Android SDK possède le `isTranscriptionActive` et les `addOnIsTranscriptionActiveChangedListener` font partie de l’`Call`objet. Pour les nouvelles versions bêta, ces API ont été déplacées comme une fonctionnalité étendue de `Call` tout comme décrit ci-dessous.

> [!NOTE]
> Cet API est fourni en tant qu’aperçu pour les développeurs et peut être modifié en fonction des commentaires que nous avons reçus. N’utilisez pas cet API dans un environnement de production. Pour utiliser cette API, utilisez la version bêta de ACS Calling Android SDK

La transcription des appels est une fonctionnalité étendue de l’API `Call` principale. Vous devez d’abord obtenir l’objet d’API de la fonctionnalité de transcription :

```java
TranscriptionCallFeature callTranscriptionFeature = call.api(Features.TRANSCRIPTION);
```

Ensuite, pour vérifier si l’appel est en cours d’enregistrement, vérifiez la propriété `isTranscriptionActive` de `callTranscriptionFeature`. Elle retourne `boolean`.

```java
boolean isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive();
```

Vous pouvez également vous abonner aux modifications apportées à la transcription :

```java
private void handleCallOnIsTranscriptionChanged(PropertyChangedEvent args) {
    boolean isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive();
}

callTranscriptionFeature.addOnIsTranscriptionActiveChangedListener(handleCallOnIsTranscriptionChanged);
```
