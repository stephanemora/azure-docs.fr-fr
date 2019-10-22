---
title: Fonctionnalités et commandes Apache Cassandra prises en charge par l’API Cassandra Azure Cosmos DB
description: Découvrir la prise en charge des fonctionnalités Apache Cassandra dans l’API Cassandra Azure Cosmos DB
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 66a972e66c35cdd5b8dedceefbe3dbd008380da9
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327156"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Fonctionnalités Apache Cassandra prises en charge par l’API Cassandra Azure Cosmos DB 

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Vous pouvez communiquer avec l’API Cassandra Azure Cosmos DB via les [pilotes](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) clients Cassandra open source et conformes au [protocole de transmission](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) du langage de requête Cassandra (CQL) v4. 

En utilisant l’API Cassandra Azure Cosmos DB, vous pouvez bénéficier des avantages des API Cassandra Apache et des fonctionnalités d’entreprise que fournit Azure Cosmos DB. Parmi les fonctionnalités d’entreprise, citons la [distribution mondiale](distribute-data-globally.md), le [partitionnement automatique de la montée en charge](partition-data.md), des garanties de disponibilité et de latence, le chiffrement au repos, les sauvegardes et bien plus encore.

## <a name="cassandra-protocol"></a>Protocole Cassandra 

L’API Cassandra Azure Cosmos DB est compatible avec CQL version **v4**. Les commandes CQL, les outils, les limitations et les exceptions pris en charge sont listés ci-dessous. Les pilotes clients comprenant ces protocoles doivent pouvoir se connecter à l’API Cassandra Azure Cosmos DB.

## <a name="cassandra-driver"></a>Pilote Cassandra

Les versions suivantes des pilotes Cassandra sont prises en charge par l’API Cassandra Azure Cosmos DB :

