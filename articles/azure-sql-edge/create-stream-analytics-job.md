---
title: Créer une tâche T-SQL Streaming dans Azure SQL Edge (préversion)
description: En savoir plus sur la création de tâches Stream Analytics dans Azure SQL Edge (préversion)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 323ec00667350917e6b16827f908ac1abeee77d6
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233319"
---
# <a name="create-stream-analytics-job-in-azure-sql-edge-preview"></a>Créer une tâche Stream Analytics dans Azure SQL Edge (préversion) 

Cet article explique comment créer une tâche T-SQL Streaming dans Azure SQL Edge (préversion) Pour créer une tâche de streaming dans SQL Edge, les étapes suivantes sont nécessaires :

1. Créer les objets d’entrée et de sortie du flux externe
2. Définir la requête de tâche de streaming dans le cadre de la création de la tâche de streaming

> [!NOTE]
> Pour activer la fonctionnalité de diffusion en continu T-SQL dans Azure SQL Edge, activez TF 11515 en tant qu’option de démarrage, ou utilisez la commande [DBCC TRACEON]( https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql). Pour plus d’informations sur la façon d’activer les indicateurs de trace à l’aide du fichier mssql.conf, consultez [Configurer à l’aide du fichier mssql.conf](configure.md#configure-using-mssqlconf-file). Cette exigence sera supprimée dans les futures mises à jour d’Azure SQL Edge (préversion).

## <a name="configure-an-external-stream-input-and-output-object"></a>Configurer un objet d’entrée et de sortie de flux externe

T-SQL Streaming utilise la fonctionnalité de source de données externe de SQL Server pour définir les sources de données associées aux entrées et sorties du flux externe de la tâche de streaming. Les commandes T-SQL suivantes sont requises pour créer un objet de sortie ou d’entrée de flux externe.

[CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql)

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql)

[CREATE EXTERNAL STREAM (Transact-SQL)](#example-create-an-external-stream-object-sql-database)

En outre, si SQL Edge (SQL Server ou Azure SQL) est utilisé en tant que flux de sortie, la commande T-SQL [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) est requise pour définir les informations d’identification permettant d’accéder à la base de données SQL.

### <a name="supported-input-and-output-stream-data-sources"></a>Sources de données de flux d’entrée et de sortie prises en charge

Actuellement, Azure SQL Edge prend uniquement en charge les sources de données suivantes en tant qu’entrées et sorties de flux.

| Type de source de données | Entrée | Output | Description |
|------------------|-------|--------|------------------|
| Hub Azure IoT Edge | O | O | Source de données pour lire/écrire des données de streaming dans un hub Azure IoT Edge. Pour plus d’informations sur le hub Azure IoT Edge, consultez [Hub IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)|
| SQL Database | N | O | Connexion à la source de données pour écrire les données de streaming dans SQL Database. La base de données SQL Database peut être une base de données SQL Edge locale ou une instance distante de SQL Server ou Azure SQL Database|
| Stockage Blob Azure | N | O | Source de données pour écrire des données dans un objet blob de compte de stockage Azure. |
| Kafka | O | N | Source de données pour lire les données de streaming à partir d’une rubrique Kafka. Actuellement, cet adaptateur est uniquement disponible pour la version Intel/AMD d’Azure SQL Edge, et non pour la version ARM64 de SQL Edge.|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>Exemple : Créer un objet d’entrée/de sortie de flux externe pour le hub Azure IoT Edge

L’exemple ci-dessous crée un objet de flux externe pour le hub Edge. Pour créer une source de données d’entrée/de sortie de flux externe pour le hub Azure IoT Edge, vous devez d’abord créer un format de fichier externe pour SQL afin de comprendre la disposition des données en lecture/écriture.

1. Créez un format de fichier externe avec le type de format JSON.

    ```sql
    Create External file format InputFileFormat
    WITH (  
       format_type = JSON,
    )
    go
    ```

2. Créez une source de données externe pour le hub IoT Edge. Le script T-SQL ci-dessous crée une connexion de source de données à un hub Edge s’exécutant sur le même hôte docker que SQL Edge.

    ```sql
    CREATE EXTERNAL DATA SOURCE EdgeHubInput WITH (
    LOCATION = 'edgehub://'
    )
    go
    ```

3. Créez l’objet de flux externe pour le hub IoT Edge. Le script T-SQL ci-dessous crée un objet de flux pour le hub Edge. Dans le cas d’un objet de flux de hub Edge, le paramètre LOCATION correspond au nom de la rubrique/du canal de hub Edge en cours de lecture ou d’écriture.

    ```sql
    CREATE EXTERNAL STREAM MyTempSensors WITH (
    DATA_SOURCE = EdgeHubInput,
    FILE_FORMAT = InputFileFormat,
    LOCATION = N'TemperatureSensors',
    INPUT_OPTIONS = N'',
    OUTPUT_OPTIONS = N''
    )
    go
    ```

### <a name="example-create-an-external-stream-object-sql-database"></a>Exemple : Créer un objet de flux externe SQL Database

L’exemple ci-dessous crée un objet de flux externe dans la base de données SQL Edge locale. 

1. Créez une clé principale sur la base de données. C’est nécessaire pour chiffrer le secret des informations d’identification.

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. Créez des informations d’identification incluses dans l’étendue de la base de données pour accéder à la source SQL Server. L’exemple suivant crée des informations d’identification pour la source de données externe avec IDENTITY = 'username' and SECRET = 'password'.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. Créez une source de données externe avec CREATE EXTERNAL DATA SOURCE. L’exemple suivant :

    * Crée une source de données externe pour nommée LocalSQLOutput
    * Identifie la source de données externe (LOCATION = '<vendor>://<server>[:<port>]'). Dans l’exemple, elle pointe vers une instance locale de SQL Edge.
    * Pour finir, l’exemple utilise les informations d’identification créées précédemment.

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
    LOCATION = 'sqlserver://tcp:.,1433'
    ,CREDENTIAL = SQLCredential
    )
    go
    ```

4. Créez l’objet de flux externe. L’exemple ci-dessous crée un objet de flux externe pointant vers une table *dbo.TemperatureMeasurements* dans la base de données *MySQLDatabase*.

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurements WITH (
    DATA_SOURCE = LocalSQLOutput,
    LOCATION = N'MySQLDatabase.dbo.TemperatureMeasurements',
    INPUT_OPTIONS = N'',
    OUTPUT_OPTIONS = N''
    )
    ```

