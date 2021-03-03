---
title: Ressources prises en charge pour les alertes de métrique dans Azure Monitor
description: Référence sur les métriques et les journaux d’activité de support pour les alertes métriques dans Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 02/10/2021
ms.subservice: alerts
ms.openlocfilehash: 7b4cdd37cefb628c03129a990d52ed348acd3ac1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100598665"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Ressources prises en charge pour les alertes de métrique dans Azure Monitor

Azure Monitor prend désormais en charge un [nouveau type d’alerte de métrique](../platform/alerts-overview.md) qui présente d’importants avantages par rapport aux anciennes [alertes de métrique classiques](./alerts-classic.overview.md). Des métriques sont disponibles pour une [longue liste de services Azure](../platform/metrics-supported.md). Les alertes plus récentes prennent en charge un sous-ensemble (croissant) des types de ressource. Cet article répertorie ce sous-ensemble.

Vous pouvez également utiliser des alertes de métriques plus récentes sur des données de journaux d’activité populaires d’un espace de travail Log Analytics extraites en tant que métriques. Pour plus d’informations, consultez [Alertes de métrique pour les journaux d’activité](./alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portail, PowerShell, CLI, prise en charge de REST
À l’heure actuelle, il n’est possible de créer des alertes métriques plus récentes que sur le Portail Azure, [l’API REST](/rest/api/monitor/metricalerts/) ou les [modèles Resource Manager](./alerts-metric-create-templates.md). La prise en charge de la configuration d’alertes plus récentes en utilisant PowerShell et Azure CLI versions 2.0 et supérieures sera bientôt disponible.

## <a name="metrics-and-dimensions-supported"></a>Métriques et dimensions prises en charge
Les alertes métriques plus récentes prennent en charge la génération d’alertes pour les métriques qui utilisent des dimensions. Vous pouvez utiliser les dimensions pour filtrer votre métrique au niveau approprié. Tous les métriques pris en charge, ainsi que les dimensions applicables, peuvent être examinés et visualisés à partir [d’Azure Monitor - Metrics Explorer](../essentials/metrics-charts.md).

Voici la liste complète des sources de métrique d’Azure Monitor prises en charge par les alertes plus récentes :

|Type de ressource  |Dimensions prises en charge |Alertes à plusieurs ressources| Mesures disponibles|
|---------|---------|-----|----------|
|Microsoft.Aadiam/azureADMetrics | Oui | Non | |
|Microsoft.ApiManagement/service | Oui | Non | [Gestion des API](../platform/metrics-supported.md#microsoftapimanagementservice) |
|Microsoft.AppConfiguration/configurationStores |Oui | Non | [Configuration de l’application](../platform/metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft.AppPlatform/Spring | Oui | Non | [Azure Spring Cloud](../platform/metrics-supported.md#microsoftappplatformspring) |
|Microsoft.Automation/automationAccounts | Oui| Non | [Comptes Automation](../platform/metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft.AVS/privateClouds | Non | Non | [Azure VMware Solution](../platform/metrics-supported.md#microsoftavsprivateclouds) |
|Microsoft.Batch/batchAccounts | Oui | Non | [Comptes Batch](../platform/metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft.Cache/Redis | Oui | Oui | [Cache Azure pour Redis](../platform/metrics-supported.md#microsoftcacheredis) |
|Microsoft.ClassicCompute/domainNames/slots/roles | Non | Non | [Services cloud (classiques)](../platform/metrics-supported.md#microsoftclassiccomputedomainnamesslotsroles) |
|Microsoft.ClassicCompute/virtualMachines | Non | Non | [Machines virtuelles classiques](../platform/metrics-supported.md#microsoftclassiccomputevirtualmachines) |
|Microsoft.ClassicStorage/storageAccounts | Oui | Non | [Comptes de stockage (classiques)](../platform/metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft.ClassicStorage/storageAccounts/blobServices | Oui | Non | [Comptes de stockage (classiques) – Blobs](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountsblobservices) |
|Microsoft.ClassicStorage/storageAccounts/fileServices | Oui | Non | [Comptes de stockage (classiques) – Fichiers](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountsfileservices) |
|Microsoft.ClassicStorage/storageAccounts/queueServices | Oui | Non | [Comptes de stockage (classiques) – Files d’attente](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountsqueueservices) |
|Microsoft.ClassicStorage/storageAccounts/tableServices | Oui | Non | [Comptes de stockage (classiques) – Tables](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountstableservices) |
|Microsoft.CognitiveServices/accounts | Oui | Non | [Cognitive Services](../platform/metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft.Compute/virtualMachines | Oui | Oui<sup>1</sup> | [Machines virtuelles](../platform/metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | Oui | Non |[Groupes de machines virtuelles identiques](../platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft.ContainerInstance/containerGroups | Oui| Non | [Groupes de conteneur](../platform/metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft.ContainerRegistry/registries | Non | Non | [Registres de conteneurs](../platform/metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft.ContainerService/managedClusters | Oui | Non | [Clusters managés](../platform/metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | Oui | Oui | [Data Box](../platform/metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft.DataFactory/datafactories| Oui| Non | [Fabriques de données V1](../platform/metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft.DataFactory/factories |Oui | Non | [Fabriques de données V2](../platform/metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft.DataShare/accounts | Oui | Non | [Partages de données](../platform/metrics-supported.md#microsoftdatashareaccounts) |
|Microsoft.DBforMariaDB/servers | Non | Non | [DB for MariaDB](../platform/metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft.DBforMySQL/servers | Non | Non |[Base de données pour MySQL](../platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers | Non | Non | [Base de données pour PostgreSQL](../platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.DBforPostgreSQL/serversv2 | Non | Non | [DB pour PostgreSQL V2](../platform/metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft.DBforPostgreSQL/flexibleServers | Oui | Non | [Base de données pour PostgreSQL (serveurs flexibles)](../platform/metrics-supported.md#microsoftdbforpostgresqlflexibleservers)|
|Microsoft.Devices/IotHubs | Oui | Non |[IoT Hub](../platform/metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft.Devices/provisioningServices| Oui | Non | [Services de provisionnement d’appareil](../platform/metrics-supported.md#microsoftdevicesprovisioningservices) |
|Microsoft.DigitalTwins/digitalTwinsInstances | Oui | Non | [Digital Twins](../platform/metrics-supported.md#microsoftdigitaltwinsdigitaltwinsinstances) |
|Microsoft.DocumentDB/databaseAccounts | Oui | Non | [Cosmos DB](../platform/metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft.EventGrid/domains | Oui | Non | [Domaines Event Grid](../platform/metrics-supported.md#microsofteventgriddomains) |
|Microsoft.EventGrid/systemTopics | Oui | Non | [Rubriques système Event Grid](../platform/metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft.EventGrid/topics |Oui | Non | [Rubriques Event Grid](../platform/metrics-supported.md#microsofteventgridtopics) |
|Microsoft.EventHub/clusters |Oui| Non | [Clusters Event Hubs](../platform/metrics-supported.md#microsofteventhubclusters) |
|Microsoft.EventHub/namespaces |Oui| Non | [Hubs d'événements](../platform/metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft.HDInsight/clusters | Oui | Non | [Clusters HDInsight](../platform/metrics-supported.md#microsofthdinsightclusters) |
|Microsoft.Insights/Components | Oui | Non | [Application Insights](../platform/metrics-supported.md#microsoftinsightscomponents) |
|Microsoft.KeyVault/vaults | Oui |Oui |[Coffres](../platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Kusto/Clusters | Oui |Non |[Clusters Data Explorer](../platform/metrics-supported.md#microsoftkustoclusters)|
|Microsoft.Logic/integrationServiceEnvironments | Oui | Non |[Environnements de service d’intégration](../platform/metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft.Logic/workflows | Non | Non |[Logic Apps](../platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces | Oui | Non | [Machine Learning](../platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.Maps/accounts | Oui | Non | [Comptes Maps](../platform/metrics-supported.md#microsoftmapsaccounts) |
|Microsoft.Media/mediaservices | Non | Non | [Media Services](../platform/metrics-supported.md#microsoftmediamediaservices) |
|Microsoft.Media/mediaservices/streamingEndpoints | Oui | Non | [Points de terminaison de streaming Media Services](../platform/metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft.NetApp/netAppAccounts/capacityPools | Oui | Oui | [Pools de capacités Azure NetApp](../platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft.NetApp/netAppAccounts/capacityPools/volumes | Oui | Oui | [Volumes Azure NetApp](../platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways | Oui | Non | [Application Gateways](../platform/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/azurefirewalls | Oui | Non | [Pare-feux](../platform/metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft.Network/dnsZones | Non | Non | [Zones DNS](../platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | Oui | Non |[Circuits ExpressRoute](../platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/expressRoutePorts | Oui | Non |[ExpressRoute Direct](../platform/metrics-supported.md#microsoftnetworkexpressrouteports) |
|Microsoft.Network/loadBalancers (uniquement pour les références SKU Standard)| Oui| Non | [Équilibreurs de charge](../platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/natGateways| Non | Non | [Passerelles NAT](../platform/metrics-supported.md#microsoftnetworknatgateways) |
|Microsoft.Network/privateEndpoints| Non | Non | [Points de terminaison privés](../platform/metrics-supported.md#microsoftnetworkprivateendpoints) |
|Microsoft.Network/privateLinkServices| Non | Non | [Services de liaison privée](../platform/metrics-supported.md#microsoftnetworkprivatelinkservices) |
|Microsoft.Network/publicipaddresses | Non | Non | [Adresses IP publiques](../platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Oui | Non | [Profils Traffic Manager](../platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Oui | Non | [Espaces de travail Log Analytics](../platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.Peering/peerings | Oui | Non | [Peerings](../platform/metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft.Peering/peeringServices | Oui | Non | [Services de peering](../platform/metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft.PowerBIDedicated/capacities | Non | Non | [Capacités](../platform/metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft.Relay/namespaces | Oui | Non | [Relais](../platform/metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft.Search/searchServices | Non | Non | [Services Recherche](../platform/metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft.ServiceBus/namespaces | Oui | Non | [Service Bus](../platform/metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft.Sql/managedInstances | Non | Oui | [Instances managées SQL](../platform/metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft.Sql/servers/databases | Non | Oui | [Bases de données SQL](../platform/metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft.Sql/servers/elasticPools | Non | Oui | [Pools élastiques SQL](../platform/metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft.Storage/storageAccounts |Oui | Non | [Comptes de stockage](../platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/blobServices | Oui| Non | [Comptes de stockage – Blobs](../platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices) |
|Microsoft.Storage/storageAccounts/fileServices | Oui| Non | [Comptes de stockage – Fichiers](../platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices) |
|Microsoft.Storage/storageAccounts/queueServices | Oui| Non | [Comptes de stockage – Files d’attente](../platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) |
|Microsoft.Storage/storageAccounts/tableServices | Oui| Non | [Comptes de stockage – Tables](../platform/metrics-supported.md#microsoftstoragestorageaccountstableservices) |
|Microsoft.StorageCache/caches | Oui | Non | [Caches HPC](../platform/metrics-supported.md#microsoftstoragecachecaches) |
|Microsoft.StorageSync/storageSyncServices | Oui | Non | [Services de synchronisation de stockage](../platform/metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft.StreamAnalytics/streamingjobs | Oui | Non | [Stream Analytics](../platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Microsoft.Synapse/workspaces | Oui | Non | [Synapse Analytics](../platform/metrics-supported.md#microsoftsynapseworkspaces) |
|Microsoft.Synapse/workspaces/bigDataPools | Oui | Non | [Pools Apache Spark Synapse Analytics](../platform/metrics-supported.md#microsoftsynapseworkspacesbigdatapools) |
|Microsoft.Synapse/workspaces/sqlPools | Oui | Non | [Pools SQL Synapse Analytics](../platform/metrics-supported.md#microsoftsynapseworkspacessqlpools) |
|Microsoft.VMWareCloudSimple/virtualMachines | Oui | Non | [Machines virtuelles CloudSimple](../platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft.Web/hostingEnvironments/multiRolePools | Oui | Non | [Pools multirôles App Service Environment](../platform/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingEnvironments/workerPools | Oui | Non | [Pools de workers App Service Environment](../platform/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | Oui | Non | [Plans App Service](../platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | Oui | Non | [App Services et Functions](../platform/metrics-supported.md#microsoftwebsites)|
|Microsoft.Web/sites/slots | Oui | Non | [Slots App Service](../platform/metrics-supported.md#microsoftwebsitesslots)|

<sup>1</sup> Pas pris en charge pour les métriques de réseau des machines virtuelles (Octets entrants réseau totaux, Octets sortants réseau totaux, Flux entrants, Flux sortants, Taux de création maximal de flux entrants, Taux de création maximal de flux sortants) et les métriques personnalisées.

## <a name="payload-schema"></a>Schéma de la charge utile

> [!NOTE]
> Vous pouvez également utiliser le [schéma d’alerte commun](./alerts-common-schema.md), qui offre l’avantage de générer une seule charge utile d’alerte extensible et unifiée sur tous les services d’alerte dans Azure Monitor, pour les intégrations de votre webhook. [En savoir plus sur les définitions de schéma d’alerte commun.](./alerts-common-schema-definitions.md)


L’opération POST contient le schéma et la charge utile JSON ci-après pour toutes les alertes plus récentes basées sur des métriques lorsqu’un [groupe d’action](./action-groups.md) configuré de manière appropriée est utilisé :

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

* En savoir plus sur la nouvelle [expérience Alertes](../platform/alerts-overview.md).
* En savoir plus sur les [alertes de journal dans Azure](./alerts-unified-log.md).
* En savoir plus sur les [alertes dans Azure](../platform/alerts-overview.md).