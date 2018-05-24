---
title: Sources d’événements Azure Event Grid
description: Décrit les sources d’événements pris en charge pour Azure Event Grid
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: tomfitz
ms.openlocfilehash: f9c3bcb6b92b43fe5b5bad72c99e6ce199c17448
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/18/2018
---
# <a name="event-sources-in-azure-event-grid"></a>Sources d’événements dans Azure Event Grid

La source d’un événement désigne l’endroit où l’événement se produit. Plusieurs services Azure sont automatiquement configurés pour envoyer des événements. Vous pouvez également créer des applications personnalisées qui envoient des événements. Les applications personnalisées n’ont pas besoin d’être hébergées dans Azure pour utiliser Event Grid afin de distribuer les événements.

Cet article fournit des liens vers du contenu pour chaque source d’événement.

## <a name="azure-subscriptions"></a>Abonnements Azure

S’abonner aux événements Abonnements Azure pour répondre aux modifications apportées aux ressources dans un abonnement Azure.

|Intitulé |Description  |
|---------|---------|
| [Intégrer Azure Automation à Event Grid et Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Créez une machine virtuelle, qui envoie un événement. L’événement déclenche un runbook Automation qui balise la machine virtuelle et déclenche un message qui est envoyé à un canal Microsoft Teams. |
| [Schéma d’événement](event-schema-subscriptions.md) | Affiche les champs dans les événements d’abonnement Azure. |

## <a name="custom-topics"></a>Rubriques personnalisées

S’abonner à des rubriques personnalisées pour répondre aux événements d’application.

|Intitulé  |Description  |
|---------|---------|
| [Créer et acheminer des événements personnalisés avec Azure CLI](custom-event-quickstart.md) | Montre comment utiliser Azure CLI pour envoyer des événements personnalisés. |
| [Créer et acheminer des événements personnalisés avec Azure PowerShell](custom-event-quickstart-powershell.md) | Montre comment utiliser Azure PowerShell pour envoyer des événements personnalisés. |
| [Créer et acheminer des événements personnalisés avec le portail Azure](custom-event-quickstart-portal.md) | Montre comment utiliser le portail pour envoyer des événements personnalisés. |
| [Publier dans une rubrique personnalisée](post-to-custom-topic.md) | Montre comment publier un événement dans une rubrique personnalisée. |
| [Acheminer des événements personnalisés vers le stockage File d’attente Azure](custom-event-to-queue-storage.md) | Décrit comment envoyer des événements personnalisés à un stockage File d’attente. |
| [Schéma d’événement](event-schema.md) | Affiche les champs dans les événements personnalisés. |

## <a name="event-hubs"></a>Event Hubs

S’abonner aux événements Event Hubs pour répondre aux événements de fichier Capture.

|Intitulé  |Description  |
|---------|---------|
| [Diffuser en continu des Big Data dans un entrepôt de données](event-grid-event-hubs-integration.md) | Quand Event Hubs crée un fichier Capture, Event Grid envoie un événement à une application de fonction. L’application récupère le fichier Capture et migre les données vers un entrepôt de données. |
| [Schéma d’événement](event-schema-event-hubs.md) | Affiche les champs dans les événements Event Hubs. |

## <a name="iot-hub"></a>IoT Hub

S’abonner aux événements IoT Hub pour répondre aux événements de création et de suppression d’appareil.

|Intitulé  |Description  |
|---------|---------|
| [Envoyer des notifications par e-mail sur des événements Azure IoT Hub à l’aide de Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Une application logique envoie un e-mail de notification chaque fois qu’un appareil est ajouté à votre hub IoT. |
| [Réagir aux événements IoT Hub en utilisant Event Grid pour déclencher des actions](../iot-hub/iot-hub-event-grid.md) | Vue d’ensemble de l’intégration de hubs IoT à Event Grid. |
| [Schéma d’événement](event-schema-iot-hub.md) | Affiche les champs dans les événements IoT Hub. |

## <a name="media-services"></a>Media Services

S’abonner aux événements Media Services pour répondre aux événements d’état de travail.

|Intitulé  |Description  |
|---------|---------|
| [Réaction aux événements Media Services](../media-services/latest/reacting-to-media-services-events.md) | Vue d’ensemble de l’intégration de Media Services à Event Grid. |
| [Acheminer des événements Azure Media Services vers un point de terminaison web personnalisé à l’aide de CLI](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Montre comment envoyer des événements à partir de Media Services. |
| [Schéma d’événement](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Affiche les champs dans les événements Media Services. |

## <a name="resource-groups"></a>Groupes de ressources

S’abonner aux événements de groupe de ressources pour répondre aux modifications apportées aux ressources dans un groupe de ressources.

|Intitulé  |Description  |
|---------|---------|
| [Surveiller les modifications d’une machine virtuelle avec Azure Event Grid et Azure Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Une application logique surveille les modifications apportées à une machine virtuelle et envoie des e-mails à ce sujet. |
| [Schéma d’événement](event-schema-resource-groups.md) | Affiche les champs dans les événements de groupe de ressources. |

## <a name="service-bus"></a>Service Bus

S’abonner aux événements Service Bus pour répondre aux messages sans un écouteur actif.

|Intitulé  |Description  |
|---------|---------|
| [Exemples d’intégration d’Azure Service Bus et d’Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid envoie des messages à partir de la rubrique Service Bus à l’application de fonction et à l’application logique. |
| [Vue d’ensemble de l’intégration d’Azure Service Bus et Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Vue d’ensemble de l’intégration de Service Bus à Event Grid. |
| [Schéma d’événement](event-schema-service-bus.md) | Affiche les champs dans les événements Service Bus. |

## <a name="storage"></a>Stockage

S’abonner aux événements Stockage Blob pour répondre aux événements de création et de suppression d’objets blob.

|Intitulé  |Description  |
|---------|---------|
| [Acheminer des événements de stockage Blob vers un point de terminaison web avec Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Montre comment utiliser Azure CLI pour envoyer des événements de stockage d’objets blob. |
| [Acheminer des événements de stockage Blob vers un point de terminaison web avec PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Montre comment utiliser Azure PowerShell pour envoyer des événements de stockage d’objets blob. |
| [Réaction aux événements de stockage Blob](../storage/blobs/storage-blob-event-overview.md) | Vue d’ensemble de l’intégration du stockage Blob à Event Grid. |
| [Schéma d’événement](event-schema-blob-storage.md) | Affiche les champs dans les événements de Stockage Blob. |

## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Event Grid, consultez [À propos d’Event Grid](overview.md).
* Pour une prise en main rapide d’Event Grid, consultez [Créer et acheminer des événements personnalisés avec Azure Event Grid](custom-event-quickstart.md).
