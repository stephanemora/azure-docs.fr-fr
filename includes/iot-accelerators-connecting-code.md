---
title: Fichier Include
description: Fichier Include
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: c79b6f854dc78670a7eb8a1275c3e2fc46fcdd99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67177498"
---
### <a name="code-walkthrough"></a>Vérification du code

Cette section décrit certaines parties essentielles de l’exemple de code et explique comment elles sont liées à l’accélérateur de solution de supervision à distance.

L’extrait de code suivant montre comment les propriétés signalées qui décrivent les fonctionnalités de l’appareil sont définies. Ces propriétés sont les suivantes :

- Emplacement de l’appareil pour permettre à l’accélérateur de solution d’ajouter l’appareil à la carte.
- Version actuelle du microprogramme.
- Liste des méthodes prises en charge par l’appareil.
- Schéma des messages de télémétrie envoyés par l’appareil.

[!code-cpp[Define data structures for Chiller](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=datadefinition "Define data structures for Chiller")]

L’exemple inclut une fonction **serializeToJson** qui sérialise cette structure de données à l’aide de la bibliothèque Parson.

L’exemple inclut plusieurs fonctions de rappel qui affichent les informations sur la console lorsque le client interagit avec l’accélérateur de solution :

- **connection_status_callback**
- **send_confirm_callback**
- **reported_state_callback**
- **device_method_callback**

L’extrait de code suivant montre la fonction **device_method_callback**. Cette fonction détermine l’action à entreprendre lorsqu’un appel de méthode est reçu à partir de l’accélérateur de solution. La fonction reçoit une référence à la structure de données **Chiller** dans le paramètre **userContextCallback**. La valeur de **userContextCallback** est définie lorsque la fonction de rappel est configurée dans la fonction **main** :

[!code-cpp[Device method callback](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=devicemethodcallback "Device method callback")]

Lorsque l’accélérateur de solution appelle la méthode de mise à jour du microprogramme, l’exemple désérialise la charge utile JSON et démarre un thread d’arrière-plan pour terminer le processus de mise à jour. L’extrait de code suivant montre **do_firmware_update**, qui s’exécute sur le thread :

[!code-cpp[Firmware update thread](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=firmwareupdate "Firmware update thread")]

L’extrait de code suivant montre comment le client envoie un message de télémétrie à l’accélérateur de solution. Les propriétés du message incluent le schéma du message qui aide l’accélérateur de solution à afficher les données de télémétrie sur le tableau de bord :

[!code-cpp[Send telemetry](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=sendmessage "Send telemetry")]

La fonction **main** dans l’échantillon :

- Initialise et arrête le sous-système du kit de développement logiciel (SDK).
- Initialise la structure de données **Chiller**.
- Envoie les propriétés signalées à l’accélérateur de solution.
- Configure la fonction de rappel de méthode de l’appareil.
- Envoie des valeurs de télémétrie simulées à l’accélérateur de solution.

[!code-cpp[Main](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=main "Main")]
