---
title: Migrer des données de PostgreSQL vers un compte API Cassandra Azure Cosmos DB à l’aide d’Apache Kafka
description: Découvrez comment utiliser Kafka Connect pour synchroniser les données de PostgreSQL vers l’API Cassandra Azure Cosmos DB en temps réel.
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 01/05/2021
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: 0038219ee8c1721ff5ab2be76231d33d2bd9064d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98203063"
---
# <a name="migrate-data-from-postgresql-to-azure-cosmos-db-cassandra-api-account-using-apache-kafka"></a>Migrer des données de PostgreSQL vers un compte API Cassandra Azure Cosmos DB à l’aide d’Apache Kafka
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

L’API Cassandra d’Azure Cosmos DB est devenue un excellent choix pour les charges de travail d’entreprise exécutées sur Apache Cassandra pour diverses raisons, telles que :

* **Réduction significative des coûts :** vous pouvez réaliser des économies grâce à Azure Cosmos DB, notamment sur les machines virtuelles, la bande passante et les licences Oracle applicables. En outre, vous n'avez pas à gérer les coûts liés aux centres de données, aux serveurs, au stockage SSD, à la mise en réseau et à la consommation électrique.

* **Amélioration de l’extensibilité et de la disponibilité :** Cela élimine les points de défaillance uniques, améliore l’évolutivité et la disponibilité de vos applications.

* **Aucune surcharge liée à la gestion et à la surveillance :** Comme il s’agit d’un service cloud complètement managé, Azure Cosmos DB élimine la surcharge liée à la gestion et à la supervision d’une multitude de paramètres.

