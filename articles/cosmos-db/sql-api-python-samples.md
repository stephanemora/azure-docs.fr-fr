---
title: Exemples Python d’API SQL pour Azure Cosmos DB
description: Recherchez des exemples Python sur GitHub pour les tâches courantes dans Azure Cosmos DB, y compris les opérations CRUD.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 05/20/2020
ms.author: sngun
ms.custom: tracking-python
ms.openlocfilehash: c52651e15f648bede419501f516392acbc266e8a
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84558008"
---
# <a name="azure-cosmos-db-python-examples"></a>Exemples Python pour Azure Cosmos DB

> [!div class="op_single_selector"]
> * [Exemples du kit SDK .NET V2](sql-api-dotnet-samples.md)
> * [Exemples du kit SDK .NET V3](sql-api-dotnet-v3sdk-samples.md)
> * [Exemples du kit SDK Java V4](sql-api-java-sdk-samples.md)
> * [Exemples Node.js](sql-api-nodejs-samples.md)
> * [Exemples Python](sql-api-python-samples.md)
> * [Galerie d’exemples de code Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)

Le référentiel GitHub [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python) contient des exemples de solutions qui effectuent des opérations CRUD, ainsi que d’autres opérations courantes sur les ressources Azure Cosmos DB. Cet article fournit :

* Liens vers les tâches dans chacun des exemples de fichiers de projet Python.
* Des liens vers le contenu de référence d’API connexe.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Vous pouvez [activer les avantages d’abonnement Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) : Votre abonnement Visual Studio vous donne droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Vous devez également disposer du [Kit de développement logiciel (SDK) Python](sql-api-sdk-python.md).

   > [!NOTE]
   > Chaque exemple est autonome, se définit lui-même et se nettoie automatiquement. Les exemples émettent plusieurs appels à `CosmosClient.CreateContainer`. À chaque opération effectuée, votre abonnement est facturé pour une heure d’utilisation. Pour en savoir plus sur la facturation Azure Cosmos DB, consultez [Tarification de Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="database-examples"></a>Exemples de base de données

L’exemple Python [database_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py) explique comment effectuer les tâches suivantes. Pour en savoir plus sur les bases de données Azure Cosmos avant d’exécuter les exemples suivants, consultez l’article conceptuel [Utiliser des bases de données, des conteneurs et des éléments](databases-containers-items.md).

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Créer une base de données](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L48-L56) |CosmosClient.create_database|
| [Lire une base de données par identifiant](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L59-L67) |CosmosClient.get_database_client|
| [Interroger les bases de données](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L32-L67) |CosmosClient.query_databases|
| [Répertorier les bases de données pour un compte](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L70-L81) |CosmosClient.list_databases|
| [Supprimer une base de données](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L84-L93) |CosmosClient.delete_database|

## <a name="container-examples"></a>Exemples de conteneur

L’exemple Python [container_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py) explique comment effectuer les tâches suivantes. Pour en savoir plus sur les collections Azure Cosmos avant d’exécuter les exemples suivants, consultez l’article conceptuel [Utiliser des bases de données, des conteneurs et des éléments](databases-containers-items.md).

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Rechercher un conteneur](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L51-L66) |database.query_containers |
| [Créer un conteneur](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L69-L163) |database.create_container |
| [Lister tous les conteneurs d’une base de données](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L206-L217) |database.list_containers |
| [Obtenir un conteneur par son ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L195-L203) |database.get_container_client |
| [Gérer le débit provisionné du conteneur](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L166-L192) |container.read_offer, container.replace_throughput|
| [Supprimer un conteneur](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L220-L229) |database.delete_container |

## <a name="item-examples"></a>Exemples d’éléments

L’exemple Python [item_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py) explique comment effectuer les tâches suivantes. Pour en savoir plus sur les documents Azure Cosmos avant d’exécuter les exemples suivants, consultez l’article conceptuel [Utiliser des bases de données, des conteneurs et des éléments](databases-containers-items.md).

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Créer des éléments dans un conteneur](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L26-L38) |container.create_item |
| [Lire un élément par son ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L41-L49) |container.read_item |
| [Lire tous les éléments figurant dans un conteneur](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L52-L63) |container.read_all_items |
| [Interroger un élément par son ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78) |container.query_items |
| [Remplacer un élément](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L81-L88) |container.replace_items |
| [Effectuer une opération Upsert sur un élément](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L91-L98) |container.upsert_item |
| [Supprimer un élément](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L101-L106) |container.delete_item |
| [Obtenir le flux de modification des éléments dans un conteneur](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/change_feed_management.py) |container.query_items_change_feed |

## <a name="indexing-examples"></a>Exemples d'indexation

L’exemple Python [index_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py) explique comment effectuer les tâches suivantes. Pour en savoir plus sur l’indexation dans Azure Cosmos DB avant d’exécuter les exemples suivants, consultez les articles conceptuels sur les [stratégies d’indexation](index-policy.md), les [types d’indexation](index-types.md) et les [chemins d’indexation](index-paths.md).

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Exclure un élément spécifique de l’indexation](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L145-L201) | documents.IndexingDirective.Exclude|
| [Utiliser l’indexation manuelle avec des éléments spécifiques indexés](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L204-L263) | documents.IndexingDirective.Include |
| [Exclure de l’indexation des chemins](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L266-L336) |Définir les chemins à exclure dans la propriété `IndexingPolicy` |
| [Utiliser les index de plage sur les chaînes](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L401-L485) | Définissez la stratégie d’indexation avec des index de plage sur le type de données String. `'kind': documents.IndexKind.Range`, `'dataType': documents.DataType.String`|
| [Effectuer une transformation d’index](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L488-L544) |database.replace_container (utilisez la stratégie d’indexation mise à jour)|
| [Utiliser des analyses lorsque seul un index de hachage existe sur le chemin](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L339-L398) | Définissez `enable_scan_in_query=True` et `enable_cross_partition_query=True` lors de l’interrogation des éléments |
