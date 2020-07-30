---
title: Comptes de stockage appartenant au client pour l’ingestion des journaux
description: Utilisez votre propre compte de stockage pour l’ingestion des données de journal dans un espace de travail Log Analytics dans Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/20/2020
ms.openlocfilehash: a14f7ca3e5a7b291e430db6ea536edc5396b5448
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87318893"
---
# <a name="customer-owned-storage-accounts-for-log-ingestion-in-azure-monitor"></a>Comptes de stockage appartenant au client pour l’ingestion des journaux dans Azure Monitor

Azure Monitor utilise des comptes de stockage dans le processus d’ingestion de certains types de données tels que des [journaux personnalisés](data-sources-custom-logs.md) et certains [journaux Azure](./diagnostics-extension-logs.md). Pendant le processus d’ingestion, les journaux sont d’abord envoyés à un compte de stockage puis ingérés dans Log Analytics ou Application Insights. Si vous souhaitez contrôler vos données lors de l’ingestion, vous pouvez utiliser vos propres comptes de stockage au lieu du stockage géré par le service. L’utilisation de votre propre compte de stockage vous permet de contrôler l’accès, le contenu, le chiffrement et la conservation des journaux lors de l’ingestion. Nous appelons cela « Bring Your Own Storage », ou BYOS. 

Un scénario qui requiert BYOS est l’isolement réseau via des liaisons privés. Lorsque vous utilisez un réseau virtuel, l’isolement réseau est souvent une exigence et l’accès à l’Internet public est limité. Dans ce cas, l’accès au stockage du service Azure Monitor pour l’ingestion de journaux est soit complètement bloqué, soit considéré comme une mauvaise pratique. Au lieu de cela, les journaux doivent être ingérés par le bais d’un compte de stockage appartenant au client au sein du réseau virtuel ou facilement accessible à partir de celui-ci.

Le chiffrement des journaux avec des clés gérées par le client (CMK) est un autre scénario. Les clients peuvent chiffrer les données journalisées à l’aide de CMK sur les clusters qui stockent les journaux. La même clé peut également être utilisée pour chiffrer des journaux pendant le processus d’ingestion.

## <a name="data-types-supported"></a>Types de données prises en charge

Les types de données qui sont ingérés à partir d’un compte de stockage sont les suivants. Pour plus d’informations sur l’ingestion de ces types, consultez [Collecter des données de l’extension de diagnostic Azure et les enregistrer dans des journaux d’activité Azure Monitor](./diagnostics-extension-logs.md).

| Type | Informations sur les tables |
|:-----|:------------------|
| Journaux d’activité IIS | Blob : wad-iis-logfiles|
|Journaux des événements Windows | Tableau : WADWindowsEventLogsTable |
| syslog | Tableau : LinuxsyslogVer2v0 |
| Journaux d’activité de suivi des événements ETW Windows | Tableau : WADETWEventTable|
| Service Fabric | Tableau : WADServiceFabricSystemEventTable <br/> WADServiceFabricReliableActorEventTable<br/> WADServiceFabricReliableServicEventTable |
| Journaux d’activité personnalisés | n/a |
| Fichiers d’image mémoire Watson Azure Security Center | n/a|  

## <a name="storage-account-requirements"></a>Conditions requises pour le compte de stockage 
Le compte de stockage doit remplir les conditions suivantes :

- Accessible aux ressources de votre réseau virtuel qui écrivent des journaux dans le stockage.
- Doit se trouver dans la même région que l’espace de travail auquel il est lié.
- Autoriser l’accès Azure Monitor - Si vous choisissez de limiter l’accès à votre compte de stockage pour sélectionner des réseaux, veillez à autoriser cette exception : *Autoriser les services Microsoft approuvés à accéder à ce compte de stockage*.

## <a name="process-to-configure-customer-owned-storage"></a>Processus de configuration du stockage appartenant au client
Le processus de base pour utiliser votre propre compte de stockage pour l’ingestion est le suivant :

1. Créez un compte de stockage ou sélectionnez un compte existant.
2. Liez le compte de stockage à un espace de travail Log Analytics.
3. Gérez le stockage en examinant sa charge et sa rétention pour vous assurer qu’il fonctionne comme prévu.

La seule méthode disponible pour créer et supprimer des liaisons consiste à utiliser l’API REST. Des détails sur la requête d’API spécifique requise pour chaque processus sont fournis dans les sections ci-dessous.

## <a name="command-line-and-rest-api"></a>Ligne de commande et API REST

### <a name="command-line"></a>Ligne de commande
Pour créer et gérer des comptes de stockage liés, utilisez [az monitor log-analytics workspace linked-storage](/cli/azure/monitor/log-analytics/workspace/linked-storage). Cette commande permet de lier et de dissocier les comptes de stockage d’un espace de travail et de répertorier les comptes de stockage liés.

### <a name="request-and-cli-values"></a>Valeurs de la requête et de l’interface CLI

#### <a name="datasourcetype"></a>dataSourceType 

- AzureWatson : utilisez cette valeur pour les fichiers d’image mémoire Azure Watson Azure Security Center.
- CustomLogs : utilisez cette valeur pour les types de données suivants :
  - Journaux d’activité personnalisés
  - Journaux d’activité IIS
  - Événements (Windows)
  - Syslog (Linux)
  - Journaux ETW
  - Événements Service Fabric
  - Données d’évaluation  

