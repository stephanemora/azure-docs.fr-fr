---
title: Ressources prises en charge pour les alertes de métrique dans Azure Monitor
description: Référence sur les métriques et les journaux d’activité de support pour les alertes métriques dans Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 12/17/2019
ms.subservice: alerts
ms.openlocfilehash: 9585e74170d21ad899224462b6e41e7e9bb75727
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77664812"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Ressources prises en charge pour les alertes de métrique dans Azure Monitor

Azure Monitor prend désormais en charge un [nouveau type d’alerte de métrique](../../azure-monitor/platform/alerts-overview.md) qui présente d’importants avantages par rapport aux anciennes [alertes de métrique classiques](../../azure-monitor/platform/alerts-classic.overview.md). Des métriques sont disponibles pour une [longue liste de services Azure](../../azure-monitor/platform/metrics-supported.md). Les alertes plus récentes prennent en charge un sous-ensemble (croissant) des types de ressource. Cet article répertorie ce sous-ensemble.

Vous pouvez également utiliser des alertes de métriques plus récentes sur des données de journaux d’activité populaires d’un espace de travail Log Analytics extraites en tant que métriques. Pour plus d’informations, consultez [Alertes de métrique pour les journaux d’activité](../../azure-monitor/platform/alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portail, PowerShell, CLI, prise en charge de REST
À l’heure actuelle, il n’est possible de créer des alertes métriques plus récentes que sur le Portail Azure, [l’API REST](https://docs.microsoft.com/rest/api/monitor/metricalerts/) ou les [modèles Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md). La prise en charge de la configuration d’alertes plus récentes en utilisant PowerShell et Azure CLI versions 2.0 et supérieures sera bientôt disponible.

## <a name="metrics-and-dimensions-supported"></a>Métriques et dimensions prises en charge
Les alertes métriques plus récentes prennent en charge la génération d’alertes pour les métriques qui utilisent des dimensions. Vous pouvez utiliser les dimensions pour filtrer votre métrique au niveau approprié. Tous les métriques pris en charge, ainsi que les dimensions applicables, peuvent être examinés et visualisés à partir [d’Azure Monitor - Metrics Explorer](../../azure-monitor/platform/metrics-charts.md).

Voici la liste complète des sources de métrique d’Azure Monitor prises en charge par les alertes plus récentes :

|Type de ressource  |Dimensions prises en charge |Alertes à plusieurs ressources| Mesures disponibles|
|---------|---------|-----|----------|
|Microsoft.ApiManagement/service | Oui| Non | [Gestion des API](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft.AppPlatform/Spring |Non| Oui|
|Microsoft.Automation/automationAccounts | Oui| Non | [Comptes Automation](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | N/A| Non | [Comptes Batch](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis|Oui| Non |[Cache Azure pour Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic|Non|Oui|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/blobServices|Non|Oui|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/fileServices|Non|Oui|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/queueServices|Non|Oui|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/tableServices|Non|Oui| |
|Microsoft.CognitiveServices/accounts| N/A | Non | [Cognitive Services](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines |Oui | Oui | [Machines virtuelles](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets |N/A | Oui |[Groupes de machines virtuelles identiques](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Oui| Non | [Groupes de conteneur](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Oui | Non | [Clusters managés](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | Oui | Oui | |
|Microsoft.DataFactory/datafactories| Oui| Non | [Fabriques de données V1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories |Oui | Non |[Fabriques de données V2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft.DataShare/accounts |Non| Oui|
|Microsoft.DBforMySQL/servers |N/A| Non |[Base de données pour MySQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers |N/A | Non | [Base de données pour PostgreSQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.Devices/IotHubs | N/A | Non |[Métriques d’IoT Hub](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)|
|Microsoft.Devices/provisioningServices| Oui | Non |[Métriques DPS](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)|
|Microsoft.EventGrid/domains|Non|Oui| |
|Microsoft.EventGrid/topics |Oui | Non |[Rubriques Event Grid](../../azure-monitor/platform/metrics-supported.md#microsofteventgridtopics)|
|Microsoft.EventHub/clusters |Oui| Non |[Clusters Event Hubs](../../azure-monitor/platform/metrics-supported.md#microsofteventhubclusters)|
|Microsoft.EventHub/namespaces |Oui| Non |[Hubs d'événements](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Non |Non |[Coffres](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows |N/A | Non |[Logic Apps](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces|Oui| Non | [Machine Learning](../../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.Network/applicationGateways|N/A| Non |  |
|Microsoft.Network/dnsZones | N/A| Non | [Zones DNS](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | N/A | Non |[Circuits Express Route](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/loadBalancers (uniquement pour les références SKU Standard)| Oui| Non | [Équilibreurs de charge](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/natGateways|Non|Oui|
|Microsoft.Network/privateEndpoints|Non|Oui|
|Microsoft.Network/privateLinkServices|Non|Oui|
|Microsoft.Network/publicipaddresses |N/A | Non |[Adresses IP publiques](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Oui | Non | [Profils Traffic Manager](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Oui | Non | [Espaces de travail Log Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.Relay/namespaces | Oui | Non | [Relais](../../azure-monitor/platform/metrics-supported.md#microsoftrelaynamespaces)|
|Microsoft.Peering/peeringServices|Non|Oui|
|Microsoft.PowerBIDedicated/capacities | N/A | Non | [Capacités](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices |N/A|Non | [Services Recherche](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces |Oui| Non |[Service Bus](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Sql/servers/elasticPools |   Non | Oui |
|Microsoft.Sql/servers/databases    | Non | Oui |
|Microsoft.Storage/storageAccounts |Oui | Non | [Comptes de stockage](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services | Oui| Non | [Services blob](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices), [Services de fichiers](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices), [Services de file d’attente](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) et [Services de table](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs |N/A| Non | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.Microsoft.VMWareCloudSimple/virtualMachines |Oui|Non |[Machines virtuelles CloudSimple](../../azure-monitor/platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines)|
|Microsoft.Web/serverfarms | Oui | Non | [Plans App Service](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | Oui | Non | [App Services](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) et [Functions](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
|Microsoft.Web/sites/slots | Oui | Non | [Slots App Service](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|

## <a name="payload-schema"></a>Schéma de la charge utile

> [!NOTE]
> Vous pouvez également utiliser le [schéma d’alerte commun](https://aka.ms/commonAlertSchemaDocs), qui offre l’avantage de générer une seule charge utile d’alerte extensible et unifiée sur tous les services d’alerte dans Azure Monitor, pour les intégrations de votre webhook. [En savoir plus sur les définitions de schéma d’alerte commun.](https://aka.ms/commonAlertSchemaDefinitions)


L’opération POST contient le schéma et la charge utile JSON ci-après pour toutes les alertes plus récentes basées sur des métriques lorsqu’un [groupe d’action](../../azure-monitor/platform/action-groups.md) configuré de manière appropriée est utilisé :

```json
{
  "schemaId": "AzureMonitorMetricAlert",
  "data": {
    "version": "2.0",
    "status": "Activated",
    "context": {
      "timestamp": "2018-02-28T10:44:10.1714014Z",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
      "name": "StorageCheck",
      "description": "",
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "severity":"3",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
            "metricNamespace":"microsoft.storage/storageAccounts",
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
            "metricValue": 1
          }
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

* En savoir plus sur la nouvelle [expérience Alertes](../../azure-monitor/platform/alerts-overview.md).
* En savoir plus sur les [alertes de journal dans Azure](../../azure-monitor/platform/alerts-unified-log.md).
* En savoir plus sur les [alertes dans Azure](../../azure-monitor/platform/alerts-overview.md).
