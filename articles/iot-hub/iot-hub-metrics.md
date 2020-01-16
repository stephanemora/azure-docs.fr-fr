---
title: Utiliser des métriques pour surveiller Azure IoT Hub | Microsoft Docs
description: Guide d’utilisation des métriques Azure IoT Hub pour évaluer et surveiller l’intégrité globale de votre IoT Hub.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: 20a1ec4b8eb88f5914ce6b27acc8a472e58f5d29
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457080"
---
# <a name="understand-iot-hub-metrics"></a>Comprendre les métriques IoT Hub

Les métriques IoT Hub vous offrent de meilleures données sur l’état des ressources Azure IoT de votre abonnement Azure. Grâce aux métriques IoT Hub, vous pouvez évaluer l’intégrité globale du service et des appareils connectés à ce dernier. Les statistiques accessibles à l’utilisateur sont importantes, car elles vous permettent d’effectuer le suivi de votre hub IoT et de connaître les causes des problèmes sans contacter le support Azure.

Les métriques sont activées par défaut. Vous pouvez afficher les métriques IoT Hub à partir du portail Azure.

> [!NOTE]
> Vous pouvez utiliser les métriques IoT Hub pour afficher des informations sur les appareils IoT Plug-and-Play connectés à votre IoT Hub. Les appareils IoT Plug-and-Play font partie de la [préversion publique d’IoT Plug-and-Play](../iot-pnp/overview-iot-plug-and-play.md).

## <a name="how-to-view-iot-hub-metrics"></a>Affichage des métriques IoT Hub

1. Créez un hub IoT. Vous trouverez des instructions sur la façon de créer un hub IoT dans le guide [Envoyer des données de télémétrie à partir d’un appareil à IoT Hub](quickstart-send-telemetry-dotnet.md).

2. Ouvrez le panneau de votre hub IoT. Ici, cliquez sur **Métriques**.
   
    ![Capture d’écran montrant où se trouve l’option Métriques dans la page de ressources IoT Hub](./media/iot-hub-metrics/enable-metrics-1.png)

