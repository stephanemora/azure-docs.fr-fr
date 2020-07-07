---
title: CREATE EXTERNAL STREAM (Transact-SQL) - Azure SQL Edge (préversion)
description: En savoir plus sur l'instruction CREATE EXTERNAL STREAM dans Azure SQL Edge (préversion)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 9e1f672a62ee7687fec9cea96ca03240c893ba95
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84233333"
---
# <a name="create-external-stream-transact-sql"></a>CREATE EXTERNAL STREAM (Transact-SQL)

L’objet de flux externe présente une double fonction d'entrée et de sortie. Il peut être utilisé en tant qu'entrée pour interroger les données de streaming à partir de services d’ingestion d’événements tels qu’Azure Event ou IoT Hubs, ou en tant que sortie pour spécifier où et comment stocker les résultats d’une requête de streaming.

Un FLUX EXTERNE peut également être spécifié et créé en tant que sortie et entrée pour des services tels que Event Hub ou Stockage Blob. C’est notamment le cas des scénarios de chaînage dans lesquels une requête de streaming conserve les résultats dans le flux externe en tant que sortie et une autre requête de streaming lit le même flux externe en tant qu'entrée. 


## <a name="syntax"></a>Syntaxe

```sql
CREATE EXTERNAL STREAM {external_stream_name}  
(column definition [,column definitions ] * ) --Used for input - optional 
WITH  
(  
  DATA_SOURCE = <data_source_name>, 
  LOCATION = <location_name>, 
  EXTERNAL_FILE_FORMAT = <external_file_format_name>, --Used for input - optional 
   
  INPUT_OPTIONS =  
    ‘CONSUMER_GROUP=<consumer_group_name>; 
    ‘TIME_POLICY=<time_policy>; 
    LATE_EVENT_TOLERANCE=<late_event_tolerance_value>; 
    OUT_OF_ORDER_EVENT_TOLERANCE=<out_of_order_tolerance_value> 
     
    /* Edge options */ 
  , 
  OUTPUT_OPTIONS =  
    ‘REJECT_POLICY=<reject_policy>; 
    MINIMUM_ROWS=<row_value>; 
    MAXIMUM_TIME=<time_value_minutes>; 
    PARTITION_KEY_COLUMN=<partition_key_column_name>; 
    PROPERTY_COLUMNS=(); 
    SYSTEM_PROPERTY_COLUMNS=(); 
    PARTITION_KEY=<partition_key_name>; 
    ROW_KEY=<row_key_name>; 
    BATCH_SIZE=<batch_size_value>; 
    MAXIMUM_BATCH_COUNT=<batch_value>;  
    STAGING_AREA=<blob_data_source>’ 
     
    /* Edge options */ TAGS=<tag_column_value> 
); 
```


## <a name="arguments"></a>Arguments


