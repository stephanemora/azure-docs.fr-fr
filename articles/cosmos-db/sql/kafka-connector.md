---
title: Utiliser Kafka Connect pour Azure Cosmos DB pour lire et écrire des données
description: Kafka Connect pour Azure Cosmos DB est un connecteur permettant de lire et écrire des données dans Azure Cosmos DB. Kafka Connect est un outil pour le streaming évolutif et fiable des données entre Apache Kafka et d’autres systèmes
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: kuthapar
ms.openlocfilehash: 02489a961edf3c9044209da64882d0ab60d20bb4
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116352"
---
# <a name="kafka-connect-for-azure-cosmos-db"></a>Kafka Connect pour Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

[Kafka Connect](http://kafka.apache.org/documentation.html#connect) est un outil pour le streaming évolutif et fiable des données entre Apache Kafka et d’autres systèmes. À l’aide de Kafka Connect, vous pouvez définir des connecteurs qui déplacent des jeux de données volumineux vers et depuis Kafka. Kafka Connect pour Azure Cosmos DB est un connecteur permettant de lire et écrire des données dans Azure Cosmos DB.

## <a name="source--sink-connectors-semantics"></a>Sémantique des connecteurs sources ou récepteurs

* **Connecteur source** : actuellement, ce connecteur prend en charge la sémantique « at-least-once » (au moins une fois) avec plusieurs tâches et « exactly once » (une seule fois) pour les tâches uniques.

* **Connecteur récepteur** : ce connecteur prend entièrement en charge la sémantique « exactly once » (une seule fois).

## <a name="supported-data-formats"></a>Formats de données pris en charge

Les connecteurs source et récepteur peuvent être configurés pour prendre en charge les formats de données suivants :

| Format | Description |
| :----------- | :---------- |
| JSON simple  | Structure d’enregistrement JSON sans schéma joint. |
| JSON avec schéma | Structure d’enregistrement JSON avec des informations de schéma explicites pour garantir que les données correspondent au format attendu. |
| AVRO | Framework d’appel de procédure distante et de sérialisation des données orientés ligne, développé dans le cadre du projet Apache Hadoop. Il utilise JSON pour définir les types de données et les protocoles, et il sérialise les données dans un format binaire compact.

Les paramètres de clé et de valeur, notamment le format et la sérialisation, peuvent être configurés indépendamment dans Kafka. Par conséquent, il est possible d’utiliser des formats de données différents respectivement pour les clés et les valeurs. Pour tenir compte des différents formats de données, il existe une configuration de convertisseur pour `key.converter` et `value.converter`.

## <a name="converter-configuration-examples"></a>Exemples de configuration de convertisseur

### <a name="plain-json"></a><a id="json-plain"></a>JSON simple

Si vous devez utiliser JSON sans registre de schéma pour les données de connexion, utilisez le convertisseur `JsonConverter` pris en charge avec Kafka. L’exemple suivant montre les propriétés de clé et valeur `JsonConverter` qui sont ajoutées à la configuration :

  ```java
  key.converter=org.apache.kafka.connect.json.JsonConverter
  key.converter.schemas.enable=false
  value.converter=org.apache.kafka.connect.json.JsonConverter
  value.converter.schemas.enable=false
  ```

### <a name="json-with-schema"></a><a id="json-with-schema"></a>JSON avec schéma

Définissez les propriétés `key.converter.schemas.enable` et `value.converter.schemas.enable` sur true pour que la clé ou la valeur soit traitée comme un objet JSON composite qui contient à la fois un schéma interne et les données. Sans ces propriétés, la clé ou la valeur est traitée comme un code JSON simple. 

  ```java
  key.converter=org.apache.kafka.connect.json.JsonConverter
  key.converter.schemas.enable=true
  value.converter=org.apache.kafka.connect.json.JsonConverter
  value.converter.schemas.enable=true
  ```

Le message résultant à Kafka doit ressembler à l’exemple ci-dessous, avec le schéma et la charge utile en tant qu’éléments de niveau supérieur dans le code JSON :

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
      ],
      "optional": false,
      "name": "ksql.users"
    },
    "payload": {
      "userid": 123,
      "name": "user's name"
    }
  }
  ```

> [!NOTE]
> Le message écrit dans Azure Cosmos DB est constitué du schéma et de la charge utile. Notez la taille du message, ainsi que la proportion qui est constituée de la charge utile par rapport au schéma. Le schéma est répété dans chaque message que vous écrivez dans Kafka. Dans les scénarios de ce type, vous pouvez utiliser un format de sérialisation comme le schéma JSON ou AVRO, dans lequel le schéma est stocké séparément et le message contient uniquement la charge utile.

### <a name="avro"></a><a id="avro"></a>AVRO

Le connecteur Kafka Connect prend en charge le format de données AVRO. Pour utiliser le format AVRO, configurez `AvroConverter` de telle manière que Kafka Connect sache comment utiliser les données AVRO. Kafka Connect pour Azure Cosmos DB a été testé avec [AvroConverter](https://www.confluent.io/hub/confluentinc/kafka-connect-avro-converter) fourni par Confluent, sous licence Apache 2.0. Vous pouvez également utiliser un autre convertisseur personnalisé si vous préférez.

Kafka traite les clés et les valeurs indépendamment. Spécifiez les propriétés `key.converter` et `value.converter` selon les besoins dans la configuration Worker. Quand vous utilisez `AvroConverter`, ajoutez une propriété de convertisseur supplémentaire qui fournit l’URL pour le registre de schémas. L’exemple suivant montre les propriétés de clé et valeur AvroConverter qui sont ajoutées à la configuration :

  ```java
  key.converter=io.confluent.connect.avro.AvroConverter
  key.converter.schema.registry.url=http://schema-registry:8081
  value.converter=io.confluent.connect.avro.AvroConverter
  value.converter.schema.registry.url=http://schema-registry:8081
  ```

## <a name="choose-a-conversion-format"></a>Choisir un format de conversion

Voici quelques points à prendre en compte pour choisir un format de conversion :

* Lors de la configuration d’un **connecteur source** :

  * Si vous souhaitez que Kafka Connect inclue le code JSON simple dans le message qu’il écrit dans Kafka, définissez la configuration [JSON simple](#json-plain).

  * Si vous souhaitez que Kafka Connect inclue le schéma dans le message qu’il écrit dans Kafka, définissez la configuration [JSON avec schéma](#json-with-schema).

  * Si vous souhaitez que Kafka Connect inclue le format AVRO dans le message qu’il écrit dans Kafka, définissez la configuration [AVRO](#avro).

* Si vous utilisez des données JSON à partir d’une rubrique Kafka dans un **connecteur récepteur**, découvrez comment le code JSON a été sérialisé lors de l’écriture dans la rubrique Kafka :

  * S’il a été écrit avec le sérialiseur JSON, définissez Kafka Connect pour qu’il utilise le convertisseur JSON `(org.apache.kafka.connect.json.JsonConverter)`.

    * Si les données JSON ont été écrites sous forme de chaîne simple, déterminez si les données incluent un schéma imbriqué ou une charge utile. Si c’est le cas, définissez la configuration [JSON avec schéma](#json-with-schema).
    * Toutefois, si vous utilisez des données JSON et qu’elles ne disposent pas de la construction de schéma ou de charge utile, vous devez indiquer à Kafka Connect de **ne pas** rechercher un schéma en définissant la valeur `schemas.enable=false` conformément à la configuration [JSON simple](#json-plain).

  * S’il a été écrit avec le sérialiseur AVRO, définissez Kafka Connect pour qu’il utilise le convertisseur AVRO `(io.confluent.connect.avro.AvroConverter)` conformément à la configuration [AVRO](#avro).

## <a name="configuration"></a>Configuration

### <a name="common-configuration-properties"></a>Propriétés de configuration communes

Les connecteurs source et récepteur partagent les propriétés de configuration communes suivantes :

| Name | Type | Description | Obligatoire ou facultatif |
| :--- | :--- | :--- | :--- |
| connect.cosmos.connection.endpoint | URI | Chaîne d’URI de point de terminaison Cosmos | Requis |
| connect.cosmos.master.key | string | Clé primaire Azure Cosmos DB à laquelle le récepteur se connecte. | Requis |
| connect.cosmos.databasename | string | Nom de la base de données Azure Cosmos dans laquelle le récepteur écrit. | Requis |
| connect.cosmos.containers.topicmap | string | Mappage entre les rubriques Kafka et les conteneurs Azure Cosmos DB. Il présente un format CSV comme `topic#container,topic2#container2` | Requis |

