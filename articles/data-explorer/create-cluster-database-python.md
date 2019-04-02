---
title: 'Démarrage rapide : Créer un cluster et une base de données Azure Data Explorer en utilisant Python'
description: Découvrir comment créer un cluster et une base de données Azure Data Explorer en utilisant Python
services: data-explorer
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/25/2019
ms.openlocfilehash: 24e482d223fec2c1f95d7cc964f62eac81c5de05
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58472577"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Créer un cluster et une base de données Azure Data Explorer en utilisant Python

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [INTERFACE DE LIGNE DE COMMANDE](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

Azure Data Explorer est un service d’analytique données rapide et complètement managé pour l’analyse en temps réel de gros volumes de données diffusées en continu par des applications, des sites web, des appareils IoT, etc. Pour utiliser Azure Data Explorer, créez tout d’abord un cluster et une ou plusieurs bases de données dans ce cluster. Ensuite, ingérez (chargez) des données dans une base de données pour pouvoir exécuter des requêtes dessus. Dans ce guide de démarrage rapide, vous créez un cluster et une base de données en utilisant Python.

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="install-python-package"></a>Installer le package Python

Pour installer le package Python pour Azure Data Explorer (Kusto), ouvrez une invite de commandes dont le chemin contient Python. Exécutez cette commande :

```
pip install azure-mgmt-kusto
```

## <a name="create-the-azure-data-explorer-cluster"></a>Créer le cluster Azure Data Explorer

1. Créez votre cluster avec la commande suivante :

    ```Python
    from azure.mgmt.kusto.kusto_management_client import KustoManagementClient
    from azure.mgmt.kusto.models import Cluster, AzureSku

    credentials = xxxxxxxxxxxxxxx
    
    subscription_id = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx'
    location = 'Central US'
    sku = 'D13_v2'
    capacity = 5
    resource_group_name = 'testrg'
    cluster_name = 'mykustocluster'
    cluster = Cluster(location=location, sku=AzureSku(name=sku, capacity=capacity))
    
    kustoManagementClient = KustoManagementClient(credentials, subscription_id)
    
    cluster_operations = kustoManagementClient.clusters
    
    cluster_operations.create_or_update(resource_group_name, cluster_name, cluster)
    ```

   |**Paramètre** | **Valeur suggérée** | **Description du champ**|
   |---|---|---|
   | nom_cluster | *mykustocluster* | Nom souhaité de votre cluster.|
   | sku | *D13_v2* | Référence SKU utilisée pour votre cluster. |
   | resource_group_name | *testrg* | Nom du groupe de ressources dans lequel sera créé le cluster. |

    Vous pouvez définir d’autres paramètres facultatifs, comme la capacité du cluster.
    
1. Définir [*vos informations d’identification*](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python)

1. Exécutez la commande suivante pour vérifier si votre cluster a bien été créé :

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

Si le résultat contient `provisioningState` avec la valeur `Succeeded`, alors le cluster a correctement été créé.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Créer la base de données dans le cluster Azure Data Explorer

1. Créez votre base de données avec la commande suivante :

    ```Python
    from azure.mgmt.kusto.models import Database
    from datetime import timedelta
    
    softDeletePeriod = timedelta(days=3650)
    hotCachePeriod = timedelta(days=3650)
    databaseName="mykustodatabase"
    
    database_operations = kusto_management_client.databases 
    _database = Database(location=location,
                        soft_delete_period=softDeletePeriod,
                        hot_cache_period=hotCachePeriod)
    
    database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName, parameters = _database)
    ```

   |**Paramètre** | **Valeur suggérée** | **Description du champ**|
   |---|---|---|
   | nom_cluster | *mykustocluster* | Nom du cluster dans lequel la base de données est créée.|
   | database_name | *mykustodatabase* | Nom de votre base de données.|
   | resource_group_name | *testrg* | Nom du groupe de ressources dans lequel sera créé le cluster. |
   | soft_delete_period | *3650 days, 0:00:00* | Durée pendant laquelle les données restent disponibles pour les requêtes. |
   | hot_cache_period | *3650 days, 0:00:00* | Durée pendant laquelle les données sont conservées dans le cache. |

1. Exécutez la commande suivante pour voir la base de données que vous avez créée :

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

Vous disposez maintenant d’un cluster et d’une base de données.

## <a name="clean-up-resources"></a>Supprimer des ressources

* Si vous envisagez de suivre nos autres tutoriels et guides de démarrage rapide, gardez les ressources que vous avez créées.
* Pour nettoyer les ressources, supprimez le cluster. Lorsque vous supprimez un cluster, cela supprime également toutes les bases de données qu’il contient. Utilisez la commande suivante pour supprimer votre cluster :

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Démarrage rapide : Ingérer des données à l’aide de la bibliothèque Python d’Azure Data Explorer](python-ingest-data.md)
