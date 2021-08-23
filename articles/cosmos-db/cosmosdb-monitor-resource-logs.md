---
title: Surveillez les données Azure Cosmos DB à l'aide des paramètres de diagnostic Azure
description: Découvrez comment utiliser les paramètres de diagnostic Azure pour surveiller les performances et la disponibilité des données stockées dans Azure Cosmos DB.
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/20/2021
ms.author: sngun
ms.openlocfilehash: 8c9303097a7b3b545d8fa106dd23f8d5662fc69d
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111964221"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Surveillez les données Azure Cosmos DB à l’aide des paramètres de diagnostic dans Azure
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Les paramètres de diagnostic dans Azure sont utilisés pour collecter les journaux des ressources. Les journaux de ressources Azure sont émis par une ressource et fournissent des données riches et fréquentes sur le fonctionnement de cette ressource. Ces journaux sont capturés par requête et sont également appelés « journaux des plans de données ». Les opérations Delete, Insert et readFeed sont des exemples d’opérations de plan de données. Le contenu de ces journaux d’activité varie en fonction du type de ressource.

Les indicateurs de performance de la plateforme et le journal d’activité sont collectés automatiquement, mais vous devez créer un paramètre de diagnostic pour collecter les journaux des ressources ou les transférer en dehors d’Azure Monitor. Vous pouvez activer le paramètre de diagnostic pour les comptes Azure Cosmos DB et envoyer les journaux des ressources aux sources suivantes :
- Espaces de travail Log Analytics
  - Les données envoyées à Log Analytics peuvent être écrites dans des tables **Diagnostics Azure (héritées)** ou **Spécifique à la ressource (préversion)** 
- Event Hub
- Compte de stockage
  
