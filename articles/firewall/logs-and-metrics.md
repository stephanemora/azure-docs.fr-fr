---
title: Vue d’ensemble des journaux de Pare-feu Azure
description: Cet article offre une vue d’ensemble des journaux de diagnostic de Pare-feu Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 0698f1dbc491781089ef94eec32f2a427fd3cca4
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52422386"
---
# <a name="azure-firewall-logs"></a>Journaux de Pare-feu Azure

Vous pouvez surveiller le service Pare-feu Azure à l’aide des journaux de pare-feu. Vous pouvez également utiliser les journaux d’activité pour auditer les opérations sur les ressources de Pare-feu Azure.

Vous pouvez accéder à certains de ces journaux via le portail. Les journaux peuvent être envoyés vers les services [Log Analytics](../azure-monitor/insights/azure-networking-analytics.md), Stockage et Event Hubs, puis analysés dans Log Analytics ou par différents outils comme Excel et Power BI.

## <a name="diagnostic-logs"></a>Journaux de diagnostic

 Les journaux de diagnostic suivants sont disponibles pour Pare-feu Azure :

* **Journal de règles d’application**

   Le journal de règles d’application est enregistré dans un compte de stockage, transmis en continu au service Event Hubs et/ou envoyé vers Log Analytics uniquement si vous l’avez activé pour chaque Pare-feu Azure. Chaque nouvelle connexion qui correspond à l’une de vos règles d’application configurées entraîne un journal pour la connexion acceptée/refusée. Les données sont consignées au format JSON, comme indiqué dans l’exemple suivant :

   ```
   Category: application rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **Journal de règles de réseau**

   Le journal de règles de réseau est enregistré dans un compte de stockage, transmis en continu au service Event Hubs et/ou envoyé vers Log Analytics uniquement si vous l’avez activé pour chaque Pare-feu Azure. Chaque nouvelle connexion qui correspond à l’une de vos règles de réseau configurées entraîne un journal pour la connexion acceptée/refusée. Les données sont consignées au format JSON, comme indiqué dans l’exemple suivant :

   ```
   Category: network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

Pour stocker vos journaux, vous disposez de trois options :

* **Compte de stockage** : les comptes de stockage conviennent parfaitement aux journaux lorsqu’ils sont stockés pour une durée plus longue et consultés lorsque nécessaire.
* **Concentrateurs d’événements** : les concentrateurs d’événements constituent une excellente solution pour l’intégration avec d’autres outils SEIM (Security Information and Event Management) afin de recevoir des alertes sur vos ressources.
* **Log Analytics** : Log Analytics convient parfaitement pour la surveillance en temps réel générale de votre application ou la recherche de tendances.

## <a name="activity-logs"></a>Journaux d’activité

   Les entrées du journal d’activité sont recueillies par défaut et vous pouvez les afficher dans le Portail Azure.

   Vous pouvez utiliser les [journaux d’activité Azure](../azure-resource-manager/resource-group-audit.md) (anciennement journaux des opérations et journaux d’audit) pour afficher toutes les opérations soumises à votre abonnement Azure.


## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment surveiller les métriques et les journaux de Pare-feu Azure, consultez le [didacticiel : Surveiller les journaux de Pare-feu Azure](tutorial-diagnostics.md).