- [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- **LOCATION** : Spécifie le nom des données ou de l’emplacement réels dans la source de données. Dans le cas d’un objet de flux Edge Hub ou Kafka, l'emplacement spécifie le nom de la rubrique Edge Hub ou Kafka où lire et écrire.
- **INPUT_OPTIONS** : Spécifie les options en tant que paires clé-valeur pour des services tels que Event Hub et IoT Hub correspondant à des entrées de requêtes de streaming
    - CONSUMER_GROUP : Event Hub et IoT Hub limitent le nombre de lecteurs au sein d'un groupe de consommateurs (à 5). Laissez ce champ vide pour utiliser le groupe de consommateurs « $Default ».
      - S’applique à Event Hub et IoT Hub
    - TIME_POLICY : Indique s’il convient de supprimer des événements ou d'ajuster l’heure des événements lorsque des événements tardifs ou en désordre dépassent leur valeur de tolérance.
      - S’applique à Event Hub et IoT Hub
    - LATE_EVENT_TOLERANCE : Délai maximal acceptable. Ce délai correspond à la différence entre le timestamp de l’événement et l’horloge système.
      - S’applique à Event Hub et IoT Hub
    - OUT_OF_ORDER_EVENT_TOLERANCE : Des événements peuvent arriver en désordre après avoir transité de l'entrée vers la requête de streaming. Vous pouvez accepter ces événements tels quels ou choisir de les suspendre pendant une période définie afin de les mettre dans l'ordre souhaité.
      - S’applique à Event Hub et IoT Hub
- **OUTPUT_OPTIONS** : Spécifie les options en tant que paires clé-valeur pour les services pris en charge correspondant à des entrées de requêtes de streaming 
  - REJECT_POLICY :  DROP | RETRY Spécifie les stratégies en matière de gestion des erreurs de données lorsque des erreurs de conversion de données se produisent. 
    - S’applique à toutes les sorties prises en charge. 
  - MINIMUM_ROWS :  
    Nombre minimal de lignes requises par lot écrit dans une sortie. Pour Parquet, chaque lot crée un nouveau fichier. 
    - S’applique à toutes les sorties prises en charge. 
  - MAXIMUM_TIME :  
    Délai d’attente maximal par lot. À l’issue de cette période, le lot est écrit dans la sortie même si l’exigence de lignes minimum n’est pas remplie. 
    - S’applique à toutes les sorties prises en charge. 
  - PARTITION_KEY_COLUMN :  
    Colonne utilisée pour la clé de partition. 
    - S’applique à Event Hub et à la rubrique Service Bus 
  - PROPERTY_COLUMNS :  
    Liste de noms de colonne de sortie séparés par des virgules à attacher aux messages en tant que propriétés personnalisées, le cas échéant  
    - S’applique à Event Hub et à la rubrique et la file d'attente Service Bus 
  - SYSTEM_PROPERTY_COLUMNS :  
    Collection JSON de paires nom/valeur de noms de propriétés système et de colonnes de sortie à remplir sur les messages Service Bus. Par exemple, {« MessageId » : « Column1 », « PartitionKey » : « Column2 »} 
    - S’applique à la rubrique et la file d'attente Service Bus 
  - PARTITION_KEY :  
    Nom de la colonne de sortie contenant la clé de partition. La clé de partition est un identificateur unique pour la partition dans une table donnée qui constitue la première partie de la clé primaire d’une entité. C’est une valeur de chaîne qui peut atteindre 1 Ko. 
    - S'applique à Stockage Table ou Azure Function 
  - ROW_KEY :  
    Nom de la colonne de sortie contenant la clé de ligne. La clé de ligne est un identificateur unique pour une entité dans une partition donnée. Elle constitue la deuxième partie de la clé primaire d'une entité. La clé de ligne est une valeur de chaîne qui peut atteindre 1 Ko. 
    - S'applique à Stockage Table ou Azure Function 
  - BATCH_SIZE :  
    Correspond au nombre de transactions pour le stockage de table avec un maximum de 100 enregistrements. Pour Azure Functions, il s’agit de la taille du lot en octets envoyé à la fonction par appel, la valeur (256 Ko par défaut). 
    - S'applique à Stockage Table ou Azure Function 
  - MAXIMUM_BATCH_COUNT :  
    Nombre maximal d'événements envoyés à la fonction par appel pour la fonction Azure (100 par défaut). Pour SQL Database, cela représente le nombre maximal d'enregistrements envoyés avec chaque transaction d'insertion en bloc (10 000 par défaut). 
    - S'applique à SQL Database et Azure Function 
  - STAGING_AREA : Objet EXTERNAL DATA SOURCE vers Stockage Blob Zone de transit pour l’ingestion de données à haut débit dans SQL Data Warehouse 
    - S'applique à SQL Data Warehouse


## <a name="examples"></a>Exemples

### <a name="example-1---edgehub"></a>Exemple 1 - EdgeHub

Tapez : Entrée ou Sortie<br>
Paramètres :
- Entrée ou Sortie
  - Alias 
  - Format de sérialisation de l’événement 
  - Encodage 
- Entrée uniquement : 
  - Type de compression d’événement 

Syntaxe :

```sql
CREATE EXTERNAL DATA SOURCE MyEdgeHub 
WITH  
(      
  LOCATION = 'edgehub://'       
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat  
WITH (  
   FORMAT_TYPE = 'JSON', 
); 
 
CREATE EXTERNAL STREAM Stream_A  
WITH    
(   
   DATA_SOURCE = MyEdgeHub, 
   EXTERNAL_FILE_FORMAT = myFileFormat, 
   LOCATION = ‘<mytopicname>’, 
   OUTPUT_OPTIONS =   
     ‘REJECT_TYPE: Drop’ 
);
```


### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>Exemple 2 - Azure SQL Database, Azure SQL Edge, SQL Server

Tapez : Output<br>
Paramètres :
- Alias de sortie  
- Base de données (nécessaire pour SQL Database) 
- Serveur (nécessaire pour SQL Database) 
- Nom d'utilisateur (nécessaire pour SQL Database) 
- Mot de passe (nécessaire pour SQL Database) 
- Table de charge de travail 
- Fusionner toutes les partitions d’entrée dans une seule écriture ou hériter du schéma de partition de l'étape de requête ou de l'entrée précédente (requis pour SQL Database) 
- Nombre maximal de lots 

Syntaxe :

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>’, 
SECRET = '<password>'; 
 
-- Azure SQL Database 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net’, 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <sqlserver://<ipaddress>,<port>’, 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Database/Edge 
CREATE EXTERNAL STREAM Stream_A 
WITH   
(  
    DATA_SOURCE = MyTargetSQLTable, 
    LOCATION = ‘<DatabaseName>.<SchemaName>.<TableName>’ 
   --Note: If table is container in the database, <TableName> should be sufficient 
   --Note: Do not need external file format in this case 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop 
); 
```

### <a name="example-3---kafka"></a>Exemple 3 - Kafka

Tapez : Entrée<br>
Paramètres :

- Serveur de démarrage Kafka 
- Nom de la rubrique Kafka 
- Nombre de partitions sources 

Syntaxe :

```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets 
WITH 
( 
  --The location maps to KafkaBootstrapServer 
  LOCATION = ' kafka://<kafkaserver>:<ipaddress>’, 
  CREDENTIAL = kafkaCredName 
 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’, 
    DELIMITER = ‘|’ 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyKafka_tweets, 
    LOCATION = ‘<KafkaTopicName>’, 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘PARTITIONS: 5’ 
); 
```

### <a name="example-4---blob-storage"></a>Exemple 4 - Stockage Blob

Tapez : Entrée ou Sortie<br>
Paramètres :
- Entrée ou sortie :
  - Alias 
  - Compte de stockage 
  - Clé du compte de stockage 
  - Conteneur 
  - Modèle de chemin d’accès 
  - Format de la date 
  - Format de l’heure 
  - Format de sérialisation de l’événement 
  - Encodage 
- Entrée uniquement : 
  - Partitions (entrée) 
  - Type de compression d’événement (entrée) 
- Sortie uniquement : 
  - Nombre minimal de lignes (sortie) 
  - Durée maximale (sortie) 
  - Mode d'authentification (sortie) 

Syntaxe :

```sql
CREATE DATABASE SCOPED CREDENTIAL StorageAcctCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = 'AccountKey'; 
 
CREATE DATABASE SCOPED CREDENTIAL StorageAcctCredNameMSI 
WITH IDENTITY = 'Managed Identity’; 
 
CREATE EXTERNAL DATA SOURCE MyBlobStorage_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = eventHubCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATE_FORMAT = 'YYYY/MM/DD HH', --Both Date and Time format 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyBlobStorage_tweets, 
    LOCATION = ‘<path_pattern>’, 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘PARTITIONS: 1’, 
  
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: (), 
      MINUMUM_ROWS: 100000, 
      MAXIMUM_TIME: 60’ 
); 
```

### <a name="example-5---event-hub"></a>Exemple 5 - Event Hub

Tapez : Entrée ou Sortie<br>
Paramètres :
- Entrée ou sortie :
  - Alias 
  - Espace de noms Service Bus 
  - Nom de l’Event Hub 
  - Nom de la stratégie du hub d’événements 
  - Clé de stratégie Event Hub 
  - Format de sérialisation de l’événement 
  - Encodage 
- Entrée uniquement : 
  - Groupe de consommateurs du hub d’événements 
  - Type de compression d’événement 
- Sortie uniquement : 
  - Colonne de clé de partition 
  - Colonnes de propriété 

Syntaxe :

```sql
CREATE DATABASE SCOPED CREDENTIAL eventHubCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyEventHub_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = eventHubCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’, 
    DELIMITER = ‘|’ 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyEventHub_tweets, 
    LOCATION = ‘<topicname>’, 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
 
    INPUT_OPTIONS =  
      ‘CONSUMER_GROUP: FirstConsumerGroup’, 
          
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: ()’ 
);
```

### <a name="example-6---iot-hub"></a>Exemple 6 - IoT Hub

Tapez : Entrée<br>
Paramètres :

- Alias d’entrée 
- IoT Hub 
- Point de terminaison 
- Nom de la stratégie d'accès partagé 
- Clé de la stratégie d’accès partagé 
- Groupe de consommateurs 
- Format de sérialisation de l’événement 
- Encodage 
- Type de compression d’événement 

Syntaxe :

```sql
CREATE DATABASE SCOPED CREDENTIAL IoTHubCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyIoTHub_tweets 
WITH 
(     
  LOCATION = ' iot://iot_hub_name.azure-devices.net’, 
  CREDENTIAL = IoTHubCredName 
);  

CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyIoTHub_tweets, 
    LOCATION = ‘<name>’, 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘ENDPOINT: Messaging, 
      CONSUMER_GROUP: ‘FirstConsumerGroup’ 
); 
```

### <a name="example-7---azure-synapse-analytics-formerly-sql-data-warehouse"></a>Exemple 7 - Azure Synapse Analytics (anciennement SQL Data Warehouse)

Tapez : Output<br>
Paramètres :
- Alias de sortie 
- Base de données 
- Serveur 
- Nom d’utilisateur 
- Mot de passe 
- Table de charge de travail 
- Zone de transit (pour la copie) 

Syntaxe :

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>’, 
SECRET = '<password>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTable 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net’, 
  CREDENTIAL = SQLCredName 
); 
 
CREATE EXTERNAL STREAM MySQLTableOutput 
WITH ( 
   DATA_SOURCE = MyTargetSQLTable, 
   LOCATION = ‘<TableName>’ 
   --Note: Do not need external file format in this case 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     STAGING_AREA: staging_area_data_source’, 
); 
```


### <a name="example-8---table-storage"></a>Exemple 8 - Stockage Table

Tapez : Output<br>
Paramètres :
- Alias de sortie 
- Compte de stockage 
- Clé du compte de stockage 
- Nom de la table 
- Clé de partition 
- Clé de ligne 
- Taille du lot 

