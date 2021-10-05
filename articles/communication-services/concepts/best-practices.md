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
ms.openlocfilehash: 47d690b53b6e8fe9ccc2660e48283ce05e262d30
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154182"
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

### <a name="handling-multiple-calls-on-multiple-tabs-on-mobile"></a>Gestion de plusieurs appels sur plusieurs onglets sur des appareils mobiles
Votre application ne doit pas se connecter aux appels à partir de plusieurs onglets de navigateur simultanément, car cela peut entraîner un comportement non défini en raison de l’allocation de ressource pour le microphone et l’appareil photo sur l’appareil. Les développeurs sont encouragés à toujours raccrocher un appel lorsqu’ils ont terminés en arrière-plan avant d’en démarrer un nouveau.
```JavaScript 
document.addEventListener("visibilitychange", function() {
    if (document.visibilityState != 'visible') {
            // call.hangUp
    }
});
 ```

### <a name="handle-os-muting-call-when-phone-call-comes-in"></a>Gérer l’appel de désactivation du système d’exploitation lorsqu’un appel téléphonique entre.
Lors d’un appel ACS (pour iOS et Android), si un appel téléphonique entre, le système d’exploitation désactive automatiquement le microphone et l’appareil photo de l’utilisateur. Sur Android, l’appel est automatiquement activé et la vidé redémarre une fois l’appel téléphonique terminé. Sur iOS, une action de l’utilisateur est nécessaire pour « réactiver » et « redémarrer la vidéo ». Vous pouvez écouter la notification indiquant que le microphone a été coupé de manière inattendue avec l’événement de qualité `microphoneMuteUnexpectedly`. Remarque : pour pouvoir participer à un appel de manière appropriée, vous devez utiliser le SDK 1.2.2-beta.1 ou une version ultérieure.
```JavaScript
const latestMediaDiagnostic = call.api(SDK.Features.Diagnostics).media.getLatest();
const isIosSafari = (getOS() === OSName.ios) && (getPlatformName() === BrowserName.safari);
if (isIosSafari && latestMediaDiagnostic.microphoneMuteUnexpectedly && latestMediaDiagnostic.microphoneMuteUnexpectedly.value) {
  // received a QualityEvent on iOS that the microphone was unexpectedly muted - notify user to unmute their microphone and to start their video stream
}
 ```
Votre application doit appeler `call.startVideo(localVideoStream);` pour démarrer un flux vidéo et doit utiliser `this.currentCall.unmute();` pour activer le son.

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