* [Java 3.5+](https://github.com/datastax/java-driver)  
* [C# 3.5+](https://github.com/datastax/csharp-driver)  
* [Nodejs 3.5+](https://github.com/datastax/nodejs-driver)  
* [Python 3.15+](https://github.com/datastax/python-driver)  
* [C++ 2.9](https://github.com/datastax/cpp-driver)   
* [PHP 1.3](https://github.com/datastax/php-driver)  
* [Gocql](https://github.com/gocql/gocql)  
 
## <a name="cql-data-types"></a>Types de données CQL 

L’API Cassandra Azure Cosmos DB prend en charge les types de données CQL suivants :

* ascii  
* bigint  
* objet blob  
* boolean  
* counter  
* date  
* décimal  
* double  
* float  
* frozen  
* inet  
* int  
* list  
* set  
* smallint  
* text  
* time  
* timestamp  
* timeuuid  
* tinyint  
* tuple  
* uuid  
* varchar  
* varint  
* tuples  
* udts  
* map  

## <a name="cql-functions"></a>Fonctions CQL

L’API Cassandra Azure Cosmos DB prend en charge les fonctions CQL suivantes :

* par jeton  
* Fonctions d’agrégation
  * min, max, avg, count
* Fonctions de conversion blob 
  * typeAsBlob(value)  
  * blobAsType(value)
* Fonctions UUID et timeuuid 
  * dateOf()  
  * now()  
  * minTimeuuid()  
  * unixTimestampOf()  
  * toDate(timeuuid)  
  * toTimestamp(timeuuid)  
  * toUnixTimestamp(timeuuid)  
  * toDate(timestamp)  
  * toUnixTimestamp(timestamp)  
  * toTimestamp(date)  
  * toUnixTimestamp(date) 
  


## <a name="cassandra-api-limits"></a>Limites de l’API Cassandra

L’API Cassandra Azure Cosmos DB n’a aucune limite sur la taille des données stockées dans une table. Des centaines de téraoctets ou pétaoctets de données peuvent être stockées tout en garantissant le respect des limites de clé de partition. De même, chaque entité ou ligne équivalente n’a aucune limite sur le nombre de colonnes. Toutefois, la taille totale de l’entité ne doit pas dépasser 2 Mo. Les données par clé de partition ne peuvent pas dépasser 10 Go, comme dans toutes les autres API.

## <a name="tools"></a>Outils 

L’API Cassandra Azure Cosmos DB est une plateforme de services managés. Elle ne nécessite aucune surcharge de gestion ni aucun utilitaire, comme le Garbage Collector, Java Virtual Machine (JVM) et nodetool, pour gérer le cluster. Elle prend en charge des outils tels que cqlsh qui utilise la compatibilité de CQLv4 binaire. 

* L’Explorateur de données du portail Azure, les métriques, les diagnostics de journaux, PowerShell et CLI sont d’autres mécanismes pris en charge pour gérer le compte.

## <a name="cql-shell"></a>Interpréteur de commandes CQL  

L’utilitaire en ligne de commande CQLSH est fourni avec Apache Cassandra 3.1.1 et prêt à fonctionner avec les variables d’environnement activées suivantes :

Avant d’exécuter les commandes ci-après, [ajoutez un certificat racine Baltimore au magasin cacerts](https://docs.microsoft.com/java/azure/java-sdk-add-certificate-ca-store?view=azure-java-stable#to-add-a-root-certificate-to-the-cacerts-store). 

**Windows :** 

```bash
set SSL_VERSION=TLSv1_2 
SSL_CERTIFICATE=<path to Baltimore root ca cert>
set CQLSH_PORT=10350 
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl 
```
**Unix/Linux/Mac :**

```bash
export SSL_VERSION=TLSv1_2 
export SSL_CERTFILE=<path to Baltimore root ca cert>
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl 
```

## <a name="cql-commands"></a>Commandes CQL

Azure Cosmos DB prend en charge les commandes de base de données suivantes sur les comptes de l’API Cassandra.

* CREATE KEYSPACE (les paramètres de réplication pour cette commande sont ignorés)
* CREATE TABLE 
* ALTER TABLE 
* USE 
* INSERT 
* SELECT 
* UPDATE 
* BATCH - Seules les commandes non journalisées sont prises en charge 
* SUPPRIMER

Toutes les opérations crud, quand elles sont exécutées par le biais du kit SDK compatible CQLV4, retournent des informations supplémentaires sur l’erreur et les unités de requête consommées. Les commandes de suppression et de mise à jour doivent être gérées en prenant en considération la gouvernance des ressource, afin d’éviter une bonne utilisation du débit provisionné. 
* Notez que la valeur gc_grace_seconds doit être égale à zéro si elle est spécifiée.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($“CustomPayload:  {key}: {value}”); 
        } 
```

## <a name="consistency-mapping"></a>Mappage de cohérence 

L’API Cassandra Azure Cosmos DB offre un choix de cohérence pour les opérations de lecture.  Le mappage de cohérence est détaillé [ici](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-across-apis#cassandra-mapping).

## <a name="permission-and-role-management"></a>Gestion des rôles et des autorisations

Azure Cosmos DB prend en charge le contrôle d’accès en fonction du rôle (RBAC) pour l’approvisionnement, la rotation de clés, l’affichage des métriques et les mots de passe/clés en lecture-écriture et en lecture seule, qui peuvent être obtenus via le [portail Azure](https://portal.azure.com). Azure Cosmos DB ne prend pas en charge les rôles pour les activités CRUD.

## <a name="keyspace-and-table-options"></a>Options d’espace de clé et de table

Les options pour le nom de région, la classe, replication_factor et le centre de centres dans la commande « CREATE KEYSPACE » sont actuellement ignorées. Le système utilise la méthode de réplication de [distribution globale](https://docs.microsoft.com/en-us/azure/cosmos-db/global-dist-under-the-hood) d’Azure Cosmos DB sous-jacente pour ajouter les régions. Si vous avez besoin de la présence de données inter-régions, vous pouvez l’activer au niveau du compte avec PowerShell, l’interface CLI ou le portail. Pour en savoir plus, consultez l’article expliquant [comment ajouter des régions](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Durable_writes ne peut pas être désactivé, car Azure Cosmos DB garantit la durabilité de chaque écriture. Dans chaque région, Azure Cosmos DB réplique les données dans le jeu de réplicas composé de quatre réplicas, et cette [configuration](global-dist-under-the-hood.md) de jeu de réplicas ne peut pas être modifiée.
 
Toutes les options sont ignorées lors de la création de la table, sauf gc_grace_seconds, qui doit être définie sur zéro.
L’espace de clé et la table ont une option supplémentaire nommée « cosmosdb_provisioned_throughput » avec une valeur minimale de 400 RU/s. Le débit d’espace de clé permet de partager le débit entre plusieurs tables, et il est utile dans les scénarios où toutes les tables n’utilisent pas le débit provisionné. La commande ALTER TABLE permet de modifier le débit provisionné dans les régions. 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```


## <a name="usage-of-cassandra-retry-connection-policy"></a>Utilisation de la stratégie de connexion de nouvelle tentative Cassandra

Azure Cosmos DB est un système régi par les ressources. Cela signifie que vous pouvez effectuer un certain nombre d’opérations durant une seconde donnée, en fonction des unités de requête consommées par les opérations. Si une application dépasse cette limite durant une seconde donnée, le taux des requêtes est limité et des exceptions sont levées. L’API Cassandra dans Azure Cosmos DB traduit ces exceptions en erreurs surchargées sur le protocole natif Cassandra. Pour vous assurer que votre application peut intercepter et effectuer de nouvelles tentatives de requête en cas de limitation du taux, les extensions [spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) et [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) sont fournies. Si vous utilisez d’autres kits SDK pour accéder à l’API Cassandra dans Azure Cosmos DB, créez une stratégie de connexion pour effectuer une nouvelle tentative quand ces exceptions se produisent.

## <a name="next-steps"></a>Étapes suivantes

- Bien démarrer avec la [création d’un compte d’API Cassandra, d’une base de données et d’une table](create-cassandra-api-account-java.md) à l’aide d’une application Java