#### <a name="storage_account_resource_id"></a>storage_account_resource_id
Cette valeur utilise la structure suivante :

```
subscriptions/{subscriptionId}/resourcesGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName1}
```


### <a name="get-linked-storage-accounts-for-all-data-source-types"></a>Obtenir les comptes de stockage liés pour tous les types de source de données

#### <a name="api-request"></a>Requête d’API

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts?api-version=2019-08-01-preview  
```

#### <a name="response"></a>response

```json
{
    [
        {
            "properties":
            {
                "dataSourceType": "CustomLogs",
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_1>",
                    "<storage_account_resource_id_2>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
            "name": "CustomLogs",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        },
        {
            "properties":
            {
                "dataSourceType": " AzureWatson "
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_3>",
                    "<storage_account_resource_id_4>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/AzureWatson",
            "name": "AzureWatson",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        }
    ]
}
```


### <a name="get-linked-storage-accounts-for-a-specific-data-source-type"></a>Obtenir les comptes de stockage liés pour un type spécifique de source de données

#### <a name="api-request"></a>Requête d’API

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

#### <a name="response"></a>response 

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs",
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
    "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
    "name": "CustomLogs",
    "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```

## <a name="create-or-modify-a-link"></a>Créer ou modifier une liaison

Une fois que vous avez lié un compte de stockage à un espace de travail, Log Analytics commencera à l’utiliser au lieu du compte de stockage détenu par le service. Vous pouvez inscrire une liste de comptes de stockage en même temps, et vous pouvez utiliser le même compte de stockage pour plusieurs espaces de travail.

Si votre espace de travail traite à la fois des ressources de réseau virtuel et des ressources externes à un réseau virtuel, vous devez vous assurer qu’il ne rejette pas le trafic provenant d’Internet. Votre stockage doit avoir les mêmes paramètres que votre espace de travail et être mis à la disposition des ressources externes à votre réseau virtuel. 

### <a name="api-request"></a>Requête d’API

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

### <a name="payload"></a>Payload

```json
{
    "properties":
    {
        "storageAccountIds  " : 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    }
}
```

### <a name="response"></a>response

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs"
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
"id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
"name": "CustomLogs",
"type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```


## <a name="unlink-a-storage-account"></a>Dissocier un compte de stockage
Si vous décidez de cesser d’utiliser un compte de stockage pour l’ingestion ou de remplacer l’espace de travail que vous utilisez, vous devez dissocier le compte de stockage de l’espace de travail.

La dissociation de tous les comptes de stockage d’un espace de travail signifie que l’ingestion tentera de se tourner vers les comptes de stockage gérés par le service. Si vos agents s’exécutent sur un réseau virtuel avec un accès limité à Internet, l’ingestion est supposée échouer. L’espace de travail doit avoir un compte de stockage lié qui est accessible à partir de vos ressources analysées.

Avant de supprimer un compte de stockage, vous devez vous assurer que toutes les données qu’il contient ont été ingérées dans l’espace de travail. Par précaution, gardez votre compte de stockage disponible après avoir lié un autre stockage. Supprimez-le uniquement une fois que tout son contenu a été ingéré, et vous pouvez voir que les nouvelles données sont écrites dans le compte de stockage qui vient d’être connecté.


### <a name="api-request"></a>Requête d’API
```
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

## <a name="replace-a-storage-account"></a>Remplacer un compte de stockage

Pour remplacer un compte de stockage utilisé pour l’ingestion, commencez par créer une liaison pour un nouveau compte de stockage. Les agents de journalisation recevront la configuration mise à jour et commenceront également à envoyer des données vers le nouveau stockage.

Ensuite, dissociez l’ancien compte de stockage pour que les agents cessent d’écrire dans le compte supprimé. Le processus d’ingestion continue de lire les données de ce compte jusqu’à ce qu’elles soient toutes ingérées. Ne supprimez pas le compte de stockage tant que tous les journaux n’ont pas été ingérés.

La configuration de l’agent sera actualisée après quelques minutes, et les journaux seront basculés vers le nouveau stockage.

## <a name="manage-storage-account"></a>Gérer un compte de stockage

### <a name="load"></a>Load

Les comptes de stockage peuvent traiter une certaine charge de demandes de lecture et d’écriture avant de commencer à les limiter. La limitation a une incidence sur le temps nécessaire à l’ingestion des journaux et peut entraîner la perte de données. Si votre stockage est surchargé, inscrivez des comptes de stockage supplémentaires et répartissez la charge entre eux. 

### <a name="related-charges"></a>Frais connexes

Les comptes de stockage sont facturés en fonction du volume de données stockées, des types de stockage et du type de redondance. Pour plus d’informations, consultez [Tarification des objets blob de blocs](https://azure.microsoft.com/pricing/details/storage/blobs/) et [Tarification de Stockage Table](https://azure.microsoft.com/pricing/details/storage/tables/).

Si le compte de stockage inscrit de votre espace de travail se trouve dans une autre région, la sortie vous sera facturée selon ces [détails de tarification de la bande passante](https://azure.microsoft.com/pricing/details/bandwidth/).



## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la configuration d’une liaison privée, consultez [Utiliser Azure Private Link pour connecter en toute sécurité des réseaux à Azure Monitor](private-link-security.md).

