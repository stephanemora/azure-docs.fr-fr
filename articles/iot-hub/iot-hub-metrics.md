---
title: Utiliser des métriques pour surveiller Azure IoT Hub | Microsoft Docs
description: Guide d’utilisation des métriques Azure IoT Hub pour évaluer et surveiller l’intégrité globale de votre IoT Hub.
author: nberdy
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: nberdy
ms.openlocfilehash: b1c9854d794c88c28c82a4b6e40c81a29552223a
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984060"
---
# <a name="understand-iot-hub-metrics"></a>Comprendre les métriques IoT Hub
Les métriques IoT Hub vous offrent de meilleures données sur l’état des ressources Azure IoT de votre abonnement Azure. Grâce aux métriques IoT Hub, vous pouvez évaluer l’intégrité globale du service et des appareils connectés à ce dernier. Les statistiques accessibles à l’utilisateur sont importantes, car elles vous permettent d’effectuer le suivi de votre hub IoT et de connaître les causes des problèmes sans contacter le support Azure.

Les métriques sont activées par défaut. Vous pouvez afficher les métriques IoT Hub à partir du portail Azure.

## <a name="how-to-view-iot-hub-metrics"></a>Affichage des métriques IoT Hub
1. Créez un hub IoT. Pour savoir comment créer un hub IoT, consultez le guide [Prise en main][lnk-get-started].
2. Ouvrez le panneau de votre hub IoT. Ici, cliquez sur **Métriques**.
   
    ![][1]
