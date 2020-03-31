---
title: Sources d’événements Azure Event Grid
description: Abonnements Azure, Container Registry, rubriques personnalisées, Event Hubs, IoT Hub, Key Vault, Media Services, groupes de ressources, Service Bus, Stockage, Maps, App Configuration, SignalR, Machine Learning.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: spelluru
ms.openlocfilehash: d4a426ea1432d0266b7ae9344afefe8ddac1d030
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79231321"
---
# <a name="event-sources-in-azure-event-grid"></a>Sources d’événements dans Azure Event Grid

La source d’un événement désigne l’endroit où l’événement se produit. Plusieurs services Azure sont automatiquement configurés pour envoyer des événements. Vous pouvez également créer des applications personnalisées qui envoient des événements. Les applications personnalisées n’ont pas besoin d’être hébergées dans Azure pour utiliser Event Grid afin de distribuer les événements.

Cet article fournit des liens vers du contenu pour chaque source d’événement.

## <a name="azure-subscriptions"></a>Abonnements Azure

S’abonner aux événements Abonnements Azure pour répondre aux modifications apportées aux ressources dans un abonnement Azure.

|Intitulé |Description  |
|---------|---------|
| [Tutoriel : Intégrer Azure Automation à Event Grid et Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Créez une machine virtuelle, qui envoie un événement. L’événement déclenche un runbook Automation qui balise la machine virtuelle et déclenche un message qui est envoyé à un canal Microsoft Teams. |
| [Guide pratique pour s’abonner aux événements via le portail](subscribe-through-portal.md) | Utilisez le portail pour vous abonner aux événements d’un abonnement Azure. |
| [Azure CLI : S’abonner aux événements d’un abonnement Azure](./scripts/event-grid-cli-azure-subscription.md) |Exemple de script qui crée un abonnement Event Grid à un abonnement Azure, et envoie les événements à un webhook. |
| [PowerShell : S’abonner aux événements d’un abonnement Azure](./scripts/event-grid-powershell-azure-subscription.md)| Exemple de script qui crée un abonnement Event Grid à un abonnement Azure, et envoie les événements à un webhook. |
| [Schéma d’événement](event-schema-subscriptions.md) | Affiche les champs dans les événements d’abonnement Azure. |

## <a name="container-registry"></a>Container Registry

Abonnez-vous aux événements du registre de conteneurs pour répondre aux modifications des images.

|Intitulé |Description  |
|---------|---------|
| [Démarrage rapide : Envoyer des événements de registre de conteneurs](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Montre comment utiliser Azure CLI pour envoyer des événements de registre de conteneurs. |
| [Schéma d’événement](event-schema-container-registry.md) | Affiche les champs des événements du registre de conteneurs. |

## <a name="custom-topics"></a>Rubriques personnalisées

S’abonner à des rubriques personnalisées pour répondre aux événements d’application.

|Intitulé  |Description  |
|---------|---------|
| [Démarrage rapide : Créer et acheminer des événements personnalisés avec Azure CLI](custom-event-quickstart.md) | Montre comment utiliser Azure CLI pour envoyer des événements personnalisés. |
| [Démarrage rapide : Créer et acheminer des événements personnalisés avec PowerShell](custom-event-quickstart-powershell.md) | Montre comment utiliser Azure PowerShell pour envoyer des événements personnalisés. |
| [Démarrage rapide : Créer et acheminer des événements personnalisés avec le portail Azure](custom-event-quickstart-portal.md) | Montre comment utiliser le portail pour envoyer des événements personnalisés. |
| [Démarrage rapide : Acheminer des événements personnalisés vers le stockage File d’attente Azure](custom-event-to-queue-storage.md) | Décrit comment envoyer des événements personnalisés à un stockage File d’attente. |
| [Guide pratique pour publier dans une rubrique personnalisée](post-to-custom-topic.md) | Montre comment publier un événement dans une rubrique personnalisée. |
| [Azure CLI : Créer une rubrique personnalisée Event Grid](./scripts/event-grid-cli-create-custom-topic.md)|Exemple de script qui crée une rubrique personnalisée. Le script récupère le point de terminaison et une clé.|
| [Azure CLI : S’abonner aux événements d’une rubrique personnalisée](./scripts/event-grid-cli-subscribe-custom-topic.md)|Exemple de script qui crée un abonnement pour une rubrique personnalisée. Il envoie les événements vers un webhook.|
| [PowerShell : Créer une rubrique personnalisée Event Grid](./scripts/event-grid-powershell-create-custom-topic.md)|Exemple de script qui crée une rubrique personnalisée. Le script récupère le point de terminaison et une clé.|
| [PowerShell : S’abonner aux événements d’une rubrique personnalisée](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Exemple de script qui crée un abonnement pour une rubrique personnalisée. Il envoie les événements vers un webhook.|
| [Modèle Resource Manager : rubrique personnalisée et point de terminaison webhook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Modèle Resource Manager qui crée une rubrique personnalisée et un abonnement pour celle-ci. Il envoie les événements vers un webhook. |
|
| [Modèle Resource Manager : rubrique personnalisée et point de terminaison Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Modèle Resource Manager qui crée un abonnement pour une rubrique personnalisée. Il envoie les événements vers Azure Event Hubs. |
| [Schéma d’événement](event-schema.md) | Affiche les champs dans les événements personnalisés. |

## <a name="event-hubs"></a>Event Hubs

S’abonner aux événements Event Hubs pour répondre aux événements de fichier Capture. Event Hubs peut servir de source d’événement ou de gestionnaire d’événements. Les articles suivants montrent comment utiliser Event Hubs comme source.

|Intitulé  |Description  |
|---------|---------|
| [Tutoriel : Diffuser en continu des Big Data dans un entrepôt de données](event-grid-event-hubs-integration.md) | Quand Event Hubs crée un fichier Capture, Event Grid envoie un événement à une application de fonction. L’application récupère le fichier Capture et migre les données vers un entrepôt de données. |
| [Schéma d’événement](event-schema-event-hubs.md) | Affiche les champs dans les événements Event Hubs. |

Pour obtenir des exemples d’Event Hubs utilisé comme gestionnaire, consultez [Gestionnaire Event Hubs](event-handlers.md#event-hubs).

## <a name="iot-hub"></a>IoT Hub

Abonnez-vous aux événements IoT Hub pour répondre aux événements de création, de suppression, de connexion et de déconnexion et de télémétrie d’appareil.

|Intitulé  |Description  |
|---------|---------|
| [Envoyer des notifications par e-mail sur des événements Azure IoT Hub à l’aide de Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Une application logique envoie un e-mail de notification chaque fois qu’un appareil est ajouté à votre hub IoT. |
| [Réagir aux événements IoT Hub en utilisant Event Grid pour déclencher des actions](../iot-hub/iot-hub-event-grid.md) | Vue d’ensemble de l’intégration de hubs IoT à Event Grid. |
| [Schéma d’événement](event-schema-iot-hub.md) | Affiche les champs dans les événements IoT Hub. |
| [Commander des événements d’état de la connexion et de la déconnexion d’appareils](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Montre comment commander des événements d’état de la connexion d’appareils. |

## <a name="key-vault-preview"></a>Key Vault (préversion)

L’intégration de Key Vault avec Event Grid est actuellement en préversion. 

Abonnez-vous aux événements Key Vault pour recevoir une notification lorsqu’un secret est sur le point ou en train d’expirer, ou qu’une nouvelle version est disponible. 

|Intitulé  |Description  |
|---------|---------|
| [Monitorage d’événements Key Vault avec Azure Event Grid](../key-vault/event-grid-overview.md) | Vue d’ensemble de l’intégration de Key Vault avec Event Grid. |
| [Tutoriel : Créer des événements Key Vault avec Event Grid et en effectuer le monitorage](../key-vault/event-grid-tutorial.md) | Découvrez comment configurer des notifications Event Grid pour Key Vault. |
| [Schéma d’événement](event-schema-key-vault.md) | Affiche les champs des événements Key Vault. |

## <a name="media-services"></a>Media Services

S’abonner aux événements Media Services pour répondre aux événements d’état de travail.

|Intitulé  |Description  |
|---------|---------|
| [Vue d’ensemble : Réaction aux événements Media Services](../media-services/latest/reacting-to-media-services-events.md) | Vue d’ensemble de l’intégration de Media Services à Event Grid. |
| [Tutoriel : Acheminer des événements Azure Media Services vers un point de terminaison web personnalisé à l’aide de CLI](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Montre comment envoyer des événements à partir de Media Services. |
| [Schéma d’événement](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Affiche les champs dans les événements Media Services. |

## <a name="resource-groups"></a>Groupes de ressources

S’abonner aux événements de groupe de ressources pour répondre aux modifications apportées aux ressources dans un groupe de ressources.

|Intitulé  |Description  |
|---------|---------|
| [Tutoriel : Surveiller les modifications d’une machine virtuelle avec Azure Event Grid et Azure Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Une application logique surveille les modifications apportées à une machine virtuelle et envoie des e-mails à ce sujet. |
| [Azure CLI : S’abonner aux événements d’un groupe de ressources](./scripts/event-grid-cli-resource-group.md)| Exemple de script qui permet de s’abonner aux événements d’un groupe de ressources. Il envoie les événements vers un webhook. |
| [Azure CLI : S’abonner aux événements d’un groupe de ressources et filtrer pour trouver une ressource](./scripts/event-grid-cli-resource-group-filter.md) | Exemple de script pour s’abonner aux événements d’un groupe de ressources et les filtrer pour trouver une ressource. |
| [PowerShell : S’abonner aux événements d’un groupe de ressources](./scripts/event-grid-powershell-resource-group.md) | Exemple de script qui permet de s’abonner aux événements d’un groupe de ressources. Il envoie les événements vers un webhook. |
| [PowerShell : S’abonner aux événements d’un groupe de ressources et filtrer pour trouver une ressource](./scripts/event-grid-powershell-resource-group-filter.md) | Exemple de script pour s’abonner aux événements d’un groupe de ressources et les filtrer pour trouver une ressource. |
| [Modèle Resource Manager : abonnement à des ressources](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) | Permet de s’abonner à des événements pour une inscription Azure ou un groupe de ressources. Il envoie les événements vers un webhook. |
| [Schéma d’événement](event-schema-resource-groups.md) | Affiche les champs dans les événements de groupe de ressources. |

## <a name="service-bus"></a>Service Bus

S’abonner aux événements Service Bus pour répondre aux messages sans un écouteur actif.

|Intitulé  |Description  |
|---------|---------|
| [Tutoriel : Exemples d’intégration d’Azure Service Bus et d’Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid envoie des messages à partir de la rubrique Service Bus à l’application de fonction et à l’application logique. |
| [Vue d’ensemble : Intégration d’Azure Service Bus à Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Vue d’ensemble de l’intégration de Service Bus à Event Grid. |
| [Schéma d’événement](event-schema-service-bus.md) | Affiche les champs dans les événements Service Bus. |

## <a name="storage"></a>Stockage

S’abonner aux événements Stockage Blob pour répondre aux événements de création et de suppression d’objets blob.

>[!NOTE]
> Seuls les comptes de stockage de type **StorageV2 (v2 universel)** et **BlobStorage** prennent en charge l’intégration d’événements. Le type **Stockage (v1 universel)** ne prend *pas* en charge l’intégration à Event Grid.

|Intitulé  |Description  |
|---------|---------|
| [Démarrage rapide : Acheminer des événements de stockage Blob vers un point de terminaison web avec Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Montre comment utiliser Azure CLI pour envoyer des événements de stockage d’objets blob à un webhook. |
| [Démarrage rapide : Acheminer des événements de stockage Blob vers un point de terminaison web avec PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Montre comment utiliser Azure PowerShell pour envoyer des événements de stockage d’objets blob à un webhook. |
| [Démarrage rapide : Créer et acheminer des événements de stockage d’objets blob avec le portail Azure](blob-event-quickstart-portal.md) | Montre comment utiliser le portail pour envoyer des événements de stockage d’objets blob à un webhook. |
| [Azure CLI : S’abonner aux événements d’un compte de stockage Blob](./scripts/event-grid-cli-blob.md) | Exemple de script pour s’abonner aux événements d’un compte de stockage Blob. Il envoie l’événement à un webhook. |
| [PowerShell : S’abonner aux événements d’un compte de stockage Blob](./scripts/event-grid-powershell-blob.md) | Exemple de script pour s’abonner aux événements d’un compte de stockage Blob. Il envoie l’événement à un webhook. |
| [Modèle Resource Manager : Créer un abonnement et un stockage d’objets blob](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Déploie un compte de stockage blob Azure et s’abonne aux événements pour ce compte de stockage. Il envoie les événements vers un webhook. |
| [Vue d’ensemble : Réaction aux événements de Stockage Blob](../storage/blobs/storage-blob-event-overview.md) | Vue d’ensemble de l’intégration du stockage Blob à Event Grid. |
| [Schéma d’événement](event-schema-blob-storage.md) | Affiche les champs dans les événements de Stockage Blob. |

## <a name="maps"></a>Cartes
Abonnez-vous aux événements Azure Maps pour répondre aux événements de limite géographique. Par exemple, une application peut envoyer une notification par e-mail chaque fois qu’un appareil franchit une limite géographique.

|Intitulé  |Description  |
|---------|---------|
| [Réagir aux événements Azure Maps à l’aide d’Event Grid](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Vue d’ensemble de l’intégration d’Azure Maps à Event Grid. |
| [Tutoriel : Définir une limite géographique](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ce tutoriel vous montre les étapes de base qui sont nécessaires à la configuration d’une limite géographique à l’aide d’Azure Maps. Vous utilisez Azure Event Grid pour diffuser en continu les résultats de la limite géographique et pour configurer une notification en fonction de ces résultats. |
| [Schéma d’événement](event-schema-azure-maps.md) | Affiche les champs dans les événements Azure Maps. |

## <a name="app-configuration"></a>Configuration d’application
Abonnez-vous aux événements Azure App Configuration pour répondre aux événements de modification clé-valeur.

|Intitulé | Description |
|---------|---------|
| [Réagir aux événements Azure App Configuration à l’aide d’Event Grid](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Vue d’ensemble de l’intégration d’Azure App Configuration avec Event Grid. |
| [Démarrage rapide : Router des événements Azure App Configuration vers un point de terminaison web personnalisé avec Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Montre comment utiliser Azure CLI pour envoyer des événements Azure App Configuration à un webhook. |
| [Schéma d’événement](event-schema-app-configuration.md) | Montre les champs dans les événements Azure App Configuration. |

## <a name="azure-signalr"></a>Azure SignalR
Abonnez-vous aux événements d’Azure SignalR Service pour répondre aux événements de connexion client.

|Intitulé | Description |
|---------|---------|
| [Réagir aux événements Azure SignalR Service à l’aide d’Event Grid](../azure-signalr/signalr-concept-event-grid-integration.md) | Vue d’ensemble de l’intégration d’Azure SignalR Service à Event Grid. |
| [Comment envoyer des événements Azure SignalR Service à Event Grid](../azure-signalr/signalr-howto-event-grid-integration.md) | Montre comment envoyer des événements Azure SignalR Service à une application via Event Grid. |
| [Schéma d’événement](event-schema-azure-signalr.md) | Affiche les champs dans les événements de Azure SignalR Service. |

## <a name="azure-machine-learning"></a>Azure Machine Learning

Abonnez-vous aux événements de l’espace de travail Azure Machine Learning pour répondre à l’inscription de modèles.

| Intitulé | Description |
| ----- | ----- |
| [Consommer des événements Azure Machine Learning](../machine-learning/concept-event-grid-integration.md) | Vue d’ensemble de l’intégration d’Azure Machine Learning avec Event Grid. |
| [Schéma des événements Azure Event Grid pour Azure Machine Learning](event-schema-machine-learning.md) | Affiche les champs des événements Azure Machine Learning. |

## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Event Grid, consultez [À propos d’Event Grid](overview.md).
* Pour une prise en main rapide d’Event Grid, consultez [Créer et acheminer des événements personnalisés avec Azure Event Grid](custom-event-quickstart.md).