## <a name="create-the-streaming-job-and-the-streaming-queries"></a>Créer la tâche et les requêtes de streaming

Utilisez la procédure stockée système **sys.sp_create_streaming_job** pour définir les requêtes de streaming et créer la tâche de streaming. La procédure stockée **sp_create_streaming_job** accepte deux paramètres :

- job_name - Nom de la tâche de streaming. Les noms des tâches de streaming sont uniques sur l’ensemble de l’instance.
- statement - Instructions de requête de streaming basées sur le [langage de requête Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

L’exemple ci-dessous crée une tâche de streaming simple avec une requête de streaming. Cette requête lit les entrées à partir du hub Edge et écrit dans *dbo. TemperatureMeasurements* dans la base de données.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

L’exemple ci-dessous crée une tâche de streaming plus complexe avec plusieurs requêtes différentes, notamment une requête qui utilise la fonction intégrée AnomalyDetection_ChangePoint pour identifier les anomalies dans les données de température.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob2', @statement=
N' Select * INTO TemperatureMeasurements1 from MyEdgeHubInput1

Select * Into TemperatureMeasurements2 from MyEdgeHubInput2

Select * Into TemperatureMeasurements3 from MyEdgeHubInput3

SELECT
Timestamp as [Time],
[Temperature] As [Temperature],
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''Score'') as ChangePointScore,
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''IsAnomaly'') as IsChangePointAnomaly,
INTO TemperatureAnomalies FROM MyEdgeHubInput2;
'
go
```

## <a name="start-stop-drop-and-monitor-streaming-jobs"></a>Démarrer, arrêter, supprimer et surveiller des tâches de streaming

Pour démarrer une tâche de streaming dans SQL Edge, exécutez la procédure stockée **sys.sp_start_streaming_job**. La procédure stockée requiert le même élément de tâche de streaming à démarrer, en tant qu'entrée.

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

Pour arrêter une tâche de streaming dans SQL Edge, exécutez la procédure stockée **sys.sp_stop_streaming_job**. La procédure stockée requiert le même élément de tâche de streaming à arrêter, en tant qu'entrée.

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

Pour une tâche de streaming dans SQL Edge, exécutez la procédure stockée **sys.sp_drop_streaming_job**. La procédure stockée requiert le même élément de tâche de streaming à supprimer, en tant qu'entrée.

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

Pour obtenir l’état actuel d’une tâche de streaming dans SQL Edge, exécutez la procédure stockée **sys.sp_get_streaming_job**. La procédure stockée requiert le même élément de tâche de streaming à supprimer, en tant qu'entrée et renvoie le nom et l'état actuel de la tâche de streaming.

```sql
exec sys.sp_get_streaming_job @name=N'StreamingJob1'
        WITH RESULT SETS
(
       (
       name nvarchar(256),
       status nvarchar(256)
       )
)
```

La tâche de streaming peut se présenter dans l’un des états suivants :

| Statut | Description |
|--------| ------------|
| Date de création | La tâche de streaming a été créée, mais n’a pas encore été démarrée |
| Démarrage en cours | La tâche de streaming est cours de démarrage |
| Idle | La tâche de streaming est en cours d’exécution, mais il n’y a aucune entrée à traiter |
| Traitement en cours | La tâche de streaming est en cours d’exécution et traite les entrées. Cet état indique un état sain de la tâche de streaming |
| Détérioré | La tâche de streaming est en cours d’exécution, mais des erreurs de sérialisation/désérialisation d'entrée ou de sortie récupérables se sont produites lors du traitement des entrées. La tâche d’entrée continuera de s’exécuter, mais supprimera les entrées rencontrant des erreurs |
| Arrêté | La tâche de streaming a été arrêtée. |
| Échec | La tâche de streaming a échoué. Cela indique généralement une erreur irrécupérable lors du traitement |

## <a name="next-steps"></a>Étapes suivantes

- [Afficher les métadonnées associées aux tâches de streaming dans Azure SQL Edge (préversion)](streaming-catalog-views.md) 
- [Créer un flux externe](create-external-stream-transact-sql.md)
