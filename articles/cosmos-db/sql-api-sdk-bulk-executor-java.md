---
title: 'Azure Cosmos DB : API Java Bulk Executor, Kit de développement logiciel (SDK) et ressources'
description: Tout savoir sur l’API de l’exécuteur en bloc Java et le kit de développement logiciel (SDK), notamment les dates de publication, les dates de retrait et les modifications apportées entre chaque version du SDK de l’exécuteur en bloc Java d’Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 09fa626e77e20feff55f7b17807754ac1d2b873f
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111568843"
---
# <a name="java-bulk-executor-library-download-information"></a>Bibliothèque d’exécuteurs en bloc Java : Télécharger des informations
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Kit de développement logiciel (SDK) .NET v3](sql-api-sdk-dotnet-standard.md)
> * [SDK .NET v2](sql-api-sdk-dotnet.md)
> * [SDK .NET Core v2](sql-api-sdk-dotnet-core.md)
> * [SDK .NET Change Feed v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Kit SDK Java v4](sql-api-sdk-java-v4.md)
> * [SDK Java Async v2](sql-api-sdk-async-java.md)
> * [SDK Java Sync v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Connecteur OLTP Spark 3](sql-api-sdk-java-spark-v3.md)
> * [Connecteur OLTP Spark 2](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [API REST Resource Provider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Exécuteur en bloc – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Exécuteur en bloc – Java](sql-api-sdk-bulk-executor-java.md)

| | Lien/notes |
|---|---|
|**Description**|La bibliothèque de l’exécuteur en bloc permet aux applications clientes d’effectuer des opérations en bloc dans des comptes Azure Cosmos DB. la bibliothèque de l’exécuteur en bloc fournit les espaces de noms BulkImport et BulkUpdate. Le module BulkImport peut ingérer en bloc des documents de manière optimisée afin que le débit provisionné pour une collection soit consommé dans ses limites maximales. Le module BulkUpdate peut mettre à jour en bloc des données existantes dans des conteneurs Azure Cosmos sous forme de correctifs.|
|**Téléchargement du Kit de développement logiciel (SDK)**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Bibliothèque d’exécuteurs en bloc dans GitHub**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **Documentation de l’API**| [Documentation de référence sur l’API Java](/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Prise en main**|[Bien démarrer avec le kit de développement logiciel (SDK) Java de la bibliothèque de l’exécuteur en bloc](bulk-executor-java.md)|
|**Runtime minimal pris en charge**|[Kit de développement Java (JDK) 7+](/java/azure/jdk/)|

## <a name="release-notes"></a>Notes de publication
### <a name="2123"></a><a name="2.12.3"></a>2.12.3

* Corriger la stratégie de nouvelle tentative quand `GoneException` est inclus dans `IllegalStateException` : cette modification est nécessaire pour s’assurer que le cache de la passerelle est actualisé sur 410 afin que le connecteur Spark (pour Spark 2,4) puisse utiliser une stratégie de nouvelle tentative personnalisée pour permettre aux requêtes de réussir pendant les fractionnements de partition

### <a name="2122"></a><a name="2.12.2"></a>2.12.2

* Résolution d’un problème entraînant la non-importation des documents sur des erreurs temporaires.

### <a name="2121"></a><a name="2.12.1"></a>2.12.1

* Mettez à niveau pour utiliser la dernière version du kit de développement logiciel Cosmos Core.

### <a name="2120"></a><a name="2.12.0"></a>2.12.0

* Améliorez la gestion du budget RU fourni par le connecteur Spark pour l’opération en bloc. Une importation en bloc unique initiale est effectuée à partir du connecteur Spark avec un baseBatchSize, et la consommation de RU pour l’importation par lot ci-dessus est collectée.
  Un miniBatchSizeAdjustmentFactor est calculé en fonction de la consommation RU ci-dessus, et la taille du mini-lot est ajustée en fonction de cette valeur. En fonction du temps écoulé et du RU consommé pour chaque importation par lot, une durée de veille est calculée pour limiter la consommation de RU par seconde et est utilisée pour suspendre le thread avant l’importation par lot suivante.

### <a name="2110"></a><a name="2.11.0"></a>2.11.0

* Corriger un bogue empêchant les mises à jour en bloc lors de l’utilisation d’une clé de partition imbriquée

### <a name="2100"></a><a name="2.10.0"></a>2.10.0

* Correctif pour DocumentAnalyzer.java devant permettre d’extraire correctement les valeurs de clé de partition imbriquées du fichier json.

### <a name="294"></a><a name="2.9.4"></a>2.9.4

* Ajout de fonctionnalité dans les opérations BulkDelete pour réessayer en cas de défaillances spécifiques, ainsi que retourner à l’utilisateur une liste d’échecs qui pourraient faire l’objet d’une nouvelle tentative.

### <a name="293"></a><a name="2.9.3"></a>2.9.3

* Mise à jour pour la Kit de développement logiciel (SDK) Cosmos version 2.4.7.

### <a name="292"></a><a name="2.9.2"></a>2.9.2

* Correctif pour « mergeAll » afin de continuer sur un « id » et une valeur de clé de partition de sorte que toutes les propriétés de document corrigées placées après l’« id » et la valeur de clé de partition soient ajoutées à la liste d’éléments mis à jour.

### <a name="291"></a><a name="2.9.1"></a>2.9.1

* Mise à jour du degré de concurrence de départ sur 1, et ajout de journaux de débogage pour mini-lot.