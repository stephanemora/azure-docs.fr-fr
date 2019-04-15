---
title: Vue d’ensemble des journaux d’activité de Pare-feu Azure
description: Cet article offre une vue d’ensemble des journaux de diagnostic de Pare-feu Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: c129c394f3d694b832722287027c1f9e58028a33
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957689"
---
# <a name="azure-firewall-logs"></a>Journaux d’activité de Pare-feu Azure

Vous pouvez surveiller le service Pare-feu Azure à l’aide des journaux d’activité de pare-feu. Vous pouvez également utiliser les journaux d’activité pour auditer les opérations sur les ressources de Pare-feu Azure.

Vous pouvez accéder à certains de ces journaux d’activité via le portail. Les journaux d’activité peuvent être envoyés au service [Journaux d’activité Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md), au stockage et aux hubs d’événements, puis analysés dans les journaux d’activité Azure Monitor ou par différents outils comme Excel et Power BI.

## <a name="diagnostic-logs"></a>Journaux de diagnostic

 Les journaux de diagnostic suivants sont disponibles pour Pare-feu Azure :

* **Journal de règles d’application**

   Le journal des applications est enregistré dans un compte de stockage, diffusés aux hubs d’événements et/ou envoyés aux journaux d’Azure Monitor uniquement si vous l’avez activé pour chaque pare-feu Azure. Chaque nouvelle connexion qui correspond à l’une de vos règles d’application configurées entraîne un journal pour la connexion acceptée/refusée. Les données sont consignées au format JSON, comme indiqué dans l’exemple suivant :

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

   Le journal de règle de réseau est enregistré dans un compte de stockage, diffusés aux hubs d’événements et/ou envoyés aux journaux d’Azure Monitor uniquement si vous l’avez activé pour chaque pare-feu Azure. Chaque nouvelle connexion qui correspond à l’une de vos règles de réseau configurées entraîne un journal pour la connexion acceptée/refusée. Les données sont consignées au format JSON, comme indiqué dans l’exemple suivant :

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

Pour stocker vos journaux d’activité, vous disposez de trois options :

* **Compte de stockage** : les comptes de stockage conviennent parfaitement aux journaux d’activité quand ils sont stockés pour une durée plus longue et consultés quand cela est nécessaire.
* **Hubs d’événements** : les hubs d’événements constituent une excellente solution pour l’intégration à d’autres outils SEIM (Security Information and Event Management) afin de recevoir des alertes sur vos ressources.
* **Journaux Azure Monitor** : Les journaux d’activité Azure Monitor conviennent parfaitement pour la supervision en temps réel générale de votre application ou la recherche de tendances.

## <a name="activity-logs"></a>Journaux d’activité

   Les entrées du journal d’activité sont recueillies par défaut et vous pouvez les afficher dans le Portail Azure.

   Vous pouvez utiliser les [journaux d’activité Azure](../azure-resource-manager/resource-group-audit.md) (anciennement journaux d’activité des opérations et journaux d’audit) pour afficher toutes les opérations soumises à votre abonnement Azure.


## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment surveiller les journaux de pare-feu d’Azure et les mesures, consultez [didacticiel : Surveiller les journaux d’activité de pare-feu Azure](tutorial-diagnostics.md).