> [!NOTE]
> Pour les comptes d’API SQL, nous vous recommandons de créer le paramètre de diagnostic en mode Spécifique à la ressource en [suivant les instructions de création d’un paramètre de diagnostic via l’API REST](cosmosdb-monitor-resource-logs.md#create-diagnostic-setting). Cette option fournit des optimisations de coût supplémentaires grâce à une vue améliorée de la gestion des données.

## <a name="create-using-the-azure-portal"></a>Création à l’aide du portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Accédez à votre compte Azure Cosmos. Ouvrez le volet des **Paramètres de Diagnostic**, puis sélectionnez l’option **Ajouter le paramètre de diagnostic**.

2. Dans le volet **Paramètres de diagnostic**, renseignez le formulaire avec vos catégories préférées.

### <a name="choosing-log-categories"></a>Sélection des catégories de journaux

|Category  |API   | Définition  | Propriétés de clé   |
|---------|---------|---------|---------|
|DataPlaneRequests     |  Toutes les API        |     Consigne les requêtes back-end en tant qu’opérations de plan de données qui sont des requêtes exécutées pour créer, mettre à jour, supprimer ou récupérer des données dans le compte.   |   `Requestcharge`, `statusCode`, `clientIPaddress`, `partitionID`, `resourceTokenPermissionId` `resourceTokenPermissionMode`      |
|MongoRequests     |    Mongo    |   Consigne les requêtes initiées par l’utilisateur depuis le serveur front-end pour servir des requêtes à l’API Azure Cosmos DB pour MongoDB. Quand vous activez cette catégorie, veillez à désactiver DataPlaneRequests.      |  `Requestcharge`, `opCode`, `retryCount`, `piiCommandText`      |
|CassandraRequests     |   Cassandra      |    Consigne les requêtes initiées par l’utilisateur depuis le serveur front-end pour servir des requêtes à l’API Azure Cosmos DB pour Cassandra. Quand vous activez cette catégorie, veillez à désactiver DataPlaneRequests.     |     `operationName`, `requestCharge`, `piiCommandText`    |
|GremlinRequests     |    Gremlin    |     Consigne les requêtes initiées par l’utilisateur depuis le serveur front-end pour servir des requêtes à l’API Azure Cosmos DB pour Gremlin. Quand vous activez cette catégorie, veillez à désactiver DataPlaneRequests.    |   `operationName`, `requestCharge`, `piiCommandText`, `retriedDueToRateLimiting`       |
|QueryRuntimeStatistics     |   SQL      |     Ce tableau détaille les opérations de requête exécutées sur un compte d’API SQL. Par défaut, le texte de la requête et ses paramètres sont obfusqués pour éviter la journalisation des données personnelles. La journalisation du texte intégral de la requête est disponible sur demande.    |    `databasename`, `partitionkeyrangeid`, `querytext`    |
|PartitionKeyStatistics     |    Toutes les API     |   Consigne les statistiques des clés de partition logique en représentant la taille de stockage (ko) des clés de partition. Cette table est utile lors de la résolution des problèmes liés à l’asymétrie du stockage.      |   `subscriptionId`, `regionName`, `partitionKey`, `sizeKB`      |
|PartitionKeyRUConsumption     |   API SQL    |     Consigne la consommation agrégée de RU/s des clés de partition. Cette table est utile pour le dépannage des partitions à chaud. Actuellement, Azure Cosmos DB signale des clés de partition pour les comptes d’API SQL uniquement et pour les opérations de lecture/écriture de point et de procédure stockée.   |     `subscriptionId`, `regionName`, `partitionKey`, `requestCharge`, `partitionKeyRangeId`   |
|ControlPlaneRequests     |   Toutes les API       |    Consigne les détails des opérations du plan de contrôle, c’est-à-dire la création d’un compte, l’ajout ou la suppression d’une région, la mise à jour des paramètres de réplication du compte, etc.     |    `operationName`, `httpstatusCode`, `httpMethod`, `region`       |
|TableApiRequests     |   API de table    |     Consigne les requêtes initiées par l’utilisateur depuis le serveur front-end pour servir des requêtes à l’API Azure Cosmos DB pour Table. Quand vous activez cette catégorie, veillez à désactiver DataPlaneRequests.       |    `operationName`, `requestCharge`, `piiCommandText`     |


## <a name="create-diagnostic-setting-via-rest-api"></a><a id="create-diagnostic-setting"></a> Créer un paramètre de diagnostic via l’API REST
Utilisez l’[API REST Azure Monitor](/rest/api/monitor/diagnosticsettings/createorupdate) pour créer un paramètre de diagnostic via la console interactive.
> [!Note]
> Si vous utilisez l’API SQL, nous vous recommandons de définir la propriété **logAnalyticsDestinationType** sur **Dedicated** pour activer les tables spécifiques à la ressource.

### <a name="request"></a>Requête

```HTTP
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

### <a name="headers"></a>headers

|Paramètres/en-têtes  | Valeur/description  |
|---------|---------|
|name     |  Nom de votre paramètre de diagnostic      |
|resourceUri     |   subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.DocumentDb/databaseAccounts/{ACCOUNT_NAME}/providers/microsoft.insights/diagnosticSettings/{DIAGNOSTIC_SETTING_NAME}      |
|api-version     |    2017-05-01-preview     |
|Content-Type     |    application/json     |

### <a name="body"></a>Corps

```json
{
    "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.DocumentDb/databaseAccounts/{ACCOUNT_NAME}/providers/microsoft.insights/diagnosticSettings/{DIAGNOSTIC_SETTING_NAME}",
    "type": "Microsoft.Insights/diagnosticSettings",
    "name": "name",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": null,
        "serviceBusRuleId": null,
        "workspaceId": "/subscriptions/{SUBSCRIPTION_ID}/resourcegroups/{RESOURCE_GROUP}/providers/microsoft.operationalinsights/workspaces/{WORKSPACE_NAME}",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "logs": [
            {
                "category": "DataPlaneRequests",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "QueryRuntimeStatistics",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "PartitionKeyStatistics",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "PartitionKeyRUConsumption",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ControlPlaneRequests",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ],
        "logAnalyticsDestinationType": "Dedicated"
    },
    "identity": null
}
```

## <a name="create-diagnostic-setting-via-azure-cli"></a>Créer un paramètre de diagnostic via Azure CLI
Pour créer un paramètre de diagnostic avec Azure CLI, utilisez la commande [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create). Pour une description des paramètres de cette commande, consultez sa documentation.

> [!Note]
> Si vous utilisez l’API SQL, nous vous recommandons de définir la propriété **export-to-resource-specific** sur **true**.

```azurecli-interactive
az monitor diagnostic-settings create --resource /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.DocumentDb/databaseAccounts/ --name {DIAGNOSTIC_SETTING_NAME} --export-to-resource-specific true --logs '[{"category": "QueryRuntimeStatistics","categoryGroup": null,"enabled": true,"retentionPolicy": {"enabled": false,"days": 0}}]' --workspace /subscriptions/{SUBSCRIPTION_ID}/resourcegroups/{RESOURCE_GROUP}/providers/microsoft.operationalinsights/workspaces/{WORKSPACE_NAME}"
```

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur la façon d’interroger des tables spécifiques à la ressource, consultez [Résoudre des problèmes avec des tables spécifiques à la ressource](cosmosdb-monitor-logs-basic-queries.md#resource-specific-queries).

* Pour plus d’informations sur la façon d’interroger des tables AzureDiagnostics, consultez [Résoudre des problèmes avec des tables AzureDiagnostics](cosmosdb-monitor-logs-basic-queries.md#azure-diagnostics-queries).

* Pour plus d’informations sur la création d’un paramètre de diagnostic à l’aide du portail Azure, de l’interface CLI ou de PowerShell, consultez l’article [Créer un paramètre de diagnostic pour collecter des journaux et métriques de plateforme dans Azure](../azure-monitor/essentials/diagnostic-settings.md).