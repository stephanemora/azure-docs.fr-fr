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
ms.openlocfilehash: 6443cb727573645792a4e6c929b80c3406d72025
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261801"
---
# <a name="service-bus-diagnostic-logs"></a>Journaux de diagnostic Service Bus

Vous pouvez afficher deux types de journaux d’activité pour Azure Service Bus :
* **[Journaux d’activité](../azure-monitor/platform/activity-logs-overview.md)** . Ces journaux d’activité contiennent des informations sur les opérations effectuées sur un travail. Les journaux d’activité sont toujours activés.
* **[Journaux de diagnostic](../azure-monitor/platform/resource-logs-overview.md)** . Vous pouvez configurer les journaux de diagnostic pour obtenir des informations plus détaillés sur tous les événements associés à un travail. Les journaux de diagnostic couvrent les activités qui se déroulent entre la création du travail et sa suppression, notamment les mises à jour et les activités durant l’exécution du travail.

## <a name="turn-on-diagnostic-logs"></a>Activer les journaux de diagnostic

Les journaux de diagnostic sont désactivés par défaut. Pour activer les journaux de diagnostic, suivez les étapes ci-dessous :

1.  Dans le [portail Azure](https://portal.azure.com), sous **Supervision + gestion**, cliquez sur **Journaux de diagnostic**.

    ![navigation dans le panneau jusqu’aux journaux de diagnostic](./media/service-bus-diagnostic-logs/image1.png)

2. Cliquez sur la ressource que vous souhaitez surveiller.  

3.  Cliquez sur **Activer les diagnostics**.

    ![activer les journaux de diagnostic](./media/service-bus-diagnostic-logs/image2.png)

4.  Pour l’**état**, cliquez sur **ACTIVÉ**.

    ![modifier l’état des journaux de diagnostic](./media/service-bus-diagnostic-logs/image3.png)

5.  Définissez la cible d’archivage de votre choix, par exemple un compte de stockage, un hub d’événements ou Journaux Azure Monitor.

6.  Enregistrez les nouveaux paramètres de diagnostic.

Les nouveaux paramètres prennent effet au bout de 10 minutes environ. Après cela, les journaux d’activité apparaissent dans la cible d’archivage configurée, dans le panneau **Journaux de diagnostic**.

Pour plus d’informations sur la configuration des diagnostics, consultez la [vue d’ensemble des journaux de diagnostic Azure](../azure-monitor/platform/resource-logs-overview.md).

## <a name="diagnostic-logs-schema"></a>Schéma des journaux de diagnostic

Tous les journaux d’activité sont stockés au format JSON (JavaScript Object Notation). Chaque entrée comporte des champs de type chaîne au format décrit dans la section suivante.

## <a name="operational-logs-schema"></a>Schéma des journaux d’activité des opérations

Les journaux d’activité de la catégorie **OperationalLogs** capturent ce qui se passe durant l’opération Service Bus. Plus précisément, ces journaux d’activité capturent le type d’opération, notamment la création de la file d’attente, les ressources utilisées et l’état de l’opération.

Les chaînes JSON du journal des opérations incluent les éléments répertoriés dans le tableau suivant :

Nom | Description
------- | -------
ActivityId | ID interne, utilisé à des fins de suivi
EventName | Nom d’opération           
resourceId | ID de ressource Azure Resource Manager
SubscriptionId | Identifiant d’abonnement
EventTimeString | Durée de l’opération
EventProperties | Propriétés de l’opération
Statut | État de l’opération
Appelant | Appelant de l’opération (portail Azure ou client de gestion)
category | OperationalLogs

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

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Service Bus, voir les liens suivants :

* [Introduction à Service Bus](service-bus-messaging-overview.md)
* [Bien démarrer avec Service Bus](service-bus-dotnet-get-started-with-queues.md)
