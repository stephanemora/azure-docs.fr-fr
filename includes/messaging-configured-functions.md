---
title: Fichier include
description: Fichier include
services: service-bus-messaging, event-hubs
author: spelluru
ms.service: service-bus-messaging, event-hubs
ms.topic: include
ms.date: 12/12/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 9bc641d680d927c44814f6814ebf6a6dde958c9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98900911"
---
Azure Functions permet de créer des tâches de réplication destinées uniquement à la configuration qui s’appuient sur un point d’entrée prédéfini. Les [exemples de réplication basée sur la configuration pour Azure Functions](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config) illustrent comment tirer parti de l’[assistance prégénérée](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) dans votre propre code ou comment éviter de manipuler du code et utiliser simplement la configuration.

## <a name="create-a-replication-task"></a>Créer une tâche de réplication
Pour créer une tâche de réplication de ce type, commencez par créer un nouveau dossier sous le dossier du projet. Le nom du nouveau dossier est celui de la fonction, par exemple `europeToAsia` ou `telemetry`. Le nom n’a pas de corrélation directe avec les entités de messagerie utilisées et sert uniquement à les identifier. Vous pouvez créer des dizaines de fonctions dans le même projet.

Ensuite, créez un fichier `function.json` dans le dossier. Le fichier configure la fonction. Commencez par le contenu suivant :

``` JSON
{
    "configurationSource": "config",
    "bindings" : [
        {
            "direction": "in",
            "name": "input" 
        },
        {
            "direction": "out",
            "name": "output"
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": -1,
        "minimumInterval": "00:00:05",
        "maximumInterval": "00:05:00"
    },
    "disabled": false,
    "scriptFile": "../bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.*"
}
```

Dans ce fichier, vous devez effectuer trois étapes de configuration qui dépendent des entités que vous souhaitez connecter :