3. Dans le panneau de métriques, vous pouvez afficher les métriques IoT Hub et créer des vues personnalisées de ces dernières. Vous pouvez choisir d’envoyer vos données métriques vers un point de terminaison Event Hubs ou un compte de stockage Azure en cliquant sur **Paramètres de diagnostic**.
   
    ![][2]

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Métriques IoT Hub et instructions d’utilisation
IoT Hub fournit plusieurs métriques afin de vous donner une vue d’ensemble de l’intégrité de votre hub et du nombre total d’appareils connectés. Vous pouvez combiner les informations de plusieurs métriques pour obtenir une image plus grande de l’état du hub IoT. Le tableau suivant décrit les métriques dont chaque hub IoT effectue le suivi et la relation de chacune avec l’état global du hub IoT.

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentatives d’envoi de message de télémétrie|Count|Total|Nombre de tentatives d’envoi de messages de télémétrie appareil vers cloud à votre hub IoT|Aucune dimension|
|d2c.telemetry.ingress.success|Messages de télémétrie envoyés|Count|Total|Nombre de messages de télémétrie appareil vers cloud envoyés avec succès à votre hub IoT|Aucune dimension|
|c2d.commands.egress.complete.success|Commandes terminées|Count|Total|Nombre de commandes cloud vers appareil terminées avec succès par l’appareil|Aucune dimension|
|c2d.commands.egress.abandon.success|Commandes abandonnées|Count|Total|Nombre de commandes cloud vers appareil abandonnées par l’appareil|Aucune dimension|
|c2d.commands.egress.reject.success|Commandes rejetées|Count|Total|Nombre de commandes cloud vers appareil rejetées par l’appareil|Aucune dimension|
|devices.totalDevices|Nombre total d’appareils (déconseillé)|Count|Total|Nombre d’appareils enregistrés sur votre hub IoT|Aucune dimension|
|devices.connectedDevices.allProtocol|Appareils connectés (déconseillé) |Count|Total|Nombre d’appareils connectés à votre hub IoT|Aucune dimension|
|d2c.telemetry.egress.success|Routage : messages de télémétrie remis|Count|Total|Nombre de fois où des messages ont été correctement remis à tous les points de terminaison à l’aide du routage IoT Hub. Si un message est routé vers plusieurs points de terminaison, cette valeur augmente d’une unité pour chaque remise réussie. Si un message est routé plusieurs fois vers le même point de terminaison, cette valeur augmente d’une unité pour chaque remise réussie.|Aucune dimension|
|d2c.telemetry.egress.dropped|Routage : messages de télémétrie annulés |Count|Total|Nombre de fois où des messages ont été annulés par le routage IoT Hub en raison de points de terminaison morts. Cette valeur ne compte pas les messages remis à un itinéraire de secours, car les messages annulés n’y sont pas remis.|Aucune dimension|
|d2c.telemetry.egress.orphaned|Routage : messages de télémétrie orphelins |Count|Total|Nombre de fois où des messages ont été définis comme orphelins par le routage IoT Hub car ils ne correspondaient à aucune règle de routage (y compris la règle de secours). |Aucune dimension|
|d2c.telemetry.egress.invalid|Routage : messages de télémétrie incompatibles|Count|Total|Nombre de fois où le routage IoT Hub n’a pas réussi à remettre des messages en raison d’une incompatibilité avec le point de terminaison. Cette valeur n’inclut pas les nouvelles tentatives.|Aucune dimension|
|d2c.telemetry.egress.fallback|Routage : messages remis à l’itinéraire de secours|Count|Total|Nombre de fois où le routage IoT Hub a remis des messages au point de terminaison associé à l’itinéraire de secours.|Aucune dimension|
|d2c.endpoints.egress.eventHubs|Routage : messages remis à Event Hub|Count|Total|Nombre de fois où le routage IoT Hub a correctement remis des messages à des points de terminaison Event Hub.|Aucune dimension|
|d2c.endpoints.latency.eventHubs|Routage : latence des messages d’Event Hub|Millisecondes|Moyenne|Latence moyenne (en millisecondes) entre les entrées de messages vers IoT Hub et dans un point de terminaison Event Hub.|Aucune dimension|
|d2c.endpoints.egress.serviceBusQueues|Routage : messages remis à la file d’attente Service Bus|Count|Total|Nombre de fois où le routage IoT Hub a correctement remis des messages aux points de terminaison de file d’attente Service Bus.|Aucune dimension|
|d2c.endpoints.latency.serviceBusQueues|Routage : latence des messages de la file d’attente Service Bus|Millisecondes|Moyenne|Latence moyenne (en millisecondes) entre les entrées de messages vers IoT Hub et de télémétrie dans un point de terminaison de file d’attente Service Bus.|Aucune dimension|
|d2c.endpoints.egress.serviceBusTopics|Routage : messages remis à la rubrique Service Bus|Count|Total|Nombre de fois où le routage IoT Hub a correctement remis des messages à des points de terminaison de rubrique Service Bus.|Aucune dimension|
|d2c.endpoints.latency.serviceBusTopics|Routage : latence des messages de la rubrique Service Bus|Millisecondes|Moyenne|Latence moyenne (en millisecondes) entre les entrées de messages vers IoT Hub et de télémétrie dans un point de terminaison de rubrique Service Bus.|Aucune dimension|
|d2c.endpoints.egress.builtIn.events|Routage : messages remis à des messages/événements|Count|Total|Nombre de fois où le routage IoT Hub a correctement remis des messages au point de terminaison intégré (messages/événements).|Aucune dimension|
|d2c.endpoints.latency.builtIn.events|Routage : latence des messages de messages/d’événements|Millisecondes|Moyenne|Latence moyenne (en millisecondes) entre les entrées de messages vers IoT Hub et de télémétrie dans le point de terminaison intégré (messages/événements).|Aucune dimension|
|d2c.endpoints.egress.storage|Routage : messages remis au stockage|Count|Total|Nombre de fois où le routage IoT Hub a correctement remis des messages à des points de terminaison de stockage.|Aucune dimension|
|d2c.endpoints.latency.storage|Routage : latence des messages du stockage|Millisecondes|Moyenne|Latence moyenne (en millisecondes) entre les entrées de messages vers IoT Hub et de télémétrie dans un point de terminaison de stockage.|Aucune dimension|
|d2c.endpoints.egress.storage.bytes|Routage : données remises au stockage|Octets|Total|Quantité de données (octets) que le routage IoT Hub a remis aux points de terminaison de stockage.|Aucune dimension|
|d2c.endpoints.egress.storage.blobs|Routage : objets blob remis au stockage|Count|Total|Nombre de fois où le routage IoT Hub a remis des objets blob à des points de terminaison de stockage.|Aucune dimension|
|d2c.twin.read.success|Lectures de représentations réussies d’appareils|Count|Total|Total des lectures de représentations réussies initiées par un appareil.|Aucune dimension|
|d2c.twin.read.failure|Lectures de représentations d’appareils en échec|Count|Total|Total des lectures de représentations en échec initiées par un appareil.|Aucune dimension|
|d2c.twin.read.size|Taille de la réponse des lectures de représentations des appareils|Octets|Moyenne|Moyenne, minimum et maximum de toutes les lectures de représentations réussies initiées par un appareil.|Aucune dimension|
|d2c.twin.update.success|Mises à jour de représentations réussies d’appareils|Count|Total|Total des mises à jour de représentations réussies initiées par un appareil.|Aucune dimension|
|d2c.twin.update.failure|Mises à jour de représentations d’appareils en échec|Count|Total|Total des mises à jour de représentations en échec initiées par un appareil.|Aucune dimension|
|d2c.twin.update.size|Taille des mises à jour de représentations d’appareils|Octets|Moyenne|Taille moyenne, minimale et maximale de toutes les mises à jour de représentations réussies initiées par un appareil.|Aucune dimension|
|c2d.methods.success|Appels de méthode directe réussis|Count|Total|Total des appels de méthode directe réussis.|Aucune dimension|
|c2d.methods.failure|Appels de méthode directe en échec|Count|Total|Total des appels de méthode directe en échec.|Aucune dimension|
|c2d.methods.requestSize|Taille de demande des appels de méthode directe|Octets|Moyenne|Moyenne, minimum et maximum de toutes les demandes de méthode directe réussies.|Aucune dimension|
|c2d.methods.responseSize|Taille de réponse des appels de méthode directe|Octets|Moyenne|Moyenne, minimum et maximum de toutes les réponses de méthode directe réussies.|Aucune dimension|
|c2d.twin.read.success|Lectures de représentations réussies de serveur principal|Count|Total|Total des lectures de représentations réussies initiées par un serveur principal.|Aucune dimension|
|c2d.twin.read.failure|Lectures de représentations de serveur principal en échec|Count|Total|Total des lectures de représentations en échec initiées par un serveur principal.|Aucune dimension|
|c2d.twin.read.size|Taille de la réponse des lectures de représentations de serveur principal|Octets|Moyenne|Moyenne, minimum et maximum de toutes les lectures de représentations réussies initiées par un serveur principal.|Aucune dimension|
|c2d.twin.update.success|Mises à jour de représentations réussies de serveur principal|Count|Total|Total des mises à jour de représentations réussies initiées par un serveur principal.|Aucune dimension|
|c2d.twin.update.failure|Mises à jour de représentations de serveur principal en échec|Count|Total|Total des mises à jour de représentations en échec initiées par un serveur principal.|Aucune dimension|
|c2d.twin.update.size|Taille des mises à jour de représentations de serveur principal|Octets|Moyenne|Taille moyenne, minimale et maximale de toutes les mises à jour de représentations réussies initiées par un serveur principal.|Aucune dimension|
|twinQueries.success|Requêtes de représentations réussies|Count|Total|Total des requêtes de représentations réussies.|Aucune dimension|
|twinQueries.failure|Requêtes de représentations en échec|Count|Total|Total des requêtes de représentations en échec.|Aucune dimension|
|twinQueries.resultSize|Taille du résultat des requêtes de représentations|Octets|Moyenne|Moyenne, minimum et maximum de la taille du résultat de toutes les requêtes de représentations réussies.|Aucune dimension|
|jobs.createTwinUpdateJob.success|Créations réussies des travaux de mises à jour de représentations|Count|Total|Total des créations réussies de travaux de mises à jour de représentations.|Aucune dimension|
|jobs.createTwinUpdateJob.failure|Créations des travaux de mises à jour de représentations en échec|Count|Total|Total des créations en échec des travaux de mises à jour de représentations.|Aucune dimension|
|jobs.createDirectMethodJob.success|Créations réussies des travaux d’appel de méthode|Count|Total|Total des créations réussies des travaux d’appel de méthode directe.|Aucune dimension|
|jobs.createDirectMethodJob.failure|Créations des travaux d’appel de méthode en échec|Count|Total|Total des créations en échec des travaux d’appel de méthode directe.|Aucune dimension|
|jobs.listJobs.success|Appels réussis pour répertorier les travaux|Count|Total|Total des appels réussis pour répertorier les travaux.|Aucune dimension|
|jobs.listJobs.failure|Appels en échec pour répertorier les travaux|Count|Total|Total des appels en échec pour répertorier les travaux.|Aucune dimension|
|jobs.cancelJob.success|Annulations de travaux réussies|Count|Total|Total des appels réussis pour annuler un travail.|Aucune dimension|
|jobs.cancelJob.failure|Annulations de travaux en échec|Count|Total|Total des appels en échec pour annuler un travail.|Aucune dimension|
|jobs.queryJobs.success|Requêtes de travaux réussies|Count|Total|Total des appels réussis pour interroger les travaux.|Aucune dimension|
|jobs.queryJobs.failure|Requêtes de travaux en échec|Count|Total|Total des appels en échec pour interroger les travaux.|Aucune dimension|
|jobs.completed|Travaux terminés|Count|Total|Total des travaux terminés.|Aucune dimension|
|jobs.failed|Travaux en échec|Count|Total|Total des travaux en échec.|Aucune dimension|
|d2c.telemetry.ingress.sendThrottle|Nombre d’erreurs de limitation|Count|Total|Nombre d’erreurs de limitation causées par des limitations de débit d’appareil|Aucune dimension|
|dailyMessageQuotaUsed|Nombre total de messages utilisés|Count|Moyenne|Nombre total de messages utilisés aujourd’hui. Il s’agit d’une valeur cumulative qui est réinitialisée sur zéro à 00h00 UTC chaque jour.|Aucune dimension|
|deviceDataUsage|Utilisation totale des données d’appareil (déconseillé)|Octets|Total|Nombre d’octets transférés vers et depuis tous les appareils connectés à IotHub|Aucune dimension|
|deviceDataUsageV2|Utilisation totale des données d’appareil (préversion)|Octets|Total|Nombre d’octets transférés vers et depuis tous les appareils connectés à IotHub|Aucune dimension|
|totalDeviceCount|Nombre total d’appareils (préversion)|Count|Moyenne|Nombre d’appareils enregistrés sur votre hub IoT|Aucune dimension|
|connectedDeviceCount|Appareils connectés (préversion)|Count|Moyenne|Nombre d’appareils connectés à votre hub IoT|Aucune dimension|
|configurations|Métriques de configuration|Count|Total|Métriques pour les opérations de configuration|Aucune dimension|

## <a name="next-steps"></a>Étapes suivantes
Les métriques IoT Hub n’étant plus un secret pour vous, suivez le lien ci-après pour en savoir plus sur la gestion d’Azure IoT Hub :

* [Surveillance des opérations][lnk-monitor]

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Guide du développeur d’IoT Hub][lnk-devguide]
* [Déploiement d’une IA sur des appareils de périphérie avec Azure IoT Edge][lnk-iotedge]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png

[lnk-get-started]: quickstart-send-telemetry-dotnet.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
