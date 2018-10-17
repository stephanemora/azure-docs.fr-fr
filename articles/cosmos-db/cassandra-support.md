---
title: Fonctionnalités et commandes Apache Cassandra prises en charge par l’API Cassandra Azure Cosmos DB
description: Découvrir la prise en charge des fonctionnalités Apache Cassandra dans l’API Cassandra Azure Cosmos DB
services: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 06f2f81f5be496e2e55874c07a31fdf1c0a5d0f2
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868670"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Fonctionnalités Apache Cassandra prises en charge par l’API Cassandra Azure Cosmos DB 

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Vous pouvez communiquer avec l’API Cassandra Azure Cosmos DB via les [pilotes](http://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) clients Cassandra open source et conformes au [protocole de transmission](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) du langage de requête Cassandra (CQL) v4. 

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
* booléenne  
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
* texte  
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


## <a name="cassandra-query-language-limits"></a>Limites du langage de requête Cassandra

L’API Cassandra Azure Cosmos DB n’a aucune limite sur la taille des données stockées dans une table. Des centaines de téraoctets ou pétaoctets de données peuvent être stockées tout en garantissant le respect des limites de clé de partition. De même, chaque entité ou ligne équivalente n’a aucune limite sur le nombre de colonnes. Toutefois, la taille totale de l’entité ne doit pas dépasser 2 Mo.

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
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> –ssl 
```
**Unix/Linux/Mac :**

```bash
export SSL_VERSION=TLSv1_2 
SSL_CERTIFICATE=<path to Baltimore root ca cert>
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> –ssl 
```

## <a name="cql-commands"></a>Commandes CQL

Azure Cosmos DB prend en charge les commandes de base de données suivantes sur les comptes de l’API Cassandra.

* CREATE KEYSPACE 
* CREATE TABLE 
* ALTER TABLE 
* USE 
* INSERT 
* SELECT 
* UPDATE 
* BATCH - Seules les commandes non journalisées sont prises en charge 
* SUPPRIMER

Toutes les opérations crud, quand elles sont exécutées via le kit SDK compatible CQLV4, retournent des informations supplémentaires sur l’erreur, les unités de requête consommées et l’ID d’activité. Les commandes de suppression et de mise à jour doivent être gérées en prenant en considération la gouvernance des ressources pour éviter une surutilisation des ressources provisionnées. 
* Notez que la valeur gc_grace_seconds doit être égale à zéro si elle est spécifiée.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            string value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($“CustomPayload:  {key}: {value}”); 
        } 
```

## <a name="consistency-mapping"></a>Mappage de cohérence 

L’API Cassandra Azure Cosmos DB offre un choix de cohérence pour les opérations de lecture. Toutes les opérations d’écriture, quelle que soit la cohérence des comptes, sont toujours écrites avec des contrats SLA de performances d’écriture.

## <a name="permission-and-role-management"></a>Gestion des rôles et des autorisations

Azure Cosmos DB prend en charge le contrôle d’accès en fonction du rôle (RBAC) et les mots de passe/clés en lecture-écriture et en lecture seule, qui peuvent être obtenus via le [portail Azure](https://portal.azure.com. Azure Cosmos DB ne prend pas encore en charge les utilisateurs et les rôles pour les activités de plan de données. 

## <a name="planned-support"></a>Futures prises en charge 
* Utilisation conjointe de timestamp et de TTL  
* Pour l’instant, le nom de région dans la commande CREATE KEYSPACE est ignoré. La distribution des données est implémentée dans la plateforme Cosmos DB sous-jacente et est exposée par le biais du portail ou de PowerShell pour le compte. 





## <a name="next-steps"></a>Étapes suivantes

- Bien démarrer avec la [création d’un compte d’API Cassandra, d’une base de données et d’une table](create-cassandra-api-account-java.md) à l’aide d’une application Java

