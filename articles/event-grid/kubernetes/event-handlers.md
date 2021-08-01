---
title: Azure Event Grid sur Kubernetes - Gestionnaires d’événements et destinations
description: Cet article décrit les différents types de gestionnaires d’événements et de destinations pris en charge par Event Grid sur Kubernetes.
author: jfggdl
ms.author: jafernan
ms.subservice: kubernetes
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: 838084204ed2c1979f618bb2bfe644d1f88cd51e
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110536496"
---
# <a name="event-handlers-destinations-in-event-grid-on-kubernetes"></a>Destinations des gestionnaires d’événements dans Event Grid sur Kubernetes
Un gestionnaire d’événements est un système qui expose un point de terminaison et qui est la destination d’événements envoyés par Event Grid. Un gestionnaire d’événements qui reçoit un événement agit sur celui-ci et utilise la charge utile de l’événement pour exécuter une logique, ce qui peut entraîner l’occurrence de nouveaux événements.

La façon de configurer Event Grid pour envoyer des événements à une destination se fait via la création d’un abonnement aux événements. Vous pouvez le faire via [Azure CLI](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create), le [SDK de gestion](../sdk-overview.md#management-sdks) ou en utilisant des appels HTTPS directs avec la version de [l’API 2020-10-15-preview](/rest/api/eventgrid/version2020-10-15-preview/eventsubscriptions/createorupdate).

En général, Event Grid sur Kubernetes peut envoyer des événements à n’importe quelle destination via des **Webhooks**. Les Webhooks sont des points de terminaison HTTP(S) exposés par un service ou une charge de travail à laquelle Event Grid a accès. Le Webhook peut être une charge de travail hébergée dans le même cluster, dans le même espace réseau, sur le cloud, localement ou à n’importe quel endroit accessible par Event Grid. 

[!INCLUDE [event-grid-preview-feature-note.md](../../../includes/event-grid-preview-feature-note.md)]

Via des Webhooks, Event Grid prend en charge les destinations suivantes **hébergées sur un cluster Kubernetes** :

* Azure App Service sur Kubernetes avec Azure Arc. 
* Azure Functions sur Kubernetes avec Azure Arc. 
* Azure Logic Apps sur Kubernetes avec Azure Arc.

En plus des Webhooks, Event Grid sur Kubernetes peut envoyer des événements aux destinations suivantes **hébergées sur Azure** :

- Azure Event Grid  **en utilisant des Webhooks**
- Azure Functions  **en utilisant des Webhooks uniquement**
- Azure Event Hubs en utilisant son ID de ressource Azure Resource Manager
- Des rubriques ou des files d’attente en utilisant son ID de ressource Azure Resource Manager
- File d’attente Stockage Azure en utilisant son ID de ressource Azure Resource Manager



## <a name="feature-parity"></a>Parité des fonctionnalités
Event Grid sur Kubernetes offre un bon niveau de parité des fonctionnalités avec la prise en charge par Azure Event Grid des abonnements aux événements. La liste suivante énumère les principales différences en matière de fonctionnalités des abonnements aux événements. Outre ces différences, vous pouvez utiliser l’[API REST version 2020-10-15-preview](/rest/api/eventgrid/version2020-10-15-preview/eventsubscriptions) d’Azure Event Grid comme référence lors de la gestion des abonnements aux événements sur Event Grid sur Kubernetes.

1. Utilisez l’[API REST version 2020-10-15-preview](/rest/api/eventgrid/version2020-10-15-preview/eventsubscriptions).
2. Le [déclencheur Azure Event Grid pour Azure Functions](../../azure-functions/functions-bindings-event-grid-trigger.md?tabs=csharp%2Cconsole) n’est pas pris en charge. Vous pouvez utiliser un type de destination de Webhook pour distribuer des événements à Azure Functions.
3. Il n’y a pas de prise en charge de l’[emplacement des lettres mortes](../manage-event-delivery.md#set-dead-letter-location). Cela signifie que vous ne pouvez pas utiliser ``properties.deadLetterDestination`` dans votre charge utile d’abonnement aux événements.
4. Les connexions hybrides d’Azure Relay en tant que destination ne sont pas encore prises en charge.
5. Seul le schéma CloudEvents est pris en charge. La valeur du schéma pris en charge est « [CloudEventSchemaV1_0](/rest/api/eventgrid/version2020-10-15-preview/eventsubscriptions/createorupdate#eventdeliveryschema) ». Le schéma CloudEvents est extensible et s’appuie sur des standards ouverts.  
6. Les étiquettes ([properties.labels](/rest/api/eventgrid/version2020-10-15-preview/eventsubscriptions/createorupdate#request-body)) ne sont pas applicables à Event Grid sur Kubernetes. Par conséquent, elles ne sont pas disponibles.
7. La [distribution avec identité des ressources](/rest/api/eventgrid/version2020-10-15-preview/eventsubscriptions/createorupdate#deliverywithresourceidentity) n’est pas prise en charge. Par conséquent, toutes les propriétés de l’[identité des abonnements aux événements](/rest/api/eventgrid/version2020-10-15-preview/eventsubscriptions/createorupdate#eventsubscriptionidentity) ne sont pas prises en charge.
8. La [validation de point de terminaison de destination](../webhook-event-delivery.md#endpoint-validation-with-event-grid-events) n’est pas encore prise en charge.

## <a name="event-filtering-in-event-subscriptions"></a>Filtrage des événements dans les abonnements aux événements
L’autre aspect important de la configuration d’un abonnement aux événements est de sélectionner les événements qui sont destinés à être distribués à une destination. Pour plus d’informations, consultez [Filtrage des événements](filter-events.md).

## <a name="sample-destination-configurations"></a>Exemples de configurations de destination

Voici quelques exemples de configurations de base en fonction de la destination prévue.

## <a name="webhook"></a>WebHook
Pour publier sur un point de terminaison WebHook, définissez `endpointType` sur `WebHook` et indiquez :

* **endpointUrl** : URL du point de terminaison du Webhook.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-endpoint>"
              }
            }
          }
        }
    ```

## <a name="azure-event-grid"></a>Azure Event Grid

Pour publier sur un point de terminaison cloud Azure Event Grid, définissez `endpointType` sur `WebHook` et indiquez :

* **endpointUrl** : URL de rubrique de la grille d’événements Azure dans le Cloud avec le paramètre de version de l’API défini sur **2018-01-01** et `aeg-sas-key` défini sur la clé SAS encodée en URL. 

   ```json
    {
        "properties": {
            "destination": {
                "endpointType": "WebHook",
                "properties": {
                    "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01&aeg-sas-key=urlencoded(sas-key-value)"
                }
            }
        }
    }
   ```

## <a name="event-hubs"></a>Event Hubs

Pour publier sur un hub d’événements, définissez `endpointType` sur `eventHub` et indiquez :

* **resourceId** : ID de ressource pour le hub d’événements spécifique.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "resourceId": "<Azure Resource ID of your event hub>"
              }
            }
          }
        }
    ```

## <a name="service-bus-queues"></a>Files d’attente Service Bus

Pour publier sur une file d’attente Service Bus, définissez `endpointType` sur `serviceBusQueue` et spécifiez :

* **resourceId** : ID de ressource pour la file d’attente Service Bus spécifique.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "resourceId": "<Azure Resource ID of your Service Bus queue>"
              }
            }
          }
        }
    ```

## <a name="service-bus-topics"></a>Rubriques Service Bus

Pour publier sur une rubrique Service Bus, définissez `endpointType` sur `serviceBusTopic` et spécifiez :

* **resourceId** : ID de ressource pour la rubrique Service Bus spécifique.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "resourceId": "<Azure Resource ID of your Service Bus topic>"
              }
            }
          }
        }
    ```

## <a name="storage-queues"></a>Files d’attente de stockage

Pour publier dans une file d’attente de stockage, définissez `endpointType` sur `storageQueue` et indiquez :

* **queueName** : nom de la file d’attente Stockage Azure sur laquelle vous publiez.
* **resourceID** : ID de ressource Azure du compte de stockage qui contient la file d’attente.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "resourceId": "<Azure Resource ID of your Storage account>"
              }
            }
          }
        }
    ```

## <a name="next-steps"></a>Étapes suivantes
* Ajoutez la [configuration du filtre](filter-events.md) à votre abonnement aux événements pour sélectionner les événements à distribuer. 
* Pour plus d’informations sur les schémas que prend en charge Event Grid sur Azure Arc pour Kubernetes, consultez [Event Grid sur Kubernetes - Schémas d’événements](event-schemas.md).