3. Dans le panneau de métriques, vous pouvez afficher les métriques IoT Hub et créer des vues personnalisées de ces dernières. 
   
    ![Capture d’écran montrant la page Métriques pour IoT Hub](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. Vous pouvez choisir d’envoyer vos données métriques à un point de terminaison Event Hubs ou un compte de stockage Azure en cliquant sur **Paramètres de diagnostic**, puis sur **Ajouter un paramètre de diagnostic**.

   ![Capture d’écran montrant où se trouve le bouton Paramètres de diagnostic](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Métriques IoT Hub et instructions d’utilisation

IoT Hub fournit plusieurs métriques afin de vous donner une vue d’ensemble de l’intégrité de votre hub et du nombre total d’appareils connectés. Vous pouvez combiner les informations de plusieurs métriques pour obtenir une image plus grande de l’état du hub IoT. Le tableau suivant décrit les métriques dont chaque hub IoT effectue le suivi et la relation de chacune avec l’état global du hub IoT.

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentatives d’envoi de message de télémétrie|Count|Total|Nombre de tentatives d’envoi de messages de télémétrie appareil vers cloud à votre hub IoT|None|
|d2c.telemetry.ingress.success|Messages de télémétrie envoyés|Count|Total|Nombre de messages de télémétrie appareil vers cloud envoyés avec succès à votre hub IoT|None|
|c2d.commands.egress.complete.success|Remises de messages cloud vers appareil terminées|Count|Total|Nombre de remises de messages cloud vers appareil terminées avec succès par l’appareil|None|
|c2d.commands.egress.abandon.success|Messages cloud vers appareil abandonnés|Count|Total|Nombre de messages cloud vers appareil abandonnés par l’appareil|None|
|c2d.commands.egress.reject.success|Messages cloud vers appareil rejetés|Count|Total|Nombre de messages cloud vers appareil rejetés par l’appareil|None|
|C2DMessagesExpired|Messages cloud vers appareil ayant expiré (préversion)|Count|Total|Nombre de messages cloud vers appareil ayant expiré|None|
|devices.totalDevices|Nombre total d’appareils (déconseillé)|Count|Total|Nombre d’appareils enregistrés sur votre hub IoT|None|
|devices.connectedDevices.allProtocol|Appareils connectés (déconseillé) |Count|Total|Nombre d’appareils connectés à votre hub IoT|None|
|d2c.telemetry.egress.success|Routage : messages de télémétrie remis|Count|Total|Nombre de fois où des messages ont été correctement remis à tous les points de terminaison à l’aide du routage IoT Hub. Si un message est routé vers plusieurs points de terminaison, cette valeur augmente d’une unité pour chaque remise réussie. Si un message est routé plusieurs fois vers le même point de terminaison, cette valeur augmente d’une unité pour chaque remise réussie.|None|
|d2c.telemetry.egress.dropped|Routage : messages de télémétrie annulés |Count|Total|Nombre de fois où des messages ont été annulés par le routage IoT Hub en raison de points de terminaison morts. Cette valeur ne compte pas les messages remis à un itinéraire de secours, car les messages annulés n’y sont pas remis.|None|
|d2c.telemetry.egress.orphaned|Routage : messages de télémétrie orphelins |Count|Total|Nombre de fois où des messages ont été définis comme orphelins par le routage IoT Hub car ils ne correspondaient à aucune règle de routage (y compris la règle de secours). |None|
|d2c.telemetry.egress.invalid|Routage : messages de télémétrie incompatibles|Count|Total|Nombre de fois où le routage IoT Hub n’a pas réussi à remettre des messages en raison d’une incompatibilité avec le point de terminaison. Cette valeur n’inclut pas les nouvelles tentatives.|None|
|d2c.telemetry.egress.fallback|Routage : messages remis à l’itinéraire de secours|Count|Total|Nombre de fois où le routage IoT Hub a remis des messages au point de terminaison associé à l’itinéraire de secours.|None|
|d2c.endpoints.egress.eventHubs|Routage : messages remis à Event Hub|Count|Total|Nombre de fois où le routage IoT Hub a correctement remis des messages à des points de terminaison Event Hub.|None|
|d2c.endpoints.latency.eventHubs|Routage : latence des messages d’Event Hub|Millisecondes|Average|Latence moyenne (en millisecondes) entre les entrées de messages vers IoT Hub et dans un point de terminaison Event Hub.|None|
|d2c.endpoints.egress.serviceBusQueues|Routage : messages remis à la file d’attente Service Bus|Count|Total|Nombre de fois où le routage IoT Hub a correctement remis des messages aux points de terminaison de file d’attente Service Bus.|None|
|d2c.endpoints.latency.serviceBusQueues|Routage : latence des messages de la file d’attente Service Bus|Millisecondes|Average|Latence moyenne (en millisecondes) entre les entrées de messages vers IoT Hub et de télémétrie dans un point de terminaison de file d’attente Service Bus.|None|
|d2c.endpoints.egress.serviceBusTopics|Routage : messages remis à la rubrique Service Bus|Count|Total|Nombre de fois où le routage IoT Hub a correctement remis des messages à des points de terminaison de rubrique Service Bus.|None|
|d2c.endpoints.latency.serviceBusTopics|Routage : latence des messages de la rubrique Service Bus|Millisecondes|Average|Latence moyenne (en millisecondes) entre les entrées de messages vers IoT Hub et de télémétrie dans un point de terminaison de rubrique Service Bus.|None|
|d2c.endpoints.egress.builtIn.events|Routage : messages remis à des messages/événements|Count|Total|Nombre de fois où le routage IoT Hub a correctement remis des messages au point de terminaison intégré (messages/événements).|None|
|d2c.endpoints.latency.builtIn.events|Routage : latence des messages de messages/d’événements|Millisecondes|Average|Latence moyenne (en millisecondes) entre les entrées de messages vers IoT Hub et de télémétrie dans le point de terminaison intégré (messages/événements).|None|
|d2c.endpoints.egress.storage|Routage : messages remis au stockage|Count|Total|Nombre de fois où le routage IoT Hub a correctement remis des messages à des points de terminaison de stockage.|None|
|d2c.endpoints.latency.storage|Routage : latence des messages du stockage|Millisecondes|Average|Latence moyenne (en millisecondes) entre les entrées de messages vers IoT Hub et de télémétrie dans un point de terminaison de stockage.|None|
|d2c.endpoints.egress.storage.bytes|Routage : données remises au stockage|Octets|Total|Quantité de données (octets) que le routage IoT Hub a remis aux points de terminaison de stockage.|None|
|d2c.endpoints.egress.storage.blobs|Routage : objets blob remis au stockage|Count|Total|Nombre de fois où le routage IoT Hub a remis des objets blob à des points de terminaison de stockage.|None|
|EventGridDeliveries|Remises Event Grid (préversion)|Count|Total|Nombre d’événements IoT Hub publiés dans Event Grid. Utilisez la dimension Résultat pour le nombre de requêtes ayant réussi et ayant échoué. La dimension EventType représente le type de l’événement (https://aka.ms/ioteventgrid) ).|ResourceId,<br/>Result,<br/>Type d’événement|
|EventGridLatency|Latence d’Event Grid (préversion)|Millisecondes|Average|Latence moyenne (en millisecondes) entre le moment où l’événement Iot Hub a été généré et le moment où l’événement a été publié dans Event Grid. Ce nombre est une moyenne de tous les types d’événement. Utilisez la dimension Type d’événement pour afficher la latence d’un type d’événement spécifique.|ResourceId,<br/>Type d’événement|
|d2c.twin.read.success|Lectures de représentations réussies d’appareils|Count|Total|Total des lectures de représentations réussies initiées par un appareil.|None|
|d2c.twin.read.failure|Lectures de représentations d’appareils en échec|Count|Total|Total des lectures de représentations en échec initiées par un appareil.|None|
|d2c.twin.read.size|Taille de la réponse des lectures de représentations des appareils|Octets|Average|Moyenne, minimum et maximum de toutes les lectures de représentations réussies initiées par un appareil.|None|
|d2c.twin.update.success|Mises à jour de représentations réussies d’appareils|Count|Total|Total des mises à jour de représentations réussies initiées par un appareil.|None|
|d2c.twin.update.failure|Mises à jour de représentations d’appareils en échec|Count|Total|Total des mises à jour de représentations en échec initiées par un appareil.|None|
|d2c.twin.update.size|Taille des mises à jour de représentations d’appareils|Octets|Average|Taille moyenne, minimale et maximale de toutes les mises à jour de représentations réussies initiées par un appareil.|None|
|c2d.methods.success|Appels de méthode directe réussis|Count|Total|Total des appels de méthode directe réussis.|None|
|c2d.methods.failure|Appels de méthode directe en échec|Count|Total|Total des appels de méthode directe en échec.|None|
|c2d.methods.requestSize|Taille de demande des appels de méthode directe|Octets|Average|Moyenne, minimum et maximum de toutes les demandes de méthode directe réussies.|None|
|c2d.methods.responseSize|Taille de réponse des appels de méthode directe|Octets|Average|Moyenne, minimum et maximum de toutes les réponses de méthode directe réussies.|None|
|c2d.twin.read.success|Lectures de représentations réussies de serveur principal|Count|Total|Total des lectures de représentations réussies initiées par un serveur principal.|None|
|c2d.twin.read.failure|Lectures de représentations de serveur principal en échec|Count|Total|Total des lectures de représentations en échec initiées par un serveur principal.|None|
|c2d.twin.read.size|Taille de la réponse des lectures de représentations de serveur principal|Octets|Average|Moyenne, minimum et maximum de toutes les lectures de représentations réussies initiées par un serveur principal.|None|
|c2d.twin.update.success|Mises à jour de représentations réussies de serveur principal|Count|Total|Total des mises à jour de représentations réussies initiées par un serveur principal.|None|
|c2d.twin.update.failure|Mises à jour de représentations de serveur principal en échec|Count|Total|Total des mises à jour de représentations en échec initiées par un serveur principal.|None|
|c2d.twin.update.size|Taille des mises à jour de représentations de serveur principal|Octets|Average|Taille moyenne, minimale et maximale de toutes les mises à jour de représentations réussies initiées par un serveur principal.|None|
|twinQueries.success|Requêtes de représentations réussies|Count|Total|Total des requêtes de représentations réussies.|None|
|twinQueries.failure|Requêtes de représentations en échec|Count|Total|Total des requêtes de représentations en échec.|None|
|twinQueries.resultSize|Taille du résultat des requêtes de représentations|Octets|Average|Moyenne, minimum et maximum de la taille du résultat de toutes les requêtes de représentations réussies.|None|
|jobs.createTwinUpdateJob.success|Créations réussies des travaux de mises à jour de représentations|Count|Total|Total des créations réussies de travaux de mises à jour de représentations.|None|
|jobs.createTwinUpdateJob.failure|Créations des travaux de mises à jour de représentations en échec|Count|Total|Total des créations en échec des travaux de mises à jour de représentations.|None|
|jobs.createDirectMethodJob.success|Créations réussies des travaux d’appel de méthode|Count|Total|Total des créations réussies des travaux d’appel de méthode directe.|None|
|jobs.createDirectMethodJob.failure|Créations des travaux d’appel de méthode en échec|Count|Total|Total des créations en échec des travaux d’appel de méthode directe.|None|
|jobs.listJobs.success|Appels réussis pour répertorier les travaux|Count|Total|Total des appels réussis pour répertorier les travaux.|None|
|jobs.listJobs.failure|Appels en échec pour répertorier les travaux|Count|Total|Total des appels en échec pour répertorier les travaux.|None|
|jobs.cancelJob.success|Annulations de travaux réussies|Count|Total|Total des appels réussis pour annuler un travail.|None|
|jobs.cancelJob.failure|Annulations de travaux en échec|Count|Total|Total des appels en échec pour annuler un travail.|None|
|jobs.queryJobs.success|Requêtes de travaux réussies|Count|Total|Total des appels réussis pour interroger les travaux.|None|
|jobs.queryJobs.failure|Requêtes de travaux en échec|Count|Total|Total des appels en échec pour interroger les travaux.|None|
|jobs.completed|Travaux terminés|Count|Total|Total des travaux terminés.|None|
|jobs.failed|Travaux en échec|Count|Total|Total des travaux en échec.|None|
|d2c.telemetry.ingress.sendThrottle|Nombre d’erreurs de limitation|Count|Total|Nombre d’erreurs de limitation causées par des limitations de débit d’appareil|None|
|dailyMessageQuotaUsed|Nombre total de messages utilisés|Count|Average|Nombre total de messages utilisés aujourd’hui. Il s’agit d’une valeur cumulative qui est réinitialisée sur zéro à 00h00 UTC chaque jour.|None|
|deviceDataUsage|Utilisation totale des données d’appareil|Octets|Total|Nombre d’octets transférés vers et depuis tous les appareils connectés à IotHub|None|
|deviceDataUsageV2|Utilisation totale des données d’appareil (préversion)|Octets|Total|Nombre d’octets transférés vers et depuis tous les appareils connectés à IotHub|None|
|totalDeviceCount|Nombre total d’appareils (préversion)|Count|Average|Nombre d’appareils enregistrés sur votre hub IoT|None|
|connectedDeviceCount|Appareils connectés (préversion)|Count|Average|Nombre d’appareils connectés à votre hub IoT|None|
|configurations|Métriques de configuration|Count|Total|Métriques pour les opérations de configuration|None|

## <a name="next-steps"></a>Étapes suivantes

Les métriques IoT Hub n’étant plus un secret pour vous, suivez le lien ci-après pour en savoir plus sur la gestion d’Azure IoT Hub :

* [Surveillance des opérations](iot-hub-operations-monitoring.md)

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Guide du développeur d’IoT Hub](iot-hub-devguide.md)

* [Déploiement d’une IA sur des appareils de périmètre avec Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
