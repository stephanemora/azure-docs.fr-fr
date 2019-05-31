---
title: Comparer Event Grid et le routage pour IoT Hub | Microsoft Docs
description: IoT Hub offre son propre service de routage de messages, mais il s’intègre également à Event Grid pour la publication d’événement. Comparez les deux fonctionnalités.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 0b17a87fa02c382ae19cca6e4abcfff2ec475450
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66252692"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Comparer le routage des messages et Event Grid pour IoT Hub

Azure IoT Hub vous permet de diffuser des données à partir de vos appareils connectés et d’intégrer ces données dans vos applications d’entreprise. IoT Hub propose deux méthodes permettant d’intégrer les événements IoT dans d’autres services Azure ou applications métier. Cet article décrit les deux fonctions qui offrent cette fonctionnalité. Vous pourrez ainsi choisir l’option qui convient le mieux à votre scénario.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[Routage des messages IoT Hub](iot-hub-devguide-messages-d2c.md)** : Cette fonctionnalité IoT Hub permet aux utilisateurs router les messages appareil-à-cloud vers les points de terminaison de service tels que les conteneurs de stockage Azure, Event Hubs, files d’attente Service Bus et rubriques Service Bus. Le routage fournit également une fonctionnalité de requête pour filtrer les données avant de les router vers les points de terminaison. En plus des données de télémétrie d’appareil, vous pouvez également envoyer des [événements non liés à la télémétrie](iot-hub-devguide-messages-d2c.md#non-telemetry-events) qui peuvent être utilisés pour déclencher des actions. 

**Intégration d’IoT Hub avec Event Grid**: Azure Event Grid est un service de routage d'événement complètement managé qui utilise le modèle publication-abonnement. IoT Hub et Event Grid collaborent afin [d’intégrer les événements IoT Hub dans les services Azure et non-Azure](iot-hub-event-grid.md), quasiment en temps réel. IoT Hub publie [événements d’appareil](iot-hub-event-grid.md#event-types), qui sont généralement disponible et publie désormais également des événements de télémétrie, qui est en version préliminaire publique.

## <a name="differences"></a>Différences

Le routage des messages et Event Grid permettent tous deux de configurer des alertes, mais il existe entre eux des différences clés. Pour plus dé détails, consultez le tableau suivant :

| Fonctionnalité | Routage des messages IoT Hub | Intégration d’IoT Hub avec Event Grid |
| ------- | --------------- | ---------- |
| **Événements et les messages de l’appareil** | Oui, le routage des messages utilisable pour les données de télémétrie et signaler les modifications de jumeau et événements de cycle de vie d’appareil. | Oui, Event Grid peut être utilisé pour les données de télémétrie, mais peut également signaler quand des appareils sont créés, supprimés, connectées et déconnectées d’IoT Hub |
| **Ordonnancement** | Oui, l’ordonnancement des événements est conservé.  | Non, l’ordonnancement des événements n’est pas garanti. | 
| **Filtering** | Filtrage élaboré opérant sur les propriétés de l’application de messagerie, les propriétés système des messages, le corps des messages, ainsi que les étiquettes et propriétés de jumeaux d’appareil. Pour obtenir des exemples, consultez [Syntaxe des requêtes de routage des messages](iot-hub-devguide-routing-query-syntax.md). | Filtrage basé sur le type d’événement, type d’objet et attributs dans chaque événement. Pour obtenir des exemples, consultez [comprendre les événements de filtrage dans les abonnements Event Grid](../event-grid/event-filtering.md). Lors de l’abonnement aux événements de télémétrie, vous pouvez appliquer des filtres supplémentaires sur les données de filtre sur les propriétés de message, la représentation de corps et de périphérique de message dans votre IoT Hub, avant la publication d’Event Grid. Consultez [comment filtrer les événements](../iot-hub/iot-hub-event-grid.md#filter-events). |
| **Points de terminaison** | <ul><li>Event Hubs</li> <li>un stockage Azure Blob</li> <li>File d’attente Service Bus</li> <li>Rubriques Service Bus</li></ul><br>Les références SKU IoT Hub payantes (S1, S2 et S3) sont limitées à 10 points de terminaison personnalisés. Vous pouvez créer 100 itinéraires par hub IoT. | <ul><li>Azure Functions</li> <li>Azure Automation</li> <li>Event Hubs</li> <li>Logic Apps</li> <li>Storage Blob</li> <li>Rubriques personnalisées</li> <li>Stockage File d’attente</li> <li>Microsoft Flow</li> <li>Services tiers par le biais de WebHooks</li></ul><br>500 points de terminaison par IoT Hub sont pris en charge. Pour obtenir la liste la plus récente des points de terminaison, consultez [Gestionnaires d’événements Event Grid](../event-grid/overview.md#event-handlers). |
| **Coût** | Le routage des messages n’est pas facturé séparément. Seule l’entrée de télémétrie dans IoT Hub est facturée. Par exemple, si vous avez un message acheminé vers trois points de terminaison différents, un seul message vous est facturé. | Aucun frais IoT Hub n’est facturé. Event Grid offre les 100 000 premières opérations par mois gratuitement et puis 0,60 $ par million d’opérations par la suite. |

## <a name="similarities"></a>Similitudes

Le routage des messages IoT Hub et Event Grid offrent aussi quelques similitudes, dont certaines sont détaillées dans le tableau suivant :

| Fonctionnalité | Routage des messages IoT Hub | Intégration d’IoT Hub avec Event Grid |
| ------- | --------------- | ---------- |
| **Taille de message maximale** | 256 Ko, appareil-à-cloud | 256 Ko, appareil-à-cloud |
| **Fiabilité** | Élevée : Remet chaque message au point de terminaison au moins une fois pour chaque itinéraire. Provoque l’expiration de tous les messages qui ne sont pas remis dans l’heure. | Élevée : Remet chaque message au webhook au moins une fois pour chaque abonnement. Provoque l’expiration de tous les événements qui ne sont pas remis dans les 24 heures. | 
| **Extensibilité** | Élevée : Optimisé pour la prise en charge des millions d’appareils connectés simultanément en envoyant des milliards de messages. | Élevée : Capable de 10 000 000 événements par seconde par région. |
| **Latence** | Faible : Quasiment en temps réel. | Faible : Quasiment en temps réel. |
| **Envoi à plusieurs points de terminaison** | Oui, envoi d’un message unique à plusieurs points de terminaison. | Oui, envoi d’un message unique à plusieurs points de terminaison.  
| **Sécurité** | Iot Hub fournit une identité par appareil et un contrôle d’accès révocable. Pour plus d’informations, consultez [Contrôle d’accès IoT Hub](iot-hub-devguide-security.md). | Event Grid fournit une validation à trois points : abonnements aux événements, publication d’événements et remise d’événements webhook. Pour en savoir plus, consultez la page [Sécurité et authentification pour Event Grid](../event-grid/security-authentication.md). |

## <a name="how-to-choose"></a>Choix

Le routage des messages IoT Hub et l’intégration d’IoT Hub avec Event Grid effectuent différentes actions pour parvenir à des résultats similaires. Tous deux prennent des informations à partir de votre solution IoT Hub et les transmettent afin que d’autres services puissent réagir. Comment choisir entre les deux ? Posez-vous les questions suivantes pour guider votre décision : 

* **Quel type de données envoyez-vous aux points de terminaison ?**

   Utilisez le routage des messages IoT Hub quand vous devez envoyer des données de télémétrie à d’autres services. Le routage des messages permet également l’interrogation des propriétés de l’application de messagerie, des propriétés système des messages, des corps de messages, des étiquettes et des propriétés de jumeaux d’appareil.

   L’intégration d’IoT Hub avec Event Grid fonctionne avec les événements qui se produisent dans le service IoT Hub. Ces événements IoT Hub incluent des données de télémétrie, appareil créé, supprimé, connecté et déconnecté. Lors de l’abonnement aux événements de télémétrie, vous pouvez appliquer des filtres supplémentaires sur les données de filtre sur les propriétés de message, la représentation de corps et de périphérique de message dans votre IoT Hub, avant la publication d’Event Grid. Consultez [comment filtrer les événements](../iot-hub/iot-hub-event-grid.md#filter-events).

* **Quels points de terminaison doivent recevoir ces informations ?**

   Routage des messages IoT Hub prend en charge le nombre limité de points de terminaison uniques et les types de point de terminaison, mais vous pouvez créer des connecteurs pour réacheminer les données et les événements à des points de terminaison supplémentaires. Pour obtenir une liste complète des points de terminaison pris en charge, consultez le tableau dans la section précédente. 

   L’intégration d’IoT Hub avec Event Grid prend en charge 500 points de terminaison par IoT Hub et un éventail plus large de types de point de terminaison. Naïvement s’intègre avec Azure Functions, Logic Apps, files d’attente de stockage et Service Bus, elle fonctionne également avec des webhooks pour étendre l’envoi de données en dehors de l’écosystème de services Azure et dans les applications métier tiers.

* **Est-il important que vos données arrivent dans l’ordre ?**

   Le routage des messages IoT Hub conserve l’ordre dans lequel les messages sont envoyés. Ainsi, l’ordre d’arrivée est le même.

   Event Grid ne garantit pas que les points de terminaison recevront les événements dans l’ordre dans lequel ils se sont produits. Pour les cas où ordre absolu des messages est important et/ou dans lesquels un consommateur doit un identificateur unique fiable pour les messages, nous recommandons à l’aide du routage des messages. 

## <a name="next-steps"></a>Étapes suivantes

* Découvrez plus en détail le [routage des messages IoT Hub](iot-hub-devguide-messages-d2c.md) et les [points de terminaison IoT Hub](iot-hub-devguide-endpoints.md).
* Apprenez-en plus sur [Azure Event Grid](../event-grid/overview.md).
* Pour découvrir comment créer des routes de messages, consultez le tutoriel [Traiter les messages appareil-à-cloud IoT Hub avec des routes](../iot-hub/tutorial-routing.md).
* Essayer l’intégration Event Grid [envoi de notifications de courrier électronique sur les événements d’Azure IoT Hub à l’aide de Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).