1. [Configurer la direction d’entrée](#configure-the-input-direction)
2. [Configurer la direction de sortie](#configure-the-output-direction)
3. [Configurer le point d’entrée](#configure-the-entry-point)

### <a name="configure-the-input-direction"></a>Configurer la direction d’entrée

#### <a name="event-hub-input"></a>Entrée d’Event Hub

Si vous souhaitez recevoir des événements d’un Event Hub, ajoutez des informations de configuration dans la section supérieure sous « bindings » qui définit :

* **type** : type « eventHubTrigger ».
* **connection** : nom de la valeur des paramètres de l’application pour la chaîne de connexion du Event Hub. 
* **eventHubName** : nom du Event Hub dans l’espace de noms identifié par la chaîne de connexion.
* **consumerGroup** : nom du groupe de consommateurs. Notez que le nom est placé entre signes de pourcentage et qu’il fait donc également référence à une valeur des paramètres de l’application.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "eventHubTrigger",
            "connection": "functionname-source-connection",
            "eventHubName": "eventHubA",
            "consumerGroup" : "%functionname-source-consumergroup%",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-queue-input"></a>Entrée de file d’attente Service Bus

Si vous souhaitez recevoir des événements d’une file d’attente Service Bus, ajoutez des informations de configuration dans la section supérieure sous « bindings » qui définit :

* **type** : type « serviceBusTrigger ».
* **connection** : nom de la valeur des paramètres de l’application pour la chaîne de connexion de Service Bus.
* **queueName** : nom de la file d’attente Service Bus dans l’espace de noms identifié par la chaîne de connexion.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "queueName": "queue-a",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-topic-input"></a>Entrée de rubrique Service Bus

Si vous souhaitez recevoir des événements d’une rubrique Service Bus, ajoutez des informations de configuration dans la section supérieure sous « bindings » qui définit :

* **type** : type « serviceBusTrigger ».
* **connection** : nom de la valeur des paramètres de l’application pour la chaîne de connexion de Service Bus. Cette valeur doit être `{FunctionName}-source-connection` si vous souhaitez utiliser les scripts fournis.
* **topicName** : nom de la rubrique Service Bus dans l’espace de noms identifié par la chaîne de connexion.
* **subscriptionName** : nom de l’abonnement Service Bus sur la rubrique donnée dans l’espace de noms identifié par la chaîne de connexion.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "topicName": "topic-x",
            "subscriptionName" : "sub-y",
            "name": "input" 
        }
    ...
```

### <a name="configure-the-output-direction"></a>Configurer la direction de sortie

#### <a name="event-hub-output"></a>Sortie d’Event Hub

Si vous souhaitez transférer des événements vers un Event Hub, ajoutez des informations de configuration dans la section inférieure sous « bindings » qui définit :

* **type** : type « eventHub ».
* **connection** : nom de la valeur des paramètres de l’application pour la chaîne de connexion du Event Hub. 
* **eventHubName** : nom du Event Hub dans l’espace de noms identifié par la chaîne de connexion.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "eventHub",
            "connection": "functionname-target-connection",
            "eventHubName": "eventHubB",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-queue-output"></a>Sortie de file d’attente Service Bus

Si vous souhaitez transférer des événements vers une file d’attente Service Bus, ajoutez des informations de configuration dans la section inférieure sous « bindings » qui définit :

* **type** : type « serviceBus ».
* **connection** : nom de la valeur des paramètres de l’application pour la chaîne de connexion de Service Bus. 
* **queueName** : nom de la file d’attente Service Bus dans l’espace de noms identifié par la chaîne de connexion.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "queueName": "queue-b",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-topic-output"></a>Sortie de rubrique Service Bus

Si vous souhaitez transférer des événements vers une rubrique Service Bus, ajoutez des informations de configuration dans la section inférieure sous « bindings » qui définit :

* **type** : type « serviceBus ».
* **connection** : nom de la valeur des paramètres de l’application pour la chaîne de connexion de Service Bus. 
* **topicName** : nom de la rubrique Service Bus dans l’espace de noms identifié par la chaîne de connexion.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "topicName": "topic-b",
            "name": "output" 
        }
    ...
```

### <a name="configure-the-entry-point"></a>Configurer le point d’entrée

La configuration du point d’entrée choisit l’une des tâches de réplication standard. Si vous modifiez le projet `Azure.Messaging.Replication`, vous pouvez également ajouter des tâches et y faire référence ici. Exemple :

```JSON
    ...
    "scriptFile": "../dotnet/bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub"
    ...
```

Le tableau suivant fournit les valeurs correctes pour les combinaisons de sources et de cibles :

| Source      | Cible      | Point d'entrée 
|-------------|-------------|------------------------------------------------------------------------
| Event Hub   | Event Hub   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| Event Hub   | Service Bus | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Service Bus | Event Hub   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Service Bus | Service Bus | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`

### <a name="retry-policy"></a>Stratégie de nouvelles tentatives

Reportez-vous à la [documentation d’Azure Functions sur les nouvelles tentatives](../articles/azure-functions/functions-bindings-error-pages.md) pour configurer la stratégie de nouvelles tentatives. Les paramètres de stratégie choisis dans tous les projets de ce référentiel configurent une stratégie de backoff exponentiel avec des intervalles avant nouvelle tentative de 5 secondes à 5 minutes, et un nombre infini de nouvelles tentatives pour éviter de perdre des données.

Pour Service Bus, consultez la section [Utilisation de la prise en charge des nouvelles tentatives en plus de la résilience de déclencheur](../articles/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience) pour comprendre l’interaction des déclencheurs et connaître le nombre maximal de remises défini pour la file d’attente.

### <a name="build-deploy-and-configure"></a>Générer, déployer et configurer

Bien que vous vous concentriez sur la configuration, les tâches nécessitent toujours la création d’une application déployable et la configuration des hôtes Azure Functions de telle sorte qu’ils disposent de toutes les informations requises pour se connecter aux points de terminaison donnés. 

Ceci est illustré, ainsi que les scripts réutilisables, dans les [exemples de réplication basée sur la configuration pour Azure Functions](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config).