---
title: Azure Communication Services - Meilleures pratiques
description: En savoir plus sur les meilleures pratiques pour Azure Communication Services
author: srahaman
manager: akania
services: azure-communication-services
ms.author: srahaman
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: a5181a5a95c3e6eb33eb084d41674746096dd8c2
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123259133"
---
# <a name="best-practices-azure-communication-services-calling-sdks"></a>Meilleures pratiques : Kit de développement logiciel (SDK) d’appel Azure Communication Services
Cet article fournit des informations sur les meilleures pratiques concernant le Kit de développement logiciel (SDK) d’appel Azure Communication Services (ACS).

## <a name="acs-web-javascript-sdk-best-practices"></a>Meilleures pratiques pour le Kit de développement logiciel (SDK) Web JavaScript ACS
Cette section fournit des informations sur les meilleures pratiques associées au Kit de développement logiciel (SDK) JavaScript pour les appels audio et vidéo Azure Communication Services.

## <a name="javascript-voice-and-video-calling-sdk"></a>Kit de développement logiciel (SDK) JavaScript pour les appels audio et vidéo

### <a name="plug-in-microphone-or-enable-microphone-from-device-manager-when-acs-call-in-progress"></a>Brancher un microphone ou l’activer à partir du gestionnaire de périphériques lorsque l’appel ACS est en cours
Quand aucun microphone n’est disponible au début d’un appel, puis qu’un microphone devient disponible, l’événement de diagnostic d’appel « noMicrophoneDevicesEnumerated » est déclenché.
Dans ce cas, votre application doit appeler `askDevicePermission` pour obtenir le consentement de l’utilisateur pour l’énumération des appareils. L’utilisateur peut ensuite activer/désactiver le microphone.

### <a name="stop-video-on-page-hide"></a>Arrêter la vidéo sur la page masquée
Quand l’utilisateur quitte l’onglet d’appel, la diffusion vidéo s’arrête. Certains appareils continuent de diffuser la dernière image. Pour éviter ce problème, les développeurs sont encouragés à arrêter la diffusion vidéo en continu quand les utilisateurs quittent un appel vidéo actif. La vidéo peut être arrêtée en appelant l’API `call.stopVideo`.
```JavaScript
document.addEventListener("visibilitychange", function() {
    if (document.visibilityState === 'visible') {
        // Start Video if it was stopped on visibility change (flag true)
    } else {
        // Stop Video if it's on and set flag = true to keep track
    }
});
```

### <a name="dispose-video-stream-renderer-view"></a>Supprimer la vue du convertisseur de flux vidéo
Les applications Communication Services doivent supprimer `VideoStreamRendererView`, ou son instance parente `VideoStreamRenderer`, lorsqu’elle n’est plus nécessaire.

### <a name="hang-up-the-call-on-onbeforeunload-event"></a>Raccrocher l’appel lors d’un événement onbeforeunload
Votre application doit appeler `call.hangup` lorsque l'événement `onbeforeunload` est émis.

### <a name="hang-up-the-call-on-microphonemuteunexpectedly-ufd"></a>Raccrocher l’appel sur le disque mémoire flash USB microphoneMuteUnexpectedly
Lorsqu’un utilisateur iOS/Safari reçoit un appel RTC, les services Azure Communication Services perdent l’accès au microphone. Les services Azure Communication Services déclenchent l'événement d’appel de diagnostic `microphoneMuteUnexpectedly` et, à ce stade, les services de communication ne sont pas en mesure de récupérer l’accès au microphone.
Il est recommandé de raccrocher l’appel (`call.hangUp`) lorsque cette situation se produit.

### <a name="device-management"></a>Gestion des périphériques
Vous pouvez utiliser le Kit de développement logiciel (SDK) Azure Communication Services pour gérer vos appareils et vos opérations multimédias.
- Votre application ne doit pas utiliser les API de navigateur natives comme `getUserMedia` ou `getDisplayMedia` pour acquérir des flux en dehors du Kit de développement logiciel (SDK). Si vous le faites, vous devrez supprimer manuellement vos flux de médias avant d’utiliser `DeviceManager` ou d'autres API de gestion des appareils via le Kit de développement logiciel (SDK) Communication Services.

### <a name="request-device-permissions"></a>Demander des autorisations d’appareil
Vous pouvez demander des autorisations d’appareil à l’aide du Kit de développement logiciel (SDK) :
- Votre application doit utiliser `DeviceManager.askDevicePermission` pour demander l’accès à des périphériques audio et/ou vidéo.
- Si l’utilisateur refuse l’accès, `DeviceManager.askDevicePermission` retourne la valeur « false » pour un type d’appareil donné (audio ou vidéo) lors des appels suivants, même après l’actualisation de la page. Dans ce scénario, votre application doit détecter que l’utilisateur a précédemment refusé l’accès et demander à l’utilisateur de réinitialiser manuellement ou d’accorder explicitement l’accès à un type d’appareil donné.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez les articles suivants :

- [Ajouter Chat à votre application](../quickstarts/chat/get-started.md)
- [Ajouter l’appel vocal à votre application](../quickstarts/voice-video-calling/getting-started-with-calling.md)
- [Documentation de référence](reference.md)