Syntaxe :

```sql
CREATE DATABASE SCOPED CREDENTIAL TableStorageCredName 
WITH IDENTITY = ‘Storage account Key’, 
SECRET = '<storage_account_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net’, 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = ‘<TableName>’, 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     PARTITION_KEY: <column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100’ 
); 
```


### <a name="example-9---service-bus-topic-same-properties-as-queue"></a>Exemple 9 - Rubrique Service Bus (mêmes propriétés que la file d’attente)

Tapez : Output<br>
Paramètres :
- Alias de sortie 
- Espace de noms Service Bus 
- Nom de la rubrique 
- Nom de la stratégie de rubrique 
- Clé de la stratégie de rubrique 
- Colonnes de propriété 
- Colonnes de propriétés système 
- Format de sérialisation de l’événement 
- Encodage 

Syntaxe :

```sql
CREATE DATABASE SCOPED CREDENTIAL serviceBusCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyServiceBus_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = serviceBusCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM MyServiceBusOutput 
WITH ( 
   DATA_SOURCE = MyServiceBus_tweets, 
   LOCATION = ‘<topic_name>’, 
   EXTERNAL_FILE_FORMAT = myFileFormat 
       OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     PARTITION_KEY_COLUMN: , 
     PROPERTY_COLUMNS: (), 
     SYSTEM_PROPERTY_COLUMNS: ()’ 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
           
); 
```


### <a name="example-10---cosmos-db"></a>Exemple 10 - Cosmos DB

Tapez : Output<br>
Paramètres :
- Alias de sortie 
- ID de compte 
- Clé de compte 
- Base de données 
- Nom du conteneur 
- ID du document 


Syntaxe :

```sql
CREATE DATABASE SCOPED CREDENTIAL cosmosDBCredName 
WITH IDENTITY = ‘Storage Account Key’, 
SECRET = '<accountKey>'; 
 
CREATE EXTERNAL DATA SOURCE MyCosmosDB_tweets 
WITH 
(     
  LOCATION = ' cosmosdb://accountid.documents.azure.com:443/ database’, 
  CREDENTIAL = cosmosDBCredName 
); 
 
CREATE EXTERNAL STREAM MyCosmosDBOutput 
WITH ( 
   DATA_SOURCE = MyCosmosDB_tweets, 
   LOCATION = ‘<container/documentID>’ 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop', 
     --Note: Do not need external file format in this case 
          
);
```



### <a name="example-11---power-bi"></a>Exemple 11 - Power BI

Tapez : Output<br>
Paramètres :
- Alias de sortie 
- Nom du jeu de données 
- Nom de la table 


Syntaxe :

```sql
CREATE DATABASE SCOPED CREDENTIAL PBIDBCredName 
WITH IDENTITY = ‘Managed Identity’; 
 
CREATE EXTERNAL DATA SOURCE MyPbi_tweets 
WITH 
( 
  LOCATION = 'pbi://dataset/’, 
  CREDENTIAL = PBIDBCredName 
 
); 
 
CREATE EXTERNAL STREAM MyPbiOutput 
WITH ( 
   DATA_SOURCE = MyPbi_tweets, 
   LOCATION = 'tableName', 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop' 
        
);
```

### <a name="example-12---azure-function"></a>Exemple 12 - Azure Function

Tapez : Output<br>
Paramètres :
- Conteneur de fonctions 
- Fonction 
- Clé 
- Taille de lot maximale 
- Nombre maximal de lots 

Syntaxe :

```sql
CREATE DATABASE SCOPED CREDENTIAL AzureFunctionCredName 
WITH IDENTITY = ‘Function Key’, 
SECRET = '<function_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net’, 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = ‘<TableName>’, 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: 'Drop'      
     PARTITION_KEY: ‘<column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100’ 
); 
```


## <a name="see-also"></a>Voir aussi

- [ALTER EXTERNAL STREAM (Transact-SQL)](alter-external-stream-transact-sql.md) 
- [DROP EXTERNAL STREAM (Transact-SQL)](drop-external-stream-transact-sql.md) 

