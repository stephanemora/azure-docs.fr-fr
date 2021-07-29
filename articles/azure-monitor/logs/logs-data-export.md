---
title: Exportation des données de l’espace de travail Log Analytics dans Azure Monitor (préversion)
description: L’exportation de données Log Analytics vous permet d’exporter en continu des données de tables sélectionnées de votre espace de travail Log Analytics vers un compte de stockage Azure ou Azure Event Hubs au fur et à mesure de leur collecte.
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
author: bwren
ms.author: bwren
ms.date: 05/07/2021
ms.openlocfilehash: 73cce13f296d65167ab2c45f677849b7f05f8b3a
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111410114"
---
# <a name="log-analytics-workspace-data-export-in-azure-monitor-preview"></a>Exportation des données de l’espace de travail Log Analytics dans Azure Monitor (préversion)
L’exportation des données de l’espace de travail Log Analytics dans Azure Monitor vous permet d’exporter en continu des données de tables sélectionnées dans votre espace de travail Log Analytics vers un compte de stockage Azure ou Azure Event Hubs à mesure qu’elles sont collectées. Cet article fournit des informations détaillées sur cette fonctionnalité et les étapes à suivre pour configurer l’exportation de données dans vos espaces de travail.

## <a name="overview"></a>Vue d’ensemble
Une fois que l’exportation de données est configurée pour votre espace de travail Log Analytics, toutes les nouvelles données envoyées aux tables sélectionnées dans l’espace de travail sont automatiquement exportées en objets blob ajoutés toutes les heures vers votre compte de stockage ou vers Event Hubs en temps quasi-réel.

![Vue d’ensemble de l’exportation de données](media/logs-data-export/data-export-overview.png)

Toutes les données des tables incluses sont exportées sans filtre. Par exemple, quand vous configurez une règle d’exportation de données pour table *SecurityEvent*, toutes les données envoyées à la table *SecurityEvent* sont exportées à compter de l’heure de configuration.


## <a name="other-export-options"></a>Autres options d’exportation
L’exportation des données d’espace de travail Log Analytics exporte en continu des données à partir d’un espace de travail Log Analytics. Les autres options d’exportation de données pour des scénarios particuliers sont les suivantes :

