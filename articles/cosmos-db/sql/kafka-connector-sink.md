---
title: Kafka Connect pour Azure Cosmos DB – Connecteur du récepteur
description: Le connecteur du récepteur Azure Cosmos DB vous permet d’exporter des données de rubriques Apache Kafka vers une base de données Azure Cosmos DB. Il interroge les données issues de Kafka pour écrire dans les conteneurs de la base de données en fonction de l’abonnement aux rubriques.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: kuthapar
ms.openlocfilehash: aa4b870a76d9d346116a48633bf3baddc7c44ef4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131056687"
---
# <a name="kafka-connect-for-azure-cosmos-db---sink-connector"></a>Kafka Connect pour Azure Cosmos DB – Connecteur du récepteur
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Kafka Connect pour Azure Cosmos DB est un connecteur permettant de lire et d’écrire des données dans Azure Cosmos DB. Le connecteur du récepteur Azure Cosmos DB vous permet d’exporter des données de rubriques Apache Kafka vers une base de données Azure Cosmos DB. Il interroge les données issues de Kafka pour écrire dans les conteneurs de la base de données en fonction de l’abonnement aux rubriques.

## <a name="prerequisites"></a>Prérequis

* Partez de la [configuration de plateforme Confluent](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Confluent_Platform_Setup.md), qui offre un environnement complet. Si vous ne souhaitez pas utiliser la plateforme Confluent, vous devrez installer et configurer vous-même ZooKeeper, Apache Kafka et Kafka Connect. Il vous faudra également installer et configurer manuellement les connecteurs Azure Cosmos DB.
* Créez un compte Azure Cosmos DB et un conteneur ([guide de configuration](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/CosmosDB_Setup.md))
* Munissez-vous d'un interpréteur de commandes Bash, testé sur GitHub Codespaces, Mac, Ubuntu et Windows avec WSL2. Cet interpréteur de commandes ne fonctionne pas dans Cloud Shell ou WSL1.
* Télécharger [Java 11 (ou version ultérieure)](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)
* Téléchargez [Maven](https://maven.apache.org/download.cgi).

## <a name="install-sink-connector"></a>Installation du connecteur du récepteur

Si vous utilisez la [configuration de plateforme Confluent](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Confluent_Platform_Setup.md) recommandée, le connecteur du récepteur Azure Cosmos DB est inclus dans l’installation. Vous pouvez donc ignorer cette étape.  

Dans le cas contraire, vous pouvez télécharger le fichier JAR dans la dernière [version](https://github.com/microsoft/kafka-connect-cosmosdb/releases) ou créer un package à partir de ce référentiel pour obtenir un fichier JAR. Pour installer manuellement le connecteur à l’aide du fichier JAR, consultez ces [instructions](https://docs.confluent.io/current/connect/managing/install.html#install-connector-manually). Vous pouvez également empaqueter un nouveau fichier JAR à partir du code source.

```bash
# clone the kafka-connect-cosmosdb repo if you haven't done so already
git clone https://github.com/microsoft/kafka-connect-cosmosdb.git
cd kafka-connect-cosmosdb

# package the source code into a JAR file
mvn clean package

# include the following JAR file in Kafka Connect installation
ls target/*dependencies.jar
```

## <a name="create-a-kafka-topic-and-write-data"></a>Création d’une rubrique Kafka et écriture de données

Si vous utilisez la plateforme Confluent, le moyen le plus simple de créer une rubrique Kafka consiste à passer par l’expérience utilisateur du centre de contrôle fournie. Dans le cas contraire, vous pouvez créer manuellement une rubrique Kafka suivant cette syntaxe :

```bash
./kafka-topics.sh --create --zookeeper <ZOOKEEPER_URL:PORT> --replication-factor <NO_OF_REPLICATIONS> --partitions <NO_OF_PARTITIONS> --topic <TOPIC_NAME>
```

Dans ce scénario, nous allons créer une rubrique Kafka nommée « hotels » et y écrire des données JSON sans schéma incorporé. Pour créer une rubrique dans le centre de contrôle, consultez le [guide de Confluent](https://docs.confluent.io/platform/current/quickstart/ce-docker-quickstart.html#step-2-create-ak-topics).

Ensuite, lancez le producteur de la console Kafka pour écrire quelques enregistrements dans la rubrique « hotels ».

```powershell
# Option 1: If using Codespaces, use the built-in CLI utility
kafka-console-producer --broker-list localhost:9092 --topic hotels

# Option 2: Using this repo's Confluent Platform setup, first exec into the broker container
docker exec -it broker /bin/bash
kafka-console-producer --broker-list localhost:9092 --topic hotels

# Option 3: Using your Confluent Platform setup and CLI install
<path-to-confluent>/bin/kafka-console-producer --broker-list <kafka broker hostname> --topic hotels
```

Dans le producteur de la console, entrez le code suivant :

```json
{"id": "h1", "HotelName": "Marriott", "Description": "Marriott description"}
{"id": "h2", "HotelName": "HolidayInn", "Description": "HolidayInn description"}
{"id": "h3", "HotelName": "Motel8", "Description": "Motel8 description"}
```

Les trois enregistrements entrés sont publiés dans la rubrique Kafka « hotels » au format JSON.

## <a name="create-the-sink-connector"></a>Création du connecteur du récepteur

Créez le connecteur du récepteur Azure Cosmos DB dans Kafka Connect. Le corps JSON suivant définit la configuration du connecteur du récepteur. Veillez à remplacer la valeur des propriétés `connect.cosmos.connection.endpoint` et `connect.cosmos.master.key`, que vous devez avoir enregistrées dans les prérequis du guide de configuration d’Azure Cosmos DB.

Pour plus d’informations sur chacune de ces propriétés de configuration, consultez la section [Propriétés du récepteur](#sink-configuration-properties).

```json
{
  "name": "cosmosdb-sink-connector",
  "config": {
    "connector.class": "com.azure.cosmos.kafka.connect.sink.CosmosDBSinkConnector",
    "tasks.max": "1",
    "topics": [
      "hotels"
    ],
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",
    "value.converter.schemas.enable": "false",
    "key.converter": "org.apache.kafka.connect.json.JsonConverter",
    "key.converter.schemas.enable": "false",
    "connect.cosmos.connection.endpoint": "https://<cosmosinstance-name>.documents.azure.com:443/",
    "connect.cosmos.master.key": "<cosmosdbprimarykey>",
    "connect.cosmos.databasename": "kafkaconnect",
    "connect.cosmos.containers.topicmap": "hotels#kafka"
  }
}
```

Une fois que toutes les valeurs sont remplies, enregistrez le fichier JSON en local. Vous pourrez l'utiliser pour créer le connecteur à l'aide de l'API REST.

### <a name="create-connector-using-control-center"></a>Création d’un connecteur avec le centre de contrôle

Une option simple pour créer le connecteur consiste à passer par la page web du centre de contrôle. Suivez ce [guide d’installation](https://docs.confluent.io/platform/current/quickstart/ce-docker-quickstart.html#step-3-install-a-ak-connector-and-generate-sample-data) pour créer un connecteur à partir du centre de contrôle. Au lieu d’utiliser l’option `DatagenConnector`, utilisez la vignette `CosmosDBSinkConnector`. Quand vous configurez le connecteur du récepteur, renseignez les valeurs de la même manière que dans le fichier JSON que vous avez rempli.

Vous pouvez également, sur la page des connecteurs, charger le fichier JSON créé avec l’option **Chargement du fichier de configuration du connecteur**.

:::image type="content" source="./media/kafka-connector-sink/upload-connector-config.png" alt-text="Chargement de la configuration du connecteur.":::

### <a name="create-connector-using-rest-api"></a>Création d’un connecteur avec l’API REST

Créez le connecteur du récepteur avec l’API REST Connect :

```bash
# Curl to Kafka connect service
curl -H "Content-Type: application/json" -X POST -d @<path-to-JSON-config-file> http://localhost:8083/connectors

```

## <a name="confirm-data-written-to-cosmos-db"></a>Vérification des données écrites dans Cosmos DB

Connectez-vous au [Portail Azure](https://portal.azure.com/learn.docs.microsoft.com) et accédez à votre compte Azure Cosmos DB. Vérifiez que les trois enregistrements de la rubrique « hotels » sont créés dans votre compte.

## <a name="cleanup"></a>Nettoyage

Pour supprimer le connecteur du récepteur que vous avez créé, accédez-y dans le centre de contrôle, puis cliquez sur l’icône **Supprimer**.

:::image type="content" source="./media/kafka-connector-sink/delete-connector.png" alt-text="Suppression du connecteur.":::

Vous pouvez également utiliser l’API REST Connect pour le supprimer :

```bash
# Curl to Kafka connect service
curl -X DELETE http://localhost:8083/connectors/cosmosdb-sink-connector
```

Pour supprimer le service Azure Cosmos DB créé et son groupe de ressources avec Azure CLI, consultez cette [procédure](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/CosmosDB_Setup.md#cleanup).

## <a name="sink-configuration-properties"></a><a id="sink-configuration-properties"></a>Propriétés de configuration du récepteur

Les paramètres suivants sont utilisés pour configurer le connecteur du récepteur Kafka Cosmos DB. Ces valeurs de configuration déterminent dans quelles rubriques Kafka les données sont consommées, dans quels conteneurs Azure Cosmos DB elles sont écrites et quels formats sont utilisés pour les sérialiser. Pour voir un exemple de fichier de configuration comportant les valeurs par défaut, consultez [cette configuration]( https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/src/docker/resources/sink.example.json).

| Nom | Type | Description | Obligatoire ou facultatif |
| :--- | :--- | :--- | :--- |
| Rubriques | list | Liste de rubriques Kafka à surveiller. | Obligatoire |
| connector.class | string | Nom de la classe du récepteur Azure Cosmos DB. Doit avoir la valeur `com.azure.cosmos.kafka.connect.sink.CosmosDBSinkConnector`. | Obligatoire |
| connect.cosmos.connection.endpoint | URI | Chaîne d’URI du point de terminaison Azure Cosmos. | Obligatoire |
| connect.cosmos.master.key | string | Clé primaire Azure Cosmos DB à laquelle le récepteur se connecte. | Obligatoire |
| connect.cosmos.databasename | string | Nom de la base de données Azure Cosmos dans laquelle le récepteur écrit. | Obligatoire |
| connect.cosmos.containers.topicmap | string | Mappage entre les rubriques Kafka et les conteneurs Azure Cosmos DB, au format CSV : `topic#container,topic2#container2`. | Obligatoire |
| key.converter | string | Format de sérialisation des données de clé écrites dans la rubrique Kafka. | Obligatoire |
| value.converter | string | Format de sérialisation des données de valeur écrites dans la rubrique Kafka. | Obligatoire |
| key.converter.schemas.enable | string | Propriété définie sur « true » si les données de clé possèdent un schéma incorporé. | Facultatif |
| value.converter.schemas.enable | string | Propriété définie sur « true » si les données de clé possèdent un schéma incorporé. | Facultatif |
| tasks.max | int | Nombre maximal de tâches du récepteur du connecteur. La valeur par défaut est `1` | Facultatif |

Les données sont toujours écrites dans Azure Cosmos DB au format JSON sans aucun schéma.

## <a name="supported-data-types"></a>Types de données pris en charge

Le connecteur du récepteur Azure Cosmos DB convertit l’enregistrement du récepteur en document JSON prenant en charge les types de schémas suivants :

| Type de schéma | Type de données JSON |
| :--- | :--- |
| Array | Array |
| Boolean | Booléen |
| Float32 | Nombre |
| Float64 | Nombre |
| Int8 | Nombre |
| Int16 | Nombre |
| Int32 | Number |
| Int64 | Nombre|
| Mappage | Objet (JSON)|
| Chaîne | Chaîne<br> Null |
| Structure | Objet (JSON) |

Le connecteur du récepteur prend également en charge les types logiques Avro suivants :

| Type de schéma | Type de données JSON |
| :--- | :--- |
| Date | Nombre |
| Heure | Nombre |
| Timestamp | Nombre |

> [!NOTE]
> La désérialisation d’octets n’est à l’heure actuelle pas prise en charge par le connecteur du récepteur Azure Cosmos DB.

## <a name="single-message-transformssmt"></a>Transformations de message unique (SMT)

En parallèle des paramètres du connecteur du récepteur, vous pouvez exiger l’utilisation des transformations de message unique (SMT, Single Message Transform) pour modifier les messages qui transitent par la plateforme Kafka Connecter. Pour plus d’informations, consultez la documentation [SMT Confluent](https://docs.confluent.io/platform/current/connect/transforms/overview.html).

### <a name="using-the-insertuuid-smt"></a>Utilisation de la transformation SMT InsertUUID

La transformation SMT InsertUUID permet d’ajouter automatiquement des identificateurs d’éléments. Avec la transformation SMT `InsertUUID` personnalisée, vous pouvez insérer le champ `id` avec une valeur UUID aléatoire pour chaque message, avant qu’il ne soit écrit dans Azure Cosmos DB.

> [!WARNING]
> N’utilisez cette transformation SMT que si les messages ne contiennent pas le champ `id`. Dans le cas contraire, les valeurs `id` sont remplacées ; vous risquez donc de vous retrouver avec des doublons d’éléments dans votre base de données. Il peut être rapide et facile d’utiliser des UUID comme identificateurs de messages, mais [il ne s’agit pas d’une clé de partition idéale](https://stackoverflow.com/questions/49031461/would-using-a-substring-of-a-guid-in-cosmosdb-as-partitionkey-be-a-bad-idea) dans Azure Cosmos DB.

### <a name="install-the-smt"></a>Installation de la transformation SMT

Pour pouvoir utiliser la transformation SMT `InsertUUID`, vous devez l’installer dans votre configuration de plateforme Confluent. Si vous vous servez de la configuration de plateforme Confluent de ce référentiel, la transformation est déjà incluse dans l’installation. Vous pouvez donc ignorer cette étape.

Vous pouvez également empaqueter la [source InsertUUID](https://github.com/confluentinc/kafka-connect-insert-uuid) pour créer un fichier JAR. Pour installer manuellement le connecteur à l’aide du fichier JAR, consultez ces [instructions](https://docs.confluent.io/current/connect/managing/install.html#install-connector-manually).

```bash
# clone the kafka-connect-insert-uuid repo
https://github.com/confluentinc/kafka-connect-insert-uuid.git
cd kafka-connect-insert-uuid

# package the source code into a JAR file
mvn clean package

# include the following JAR file in Confluent Platform installation
ls target/*.jar
```

### <a name="configure-the-smt"></a>Configuration de la transformation SMT

Dans la configuration du connecteur du récepteur, ajoutez les propriétés suivantes pour définir `id`.

```json
"transforms": "insertID",
"transforms.insertID.type": "com.github.cjmatta.kafka.connect.smt.InsertUuid$Value",
"transforms.insertID.uuid.field.name": "id"
```

Pour plus d’informations sur l’utilisation de cette transformation SMT, consultez le [référentiel InsertUUID](https://github.com/confluentinc/kafka-connect-insert-uuid).

### <a name="using-smts-to-configure-time-to-live-ttl"></a>Configuration de la durée de vie (TTL) avec des transformations SMT

Les transformations SMT `InsertField` et `Cast` permettent de configurer la durée de vie (TTL, Time to live) sur chacun des éléments créés dans Azure Cosmos DB. Activez la durée de vie sur le conteneur avant de l’activer au niveau élément. Pour plus d’informations, consultez la documentation [Durée de vie](how-to-time-to-live.md#enable-time-to-live-on-a-container-using-azure-portal).

Dans la configuration du connecteur du récepteur, ajoutez les propriétés suivantes pour spécifier la durée de vie en secondes. Dans l’exemple suivant, la durée de vie est définie sur 100 secondes. Si le message contient déjà le champ `TTL`, la valeur `TTL` est remplacée par ces transformations SMT.

```json
"transforms": "insertTTL,castTTLInt",
"transforms.insertTTL.type": "org.apache.kafka.connect.transforms.InsertField$Value",
"transforms.insertTTL.static.field": "ttl",
"transforms.insertTTL.static.value": "100",
"transforms.castTTLInt.type": "org.apache.kafka.connect.transforms.Cast$Value",
"transforms.castTTLInt.spec": "ttl:int32"
```

Pour plus d’informations sur ces transformations SMT, consultez la documentation [InsertField](https://docs.confluent.io/platform/current/connect/transforms/insertfield.html) et [Cast](https://docs.confluent.io/platform/current/connect/transforms/cast.html).

## <a name="troubleshooting-common-issues"></a>Résolution des problèmes courants

Voici des solutions à certains problèmes courants que vous pouvez rencontrer en utilisant le connecteur du récepteur Kafka.

### <a name="read-non-json-data-with-jsonconverter"></a>Lecture de données qui ne sont pas au format JSON avec JsonConverter

Si votre rubrique source comprend des données qui ne sont pas au format JSON dans Kafka et que vous tentez de les lire avec `JsonConverter`, vous obtenez l’exception suivante :

```console
org.apache.kafka.connect.errors.DataException: Converting byte[] to Kafka Connect data failed due to serialization error:
…
org.apache.kafka.common.errors.SerializationException: java.io.CharConversionException: Invalid UTF-32 character 0x1cfa7e2 (above 0x0010ffff) at char #1, byte #7)

```

Cette erreur est probablement due à la sérialisation des données de la rubrique source au format Avro ou autre, par exemple une chaîne CSV.

**Solution** : si les données de la rubrique sont au format Avro, modifiez le connecteur du récepteur Kafka Connect de sorte qu’il utilise `AvroConverter` (cf. ci-dessous).

```json
"value.converter": "io.confluent.connect.avro.AvroConverter",
"value.converter.schema.registry.url": "http://schema-registry:8081",
```

### <a name="read-non-avro-data-with-avroconverter"></a>Lecture de données qui ne sont pas au format Avro avec AvroConverter

Ce scénario s’applique lorsque vous essayez d’utiliser le convertisseur Avro pour lire des données d’une rubrique qui ne sont pas au format Avro. Sont incluses les données écrites par un sérialiseur Avro autre que celui de Confluent Schema Registry, qui possède son propre format filaire.

```console
org.apache.kafka.connect.errors.DataException: my-topic-name
at io.confluent.connect.avro.AvroConverter.toConnectData(AvroConverter.java:97)
…
org.apache.kafka.common.errors.SerializationException: Error deserializing Avro message for id -1
org.apache.kafka.common.errors.SerializationException: Unknown magic byte!

```

**Solution** : vérifiez le format de sérialisation de la rubrique source. Ensuite, basculez du connecteur du récepteur Kafka Connect vers le convertisseur approprié ou remplacez le format en amont par Avro.

### <a name="read-a-json-message-without-the-expected-schemapayload-structure"></a>Lecture d’un message JSON sans la structure schéma/charge utile attendue

Kafka Connect prend en charge une structure spéciale de messages JSON contenant à la fois la charge utile et le schéma.

```json
{
  "schema": {
    "type": "struct",
    "fields": [
      {
        "type": "int32",
        "optional": false,
        "field": "userid"
      },
      {
        "type": "string",
        "optional": false,
        "field": "name"
      }
    ]
  },
  "payload": {
    "userid": 123,
    "name": "Sam"
  }
}
```

Si vous tentez de lire des données JSON qui ne contiennent pas les données de cette structure, vous obtenez l’erreur suivante :

```none
org.apache.kafka.connect.errors.DataException: JsonConverter with schemas.enable requires "schema" and "payload" fields and may not contain additional fields. If you are trying to deserialize plain JSON data, set schemas.enable=false in your converter configuration.
```

En termes clairs, la seule structure JSON valide pour `schemas.enable=true` possède pour éléments de niveau supérieur des champs de schéma et de charge utile (cf. ci-dessus). Comme l’indique le message d’erreur, si vous ne disposez que de données JSON brutes, modifiez ainsi la configuration de votre connecteur :

```json
"value.converter": "org.apache.kafka.connect.json.JsonConverter",
"value.converter.schemas.enable": "false",
```

## <a name="limitations"></a>Limites

* L’autocréation de bases de données et de conteneurs dans Azure Cosmos DB n’est pas prise en charge. La base de données et les conteneurs doivent déjà exister et être configurés correctement.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le flux de modification dans Azure Cosmo DB, consultez les documents suivants :

* [Introduction au flux de modification](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
* [Lecture à partir du flux de modification](read-change-feed.md)