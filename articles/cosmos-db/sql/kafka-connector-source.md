---
title: Kafka Connect pour Azure Cosmos DB - Connecteur source
description: Le connecteur source Azure Cosmos DB permet de lire les données du flux de modification Azure Cosmos DB et de les publier dans une rubrique Kafka. Kafka Connect pour Azure Cosmos DB est un connecteur permettant de lire et écrire des données dans Azure Cosmos DB.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: kuthapar
ms.openlocfilehash: 99879c5fa14cc91e8a39b99ff1a428f31bfde744
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116051"
---
# <a name="kafka-connect-for-azure-cosmos-db---source-connector"></a>Kafka Connect pour Azure Cosmos DB - Connecteur source
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Kafka Connect pour Azure Cosmos DB est un connecteur permettant de lire et écrire des données dans Azure Cosmos DB. Le connecteur source Azure Cosmos DB permet de lire les données du flux de modification Azure Cosmos DB et de les publier dans une rubrique Kafka.

## <a name="prerequisites"></a>Prérequis

* Partez de la [configuration de plateforme Confluent](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Confluent_Platform_Setup.md), qui offre un environnement complet. Si vous ne souhaitez pas utiliser la plateforme Confluent, vous devrez installer et configurer vous-même ZooKeeper, Apache Kafka et Kafka Connect. Il vous faudra également installer et configurer manuellement les connecteurs Azure Cosmos DB.
* Créez un compte Azure Cosmos DB et un conteneur ([guide de configuration](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/CosmosDB_Setup.md))
* Munissez-vous d'un interpréteur de commandes Bash, testé sur GitHub Codespaces, Mac, Ubuntu et Windows avec WSL2. Cet interpréteur de commandes ne fonctionne pas dans Cloud Shell ou WSL1.
* Télécharger [Java 11 (ou version ultérieure)](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)
* Télécharger [Maven](https://maven.apache.org/download.cgi)

## <a name="install-the-source-connector"></a>Installer le connecteur source

Si vous utilisez la [configuration de plateforme Confluent](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Confluent_Platform_Setup.md) recommandée, le connecteur source Azure Cosmos DB est inclus dans l'installation. Vous pouvez donc ignorer cette étape.

Sinon, vous pouvez utiliser le fichier JAR de la dernière [mise en production](https://github.com/microsoft/kafka-connect-cosmosdb/releases) et installer le connecteur manuellement. Pour plus d'informations, consultez ces [instructions](https://docs.confluent.io/current/connect/managing/install.html#install-connector-manually). Vous pouvez également empaqueter un nouveau fichier JAR à partir du code source :

```bash
# clone the kafka-connect-cosmosdb repo if you haven't done so already
git clone https://github.com/microsoft/kafka-connect-cosmosdb.git
cd kafka-connect-cosmosdb

# package the source code into a JAR file
mvn clean package

# include the following JAR file in Confluent Platform installation
ls target/*dependencies.jar
```

## <a name="create-a-kafka-topic"></a>Création d’une rubrique Kafka

Créez une rubrique Kafka à l'aide de l'outil Confluent Control Center. Dans ce scénario, nous allons créer une rubrique Kafka nommée « apparels » et y écrire des données JSON sans schéma incorporé. Pour créer une rubrique dans l'outil Control Center, consultez la [documentation de création d'une rubrique Kafka](https://docs.confluent.io/platform/current/quickstart/ce-docker-quickstart.html#step-2-create-ak-topics).

## <a name="create-the-source-connector"></a>Créer le connecteur source

### <a name="create-the-source-connector-in-kafka-connect"></a>Créer le connecteur source dans Kafka Connect

Pour créer le connecteur source Azure Cosmos DB dans Kafka Connect, utilisez la configuration JSON suivante. Veillez à remplacer les valeurs d'espace réservé des propriétés `connect.cosmos.connection.endpoint` et `connect.cosmos.master.key` que vous avez dû enregistrer à partir du guide de configuration d'Azure Cosmos DB dans les conditions préalables.

```json
{
  "name": "cosmosdb-source-connector",
  "config": {
    "connector.class": "com.azure.cosmos.kafka.connect.source.CosmosDBSourceConnector",
    "tasks.max": "1",
    "key.converter": "org.apache.kafka.connect.json.JsonConverter",
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",
    "connect.cosmos.task.poll.interval": "100",
    "connect.cosmos.connection.endpoint": "https://<cosmosinstance-name>.documents.azure.com:443/",
    "connect.cosmos.master.key": "<cosmosdbprimarykey>",
    "connect.cosmos.databasename": "kafkaconnect",
    "connect.cosmos.containers.topicmap": "apparels#kafka",
    "connect.cosmos.offset.useLatest": false,
    "value.converter.schemas.enable": "false",
    "key.converter.schemas.enable": "false"
  }
}
```

Pour plus d'informations sur chacune de ces propriétés de configuration, consultez la section [Propriétés de la source](#source-configuration-properties). Une fois toutes les valeurs renseignées, enregistrez le fichier JSON en local. Vous pourrez l'utiliser pour créer le connecteur à l'aide de l'API REST.

#### <a name="create-connector-using-control-center"></a>Créer un connecteur à l'aide de l'outil Control Center

Une option simple pour créer le connecteur consiste à utiliser le portail Confluent Control Center. Suivez le [guide de configuration de Confluent](https://docs.confluent.io/platform/current/quickstart/ce-docker-quickstart.html#step-3-install-a-ak-connector-and-generate-sample-data) pour créer un connecteur à partir de Control Center. Lors de la configuration, plutôt que d'utiliser l'option `DatagenConnector`, utilisez la vignette `CosmosDBSourceConnector`. Et lors de la configuration du connecteur source, renseignez les valeurs de la même manière que dans le fichier JSON.

Sur la page des connecteurs, vous pouvez également charger le fichier JSON créé à la section précédente à l'aide de l'option **Charger le fichier de configuration du connecteur**.

:::image type="content" source="./media/kafka-connector-source/upload-connector-config.png" alt-text="Chargement de la configuration du connecteur.":::

#### <a name="create-connector-using-rest-api"></a>Créer un connecteur à l'aide de l'API REST

Créez le connecteur source à l'aide de l'API REST Connect

```bash
# Curl to Kafka connect service
curl -H "Content-Type: application/json" -X POST -d @<path-to-JSON-config-file> http://localhost:8083/connectors
```

## <a name="insert-document-into-azure-cosmos-db"></a>Insérer un document dans Azure Cosmos DB

1. Connectez-vous au [portail Azure](https://portal.azure.com/learn.docs.microsoft.com) et accédez à votre compte Azure Cosmos DB.
1. Ouvrez l'onglet **Exploration des données** et sélectionnez **Bases de données**
1. Ouvrez la base de données « kafkaconnect » et le conteneur « kafka » créés précédemment.
1. Pour créer un nouveau document JSON, dans le volet API SQL, développez le conteneur « kafka », sélectionnez **Éléments**, puis sélectionnez **Nouvel élément** dans la barre d'outils.
1. À présent, ajoutez un document au conteneur, avec la structure suivante. Collez l'exemple de code JSON suivant dans l'onglet Éléments, en écrasant le contenu existant :

   ``` json
 
   {
     "id": "2",
     "productId": "33218897",
     "category": "Women's Outerwear",
     "manufacturer": "Contoso",
     "description": "Black wool pea-coat",
     "price": "49.99",
     "shipping": {
       "weight": 2,
       "dimensions": {
         "width": 8,
         "height": 11,
         "depth": 3
       }
     }
   }
 
   ```

1. Sélectionnez **Enregistrer**.
1. Vérifiez que le document a été enregistré en consultant les éléments du menu de gauche.

### <a name="confirm-data-written-to-kafka-topic"></a>Vérifier les données écrites dans la rubrique Kafka

1. Ouvrez l'interface utilisateur de la rubrique Kafka sur `<http://localhost:9000>`.
1. Sélectionnez la rubrique Kafka « apparels » que vous avez créée.
1. Vérifiez que le document que vous avez précédemment inséré dans Azure Cosmos DB apparaît dans la rubrique Kafka.

### <a name="cleanup"></a>Nettoyage

Pour supprimer le connecteur source que vous avez créé dans Confluent Control Center, accédez à celui-ci, puis sélectionnez l'icône **Supprimer**.

:::image type="content" source="./media/kafka-connector-source/delete-source-connector.png" alt-text="Suppression du connecteur dans Confluent Center":::

Vous pouvez également utiliser l'API REST du connecteur :

```bash
# Curl to Kafka connect service
curl -X DELETE http://localhost:8083/connectors/cosmosdb-source-connector
```

Pour supprimer le service Azure Cosmos DB et son groupe de ressources à l'aide d'Azure CLI, consultez cette [procédure](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/CosmosDB_Setup.md#cleanup).

## <a name="source-configuration-properties"></a>Propriétés de la configuration de la source

Les paramètres suivants sont utilisés pour configurer le connecteur source Kafka. Ces valeurs de configuration déterminent quel conteneur Azure Cosmos DB est utilisé, les données à partir desquelles les rubriques Kafka sont écrites et les formats utilisés pour sérialiser les données. Pour accéder à un exemple comportant les valeurs par défaut, consultez ce [fichier de configuration](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/src/docker/resources/source.example.json).

| Nom | Type | Description | Obligatoire ou facultatif |
| :--- | :--- | :--- | :--- |
| connector.class | String | Nom de la classe de la source Azure Cosmos DB. Celui-ci doit être défini sur `com.azure.cosmos.kafka.connect.source.CosmosDBSourceConnector` | Obligatoire |
| connect.cosmos.databasename | String | Nom de la base de données à lire. | Obligatoire |
| connect.cosmos.master.key | String | Clé primaire Azure Cosmos DB. | Obligatoire |
| connect.cosmos.connection.endpoint | URI | Point de terminaison du compte. | Obligatoire |
| connect.cosmos.containers.topicmap | String | Mappages entre rubrique et conteneur, séparés par des virgules. Par exemple, topic1#coll1, topic2#coll2 | Obligatoire |
| connect.cosmos.messagekey.enabled | Boolean | Cette valeur indique si la clé de message Kafka doit être définie. La valeur par défaut est `true` | Obligatoire |
| connect.cosmos.messagekey.field | String | Utilisez la valeur du champ du document comme clé de message. La valeur par défaut est `id`. | Obligatoire |
| connect.cosmos.offset.useLatest | Boolean |  Définissez cette propriété sur `true` pour utiliser le décalage source le plus récent. Définissez-la sur `false` pour utiliser le décalage enregistré le plus ancien. La valeur par défaut est `false`. | Obligatoire |
| connect.cosmos.task.poll.interval | Int | Intervalle d'interrogation du conteneur du flux de modification. | Obligatoire |
| key.converter | String | Format de sérialisation des données de clé écrites dans la rubrique Kafka. | Obligatoire |
| value.converter | String | Format de sérialisation des données de valeur écrites dans la rubrique Kafka. | Obligatoire |
| key.converter.schemas.enable | String | Définissez cette propriété sur `true` si les données de clé possèdent un schéma incorporé. | Facultatif |
| value.converter.schemas.enable | String | Définissez cette propriété sur `true` si les données de clé possèdent un schéma incorporé. | Facultatif |
| tasks.max | Int | Nombre maximum de tâches sources de connecteurs. La valeur par défaut est `1`. | Facultatif |

## <a name="supported-data-types"></a>Types de données pris en charge

Le connecteur source Azure Cosmos DB convertit le document JSON en schéma et prend en charge les types de données JSON suivants :

| Type de données JSON | Type de schéma |
| :--- | :--- |
| Array | Array |
| Boolean | Boolean | 
| Number | Float32<br>Float64<br>Int8<br>Int16<br>Int32<br>Int64|
| Null | String |
| Objet (JSON)| Structure|
| String | String |

## <a name="next-steps"></a>Étapes suivantes

* Kafka Connect pour Azure Cosmos DB [connecteur récepteur](kafka-connector-sink.md)