- Exportation planifiée à partir d’une requête de journal à l’aide d’une application logique. Cela est similaire à la fonctionnalité d’exportation de données, mais vous permet d’envoyer des données filtrées ou agrégées vers le stockage Azure. Cette méthode étant sujette aux [limites des requêtes de journal](../service-limits.md#log-analytics-workspaces), consultez [Archiver des données de l’espace de travail Log Analytics dans le Stockage Azure à l’aide d’une application logique](logs-export-logic-app.md).
- Exportation unique vers l’ordinateur local à l’aide d’un script PowerShell. Consultez [Invoke-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport).


## <a name="limitations"></a>Limites

- Actuellement, la configuration peut être effectuée à l’aide d’une interface CLI ou de requêtes REST. Le Portail Azure ou PowerShell ne sont pas encore pris en charge.
- L’option `--export-all-tables` dans l’interface CLI et REST n’est pas prise en charge et sera supprimée. Vous devez fournir explicitement la liste des tables dans les règles d’exportation.
- Les tables prises en charge sont actuellement limitées à celles qui sont propres à la section [tables prises en charge](#supported-tables) ci-dessous. Par exemple, les tables de journal personnalisées ne sont actuellement pas prises en charge.
- Si la règle d’exportation de données comprend une table non prise en charge, l’opération réussit, mais aucune donnée n’est exportée pour cette table tant qu’elle n’est pas prise en charge. 
- Si la règle d’exportation de données comprend une table qui n’existe pas, elle échoue avec l’erreur `Table <tableName> does not exist in the workspace`.
- L'exportation des données sera disponible dans toutes les régions, mais elle n'est actuellement pas disponible dans les régions suivantes : régions Azure Government, Japon Ouest, Brésil Sud-Est, Norvège Est, Norvège Ouest, Émirats arabes unis Nord, Émirats arabes unis Centre, Australie Centre 2, Suisse Nord, Suisse Ouest, Allemagne Centre-Ouest, Inde Sud, France Sud, Japon Ouest
- Vous pouvez définir l'activation d'un maximum de 10 règles dans votre espace de travail. Des règles supplémentaires sont autorisées mais à l'état désactivé. 
- La destination doit être unique pour toutes les règles d’exportation de votre espace de travail.
- Le compte de stockage de destination ou Event Hub doit se trouver dans la même région que l’espace de travail Log Analytics.
- Les noms des tables à exporter ne peuvent pas dépasser 60 caractères pour un compte de stockage et 47 caractères pour un Event Hub. Les tables avec des noms plus longs ne seront pas exportées.

## <a name="data-completeness"></a>Exhaustivité des données
L’exportation de données continuera de réessayer d’envoyer des données pendant 30 minutes au cas où la destination est indisponible. Si elle n’est toujours pas disponible après 30 minutes, les données seront ignorées jusqu’à ce que la destination devienne disponible.

## <a name="cost"></a>Coût
Il n’y a actuellement pas de frais supplémentaires pour la fonctionnalité d’exportation de données. La tarification de l’exportation des données sera annoncée à l’avenir et un avis sera envoyé avant le début de la facturation. Si vous choisissez de continuer à utiliser l’exportation de données après la période de préavis, vous serez facturé au tarif applicable.

## <a name="export-destinations"></a>Destinations d’exportation

### <a name="storage-account"></a>Compte de stockage
Les données sont envoyées aux comptes de stockage à mesure qu’elles atteignent Azure Monitor, puis elles sont stockées dans des objets blob ajoutés toutes les heures. La configuration de l’exportation de données crée un conteneur pour chaque table du compte de stockage portant le nom *am-* suivi du nom de la table. Par exemple, la table *SecurityEvent* serait envoyée à un conteneur nommé *am-SecurityEvent*.

Le chemin d'accès à l’objet Blob du compte de stockage est *WorkspaceResourceId=/subscriptions/subscription-id/resourcegroups/\<resource-group\>/providers/microsoft.operationalinsights/workspaces/\<workspace\>/y=\<four-digit numeric year\>/m=\<two-digit numeric month\>/d=\<two-digit numeric day\>/h=\<two-digit 24-hour clock hour\>/m=00/PT1H.json*. Étant donné que les objets Blob d’ajout sont limités à 50 000 écritures dans le stockage, le nombre d’objets Blob exportés peut s’étendre si le nombre d’ajouts est élevé. Dans ce cas, le modèle d’affectation de noms pour les objets Blob serait PT1H_#.json, où # est le nombre d’objets Blob incrémenté.

Le format des données du compte de stockage est en [lignes JSON](../essentials/resource-logs-blob-format.md). Cela signifie que chaque enregistrement est délimité par une nouvelle ligne, sans tableau d’enregistrements extérieurs ni virgule entre les enregistrements JSON. 

[![Exemple de données de stockage](media/logs-data-export/storage-data.png)](media/logs-data-export/storage-data.png#lightbox)

L’exportation de données Log Analytics peut écrire des objets Blob d’ajout à des comptes de stockage immuables lorsque le paramètre *allowProtectedAppendWrites* est activé sur les stratégies de rétention temporelles. Cela permet l’écriture de nouveaux blocs dans un objet blob d’ajout tout en conservant la protection et la conformité de l’immuabilité. Consultez [Autoriser les écritures protégées d’objets blob d’ajout](../../storage/blobs/storage-blob-immutable-storage.md#allow-protected-append-blobs-writes).

### <a name="event-hub"></a>Event Hub
Les données sont envoyées à votre Event Hub quasiment en temps réel à mesure qu’elles atteignent Azure Monitor. Un Event Hub est créé pour chaque type de données que vous exportez avec le nom *am-* suivi du nom de la table. Par exemple, la table *SecurityEvent* serait envoyée à un Event Hub nommé *am-SecurityEvent*. Si vous souhaitez que les données exportées atteignent un Event Hub spécifique, ou si vous avez une table avec un nom qui dépasse la limite de 47 caractères, vous pouvez fournir votre propre nom Event Hub et y exporter toutes les données pour les tables définies.

> [!IMPORTANT]
> Le [nombre d’Event Hubs pris en charge par niveaux d’espaces de noms « De base » et « Standard » est de 10](../../event-hubs/event-hubs-quotas.md#common-limits-for-all-tiers). Si vous exportez plus de 10 tables, fractionnez les tables entre plusieurs règles d’exportation vers différents espaces de noms d’Event Hubs, ou indiquez le nom de l’Event Hub dans la règle d’exportation et exportez toutes les tables vers cet Event Hub.

Considérations :
1. La référence SKU de l’Event Hub « De base » prend en charge une [limite](../../event-hubs/event-hubs-quotas.md#basic-vs-standard-vs-premium-vs-dedicated-tiers) de taille d’événement inférieure, et certains journaux de votre espace de travail peuvent dépasser cette taille et être supprimés. Nous vous recommandons d’utiliser un Event Hub « Standard » ou « Dédié » comme destination de l’exportation.
2. Le volume des données exportées augmente souvent dans le temps, et la mise à l’échelle du Event Hub doit être augmentée pour gérer des taux de transfert plus importants et éviter les scénarios de limitation et de latence des données. Vous devez utiliser la fonctionnalité de majoration automatique d’Event Hubs pour augmenter ou diminuer automatiquement le nombre d’unités de débit pour répondre aux besoins d’utilisation. Pour plus d’informations, consultez [Mettre automatiquement à l’échelle les unités de débit Azure Event Hubs](../../event-hubs/event-hubs-auto-inflate.md).

## <a name="prerequisites"></a>Prérequis
Voici les conditions préalables qui doivent être remplies avant de configurer l’exportation de données Log Analytics :

- Les destinations doivent être créées avant la configuration de la règle d’exportation et doivent se trouver dans la même région que votre espace de travail Log Analytics. Si vous devez répliquer vos données vers d’autres comptes de stockage, vous pouvez utiliser l’une des [options de redondance du stockage Azure](../../storage/common/storage-redundancy.md).  
- Le compte de stockage doit être StorageV1 ou StorageV2. Le stockage classique n’est pas pris en charge  
- Si vous avez configuré votre compte de stockage pour autoriser l’accès à partir de réseaux sélectionnés, vous devez ajouter une exception dans les paramètres de votre compte de stockage pour permettre à Azure Monitor d’écrire dans votre stockage.

## <a name="enable-data-export"></a>Activer l’exportation de données
Les étapes suivantes doivent être effectuées pour permettre l’exportation de données Log Analytics. Pour plus d’informations sur chacune d’elles, consultez les sections suivantes.

- Inscrivez un fournisseur de ressources.
- Autorisez les services Microsoft approuvés.
- Créez une ou plusieurs règles d’exportation de données qui définissent les tables à exporter et leur destination.

### <a name="register-resource-provider"></a>S’inscrire auprès du fournisseur de ressources
Le fournisseur de ressources Azure suivant doit être inscrit pour votre abonnement pour permettre l’exportation de données Log Analytics. 

- Microsoft.Insights

Ce fournisseur de ressources sera probablement déjà inscrit pour la plupart des utilisateurs Azure Monitor. Pour vérifier, accédez à **Abonnements** dans le Portail Azure. Sélectionnez votre abonnement, puis cliquez sur **Fournisseurs de ressources** dans la section **Paramètres** du menu. Recherchez **Microsoft.Insights**. Si son état est **Inscrit**, il est déjà inscrit. Si ce n’est pas le cas, cliquez sur **Inscrire** pour l’inscrire.

Vous pouvez également utiliser une des méthodes disponibles pour inscrire un fournisseur de ressources, comme décrit dans [Fournisseurs et types de ressources Azure](../../azure-resource-manager/management/resource-providers-and-types.md). Voici un exemple de commande utilisant PowerShell :

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.insights
```

### <a name="allow-trusted-microsoft-services"></a>Autoriser les services Microsoft approuvés
Si vous avez configuré votre compte de stockage pour autoriser l’accès à partir de réseaux sélectionnés, vous devez ajouter une exception pour autoriser Azure Monitor à écrire dans le compte. À partir de **Pare-feux et réseaux virtuels** pour votre compte de stockage, sélectionnez **Autoriser les services Microsoft approuvés à accéder à ce compte de stockage**.

[![Pare-feux de compte de stockage et réseaux virtuels](media/logs-data-export/storage-account-vnet.png)](media/logs-data-export/storage-account-vnet.png#lightbox)

### <a name="create-or-update-data-export-rule"></a>Créer ou mettre à jour une règle d’exportation de données
Une règle d’exportation de données définit les tables pour lesquelles les données sont exportées et la destination. Vous pouvez avoir 10 règles activées dans votre espace de travail alors que toute règle supplémentaire au-delà de 10 doit être à l’état désactivé. Une destination doit être unique pour toutes les règles d’exportation de votre espace de travail.

> [!NOTE]
> L’exportation de données envoie les journaux vers des destinations dont vous êtes propriétaire, mais celles-ci ont certaines limites : [scalabilité des comptes de stockage](../../storage/common/scalability-targets-standard-account.md#scale-targets-for-standard-storage-accounts), [quota d’espaces de noms d’Event Hubs](../../event-hubs/event-hubs-quotas.md). Il est recommandé de surveiller les destinations et d'appliquer des mesures lorsqu'elles se rapprochent de leur limite. Par exemple : 
> - Définissez la fonctionnalité de majoration automatique dans l’Event Hub pour effectuer un scale-up automatiquement et augmenter le nombre d’unités de débit (TU). Vous pouvez demander plus d’unités de débit lorsque la majoration automatique est au maximum.
> - Fractionnement des tables en plusieurs règles d’exportation, chacune d’entre elles étant destinée à des destinations différentes

La règle d’exportation doit inclure les tables que vous avez dans votre espace de travail. Exécutez cette requête pour obtenir la liste des tables disponibles dans votre espace de travail.

```kusto
find where TimeGenerated > ago(24h) | distinct Type
```

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

N/A

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/A

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilisez la commande suivante pour créer une règle d’exportation de données vers un compte de stockage à l’aide de l’interface CLI.

```azurecli
$storageAccountResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $storageAccountResourceId
```

Utilisez la commande suivante pour créer une règle d’exportation de données vers un Event Hub à l’aide de l’interface CLI. Un Event Hub distinct est créée pour chaque table.

```azurecli
$eventHubsNamespacesResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubsNamespacesResourceId
```

Utilisez la commande suivante pour créer une règle d’exportation de données vers un Event Hub à l’aide de l’interface de ligne de commande. Toutes les tables sont exportées vers le nom d’Event Hub fourni. 

```azurecli
$eventHubResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name/eventhubs/eventhub-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubResourceId
```

# <a name="rest"></a>[REST](#tab/rest)

Utilisez la requête suivante pour créer une règle d’exportation de données à l’aide de l’API REST. La demande doit utiliser l’autorisation du jeton du porteur et le type de contenu application/json.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

Le corps de la requête spécifie la destination des tables. Voici un exemple de corps pour la requête REST pour un compte de stockage.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
            "table1",
            "table2" 
        ],
        "enable": true
    }
}
```

Voici un exemple de corps pour la requête REST pour Event Hub.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
}
```

Voici un exemple de corps pour la requête REST pour Event Hub pour laquelle un nom d’Event Hub est fourni. Dans ce cas, toutes les données exportées sont envoyées à cet Event Hub.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name",
            "metaData": {
                "EventHubName": "eventhub-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
  }
}
```

# <a name="template"></a>[Modèle](#tab/json)

Utilisez la commande suivante pour créer une règle d’exportation de données vers un compte de stockage à l’aide d’un modèle.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "storageAccountRuleName": {
            "defaultValue": "storage-account-rule-name",
            "type": "string"
        },
        "storageAccountResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
                {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/' , parameters('storageAccountRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('storageAccountResourceId')]"
                      },
                      "tableNames": [
                          "Heartbeat",
                          "InsightsMetrics",
                          "VMConnection",
                          "Usage"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

Utilisez la commande suivante pour créer une règle d’exportation de données vers un Event Hub à l’aide d’un modèle. Un Event Hub distinct est créée pour chaque table.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "eventhubRuleName": {
            "defaultValue": "event-hub-rule-name",
            "type": "string"
        },
        "namespacesResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/microsoft.eventhub/namespaces/namespaces-name",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
              {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/', parameters('eventhubRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('namespacesResourceId')]"
                      },
                      "tableNames": [
                          "Usage",
                          "Heartbeat"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

Utilisez la commande suivante pour créer une règle d’exportation de données vers un Event Hub spécifique à l’aide d’un modèle. Toutes les tables sont exportées vers le nom d’Event Hub fourni.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "eventhubRuleName": {
            "defaultValue": "event-hub-rule-name",
            "type": "string"
        },
        "namespacesResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/microsoft.eventhub/namespaces/namespaces-name",
            "type": "String"
        },
        "eventhubName": {
            "defaultValue": "event-hub-name",
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
              {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/', parameters('eventhubRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('namespacesResourceId')]",
                          "metaData": {
                              "eventHubName": "[parameters('eventhubName')]"
                          }
                      },
                      "tableNames": [
                          "Usage",
                          "Heartbeat"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

---

## <a name="view-data-export-rule-configuration"></a>Afficher la configuration de règle d’exportation de données

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

N/A

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/A

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilisez la commande suivante pour afficher la configuration d’une règle d’exportation de données à l’aide de l’interface CLI.

```azurecli
az monitor log-analytics workspace data-export show --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

Utilisez la requête suivante pour afficher la configuration d’une règle d’exportation de données à l’aide de l’API REST. La requête doit utiliser l’autorisation du jeton du porteur.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

# <a name="template"></a>[Modèle](#tab/json)

N/A

---

## <a name="disable-an-export-rule"></a>Désactiver une règle d’exportation

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

N/A

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/A

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Les règles d’exportation peuvent être désactivées pour vous permettre d’arrêter l’exportation lorsque vous n’avez pas besoin de conserver des données pendant un certain temps, par exemple lorsque des tests sont en cours. Utilisez la commande suivante pour désactiver une règle d’exportation de données à l’aide de l’interface CLI.

```azurecli
az monitor log-analytics workspace data-export update --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --enable false
```

# <a name="rest"></a>[REST](#tab/rest)

Les règles d’exportation peuvent être désactivées pour vous permettre d’arrêter l’exportation lorsque vous n’avez pas besoin de conserver des données pendant un certain temps, par exemple lorsque des tests sont en cours. Utilisez la requête suivante pour désactiver une règle d’exportation de données à l’aide de l’API REST. La requête doit utiliser l’autorisation du jeton du porteur.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
"table1",
    "table2" 
        ],
        "enable": false
    }
}
```

# <a name="template"></a>[Modèle](#tab/json)

Les règles d’exportation peuvent être désactivées pour vous permettre d’arrêter l’exportation lorsque vous n’avez pas besoin de conserver des données pendant un certain temps, par exemple lorsque des tests sont en cours. Dans le modèle, définissez ```"enable": false``` pour désactiver une exportation de données.

---

## <a name="delete-an-export-rule"></a>Supprimer une règle d’exportation

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

N/A

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/A

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilisez la commande suivante pour supprimer une règle d’exportation de données à l’aide de l’interface CLI.

```azurecli
az monitor log-analytics workspace data-export delete --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

Utilisez la requête suivante pour supprimer une règle d’exportation de données à l’aide de l’API REST. La requête doit utiliser l’autorisation du jeton du porteur.

```rest
DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

# <a name="template"></a>[Modèle](#tab/json)

N/A

---

## <a name="view-all-data-export-rules-in-a-workspace"></a>Afficher toutes les règles d’exportation de données dans un espace de travail

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

N/A

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/A

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilisez la commande suivante pour afficher toutes les règles d’exportation de données dans un espace de travail à l’aide de l’interface CLI.

```azurecli
az monitor log-analytics workspace data-export list --resource-group resourceGroupName --workspace-name workspaceName
```

# <a name="rest"></a>[REST](#tab/rest)

Utilisez la requête suivante pour afficher toutes les règles d’exportation de données dans un espace de travail à l’aide de l’API REST. La requête doit utiliser l’autorisation du jeton du porteur.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports?api-version=2020-08-01
```

# <a name="template"></a>[Modèle](#tab/json)

N/A

---

## <a name="unsupported-tables"></a>Tables non prises en charge
Si la règle d’exportation de données comprend une table non prise en charge, la configuration échoue, mais aucune donnée n’est exportée pour cette table. Si la table est prise en charge par la suite, ses données seront exportées à ce moment-là.

Si la règle d’exportation de données inclut une table qui n’existe pas, elle échoue avec l’erreur « La table <tableName> n’existe pas dans l’espace de travail ».


## <a name="supported-tables"></a>Tables prises en charge
Les tables prises en charge sont actuellement limitées à celles spécifiées ci-dessous. Toutes les données de la table sont exportées, sauf si des restrictions sont spécifiées. Cette liste sera mise à jour à mesure que des tables supplémentaires sont ajoutées.

| Table de charge de travail | Limites |
|:---|:---|
| AACAudit |  |
| AACHttpRequest |  |
| AADDomainServicesAccountLogon |  |
| AADDomainServicesAccountManagement |  |
| AADDomainServicesDirectoryServiceAccess |  |
| AADDomainServicesLogonLogoff |  |
| AADDomainServicesPolicyChange |  |
| AADDomainServicesPrivilegeUse |  |
| AADManagedIdentitySignInLogs |  |
| AADNonInteractiveUserSignInLogs |  |
| AADProvisioningLogs |  |
| AADServicePrincipalSignInLogs |  |
| ABSBotRequests |  |
| ACSBillingUsage |  |
| ACSChatIncomingOperations |  |
| ACSSMSIncomingOperations |  |
| ADAssessmentRecommendation |  |
| ADFActivityRun |  |
| ADFPipelineRun |  |
| ADFSSignInLogs |  |
| ADFTriggerRun |  |
| ADPAudit |  |
| ADPRequests |  |
| ADReplicationResult |  |
| ADSecurityAssessmentRecommendation |  |
| ADTDigitalTwinsOperation |  |
| ADTEventRoutesOperation |  |
| ADTModelsOperation |  |
| ADTQueryOperation |  |
| ADXCommand |  |
| ADXQuery |  |
| AegDeliveryFailureLogs |  |
| AegPublishFailureLogs |  |
| Alerte |  |
| ApiManagementGatewayLogs |  |
| AppCenterError |  |
| AppPlatformSystemLogs |  |
| AppServiceAppLogs |  |
| AppServiceAuditLogs |  |
| AppServiceConsoleLogs |  |
| AppServiceFileAuditLogs |  |
| AppServiceHTTPLogs |  |
| AppServicePlatformLogs |  |
| AuditLogs |  |
| AutoscaleEvaluationsLog |  |
| AutoscaleScaleActionsLog |  |
| AWSCloudTrail |  |
| AzureAssessmentRecommendation |  |
| AzureDevOpsAuditing |  |
| BehaviorAnalytics |  |
| BlockchainApplicationLog |  |
| BlockchainProxyLog |  |
| CDBControlPlaneRequests |  |
| CDBDataPlaneRequests |  |
| CDBMongoRequests |  |
| CDBPartitionKeyRUConsumption |  |
| CDBPartitionKeyStatistics |  |
| CDBQueryRuntimeStatistics |  |
| CommonSecurityLog |  |
| ComputerGroup |  |
| ConfigurationData | Prise en charge partielle : Certaines des données sont ingérées par le biais de services internes qui ne sont pas pris en charge pour l’exportation. Cette partie est manquante dans l’exportation actuellement. |
| ContainerImageInventory |  |
| ContainerInventory |  |
| ContainerLog |  |
| ContainerLogV2 |  |
| ContainerNodeInventory |  |
| ContainerServiceLog |  |
| CoreAzureBackup |  |
| DatabricksAccounts |  |
| DatabricksClusters |  |
| DatabricksDBFS |  |
| DatabricksInstancePools |  |
| DatabricksJobs |  |
| DatabricksNotebook |  |
| DatabricksSecrets |  |
| DatabricksSQLPermissions |  |
| DatabricksSSH |  |
| DatabricksWorkspace |  |
| DeviceFileEvents |  |
| DeviceNetworkInfo |  |
| DeviceNetworkInfo |  |
| DeviceProcessEvents |  |
| DeviceRegistryEvents |  |
| DnsEvents |  |
| DnsInventory |  |
| DummyHydrationFact |  |
| Dynamics365Activity |  |
| EmailAttachmentInfo |  |
| EmailEvents |  |
| EmailUrlInfo |  |
| Événement | Prise en charge partielle : les données provenant de l'agent Log Analytics (MMA) ou de l'agent Azure Monitor (AMA) sont entièrement prises en charge lors de l'exportation. Les données arrivant via l'agent Diagnostics Extension sont collectées par le biais du stockage alors que ce chemin n'est pas pris en charge lors de l'exportation. |
| ExchangeAssessmentRecommendation |  |
| FailedIngestion |  |
| FunctionAppLogs |  |
| HDInsightAmbariClusterAlerts |  |
| HDInsightAmbariSystemMetrics |  |
| HDInsightHadoopAndYarnLogs |  |
| HDInsightHadoopAndYarnMetrics |  |
| HDInsightHiveAndLLAPLogs |  |
| HDInsightHiveAndLLAPMetrics |  |
| HDInsightHiveTezAppStats |  |
| HDInsightOozieLogs |  |
| HDInsightSecurityLogs |  |
| HDInsightSparkApplicationEvents |  |
| HDInsightSparkBlockManagerEvents |  |
| HDInsightSparkEnvironmentEvents |  |
| HDInsightSparkExecutorEvents |  |
| HDInsightSparkJobEvents |  |
| HDInsightSparkLogs |  |
| HDInsightSparkSQLExecutionEvents |  |
| HDInsightSparkStageEvents |  |
| HDInsightSparkStageTaskAccumulables |  |
| HDInsightSparkTaskEvents |  |
| Heartbeat |  |
| HuntingBookmark |  |
| InsightsMetrics | Prise en charge partielle : Certaines des données sont ingérées par le biais de services internes qui ne sont pas pris en charge pour l’exportation. Cette partie est manquante dans l’exportation actuellement. |
| IntuneAuditLogs |  |
| IntuneDevices |  |
| IntuneOperationalLogs |  |
| KubeEvents |  |
| KubeHealth |  |
| KubeMonAgentEvents |  |
| KubeNodeInventory |  |
| KubePodInventory |  |
| KubeServices |  |
| LAQueryLogs |  |
| McasShadowItReporting |  |
| MicrosoftAzureBastionAuditLogs |  |
| MicrosoftDataShareReceivedSnapshotLog |  |
| MicrosoftDataShareSentSnapshotLog |  |
| MicrosoftDataShareShareLog |  |
| MicrosoftHealthcareApisAuditLogs |  |
| NWConnectionMonitorPathResult |  |
| NWConnectionMonitorTestResult |  |
| OfficeActivity | Prise en charge partielle : Certaines des données sont ingérées via des webhooks d’O365 vers LA. Cette partie est manquante dans l’exportation actuellement. |
| Opération | Prise en charge partielle : Certaines des données sont ingérées par le biais de services internes qui ne sont pas pris en charge pour l’exportation. Cette partie est manquante dans l’exportation actuellement. |
| Perf | Prise en charge partielle : Seules les données de performances Windows sont actuellement prises en charge. Les données de performances Linux sont actuellement manquantes dans l’exportation. |
| PowerBIDatasetsWorkspace |  |
| PurviewScanStatusLogs |  |
| SCCMAssessmentRecommendation |  |
| SCOMAssessmentRecommendation |  |
| SecurityAlert |  |
| SecurityBaseline |  |
| SecurityBaselineSummary |  |
| SecurityCef |  |
| SecurityDetection |  |
| SecurityEvent | Prise en charge partielle : les données provenant de l'agent Log Analytics (MMA) ou de l'agent Azure Monitor (AMA) sont entièrement prises en charge lors de l'exportation. Les données arrivant via l'agent Diagnostics Extension sont collectées par le biais du stockage alors que ce chemin n'est pas pris en charge lors de l'exportation. |
| SecurityIncident |  |
| SecurityIoTRawEvent |  |
| SecurityNestedRecommendation |  |
| SecurityRecommendation |  |
| SentinelHealth |  |
| SfBAssessmentRecommendation |  |
| SfBOnlineAssessmentRecommendation |  |
| SharePointOnlineAssessmentRecommendation |  |
| SignalRServiceDiagnosticLogs |  |
| SigninLogs |  |
| SPAssessmentRecommendation |  |
| SQLAssessmentRecommendation |  |
| SucceededIngestion |  |
| SynapseBigDataPoolApplicationsEnded |  |
| SynapseBuiltinSqlPoolRequestsEnded |  |
| SynapseGatewayApiRequests |  |
| SynapseIntegrationActivityRuns |  |
| SynapseIntegrationPipelineRuns |  |
| SynapseIntegrationTriggerRuns |  |
| SynapseRbacOperations |  |
| SynapseSqlPoolDmsWorkers |  |
| SynapseSqlPoolExecRequests |  |
| SynapseSqlPoolRequestSteps |  |
| SynapseSqlPoolSqlRequests |  |
| SynapseSqlPoolWaits |  |
| syslog | Prise en charge partielle : les données provenant de l'agent Log Analytics (MMA) ou de l'agent Azure Monitor (AMA) sont entièrement prises en charge lors de l'exportation. Les données arrivant via l'agent Diagnostics Extension sont collectées par le biais du stockage alors que ce chemin n'est pas pris en charge lors de l'exportation. |
| ThreatIntelligenceIndicator |  |
| Update | Prise en charge partielle : Certaines des données sont ingérées par le biais de services internes qui ne sont pas pris en charge pour l’exportation. Cette partie est manquante dans l’exportation actuellement. |
| UpdateRunProgress |  |
| UpdateSummary |  |
| Utilisation |  |
| UserAccessAnalytics |  |
| UserPeerAnalytics |  |
| Liste de surveillance |  |
| WindowsEvent |  |
| WindowsFirewall |  |
| WireData | Prise en charge partielle : Certaines des données sont ingérées par le biais de services internes qui ne sont pas pris en charge pour l’exportation. Cette partie est manquante dans l’exportation actuellement. |
| WorkloadDiagnosticLogs |  |
| WVDAgentHealthStatus |  |
| WVDCheckpoints |  |
| WVDConnections |  |
| WVDErrors |  |
| WVDFeeds |  |
| WVDManagement |  |

## <a name="next-steps"></a>Étapes suivantes

- [Interrogez les données exportées à partir d’Azure Data Explorer](../logs/azure-data-explorer-query-storage.md).
