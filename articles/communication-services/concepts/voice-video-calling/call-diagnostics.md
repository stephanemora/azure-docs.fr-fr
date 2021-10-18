---
title: Diagnostics des appels d’Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Fournit une vue d’ensemble de la fonctionnalité Diagnostics des appels.
author: probableprime
ms.author: rifox
manager: chpalm
services: azure-communication-services
ms.date: 08/17/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: 23b75ff29e32bafdce320d334d9236902cbe4b2c
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667608"
---
# <a name="call-diagnostics"></a>Diagnostics d’appel

Quand vous utilisez des appels dans Azure Communication Services, des problèmes peuvent survenir qui provoquent à leur tour des problèmes pour vos clients. Pour vous aider, vous pouvez utiliser une fonctionnalité appelée « Diagnostics des appels », qui vous permet d’examiner les différentes propriétés d’un appel afin de déterminer quel peut être le problème.

**Les diagnostics des appels sont actuellement pris en charge seulement pour notre SDK JS / Web.**

## <a name="accessing-diagnostics"></a>Accès aux diagnostics

Les diagnostics d’appel constituent une fonctionnalité étendue de l’API `Call` de base et vous permettent de diagnostiquer un appel actif.

```js
const callDiagnostics = call.api(Features.Diagnostics);
```

## <a name="diagnostic-values"></a>Valeurs des diagnostics

Les diagnostics présentés aux utilisateurs suivants sont disponibles :

### <a name="network-values"></a>Valeurs concernant le réseau

| Nom                      | Description                                                     | Valeurs possibles                                                                                                                                                                                                                                  | Cas d'utilisation                                           |
| ------------------------- | --------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------- |
| noNetwork                 | Aucun réseau disponible.                                  | -Défini sur `True` lorsqu’un appel ne peut pas démarrer, car aucun réseau n’est disponible. <br/> - Défini sur `False` en cas de présence de candidats ICE.                                                                                                  | L’appareil n’est pas connecté à un réseau.               |
| networkRelaysNotReachable | Problèmes avec un réseau.                                        | - Défini sur `True` lorsque le réseau comporte une contrainte qui ne vous permet pas d’atteindre les relais ACS. <br/> - Défini sur `False` lors de la réalisation d’un nouvel appel.                                                                                                | Pendant un appel lorsque le signal WiFi s’active et se désactive. |
| networkReconnect          | La connexion a été perdue et nous nous reconnectons au réseau. | - Défini sur `Bad` lorsque le réseau est déconnecté <br/> - Défini sur `Poor` quand la connectivité de transport de média est interrompue <br/> - Défini sur `Good` lorsqu’une nouvelle session est connectée.                                                                       | Faible bande passante, pas d’Internet                          |
| networkReceiveQuality     | Indicateur concernant la qualité du flux entrant.                 | - Défini sur `Bad` en cas de problème grave de réception du flux. qualité <br/> - Défini sur `Poor` en cas de problème modéré de réception du flux. qualité <br/> - Défini sur `Good` lorsqu’il n’y a pas de problème de réception du flux. | Bande passante faible                                       |

### <a name="audio-values"></a>Valeurs concernant l’audio

