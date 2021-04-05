---
title: Intégrer Apache Kafka et l’API Cassandra Azure Cosmos DB à l’aide de Kafka Connect
description: Découvrez comment ingérer dans l’API Cassandra Azure Cosmos DB des données de Kafka à l’aide de DataStax Apache Kafka Connector
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 12/14/2020
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: 25972ba2bb30c39838c4822a42af292e8d8b1dba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97803627"
---
# <a name="ingest-data-from-apache-kafka-into-azure-cosmos-db-cassandra-api-using-kafka-connect"></a>Ingérer dans l’API Cassandra Azure Cosmos DB des données d’Apache Kafka à l’aide de Kafka Connect
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Les applications Cassandra existantes peuvent facilement fonctionner avec l’[API Cassandra Azure Cosmos DB](cassandra-introduction.md) en raison de la [compatibilité de son pilote CQLv4](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver). Vous tirez parti de cette capacité pour intégrer des plateformes de diffusion en continu, telles qu’[Apache Kafka](https://kafka.apache.org/), et importer des données dans Azure Cosmos DB.

Les données qui se trouvent dans Apache Kafka (rubriques) sont utiles uniquement lorsqu’elles sont consommées par d’autres applications ou ingérées dans d’autres systèmes. Il est possible de créer une solution à l’aide des API [Kafka Producer/Consumer](https://kafka.apache.org/documentation/#api) [à l’aide du langage et du Kit de développement logiciel (SDK) client de votre choix](https://cwiki.apache.org/confluence/display/KAFKA/Clients). Kafka Connect offre une solution alternative. Il s’agit d’une plateforme qui permet de transmettre en continu des données entre Apache Kafka et d’autres systèmes de manière évolutive et fiable. Étant donné que Kafka Connect prend en charge les connecteurs standard, dont Cassandra, vous n’avez pas besoin d’écrire de code personnalisé pour intégrer Kafka à l’API Cassandra Azure Cosmos DB. 

Dans cet article, nous utiliserons le [connecteur open source DataStax Apache Kafka](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html), qui fonctionne en complément de l’infrastructure Kafka Connect pour ingérer les enregistrements d’une rubrique Kafka dans les lignes d’une ou plusieurs tables Cassandra. L’exemple fournit une configuration réutilisable à l’aide de Docker Compose. C’est très pratique, car elle vous permet de démarrer tous les composants requis localement à l’aide d’une seule commande. Ces composants incluent Kafka, Zookeeper, le rôle de travail Kafka Connect et l’exemple d’une application de génération de données.

Voici une liste des composants et de leurs définitions de service. Vous pouvez vous référer au fichier `docker-compose` complet [dans le référentiel GitHub](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/docker-compose.yaml).

- Kafka et Zookeeper utilisent des images [debezium](https://hub.docker.com/r/debezium/kafka/).
- Pour s’exécuter en tant que conteneur Docker, le connecteur DataStax Apache Kafka est intégré à une image Docker existante : [debezium/connect-base](https://github.com/debezium/docker-images/tree/master/connect-base/1.2). Cette image comprend une installation de Kafka et de ses bibliothèques Kafka Connect, ce qui rend l’ajout de connecteurs personnalisés très pratique. Vous pouvez vous référer au [Dockerfile](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/connector/Dockerfile).
- Le service `data-generator` amorce des données générées de façon aléatoire (JSON) dans la rubrique Kafka `weather-data`. Vous pouvez vous référer au code et au `Dockerfile` dans [le référentiel GitHub](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/data-generator/).

## <a name="prerequisites"></a>Prérequis

* [Provisionner un compte de l’API Cassandra Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account)

* [Utiliser cqlsh ou un interpréteur de commandes hébergé à des fins de validation](cassandra-support.md#hosted-cql-shell-preview)

* Installer [Docker](https://docs.docker.com/get-docker/) et [Docker Compose](https://docs.docker.com/compose/install)

## <a name="create-keyspace-tables-and-start-the-integration-pipeline"></a>Créer un espace de clés et des tables et démarrer le pipeline d’intégration

À l’aide du portail Azure, créez l’espace de clés Cassandra et les tables nécessaires à l’application de démonstration.

> [!NOTE]
> Utilisez les mêmes noms d’espace de clés et de tables que ci-dessous.

```sql
CREATE KEYSPACE weather WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE weather.data_by_state (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (state, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE weather.data_by_station (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (station_id, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

Clonez le référentiel GitHub :

```bash
git clone https://github.com/Azure-Samples/cosmosdb-cassandra-kafka
cd cosmosdb-cassandra-kafka
```

Démarrez tous les services :

```shell
docker-compose --project-name kafka-cosmos-cassandra up --build
```

> [!NOTE]
> Le téléchargement et le démarrage des conteneurs peuvent prendre un certain temps : il s’agit simplement d’un processus ponctuel.

Pour vérifier si tous les conteneurs ont démarré :

```shell
docker-compose -p kafka-cosmos-cassandra ps
```

L’application de génération de données commencera à pomper les données dans la rubrique `weather-data` dans Kafka. Vous pouvez également effectuer une rapide vérification de la validité pour confirmer. Apercevez le conteneur Docker qui exécute le rôle de travail Kafka Connect :


```bash
docker exec -it kafka-cosmos-cassandra_cassandra-connector_1 bash
```

Une fois que arrivé dans l’interpréteur de commandes du conteneur, démarrez simplement le processus habituel de consommation de la console Kafka. Vous devriez voir arriver les données météorologiques (au format JSON).

```bash
cd ../bin
./kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic weather-data
```

## <a name="cassandra-sink-connector-setup"></a>Configuration du connecteur Cassandra Sink

Copiez le contenu JSON ci-dessous dans un fichier (vous pouvez le nommer `cassandra-sink-config.json`). Vous devez le mettre à jour conformément à votre configuration, et le reste de cette section vous fournira des conseils à ce sujet.

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "weather-data",
        "contactPoints": "<cosmos db account name>.cassandra.cosmos.azure.com",
        "port": 10350,
        "loadBalancing.localDc": "<cosmos db region e.g. Southeast Asia>",
        "auth.username": "<enter username for cosmosdb account>",
        "auth.password": "<enter password for cosmosdb account>",
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "/etc/alternatives/jre/lib/security/cacerts/",
        "ssl.keystore.password": "changeit",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "topic.weather-data.weather.data_by_state.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "topic.weather-data.weather.data_by_station.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter",
        "value.converter.schemas.enable": false,
        "offset.flush.interval.ms": 10000
    }
}
```

Voici un résumé des attributs :

**Connectivité de base**

- `contactPoints` : entrez le point de contact pour Cassandra Cosmos DB
- `loadBalancing.localDc` : entrez la région du compte Cosmos DB, par exemple : Asie Sud-Est
- `auth.username` : entrez le nom d’utilisateur
- `auth.password` : entrez le mot de passe
- `port` : entrez la valeur du port (il s’agit de `10350`, et non de `9042`. Laissez-le tel quel)

**Configuration SSL**

Azure Cosmos DB assure une [connectivité sécurisée par SSL](database-security.md) et le connecteur Kafka Connect prend également en charge le protocole SSL.

- `ssl.keystore.path` : chemin d’accès au magasin de clés JDK dans le conteneur : `/etc/alternatives/jre/lib/security/cacerts/`
- `ssl.keystore.password` : mot de passe du magasin de clés JDK (par défaut)
- `ssl.hostnameValidation` : nous désactivons la validation du nom d’hôte du nœud
- `ssl.provider` : `JDK` est utilisé comme fournisseur SSL

**Paramètres génériques**

- `key.converter` : nous utilisons le convertisseur de chaîne `org.apache.kafka.connect.storage.StringConverter`
- `value.converter` : étant donné que les données des rubriques Kafka sont au format JSON, nous utilisons `org.apache.kafka.connect.json.JsonConverter`
- `value.converter.schemas.enable` : comme notre charge utile JSON n’a pas de schéma qui lui est associé (pour les besoins de l’application de démonstration), nous devons indiquer à Kafka Connect de ne pas rechercher de schéma en définissant cet attribut sur `false`. Dans le cas contraire, cela entraînera des échecs.

### <a name="install-the-connector"></a>Installation du connecteur

Installez le connecteur à l’aide du point de terminaison REST Kafka Connect :

```shell
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

Pour vérifier l’état :

```
curl http://localhost:8080/connectors/kafka-cosmosdb-sink/status
```

Si tout se passe bien, le connecteur doit commencer à faire son effet. Il doit s’authentifier auprès d’Azure Cosmos DB et commencer à ingérer des données de la rubrique Kafka (`weather-data`) dans les tables Cassandra (`weather.data_by_state` et `weather.data_by_station`).

Vous pouvez désormais interroger les données des tables. Accédez au portail Azure, puis affichez l’interpréteur de commandes CQL hébergé pour votre compte Azure Cosmos DB.

:::image type="content" source="./media/cassandra-kafka-connect/cqlsh.png" alt-text="Ouvrir CQLSH":::

## <a name="query-data-from-azure-cosmos-db"></a>Interroger des données à partir d’Azure Cosmos DB

Vérifiez les tables `data_by_state` et `data_by_station`. Voici quelques exemples de requêtes pour vous aider à démarrer :

```sql
select * from weather.data_by_state where state = 'state-1';
select * from weather.data_by_state where state IN ('state-1', 'state-2');
select * from weather.data_by_state where state = 'state-3' and ts > toTimeStamp('2020-11-26');

select * from weather.data_by_station where station_id = 'station-1';
select * from weather.data_by_station where station_id IN ('station-1', 'station-2');
select * from weather.data_by_station where station_id IN ('station-2', 'station-3') and ts > toTimeStamp('2020-11-26');
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Provisionner le débit sur les conteneurs et les bases de données](set-throughput.md) 
* [Meilleures pratiques en matière de clés de partition](partitioning-overview.md#choose-partitionkey)
* Articles [Estimer le nombre d'unités de requête/seconde à l'aide du planificateur de capacité Azure Cosmos DB](estimate-ru-with-capacity-planner.md)