Pour une configuration spécifique au connecteur récepteur, consultez la [documentation du connecteur récepteur](kafka-connector-sink.md)

Pour une configuration spécifique au connecteur source, consultez la [documentation du connecteur source](kafka-connector-source.md)

## <a name="common-configuration-errors"></a>Erreurs de configuration fréquentes

En cas de configuration incorrecte des convertisseurs dans Kafka Connect, cela peut entraîner des erreurs. Ces erreurs apparaissent au niveau du récepteur du connecteur Kafka Connect, car vous essayez de désérialiser les messages déjà stockés dans Kafka. Les problèmes de convertisseur ne se produisent généralement pas dans la source, car la sérialisation est définie au niveau de la source.

Pour plus d’informations, consultez le document [Erreurs de configuration fréquentes](https://www.confluent.io/blog/kafka-connect-deep-dive-converters-serialization-explained/#common-errors).

## <a name="project-setup"></a>Configuration du projet

Reportez-vous à [Procédure pas à pas destinée aux développeurs et configuration du projet](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Developer_Walkthrough.md) pour obtenir des instructions de configuration initiales.

## <a name="performance-testing"></a>Tests de performances

Pour plus d’informations sur les tests de performances exécutés pour les connecteurs récepteur et source, consultez le [Document de test des performances](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Performance_Testing.md).

Reportez-vous à la [Configuration de l’environnement de performances](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/src/perf/README.md) pour obtenir les étapes exactes de déploiement de l’environnement de test de performances pour les connecteurs.

## <a name="resources"></a>Ressources

* [Kafka Connect](http://kafka.apache.org/documentation.html#connect)
* [Présentation approfondie de Kafka Connect – explication des convertisseurs et de la sérialisation](https://www.confluent.io/blog/kafka-connect-deep-dive-converters-serialization-explained/)

## <a name="next-steps"></a>Étapes suivantes

* Kafka Connect pour Azure Cosmos DB [connecteur source](kafka-connector-source.md)
* Kafka Connect pour Azure Cosmos DB [connecteur récepteur](kafka-connector-sink.md)