| Nom                           | Description                                                     | Valeurs possibles                                                                                                                                                                                                                                                                                                   | Cas d'utilisation                                                        |
| ------------------------------ | --------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| noSpeakerDevicesEnumerated     | Il n’y a aucun appareil de sortie audio (haut-parleur) sur le système de l’utilisateur. | - Défini sur `True` lorsque le système ne comporte pas d’appareil haut-parleur, et que la sélection de haut-parleur est prise en charge. <br/> - Défini sur `False` lorsque le système comporte au moins 1 appareil haut-parleur, et que la sélection de haut-parleur n’est pas prise en charge.                                                                                             | Tous les haut-parleurs sont débranchés                                       |
| speakingWhileMicrophoneIsMuted | Parler avec le micro désactivé.                                   | - Défini sur `True` lorsque le micro local est désactivé et que l’utilisateur local est en train de parler. <br/> - Défini sur `False` lorsque l’utilisateur local arrête de parler ou désactive le micro. <br/> \* Remarque : À ce jour, ceci n’est pas encore prise en charge sur Safari, car les échantillons de niveau audio sont extraits de webrtc. stats.                 | Pendant un appel, désactivez votre micro et parlez dedans.           |
| noMicrophoneDevicesEnumerated  | Le système de l’utilisateur ne comporte aucun appareil de capture audio (micro)      | - Défini sur `True` lorsque le système ne comporte pas de micro. <br/> - Défini sur `False` lorsque le système comporte au moins 1 micro.                                                                                                                                                              | Tous les micros sont débranchés pendant l’appel.                   |
| microphoneNotFunctioning       | Le micro ne fonctionne pas.                                  | - Défini sur `True` en cas d’échec de l’envoi du flux audio local parce que le micro a peut-être été désactivé dans le système ou qu’il est utilisé par un autre processus. Il faut environ 10 secondes pour déclencher ce périphérique. <br/> - Défini sur `False` lorsque le micro recommence à envoyer correctement le flux audio. | Aucun micro disponible, accès au micro désactivé dans un système |
| microphoneMuteUnexpectedly     | Le micro est désactivé                                             | - Défini sur `True` lorsque le micro est désactivé de manière inattendue. <br/> - Défini sur `False` lorsque le micro commence à envoyer correctement le flux audio.                                                                                                                                                                  | Le micro est désactivé dans le système.                             |
| microphonePermissionDenied     | Le volume de l’appareil est faible ou presque silencieux sous macOS. | - Défini sur `True` lorsque l’autorisation audio est refusée par les paramètres système (audio). <br/> - Défini sur `False` en cas de réussite d’acquisition de flux. <br/> Remarque : ce diagnostic fonctionne uniquement sous macOS.                                                                                                                             | Les autorisations de micro sont désactivées dans les Paramètres.             |

### <a name="camera-values"></a>Valeurs concernant la caméra

| Nom                   | Description                                            | Valeurs possibles                                                                                                                                                                                                                                                                                              | Cas d'utilisation                                                                       |
| ---------------------- | ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------- |
| cameraFreeze           | La caméra cesse de produire des images pendant plus de 5 secondes. | - Défini sur `True` lorsque le flux de vidéo local est figé. Cela signifie que le côté distant voit votre vidéo figée sur son écran ou que les participants distants ne reçoivent pas votre vidéo sur leur écran. <br/> - Défini sur `False` lorsque le blocage se termine et que les utilisateurs peuvent voir votre vidéo normalement. | La caméra a été perdue pendant l’appel ou un mauvais réseau a entraîné le blocage de la caméra. |
| cameraStartFailed      | Échec générique de la caméra.                                | - Défini sur `True` en cas d’échec de l’envoi de vidéo locale parce que la caméra a peut-être été désactivée dans le système ou qu’elle est utilisée par un autre processus. <br/> - Défini sur `False` lorsque la caméra sélectionnée parvient à envoyer la vidéo locale. .                                                  | Défaillances de caméra                                                                 |
| cameraStartTimedOut    | Scénario courant où la caméra est dans un mauvais état de fonctionnement.          | - Défini sur `True` en cas d’expiration du délai d’attente d’envoi du flux vidéo de la caméra. <br/> - Défini sur `False` lorsque la caméra sélectionnée parvient à nouveau à envoyer la vidéo locale.                                                                                                                                         | Défaillances de caméra                                                                 |
| cameraPermissionDenied | Les autorisations de caméra ont été refusées dans les paramètres.            | - Défini sur `True` lorsque l’autorisation de caméra est refusée par les paramètres système (vidéo). <br/> - Défini sur `False` en cas de réussite d’acquisition de flux. <br> Remarque : ce diagnostic fonctionne uniquement sous macOS Chrome                                                                                                                  | Les autorisations de caméra sont désactivées dans les paramètres.                                |

### <a name="misc-values"></a>Valeurs concernant d’autres aspects

