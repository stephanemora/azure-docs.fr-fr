---
title: Performances et mise à l’échelle dans Fonctions durables - Azure
description: Introduction à l’extension Fonctions durables pour Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: azfuncdf
ms.openlocfilehash: 53f561283d4d07d58bd03b59a24a30d8010caaf0
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933291"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Performances et mise à l’échelle dans Fonctions durables (Azure Functions)

Pour optimiser les performances et l’évolutivité, il est important de comprendre les caractéristiques uniques de mise à l’échelle de [Fonctions durables](durable-functions-overview.md).

Pour comprendre le comportement lié à la mise à l’échelle, vous devez également comprendre certaines informations du fournisseur de stockage Azure sous-jacent.

## <a name="history-table"></a>Table d’historique

La table d’**historique** est une table de stockage Azure qui contient les événements d’historique de toutes les instances d’orchestration au sein d’un hub de tâches. Le nom de la table apparaît sous la forme *TaskHubName*History. Au fur et à mesure que des instances sont exécutées, de nouvelles lignes sont ajoutées à cette table. La clé de partition de la table provient de l’ID d’instance de l’orchestration. Un identifiant d’instance est aléatoire dans la plupart des cas, ce qui garantit une distribution optimale des partitions internes dans le stockage Azure.

Lorsqu’une instance d’orchestration doit s’exécuter, les lignes appropriées de la table d’historique sont chargées en mémoire. Ces *événements d’historique* sont ensuite relus dans le code de fonction d’orchestrateur pour revenir à l’état contrôlé précédemment. L’utilisation de l’historique d’exécution permettant de régénérer l’état de cette façon est influencée par le [modèle d’approvisionnement en événements](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Table d’instances

La table d’**instances** est une autre table de stockage Azure qui contient les états de toutes les instances d’orchestration au sein d’un hub de tâches. Au fur et à mesure que des instances sont créées, des lignes sont ajoutées à cette table. La clé de partition de cette table est l’ID d’instance d’orchestration, alors que la clé de ligne est une constante fixe. Il y a une ligne par instance d’orchestration.

Cette table est utilisée pour répondre aux demandes de requête d’instance provenant des API [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_System_String_) (.NET) et `getStatus` (JavaScript) et de l’[API HTTP de requête d’état](durable-functions-http-api.md#get-instance-status). Sa cohérence avec le contenu de la table d’**historique** mentionnée précédemment est conservée. L’utilisation d’une table de stockage Azure distincte pour satisfaire les opérations de requête d’instance de cette façon est influencée par le [modèle de séparation des responsabilités en matière de commande et de requête (CQRS)](https://docs.microsoft.com/azure/architecture/patterns/cqrs).

## <a name="internal-queue-triggers"></a>Déclencheurs de file d’attente interne

Les fonctions d’orchestrateur et les fonctions d’activité sont déclenchées par des files d’attente internes dans le hub de tâches de l’application de fonction. Cette utilisation des files d’attente apporte des garanties de livraison de messages « au moins une fois » fiables. Il existe deux types de file d’attente dans Fonctions durables : la **file d’attente de contrôle** et la **file d’attente des éléments de travail**.

### <a name="the-work-item-queue"></a>File d’attente des éléments de travail

Il existe une file d’attente des éléments de travail par hub de tâches dans Fonctions durables. Il s’agit d’une file d’attente de base qui se comporte comme toute autre file d’attente `queueTrigger` dans Azure Functions. Cette file d’attente permet de déclencher des *fonctions d’activité* sans état en enlevant un message de la file d’attente à la fois. Chacun de ces messages contient des entrées de fonction d’activité et des métadonnées supplémentaires, par exemple la fonction à exécuter. Quand une application Fonctions durables est étendue à plusieurs machines virtuelles, ces dernières rivalisent entre elles pour acquérir le travail de la file d’attente des éléments de travail.

### <a name="control-queues"></a>File(s) d’attente de contrôle

Il existe plusieurs *files d’attente de contrôle* par hub de tâches dans Fonctions durables. Une *file d’attente de contrôle* est plus sophistiquée et complexe que la file d’attente des éléments de travail. Les files d’attente de contrôle servent à déclencher les fonctions d’orchestrateur avec état. Étant donné que les instances de fonction d’orchestrateur sont des singletons avec état, il est impossible d’utiliser un modèle de consommateur concurrent pour distribuer la charge sur les machines virtuelles. À la place, la charge des messages d’orchestrateur est équilibrée sur les files d’attente de contrôle. Vous trouverez plus d’informations sur ce comportement dans les sections suivantes.

Les files d’attente de contrôle contiennent différents types de message couvrant le cycle de vie de l’orchestration, tels que des [messages de contrôle d’orchestrateurs](durable-functions-instance-management.md), des messages de *réponse* de fonctions d’activité et des messages de minuteurs. Au maximum, 32 messages seront enlevés d’une file d’attente de contrôle lors d’une seule interrogation. Ces messages contiennent des données de charge utile ainsi que des métadonnées, et notamment l’instance d’orchestration de destination. Si plusieurs messages enlevés de la file d’attente sont prévus pour la même instance d’orchestration, ils seront traités en tant que lot.

### <a name="queue-polling"></a>Interrogation de file d'attente

L’extension Tâche durable implémente un algorithme d’interruption exponentiel et aléatoire pour réduire l’effet de l’interrogation de file d’attente inactive sur les coûts de transactions de stockage. Quand un message est détecté, le runtime vérifie s’il existe un autre message ; si aucun message n’est détecté, il attend quelques secondes avant de réessayer. Après plusieurs échecs de tentatives d’obtention d’un message de file d’attente, le temps d’attente continue à augmenter jusqu’à ce qu’il atteigne le délai d’attente maximal par défaut (30 secondes).

Le délai maximal d’interrogation est configurable via la propriété `maxQueuePollingInterval` dans le [fichier host.json](../functions-host-json.md#durabletask). Une valeur plus élevée peut entraîner une plus grande latence lors du traitement des messages. Les latences plus élevées ne devraient survenir qu'après des périodes d'inactivité. Une valeur plus faible peut entraîner une augmentation des coûts de stockage en raison d’un nombre plus important de transactions de stockage.

> [!NOTE]
> Lorsqu’il est exécuté dans les plans Consommation et Premium d’Azure Functions, le [contrôleur de mise à l'échelle Azure Functions](../functions-scale.md#how-the-consumption-and-premium-plans-work) interrogera chaque contrôle et chaque file d'attente des éléments de travail toutes les 10 secondes. Cette interrogation supplémentaire est nécessaire pour déterminer quand activer les instances d'application de fonction et pour prendre des décisions de mise à l'échelle. Au moment d’écrire ces lignes, cet intervalle de 10 secondes est constant et ne peut pas être configuré.

## <a name="storage-account-selection"></a>Sélection du compte de stockage

Les files d’attente, les tables et les objets blob utilisés par Durable Functions sont créés dans un compte de stockage Azure configuré. Le compte à utiliser peut être spécifié à l’aide du paramètre `durableTask/azureStorageConnectionStringName` dans le fichier **host.json**.

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

S’il n’est pas spécifié, le compte de stockage `AzureWebJobsStorage` par défaut est utilisé. Pour les charges de travail sensibles aux performances, il est toutefois recommandé de configurer un compte de stockage non défini par défaut. Fonctions durables utilise beaucoup le stockage Azure, et l’utilisation d’un compte de stockage dédié distingue l’usage du stockage Fonctions durables et l’usage interne par l’hôte Azure Functions.

## <a name="orchestrator-scale-out"></a>Mise à l’échelle de l’orchestrateur

Les fonctions d’activité sont sans état et automatiquement mises à l’échelle par l’ajout de machines virtuelles. Les fonctions d’orchestrateur sont, quant à elles, *partitionnées* sur une ou plusieurs files d’attente de contrôle. Le nombre de files d’attente de contrôle est défini dans le fichier **host.json**. L’exemple suivant d’extrait de code host.json définit la propriété `durableTask/partitionCount` sur `3`.

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "partitionCount": 3
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

Un hub de tâches peut être configuré avec 1 à 16 partitions. Si ce paramètre n’est pas spécifié, le nombre de partitions par défaut s’élève à **4**.

Durant la mise à l’échelle sur plusieurs instances de l’hôte de fonction (généralement sur différentes machines virtuelles), chaque instance acquiert un verrou sur l’une des files d’attente de contrôle. Ces verrous sont implémentés en interne comme des baux de stockage blob et ils garantissent qu’une instance d’orchestration s’exécute uniquement sur une seule instance d’hôte à la fois. Si un hub de tâches est configuré avec trois files d’attente de contrôle, la charge des instances d’orchestration peut être équilibrée sur trois machines virtuelles. Il est possible d’ajouter des machines virtuelles supplémentaires pour augmenter la capacité d’exécution de la fonction d’activité.

Le diagramme suivant illustre la façon dont l’hôte Azure Functions interagit avec les entités de stockage dans un environnement mis à l’échelle.

![Diagramme de mise à l’échelle](./media/durable-functions-perf-and-scale/scale-diagram.png)

Comme indiqué dans le diagramme précédent, toutes les machines virtuelles sont en concurrence pour les messages de la file d’attente des éléments de travail. Toutefois, seules trois machines virtuelles peuvent acquérir les messages des files d’attente de contrôle, et chacune d’elles verrouille une file d’attente de contrôle.

Les instances d’orchestration sont réparties sur toutes les instances de file d’attente de contrôle. La distribution s’effectue par hachage de l’ID d’instance de l’orchestration. Les ID d’instance par défaut sont des GUID aléatoires, ce qui garantit la bonne répartition des instances sur toutes les files d’attente de contrôle.

En règle générale, les fonctions d’orchestrateur sont conçues pour être légères et elles ne requièrent pas une grande puissance de calcul. Il n’est donc pas nécessaire de créer un grand nombre de partitions de file d’attente de contrôle pour obtenir un haut débit. Le travail lourd doit être principalement effectué dans les fonctions d’activité sans état, qui peuvent être mises à l’échelle à l’infini.

## <a name="auto-scale"></a>Mise à l’échelle automatique

Comme avec toutes les fonctions Azure exécutées dans le plan Consommation, Fonctions durables prend en charge la mise à l’échelle automatique par le biais du [contrôleur de mise à l’échelle Azure Functions](../functions-scale.md#runtime-scaling). Le contrôleur de mise à l’échelle surveille la latence de toutes les files d’attente en émettant régulièrement des commandes _peek_. Selon la latence des messages parcourus, le contrôleur de mise à l’échelle décide d’ajouter ou de supprimer des machines virtuelles.

Si le contrôleur de mise à l’échelle détermine que la latence des messages de file d’attente de contrôle est trop élevée, il ajoute des instances de machine virtuelle jusqu’à ce que la latence des messages diminue à un niveau acceptable ou qu’il atteigne le nombre de partitions de file d’attente de contrôle. De même, le contrôleur de mise à l’échelle ajoute continuellement des instances de machine virtuelle si le temps de latence de file d’attente des éléments de travail est élevé, quel que soit le nombre de partitions.

## <a name="thread-usage"></a>Utilisation de threads

Les fonctions d’orchestrateur sont exécutées sur un seul thread pour garantir la possibilité d’une exécution déterministe entre les nombreuses relectures. Compte tenu de cette exécution monothread, il est important que les threads de fonction d’orchestrateur n’effectuent pas de tâches nécessitant une utilisation intensive du processeur, effectuent des opérations d’E/S ou se bloquent pour une raison quelconque. Tout travail susceptible de nécessiter un thread d’E/S, un blocage ou plusieurs threads doit être transféré vers les fonctions d’activité.

Les fonctions d’activité ont les mêmes comportements que les fonctions normales déclenchées par une file d’attente. Elles peuvent effectuer en toute sécurité des opérations d’E/S ou des opérations gourmandes en ressources de processeur et utiliser plusieurs threads. Étant donné que les déclencheurs d’activité sont sans état, ils peuvent librement être mis à l’échelle sur un nombre illimité de machines virtuelles.

## <a name="concurrency-throttles"></a>Limitations de la concurrence

Azure Functions prend en charge l’exécution simultanée de plusieurs fonctions dans une seule instance d’application. Cette exécution simultanée contribue à augmenter le parallélisme et réduit le nombre de démarrages à froid auxquels une application classique est confrontée au fil du temps. Toutefois, une concurrence élevée peut entraîner une utilisation importante de la mémoire par machine virtuelle. En fonction des besoins de l’application de fonction, il peut être nécessaire de limiter la concurrence par instance afin d’éviter le risque de manquer de mémoire dans les situations de charge importante.

Les deux limites de concurrence de la fonction d’activité et de la fonction d’orchestrateur peuvent être configurées dans le fichier **host.json**. Les paramètres appropriés sont respectivement `durableTask/maxConcurrentActivityFunctions` et `durableTask/maxConcurrentOrchestratorFunctions`.

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "maxConcurrentActivityFunctions": 10,
      "maxConcurrentOrchestratorFunctions": 10
    }
  }
}
```

Dans l’exemple précédent, un maximum de 10 fonctions d’orchestrateur et 10 fonctions d’activité peut s’exécuter simultanément sur une seule machine virtuelle. S’il n’est pas spécifié, le nombre d’exécutions simultanées de fonction d’activité et de fonction d’orchestrateur est limité à 10 fois le nombre de cœurs sur la machine virtuelle.

> [!NOTE]
> Ces paramètres permettent de gérer l’utilisation de la mémoire et du processeur sur une seule machine virtuelle. Toutefois, lors de la répartition sur plusieurs machines virtuelles, chaque machine virtuelle a son propre ensemble de limites défini. Ces paramètres ne peuvent pas être utilisés pour contrôler la concurrence au niveau global.

## <a name="orchestrator-function-replay"></a>Relecture de la fonction d’orchestrateur

Comme mentionné précédemment, les fonctions d’orchestrateur sont relues à l’aide du contenu de la table d’**historique**. Par défaut, le code de fonction d’orchestrateur est relu chaque fois qu’un lot de messages est enlevé d’une file d’attente de contrôle.

Ce comportement de relecture agressif peut être estompé avec l’activation de **sessions étendues**. Lorsque des sessions étendues sont activées, les instances de fonction d’orchestrateur sont conservées en mémoire plus longtemps et les nouveaux messages peuvent être traités sans une relecture complète. Les sessions étendues sont activées par la définition de `durableTask/extendedSessionsEnabled` sur `true` dans le fichier **host.json**. Le paramètre `durableTask/extendedSessionIdleTimeoutInSeconds` permet de contrôler la durée pendant laquelle une session inactive sera conservée en mémoire :

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "extendedSessionsEnabled": true,
      "extendedSessionIdleTimeoutInSeconds": 30
    }
  }
}
```

Généralement, l’activation des sessions étendues entraîne la baisse des E/S sur le compte de stockage Azure et un meilleur débit global.

Toutefois, cette fonctionnalité présente l’inconvénient de conserver plus longtemps en mémoire les instances de fonction d’orchestrateur inactives. Il est important de tenir compte de deux incidences :

1. Augmentation globale de l’utilisation de la mémoire de l’application de fonction.
2. Diminution globale du débit s’il existe plusieurs exécutions de fonction d’orchestrateur de courte durée de vie et simultanées.

Par exemple, si `durableTask/extendedSessionIdleTimeoutInSeconds` est défini sur 30 secondes, un épisode de fonction d’orchestrateur de courte durée de vie qui s’exécute en moins de 1 seconde occupe toujours la mémoire pendant 30 secondes. Il est également comptabilisé dans le quota `durableTask/maxConcurrentOrchestratorFunctions` mentionné précédemment et empêche potentiellement l’exécution d’autres fonctions d’orchestrateur.

> [!NOTE]
> Ces paramètres doivent être utilisés uniquement après le développement et le test complets d’une fonction d’orchestrateur. Le comportement de relecture agressif par défaut permet de détecter les erreurs d’idempotence dans les fonctions d’orchestrateur au moment du développement.

## <a name="performance-targets"></a>Cibles de performance

Lors de la planification de l’utilisation de Fonctions durables pour une application de production, il est important de tenir compte très rapidement des exigences de performances dans le processus de planification. Cette section décrit certains scénarios d’utilisation de base et les débits maximaux attendus.

* **Exécution d’activité séquentielle** : ce scénario décrit une fonction d’orchestrateur qui exécute une série de fonctions d’activité, l’une après l’autre. Il ressemble beaucoup à l’exemple de [chaînage de fonctions](durable-functions-sequence.md).
* **Exécution d’activité parallèle** : ce scénario décrit une fonction d’orchestrateur qui exécute de nombreuses fonctions d’activité en parallèle à l’aide du modèle [fan-out/fan-in](durable-functions-cloud-backup.md).
* **Traitement de la réponse en parallèle** : ce scénario constitue la seconde moitié du modèle [fan-out/fan-in](durable-functions-cloud-backup.md). Il se concentre sur les performances du scénario fan-in. Contrairement au scénario fan-out, le scénario fan-in est réalisé par une instance de fonction d’orchestrateur unique et peut donc uniquement s’exécuter sur une seule machine virtuelle.
* **Traitement des événements externes** : ce scénario représente une instance unique de la fonction d’orchestrateur, qui attend les [événements externes](durable-functions-external-events.md), un par un.

> [!TIP]
> Contrairement aux opérations fan-out, les opérations fan-in sont limitées à une seule machine virtuelle. Si votre application utilise le modèle fan-out/fan-in et si vous vous préoccupez des performances de scénario fan-in, pensez à sous-diviser le scénario fan-out de fonction d’activité sur plusieurs [sous-orchestrations](durable-functions-sub-orchestrations.md).

Le tableau suivant présente les débits *maximaux* attendus pour les scénarios décrits précédemment. Le terme « instance » fait référence à l’instance unique d’une fonction d’orchestrateur en cours d’exécution sur une petite machine virtuelle ([A1](../../virtual-machines/windows/sizes-previous-gen.md#a-series)) unique dans Azure App Service. Dans tous les cas, il est supposé que les [sessions étendues](#orchestrator-function-replay) sont activées. Les résultats réels peuvent varier selon le travail de l’UC ou des E/S effectué par le code de fonction.

| Scénario | Débit maximal |
|-|-|
| Exécution d’activité séquentielle | 5 activités par seconde et par instance |
| Exécution d’activité parallèle (fan-out) | 100 activités par seconde et par instance |
| Traitement de réponse parallèle (fan-in) | 150 réponses par seconde et par instance |
| Traitement d’événements externes | 50 événements par seconde et par instance |

> [!NOTE]
> Ces chiffres concernent la version 1.4.0 (GA) de l’extension Fonctions durables. Ces données peuvent changer au fil du temps, avec l’évolution de la fonctionnalité et les diverses optimisations.

Si vous n’obtenez pas les débits que vous attendiez et si l’utilisation de l’UC et de la mémoire semble correcte, vérifiez [l’intégrité de votre compte de stockage](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). L’extension Fonctions durables peut placer une charge importante sur un compte de stockage Azure, et des charges suffisamment élevées peuvent entraîner la limitation du compte de stockage.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur la récupération d’urgence et la géo-distribution](durable-functions-disaster-recovery-geo-distribution.md)
