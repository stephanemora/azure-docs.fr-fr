---
title: Utiliser des métriques pour surveiller Azure IoT Hub | Microsoft Docs
description: Guide d’utilisation des métriques Azure IoT Hub pour évaluer et surveiller l’intégrité globale de votre IoT Hub.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: 948cdb2ab1af3fe93566497186c025f7f8f39b2e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877090"
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
|d2c<br>.telemetry<br>.ingress.<br>allProtocol|Tentatives d’envoi de message de télémétrie|Count|Total|Nombre de tentatives d’envoi de messages de télémétrie appareil vers cloud à votre hub IoT|Aucune dimension|
|d2c<br>.telemetry<br>.ingress<br>.success|Messages de télémétrie envoyés|Count|Total|Nombre de messages de télémétrie appareil vers cloud envoyés avec succès à votre hub IoT|Aucune dimension|
|c2d<br>.commands<br>.egress<br>.complete<br>.success|Commandes terminées|Count|Total|Nombre de commandes cloud vers appareil terminées avec succès par l’appareil|Aucune dimension|
|c2d<br>.commands<br>.egress<br>.abandon<br>.success|Commandes abandonnées|Count|Total|Nombre de commandes cloud vers appareil abandonnées par l’appareil|Aucune dimension|
|c2d<br>.commands<br>.egress<br>.reject<br>.success|Commandes rejetées|Count|Total|Nombre de commandes cloud vers appareil rejetées par l’appareil|Aucune dimension|
|périphériques<br>.totalDevices|Nombre total d’appareils (déconseillé)|Count|Total|Nombre d’appareils enregistrés sur votre hub IoT|Aucune dimension|
|périphériques<br>.connectedDevices<br>.allProtocol|Appareils connectés (déconseillé) |Count|Total|Nombre d’appareils connectés à votre hub IoT|Aucune dimension|
|d2c<br>.telemetry<br>.egress<br>.success|Routage : messages de télémétrie remis|Count|Total|Nombre de fois où des messages ont été correctement remis à tous les points de terminaison à l’aide du routage IoT Hub. Si un message est routé vers plusieurs points de terminaison, cette valeur augmente d’une unité pour chaque remise réussie. Si un message est routé plusieurs fois vers le même point de terminaison, cette valeur augmente d’une unité pour chaque remise réussie.|Aucune dimension|
|d2c<br>.telemetry<br>.egress<br>.dropped|Routage : messages de télémétrie annulés |Count|Total|Nombre de fois où des messages ont été annulés par le routage IoT Hub en raison de points de terminaison morts. Cette valeur ne compte pas les messages remis à un itinéraire de secours, car les messages annulés n’y sont pas remis.|Aucune dimension|
|d2c<br>.telemetry<br>.egress<br>.orphaned|Routage : messages de télémétrie orphelins |Count|Total|Nombre de fois où des messages ont été définis comme orphelins par le routage IoT Hub car ils ne correspondaient à aucune règle de routage (y compris la règle de secours). |Aucune dimension|
|d2c<br>.telemetry<br>.egress<br>.invalid|Routage : messages de télémétrie incompatibles|Count|Total|Nombre de fois où le routage IoT Hub n’a pas réussi à remettre des messages en raison d’une incompatibilité avec le point de terminaison. Cette valeur n’inclut pas les nouvelles tentatives.|Aucune dimension|
|d2c<br>.telemetry<br>.egress<br>.fallback|Routage : messages remis à l’itinéraire de secours|Count|Total|Nombre de fois où le routage IoT Hub a remis des messages au point de terminaison associé à l’itinéraire de secours.|Aucune dimension|
|d2c<br>.endpoints<br>.egress<br>.eventHubs|Routage : messages remis à Event Hub|Count|Total|Nombre de fois où le routage IoT Hub a correctement remis des messages à des points de terminaison Event Hub.|Aucune dimension|
|d2c<br>.endpoints<br>.latency<br>.eventHubs|Routage : latence des messages d’Event Hub|Millisecondes|Moyenne|Latence moyenne (en millisecondes) entre les entrées de messages vers IoT Hub et dans un point de terminaison Event Hub.|Aucune dimension|
|d2c<br>.endpoints<br>.egress<br>.serviceBusQueues|Routage : messages remis à la file d’attente Service Bus|Count|Total|Nombre de fois où le routage IoT Hub a correctement remis des messages aux points de terminaison de file d’attente Service Bus.|Aucune dimension|
|d2c<br>.endpoints<br>.latency<br>.serviceBusQueues|Routage : latence des messages de la file d’attente Service Bus|Millisecondes|Moyenne|Latence moyenne (en millisecondes) entre les entrées de messages vers IoT Hub et de télémétrie dans un point de terminaison de file d’attente Service Bus.|Aucune dimension|
|d2c<br>.endpoints<br>.egress<br>.serviceBusTopics|Routage : messages remis à la rubrique Service Bus|Count|Total|Nombre de fois où le routage IoT Hub a correctement remis des messages à des points de terminaison de rubrique Service Bus.|Aucune dimension|
|d2c<br>.endpoints<br>.latency<br>.serviceBusTopics|Routage : latence des messages de la rubrique Service Bus|Millisecondes|Moyenne|Latence moyenne (en millisecondes) entre les entrées de messages vers IoT Hub et de télémétrie dans un point de terminaison de rubrique Service Bus.|Aucune dimension|
|d2c<br>.endpoints<br>.egress<br>.builtIn<br>.events|Routage : messages remis à des messages/événements|Count|Total|Nombre de fois où le routage IoT Hub a correctement remis des messages au point de terminaison intégré (messages/événements). Cette métrique démarre uniquement lorsque le routage est activé (https://aka.ms/iotrouting) pour IoT Hub).|Aucune dimension|
|d2c<br>.endpoints<br>.latency<br>.builtIn.events|Routage : latence des messages de messages/d’événements|Millisecondes|Moyenne|Latence moyenne (en millisecondes) entre les entrées de messages vers IoT Hub et de télémétrie dans le point de terminaison intégré (messages/événements). Cette métrique démarre uniquement lorsque le routage est activé (https://aka.ms/iotrouting) pour IoT Hub).|Aucune dimension|
|d2c<br>.endpoints<br>.egress<br>.storage|Routage : messages remis au stockage|Count|Total|Nombre de fois où le routage IoT Hub a correctement remis des messages à des points de terminaison de stockage.|Aucune dimension|
|d2c<br>.endpoints<br>.latency<br>.storage|Routage : latence des messages du stockage|Millisecondes|Moyenne|Latence moyenne (en millisecondes) entre les entrées de messages vers IoT Hub et de télémétrie dans un point de terminaison de stockage.|Aucune dimension|
|d2c<br>.endpoints<br>.egress<br>.storage<br>.bytes|Routage : données remises au stockage|Octets|Total|Quantité de données (octets) que le routage IoT Hub a remis aux points de terminaison de stockage.|Aucune dimension|
|d2c<br>.endpoints<br>.egress<br>.storage<br>.blobs|Routage : objets blob remis au stockage|Count|Total|Nombre de fois où le routage IoT Hub a remis des objets blob à des points de terminaison de stockage.|Aucune dimension|
|EventGridDeliveries|Remises Event Grid (préversion)|Count|Total|Nombre d’événements IoT Hub publiés dans Event Grid. Utilisez la dimension Résultat pour le nombre de requêtes ayant réussi et ayant échoué. La dimension EventType représente le type de l’événement (https://aka.ms/ioteventgrid) ). Pour afficher l’origine des demandes, utilisez la dimension Type d’événement.|Résultat, Type d’événement|
|EventGridLatency|Latence d’Event Grid (préversion)|Millisecondes|Moyenne|Latence moyenne (en millisecondes) entre le moment où l’événement Iot Hub a été généré et le moment où l’événement a été publié dans Event Grid. Ce nombre est une moyenne de tous les types d’événement. Utilisez la dimension Type d’événement pour afficher la latence d’un type d’événement spécifique.|Type d’événement|
|d2c<br>.twin<br>.read<br>.success|Lectures de représentations réussies d’appareils|Count|Total|Total des lectures de représentations réussies initiées par un appareil.|Aucune dimension|
|d2c<br>.twin<br>.read<br>.failure|Lectures de représentations d’appareils en échec|Count|Total|Total des lectures de représentations en échec initiées par un appareil.|Aucune dimension|
|d2c<br>.twin<br>.read<br>.size|Taille de la réponse des lectures de représentations des appareils|Octets|Moyenne|Moyenne, minimum et maximum de toutes les lectures de représentations réussies initiées par un appareil.|Aucune dimension|
|d2c<br>.twin<br>.update<br>.success|Mises à jour de représentations réussies d’appareils|Count|Total|Total des mises à jour de représentations réussies initiées par un appareil.|Aucune dimension|
|d2c<br>.twin<br>.update<br>.failure|Mises à jour de représentations d’appareils en échec|Count|Total|Total des mises à jour de représentations en échec initiées par un appareil.|Aucune dimension|
|d2c<br>.twin<br>.update<br>.size|Taille des mises à jour de représentations d’appareils|Octets|Moyenne|Taille moyenne, minimale et maximale de toutes les mises à jour de représentations réussies initiées par un appareil.|Aucune dimension|
|c2d<br>.methods<br>.success|Appels de méthode directe réussis|Count|Total|Total des appels de méthode directe réussis.|Aucune dimension|
|c2d<br>.methods<br>.failure|Appels de méthode directe en échec|Count|Total|Total des appels de méthode directe en échec.|Aucune dimension|
|c2d<br>.methods<br>.requestSize|Taille de demande des appels de méthode directe|Octets|Moyenne|Moyenne, minimum et maximum de toutes les demandes de méthode directe réussies.|Aucune dimension|
|c2d<br>.methods<br>.responseSize|Taille de réponse des appels de méthode directe|Octets|Moyenne|Moyenne, minimum et maximum de toutes les réponses de méthode directe réussies.|Aucune dimension|
|c2d<br>.twin<br>.read<br>.success|Lectures de représentations réussies de serveur principal|Count|Total|Total des lectures de représentations réussies initiées par un serveur principal.|Aucune dimension|
|c2d<br>.twin<br>.read<br>.failure|Lectures de représentations de serveur principal en échec|Count|Total|Total des lectures de représentations en échec initiées par un serveur principal.|Aucune dimension|
|c2d<br>.twin<br>.read<br>.size|Taille de la réponse des lectures de représentations de serveur principal|Octets|Moyenne|Moyenne, minimum et maximum de toutes les lectures de représentations réussies initiées par un serveur principal.|Aucune dimension|
|c2d<br>.twin<br>.update<br>.success|Mises à jour de représentations réussies de serveur principal|Count|Total|Total des mises à jour de représentations réussies initiées par un serveur principal.|Aucune dimension|
|c2d<br>.twin<br>.update<br>.failure|Mises à jour de représentations de serveur principal en échec|Count|Total|Total des mises à jour de représentations en échec initiées par un serveur principal.|Aucune dimension|
|c2d<br>.twin<br>.update<br>.size|Taille des mises à jour de représentations de serveur principal|Octets|Moyenne|Taille moyenne, minimale et maximale de toutes les mises à jour de représentations réussies initiées par un serveur principal.|Aucune dimension|
|twinQueries<br>.success|Requêtes de représentations réussies|Count|Total|Total des requêtes de représentations réussies.|Aucune dimension|
|twinQueries<br>.failure|Requêtes de représentations en échec|Count|Total|Total des requêtes de représentations en échec.|Aucune dimension|
|twinQueries<br>.resultSize|Taille du résultat des requêtes de représentations|Octets|Moyenne|Moyenne, minimum et maximum de la taille du résultat de toutes les requêtes de représentations réussies.|Aucune dimension|
|jobs<br>.createTwinUpdateJob<br>.success|Créations réussies des travaux de mises à jour de représentations|Count|Total|Total des créations réussies de travaux de mises à jour de représentations.|Aucune dimension|
|jobs<br>.createTwinUpdateJob<br>.failure|Créations des travaux de mises à jour de représentations en échec|Count|Total|Total des créations en échec des travaux de mises à jour de représentations.|Aucune dimension|
|jobs<br>.createDirectMethodJob<br>.success|Créations réussies des travaux d’appel de méthode|Count|Total|Total des créations réussies des travaux d’appel de méthode directe.|Aucune dimension|
|jobs<br>.createDirectMethodJob<br>.failure|Créations des travaux d’appel de méthode en échec|Count|Total|Total des créations en échec des travaux d’appel de méthode directe.|Aucune dimension|
|jobs<br>.listJobs<br>.success|Appels réussis pour répertorier les travaux|Count|Total|Total des appels réussis pour répertorier les travaux.|Aucune dimension|
|jobs<br>.listJobs<br>.failure|Appels en échec pour répertorier les travaux|Count|Total|Total des appels en échec pour répertorier les travaux.|Aucune dimension|
|jobs<br>.cancelJob<br>.success|Annulations de travaux réussies|Count|Total|Total des appels réussis pour annuler un travail.|Aucune dimension|
|jobs<br>.cancelJob<br>.failure|Annulations de travaux en échec|Count|Total|Total des appels en échec pour annuler un travail.|Aucune dimension|
|jobs<br>.queryJobs<br>.success|Requêtes de travaux réussies|Count|Total|Total des appels réussis pour interroger les travaux.|Aucune dimension|
|jobs<br>.queryJobs<br>.failure|Requêtes de travaux en échec|Count|Total|Total des appels en échec pour interroger les travaux.|Aucune dimension|
|jobs<br>.completed|Travaux terminés|Count|Total|Total des travaux terminés.|Aucune dimension|
|jobs<br>.failed|Travaux en échec|Count|Total|Total des travaux en échec.|Aucune dimension|
|d2c<br>.telemetry<br>.ingress<br>.sendThrottle|Nombre d’erreurs de limitation|Count|Total|Nombre d’erreurs de limitation causées par des limitations de débit d’appareil|Aucune dimension|
|dailyMessage<br>QuotaUsed|Nombre total de messages utilisés|Count|Moyenne|Nombre total de messages utilisés aujourd’hui. Il s’agit d’une valeur cumulative qui est réinitialisée sur zéro à 00h00 UTC chaque jour.|Aucune dimension|
|deviceDataUsage|Utilisation totale des données d’appareil|Octets|Total|Nombre d’octets transférés vers et depuis tous les appareils connectés à IotHub|Aucune dimension|
|totalDeviceCount|Nombre total d’appareils (préversion)|Count|Moyenne|Nombre d’appareils enregistrés sur votre hub IoT|Aucune dimension|
|connected<br>DeviceCount|Appareils connectés (préversion)|Count|Moyenne|Nombre d’appareils connectés à votre hub IoT|Aucune dimension|
|configurations|Métriques de configuration|Count|Total|Métriques pour les opérations de configuration|Aucune dimension|

## <a name="next-steps"></a>Étapes suivantes

Les métriques IoT Hub n’étant plus un secret pour vous, suivez le lien ci-après pour en savoir plus sur la gestion d’Azure IoT Hub :

* [Surveillance des opérations](iot-hub-operations-monitoring.md)

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Guide du développeur d’IoT Hub](iot-hub-devguide.md)

* [Déploiement d’une IA sur des appareils de périmètre avec Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
