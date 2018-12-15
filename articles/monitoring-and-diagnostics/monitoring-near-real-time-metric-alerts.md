---
title: Ressources prises en charge pour les alertes de métrique dans Azure Monitor
description: Référence sur les métriques et les journaux de support pour les alertes métriques dans Azure Monitor
author: snehithm
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: d5802ed71c187979fb91bcd4ea532891d343f14a
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53435699"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Ressources prises en charge pour les alertes de métrique dans Azure Monitor

Azure Monitor prend désormais en charge un [nouveau type d’alerte de métrique](../azure-monitor/platform/alerts-overview.md) qui présente d’importants avantages par rapport aux anciennes [alertes de métrique classiques](../azure-monitor/platform/alerts-classic.overview.md). Des métriques sont disponibles pour une [longue liste de services Azure](../azure-monitor/platform/metrics-supported.md). Les alertes plus récentes prennent en charge un sous-ensemble (croissant) des types de ressource. Cet article répertorie ce sous-ensemble.


Vous pouvez également utiliser des alertes métriques plus récentes sur des journaux Log Analytics populaires extraits en tant que métriques. Pour plus d’informations, consultez [Alertes de métrique pour les journaux](../azure-monitor/platform/alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portail, PowerShell, CLI, prise en charge de REST
À l’heure actuelle, il n’est possible de créer des alertes métriques plus récentes que sur le Portail Azure, [l’API REST](https://docs.microsoft.com/rest/api/monitor/metricalerts/) ou les [modèles Resource Manager](../azure-monitor/platform/alerts-metric-create-templates.md). La prise en charge de la configuration d’alertes plus récentes en utilisant PowerShell et Azure CLI versions 2.0 et supérieures sera bientôt disponible.

## <a name="metrics-and-dimensions-supported"></a>Métriques et dimensions prises en charge
Les alertes métriques plus récentes prennent en charge la génération d’alertes pour les métriques qui utilisent des dimensions. Vous pouvez utiliser les dimensions pour filtrer votre métrique au niveau approprié. Tous les métriques pris en charge, ainsi que les dimensions applicables, peuvent être examinés et visualisés à partir [d’Azure Monitor - Metrics Explorer](../azure-monitor/platform/metrics-charts.md).

Voici la liste complète des sources de métrique d’Azure Monitor prises en charge par les alertes plus récentes :

|Type de ressource  |Dimensions prises en charge  | Mesures disponibles|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Oui        | [Gestion des API](../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Oui   | [Comptes Automation](../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | N/A| [Comptes Batch](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    N/A     |[Cache Azure pour Redis](../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft.CognitiveServices/accounts     |    N/A     | [Cognitive Services](../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines     |    N/A     | [Machines virtuelles](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   N/A      |[Groupes de machines virtuelles identiques](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Oui| [Groupes de conteneur](../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Oui | [Clusters managés](../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataFactory/datafactories| Oui| [Fabriques de données V1](../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories     |   Oui     |[Fabriques de données V2](../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   N/A      |[Base de données pour MySQL](../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    N/A     | [Base de données pour PostgreSQL](../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  Oui      |[Hubs d'événements](../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Non  | [Coffres](../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows     |     N/A    |[Logic Apps](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    N/A     | [Application Gateways](../azure-monitor/platform/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/expressRouteCircuits | N/A |  [Circuits Express Route](../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/dnsZones | N/A| [Zones DNS](../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/loadBalancers (uniquement pour les références SKU Standard)| Oui| [Équilibreurs de charge](../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses     |  N/A       |[Adresses IP publiques](../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.PowerBIDedicated/capacities | N/A | [Capacités](../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Network/trafficManagerProfiles | Oui | [Profils Traffic Manager](../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.Search/searchServices     |   N/A      |[Services Recherche](../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Oui       |[Service Bus](../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    Oui     | [Comptes de stockage](../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Oui    | [Services blob](../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices), [Services de fichiers](../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices), [Services de file d’attente](../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) et [Services de table](../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  N/A       | [Stream Analytics](../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
| Microsoft.Web/serverfarms | Oui | [Plans App Service](../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)  |
| Microsoft.Web/sites | Oui | [App Services](../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) et [Functions](../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
| Microsoft.Web/sites/slots | Oui | [Slots App Service](../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|
|Microsoft.OperationalInsights/workspaces| Oui|[Espaces de travail Log Analytics](../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|



## <a name="payload-schema"></a>Schéma de la charge utile

L’opération POST contient le schéma et la charge utile JSON ci-après pour toutes les alertes plus récentes basées sur des métriques lorsqu’un [groupe d’action](../azure-monitor/platform/action-groups.md) configuré de manière appropriée est utilisé :

```json
{"schemaId":"AzureMonitorMetricAlert","data":
    {
    "version": "2.0",
    "status": "Activated",
    "context": {
    "timestamp": "2018-02-28T10:44:10.1714014Z",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
    "name": "StorageCheck",
    "description": "",
    "conditionType": "SingleResourceMultipleMetricCriteria",
    "condition": {
      "windowSize": "PT5M",
      "allOf": [
        {
          "metricName": "Transactions",
          "dimensions": [
            {
              "name": "AccountResourceId",
              "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
            },
            {
              "name": "GeoType",
              "value": "Primary"
            }
          ],
          "operator": "GreaterThan",
          "threshold": "0",
          "timeAggregation": "PT5M",
          "metricValue": 1.0
        },
      ]
    },
    "subscriptionId": "00000000-0000-0000-0000-000000000000",
    "resourceGroupName": "Contoso",
    "resourceName": "diag500",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
    "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
  },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la nouvelle [expérience Alertes](../azure-monitor/platform/alerts-overview.md).
* En savoir plus sur les [alertes de journal dans Azure](../azure-monitor/platform/alerts-unified-log.md).
* En savoir plus sur les [alertes dans Azure](../azure-monitor/platform/alerts-overview.md).