| Nom                         | Description                                                  | Valeurs possibles                                                                                                                                                                                         | Cas d'utilisation                                 |
| ---------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------- |
| screenshareRecordingDisabled | Le partage d’écran du système a été refusé en raison des préférences définies dans Paramètres. | - Défini sur `True` lorsque l’autorisation de partage d’écran est refusée par les paramètres système (partage). <br/> - Défini sur `False` en cas de réussite d’acquisition de flux. <br/> Remarque : ce diagnostic fonctionne uniquement sous macOS.Chrome. | L’enregistrement d’écran est désactivé dans les Paramètres. |

## <a name="diagnostic-events"></a>Événements de diagnostic

- Abonnez-vous à l’événement `diagnosticChanged` pour surveiller tout changement de diagnostic d’un appel.

```js
/**
 *  Each diagnostic has the following data:
 * - diagnostic is the type of diagnostic, e.g. NetworkSendQuality, DeviceSpeakWhileMuted, etc...
 * - value is DiagnosticQuality or DiagnosticFlag:
 *     - DiagnosticQuality = enum { Good = 1, Poor = 2, Bad = 3 }.
 *     - DiagnosticFlag = true | false.
 * - valueType = 'DiagnosticQuality' | 'DiagnosticFlag'
 * - mediaType is the media type associated with the event, e.g. Audio, Video, ScreenShare. These are defined in `CallDiagnosticEventMediaType`.
 */
const diagnosticChangedListener = (diagnosticInfo: NetworkDiagnosticChangedEventArgs | MediaDiagnosticChangedEventArgs) => {
    console.log(`Diagnostic changed: ` +
        `Diagnostic: ${diagnosticInfo.diagnostic}` +
        `Value: ${diagnosticInfo.value}` +
        `Value type: ${diagnosticInfo.valueType}` +
        `Media type: ${diagnosticInfo.mediaType}` +

    if (diagnosticInfo.valueType === 'DiagnosticQuality') {
        if (diagnosticInfo.value === DiagnosticQuality.Bad) {
            console.error(`${diagnosticInfo.diagnostic} is bad quality`);

        } else if (diagnosticInfo.value === DiagnosticQuality.Poor) {
            console.error(`${diagnosticInfo.diagnostic} is poor quality`);
        }

    } else if (diagnosticInfo.valueType === 'DiagnosticFlag') {
        if (diagnosticInfo.value === true) {
            console.error(`${diagnosticInfo.diagnostic}`);
        }
    }
};

callDiagnostics.network.on('diagnosticChanged', diagnosticChangedListener);
callDiagnostics.media.on('diagnosticChanged', diagnosticChangedListener);
```

## <a name="get-the-latest-diagnostics"></a>Obtenir les diagnostics les plus récents

- Obtenez les dernières valeurs de diagnostic d’appel qui ont été générées. Un diagnostic n’est pas défini s’il n’a jamais été déclenché.

```js
const latestNetworkDiagnostics = callDiagnostics.network.getLatest();

console.log(
  `noNetwork: ${latestNetworkDiagnostics.noNetwork.value}, ` +
    `value type = ${latestNetworkDiagnostics.noNetwork.valueType}`
);

console.log(
  `networkReconnect: ${latestNetworkDiagnostics.networkReconnect.value}, ` +
    `value type = ${latestNetworkDiagnostics.networkReconnect.valueType}`
);

console.log(
  `networkReceiveQuality: ${latestNetworkDiagnostics.networkReceiveQuality.value}, ` +
    `value type = ${latestNetworkDiagnostics.networkReceiveQuality.valueType}`
);

const latestMediaDiagnostics = callDiagnostics.media.getLatest();

console.log(
  `speakingWhileMicrophoneIsMuted: ${latestMediaDiagnostics.speakingWhileMicrophoneIsMuted.value}, ` +
    `value type = ${latestMediaDiagnostics.speakingWhileMicrophoneIsMuted.valueType}`
);

console.log(
  `cameraStartFailed: ${latestMediaDiagnostics.cameraStartFailed.value}, ` +
    `value type = ${latestMediaDiagnostics.cameraStartFailed.valueType}`
);

console.log(
  `microphoneNotFunctioning: ${latestMediaDiagnostics.microphoneNotFunctioning.value}, ` +
    `value type = ${latestMediaDiagnostics.microphoneNotFunctioning.valueType}`
);
```
