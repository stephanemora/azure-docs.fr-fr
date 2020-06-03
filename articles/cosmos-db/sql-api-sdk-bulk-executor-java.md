---
title: 'Azure Cosmos DB : API Java Bulk Executor, Kit de développement logiciel (SDK) et ressources'
description: Tout savoir sur l’API de l’exécuteur en bloc Java et le kit de développement logiciel (SDK), notamment les dates de publication, les dates de retrait et les modifications apportées entre chaque version du SDK de l’exécuteur en bloc Java d’Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/11/2020
ms.author: anfeldma
ms.openlocfilehash: 6fe075d2679ec13217200bc8b0da132e8f4d9b57
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83660439"
---
# <a name="java-bulk-executor-library-download-information"></a>Bibliothèque d’exécuteurs en bloc Java : Télécharger des informations

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Flux de modification .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Kit SDK Java v4](sql-api-sdk-java-v4.md)
> * [Kit SDK Java asynchrone v2](sql-api-sdk-async-java.md)
> * [Kit SDK Java synchrone v2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [API REST Resource Provider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Exécuteur en bloc – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Exécuteur en bloc – Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Description**|La bibliothèque de l’exécuteur en bloc permet aux applications clientes d’effectuer des opérations en bloc dans des comptes Azure Cosmos DB. la bibliothèque de l’exécuteur en bloc fournit les espaces de noms BulkImport et BulkUpdate. Le module BulkImport peut ingérer en bloc des documents de manière optimisée afin que le débit provisionné pour une collection soit consommé dans ses limites maximales. Le module BulkUpdate peut mettre à jour en bloc des données existantes dans des conteneurs Azure Cosmos sous forme de correctifs.|
|**Téléchargement du Kit de développement logiciel (SDK)**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Bibliothèque d’exécuteurs en bloc dans GitHub**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **Documentation de l’API**| [Documentation de référence sur l’API Java](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Prise en main**|[Bien démarrer avec le kit de développement logiciel (SDK) Java de la bibliothèque de l’exécuteur en bloc](bulk-executor-java.md)|
|**Runtime minimal pris en charge**|[Kit de développement Java (JDK) 7+](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>Notes de publication

### <a name="2100"></a><a name="2.10.0"/>2.10.0

* Correctif pour DocumentAnalyzer.java devant permettre d’extraire correctement les valeurs de clé de partition imbriquées du fichier json.

### <a name="294"></a><a name="2.9.4"/>2.9.4

* Ajout de fonctionnalité dans les opérations BulkDelete pour réessayer en cas de défaillances spécifiques, ainsi que retourner à l’utilisateur une liste d’échecs qui pourraient faire l’objet d’une nouvelle tentative.

### <a name="293"></a><a name="2.9.3"/>2.9.3

* Mise à jour pour la Kit de développement logiciel (SDK) Cosmos version 2.4.7.

### <a name="292"></a><a name="2.9.2"/>2.9.2

* Correctif pour « mergeAll » afin de continuer sur un « id » et une valeur de clé de partition de sorte que toutes les propriétés de document corrigées placées après l’« id » et la valeur de clé de partition soient ajoutées à la liste d’éléments mis à jour.

### <a name="291"></a><a name="2.9.1"/>2.9.1

* Mise à jour du degré de concurrence de départ sur 1, et ajout de journaux de débogage pour mini-lot.


