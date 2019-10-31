---
title: Journaux de diagnostic Azure Service Bus | Microsoft Docs
description: Découvrez comment configurer les journaux de diagnostic pour Service Bus dans Azure.
keywords: ''
documentationcenter: .net
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 41e0bdc1f04c9491ebe939f46b59ae4eb2bc7ab6
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72592467"
---
# <a name="enable-diagnostic-logs-for-service-bus"></a>Activer les journaux de diagnostic pour Service Bus

Quand vous commencez à utiliser votre espace de noms Azure Service Bus, vous pouvez superviser le moment et le déroulement de la création, de la suppression ou de l’accès à votre espace de noms. Cet article fournit une vue d’ensemble de tous les journaux de diagnostic et d’opérations disponibles.

Azure Service Bus prend actuellement en charge les journaux d’activité/opérations qui capturent les **opérations de gestion** effectuées sur l’espace de noms Azure Service Bus. Plus précisément, ces journaux d’activité capturent le type d’opération, notamment la création de la file d’attente, les ressources utilisées et l’état de l’opération.

## <a name="operational-logs-schema"></a>Schéma des journaux d’activité des opérations

Tous les journaux sont stockés au format JSON (JavaScript Object Notation) dans les 2 emplacements ci-dessous.

- **AzureActivity** – Affiche les journaux des opérations/actions effectuées en relation avec votre espace de noms sur le portail ou via des déploiements de modèles Azure Resource Manager.
- **AzureDiagnostics** – Affiche les journaux des opérations/actions effectuées en relation avec votre espace de noms à l’aide de l’API ou via les clients de gestion sur le kit SDK du langage.

Les chaînes JSON du journal des opérations incluent les éléments répertoriés dans le tableau suivant :

| Nom | Description |
| ------- | ------- |
| ActivityId | ID interne, utilisé pour identifier l’activité spécifiée |
| EventName | Nom d’opération |
| ResourceId | ID de ressource Azure Resource Manager |
| SubscriptionId | Identifiant d’abonnement |
| EventTimeString | Durée de l’opération |
| EventProperties | Propriétés de l’opération |
| Statut | État de l’opération |
| Appelant | Appelant de l’opération (portail Azure ou client de gestion) |
| Category | OperationalLogs |

Voici un exemple de chaîne JSON du journal des opérations :

```json
{
  "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
  "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

## <a name="what-eventsoperations-are-captured-in-operational-logs"></a>Quels sont les événements/opérations capturés dans les journaux des opérations ?

Les journaux des opérations capturent toutes les opérations de gestion effectuées sur l’espace de noms Azure Service Bus. Les opérations de données ne sont pas capturées en raison du volume élevé d’opérations de données effectuées sur Azure Service Bus.

> [!NOTE]
> Pour améliorer le suivi des opérations de données, nous vous recommandons d’utiliser un suivi côté client.

Les opérations de gestion suivantes sont capturées dans les journaux des opérations : 

| Étendue | Opération|
|-------| -------- |
| Espace de noms | <ul> <li> Créer l'espace de noms</li> <li> Mettre à jour un espace de noms </li> <li> Supprimer un espace de noms </li>  </ul> | 
| File d'attente | <ul> <li> Créer une file d’attente</li> <li> Mettre à jour une file d’attente</li> <li> Supprimer une file d’attente </li> </ul> | 
| Rubrique | <ul> <li> Créer une rubrique </li> <li> Mettre à jour une rubrique </li> <li> Supprimer une rubrique </li> </ul> |
| Subscription | <ul> <li> Créer un abonnement </li> <li> Mettre à jour un abonnement </li> <li> Supprimer un abonnement </li> </ul> |

> [!NOTE]
> Actuellement, les opérations de **lecture** ne font pas l’objet d’un suivi dans les journaux des opérations.

## <a name="how-to-enable-operational-logs"></a>Comment activer les journaux des opérations ?

Les journaux des opérations sont désactivés par défaut. Pour activer les journaux de diagnostic, suivez les étapes ci-dessous :

1. Dans le [Portail Azure](https://portal.azure.com), accédez à votre espace de noms Azure Service Bus et sous **Supervision**, cliquez sur **Paramètres de diagnostic**.

   ![navigation dans le panneau jusqu’aux journaux de diagnostic](./media/service-bus-diagnostic-logs/image1.png)

2. Cliquez sur **Ajouter un paramètre de diagnostic** pour configurer les paramètres de diagnostic.  

   ![activer les journaux de diagnostic](./media/service-bus-diagnostic-logs/image2.png)

3. Configurer les paramètres de diagnostic
   1. Tapez un **nom** pour identifier les paramètres de diagnostic.
   2. Sélectionnez une destination pour les diagnostics.
      - Si vous sélectionnez **Compte de stockage**, vous devez configurer le compte de stockage dans lequel les diagnostics seront stockés.
      - Si vous sélectionnez **Hubs d’événements**, vous devez configurer le hub d’événements approprié vers lequel les paramètres de diagnostic seront diffusés.
      - Si vous sélectionnez **Log Analytics**, vous devez spécifier l’instance de Log Analytics où les diagnostics seront envoyés.
    3. Cochez **OperationalLogs**.

       ![modifier l’état des journaux de diagnostic](./media/service-bus-diagnostic-logs/image3.png)

4. Cliquez sur **Enregistrer**.


Les nouveaux paramètres prennent effet au bout de 10 minutes environ. Après cela, les journaux d’activité apparaissent dans la cible d’archivage configurée, dans le panneau **Journaux de diagnostic**.

Pour plus d’informations sur la configuration des diagnostics, consultez la [vue d’ensemble des journaux de diagnostic Azure](../azure-monitor/platform/diagnostic-logs-overview.md).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Service Bus, voir les liens suivants :

* [Introduction à Service Bus](service-bus-messaging-overview.md)
* [Bien démarrer avec Service Bus](service-bus-dotnet-get-started-with-queues.md)