[Kafka Connect](https://kafka.apache.org/documentation/#connect) est une plateforme qui permet de transmettre en continu des données entre [Apache Kafka](https://kafka.apache.org/) et d’autres systèmes de manière évolutive et fiable. Elle prend en charge plusieurs connecteurs standard, ce qui signifie que vous n’avez pas besoin de code personnalisé pour intégrer des systèmes externes à Apache Kafka.

Cet article montre comment utiliser une combinaison de connecteurs Kafka pour configurer un pipeline de données afin de synchroniser continuellement des enregistrements d’une base de données relationnelle, par exemple [PostgreSQL](https://www.postgresql.org/), vers l’[API Cassandra Azure Cosmos DB](cassandra-introduction.md).

## <a name="overview"></a>Vue d'ensemble

Voici une vue d’ensemble générale du flux de bout en bout présenté dans cet article.

Les données de la table PostgreSQL seront envoyées (push) vers Apache Kafka à l’aide du [connecteur Debezium PostgreSQL](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html), qui est un connecteur **source** de Kafka Connect. Les insertions, mises à jour ou suppressions d’enregistrements dans la table PostgreSQL sont capturées en tant qu’événements `change data` et envoyées aux rubriques Kafka. Le [connecteur DataStax Apache Kafka](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html) (connecteur **récepteur** Kafka Connect), forme la deuxième partie du pipeline. Il synchronise les événements de changement des données de la rubrique Kafka aux tables de l’API Cassandra Azure Cosmos DB.

> [!NOTE]
> L’utilisation de fonctionnalités spécifiques du connecteur DataStax Apache Kafka nous permet d’envoyer (push) des données à plusieurs tables. Dans cet exemple, le connecteur nous aidera à conserver les enregistrements de changement des données dans deux tables Cassandra pouvant prendre en charge différentes exigences de requête.

## <a name="prerequisites"></a>Prérequis

* [Provisionner un compte de l’API Cassandra Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account)
* [Utiliser cqlsh ou un interpréteur de commandes hébergé à des fins de validation](cassandra-support.md#hosted-cql-shell-preview)
* JDK 8 ou version ultérieure
* [Docker](https://www.docker.com/) (facultatif)

## <a name="base-setup"></a>Configuration de base

### <a name="set-up-postgresql-database-if-you-havent-already"></a>Configurez la base de données PostgreSQL si vous ne l’avez pas déjà fait. 

Il peut s’agir d’une base de données locale existante ou vous pouvez [en télécharger une et l’installer](https://www.postgresql.org/download/) sur votre ordinateur local. Il est également possible d’utiliser un [conteneur Docker](https://hub.docker.com/_/postgres).

Pour démarrer un conteneur :

```bash
docker run --rm -p 5432:5432 -e POSTGRES_PASSWORD=<enter password> postgres
```

Connectez-vous à votre instance PostgreSQL à l’aide du client [`psql`](https://www.postgresql.org/docs/current/app-psql.html) :

```bash
psql -h localhost -p 5432 -U postgres -W -d postgres
```

Créez une table pour stocker des exemples d’informations sur les commandes :

```sql
CREATE SCHEMA retail;

CREATE TABLE retail.orders_info (
    orderid SERIAL NOT NULL PRIMARY KEY,
    custid INTEGER NOT NULL,
    amount INTEGER NOT NULL,
    city VARCHAR(255) NOT NULL,
    purchase_time VARCHAR(40) NOT NULL
);
```

### <a name="using-the-azure-portal-create-the-cassandra-keyspace-and-the-tables-required-for-the-demo-application"></a>À l’aide du portail Azure, créez l’espace de clés Cassandra et les tables nécessaires à l’application de démonstration.

> [!NOTE]
> Utilisez les mêmes noms d’espace de clés et de tables que ci-dessous.

```sql
CREATE KEYSPACE retail WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE retail.orders_by_customer (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (customer_id, purchase_time)) WITH CLUSTERING ORDER BY (purchase_time DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE retail.orders_by_city (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (city,order_id)) WITH cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

### <a name="setup-apache-kafka"></a>Installation d’Apache Kafka 

Cet article utilise un cluster local, mais vous pouvez choisir n’importe quelle autre option. [Téléchargez Kafka](https://kafka.apache.org/downloads), décompressez-le et démarrez les clusters Zookeeper et Kafka.

```bash
cd <KAFKA_HOME>/bin

#start zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties

#start kafka (in another terminal)
bin/kafka-server-start.sh config/server.properties
```

### <a name="setup-connectors"></a>Installation des connecteurs

Installez les connecteurs Debezium PostgreSQL et DataStax Apache Kafka. Téléchargez l’archive du plug-in du connecteur Debezium PostgreSQL. Par exemple, pour télécharger la version 1.3.0 du connecteur (la plus récente au moment de la rédaction de cet article), utilisez [ce lien](https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.3.0.Final/debezium-connector-postgres-1.3.0.Final-plugin.tar.gz). Téléchargez le connecteur DataStax Apache Kafka à partir de [ce lien](https://downloads.datastax.com/#akc).

Décompressez les deux archives de connecteur et copiez les fichiers JAR dans le fichier [plugin.path de Kafka Connect](https://kafka.apache.org/documentation/#connectconfigs).


```bash
cp <path_to_debezium_connector>/*.jar <KAFKA_HOME>/libs
cp <path_to_cassandra_connector>/*.jar <KAFKA_HOME>/libs
```

> Pour plus d’informations, consultez la documentation de [Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector) et [DataStax](https://docs.datastax.com/en/kafka/doc/).

## <a name="configure-kafka-connect-and-start-data-pipeline"></a>Configurer Kafka Connect et démarrer le pipeline de données

### <a name="start-kafka-connect-cluster"></a>Démarrer le cluster Kafka Connect

```bash
cd <KAFKA_HOME>/bin
./connect-distributed.sh ../config/connect-distributed.properties
```

### <a name="start-postgresql-connector-instance"></a>Démarrer l’instance du connecteur PostgreSQL

Enregistrez la configuration (JSON) du connecteur dans un exemple de fichier `pg-source-config.json` :

```json
{
    "name": "pg-orders-source",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "localhost",
        "database.port": "5432",
        "database.user": "postgres",
        "database.password": "password",
        "database.dbname": "postgres",
        "database.server.name": "myserver",
        "plugin.name": "wal2json",
        "table.include.list": "retail.orders_info",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter"
    }
}
```

Pour démarrer l’instance du connecteur PostgreSQL :

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-config.json http://localhost:8083/connectors
```

> [!NOTE]
> Pour la supprimer, vous pouvez utiliser : `curl -X DELETE http://localhost:8083/connectors/pg-orders-source`.


### <a name="insert-data"></a>Insertion des données

La table `orders_info` contient des détails sur les commandes, tels que l’ID de commande, l’ID client, la ville, etc. Remplissez la table avec des données aléatoires à l’aide du SQL ci-dessous.

```sql
insert into retail.orders_info (
    custid, amount, city, purchase_time
)
select
    random() * 10000 + 1,
    random() * 200,
    ('{New Delhi,Seattle,New York,Austin,Chicago,Cleveland}'::text[])[ceil(random()*3)],
    NOW() + (random() * (interval '1 min'))
from generate_series(1, 10) s(i);
```

Il doit insérer 10 enregistrements dans la table. Veillez à mettre à jour le nombre d’enregistrements dans `generate_series(1, 10)` ci-dessous en fonction de votre exemple de configuration. Pour insérer `100` enregistrements, utilisez `generate_series(1, 100)`.

Pour confirmer :

```bash
select * from retail.orders_info;
```

Vérifiez les événements de capture des changement de données dans la rubrique Kafka.

> [!NOTE]
> Notez que le nom de la rubrique est `myserver.retail.orders_info` conformément à la [convention du connecteur](https://debezium.io/documentation/reference/1.3/connectors/postgresql.html#postgresql-topic-names).

```bash
cd <KAFKA_HOME>/bin

./kafka-console-consumer.sh --topic myserver.retail.orders_info --bootstrap-server localhost:9092 --from-beginning
```

Vous devez voir les événements de changement des données au format JSON.

### <a name="start-datastax-apache-kafka-connector-instance"></a>Démarrer l’instance du connecteur DataStax Apache Kafka

Enregistrez la configuration (JSON) du connecteur dans un exemple de fichier, `cassandra-sink-config.json`, et mettez à jour les propriétés conformément à votre environnement.

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "myserver.retail.orders_info",
        "contactPoints": "<Azure Cosmos DB account name>.cassandra.cosmos.azure.com",
        "loadBalancing.localDc": "<Azure Cosmos DB region e.g. Southeast Asia>",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "<path to JDK keystore path e.g. <JAVA_HOME>/jre/lib/security/cacerts>",
        "ssl.keystore.password": "<keystore password: it is 'changeit' by default>",
        "port": 10350,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "auth.username": "<Azure Cosmos DB user name (same as account name)>",
        "auth.password": "<Azure Cosmos DB password>",
        "topic.myserver.retail.orders_info.retail.orders_by_customer.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "topic.myserver.retail.orders_info.retail.orders_by_city.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "transforms": "unwrap",
        "transforms.unwrap.type": "io.debezium.transforms.ExtractNewRecordState",
        "offset.flush.interval.ms": 10000
    }
}
```

Pour démarrer l’instance du connecteur :

```bash
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

Le connecteur devrait entrer en action et le pipeline de bout en bout entre PostgreSQL et Azure Cosmos DB sera opérationnel.

### <a name="query-azure-cosmos-db"></a>Interroger Azure Cosmos DB

Vérifiez les tables Cassandra dans Azure Cosmos DB. Voici quelques-unes des requêtes que vous pouvez essayer :

```sql
select count(*) from retail.orders_by_customer;
select count(*) from retail.orders_by_city;

select * from retail.orders_by_customer;
select * from retail.orders_by_city;

select * from retail.orders_by_city where city='Seattle';
select * from retail.orders_by_customer where customer_id = 10;
```

Vous pouvez continuer à insérer d’autres données dans PostgreSQL et confirmer que les enregistrements sont synchronisés à Azure Cosmos DB.

## <a name="next-steps"></a>Étapes suivantes

* [Intégrer Apache Kafka et l’API Cassandra Azure Cosmos DB à l’aide de Kafka Connect](cassandra-kafka-connect.md)
* [Intégrer Apache Kafka Connect sur Azure Event Hubs (version préliminaire) avec Debezium pour la capture des changements de données](../event-hubs/event-hubs-kafka-connect-debezium.md)
* [Migrer des données depuis Oracle vers l’API Cassandra Azure Cosmos DB à l’aide de Blitzz](oracle-migrate-cosmos-db-blitzz.md)
* [Provisionner le débit sur les conteneurs et les bases de données](set-throughput.md) 
* [Meilleures pratiques en matière de clés de partition](partitioning-overview.md#choose-partitionkey)
* Articles [Estimer le nombre d'unités de requête/seconde à l'aide du planificateur de capacité Azure Cosmos DB](estimate-ru-with-capacity-planner.md)

