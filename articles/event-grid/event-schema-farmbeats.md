---
title: Azure FarmBeats comme source Event Grid (préversion)
description: Décrit les propriétés et le schéma qui sont fournis pour les événements Azure FarmBeats avec Azure Event Grid
ms.topic: conceptual
ms.date: 06/06/2021
ms.openlocfilehash: 552a676fcfc457a662276aa7b5948670f700d208
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531563"
---
# <a name="azure-farmbeats-as-event-grid-source-preview"></a>Azure FarmBeats comme source Event Grid (préversion)
Cet article fournit les propriétés et les schémas des événements Azure FarmBeats.  Pour une présentation des schémas d’événements, consultez [Schéma d’événements Azure Event Grid](event-schema.md). 

## <a name="available-event-types"></a>Types d’événement disponibles

|Nom de l'événement | Description|
|-----|----|
|Microsoft.AgFoodPlatform.FarmerChanged|Publié lorsqu’un agriculteur est créé, mis à jour ou supprimé. 
|Microsoft.AgFoodPlatform.FarmChanged| Publié lorsqu’une ferme est créée, mise à jour ou supprimée.
|Microsoft.AgFoodPlatform.BoundaryChanged|Publié lorsqu’une limite est créée, mise à jour ou supprimée.
|Microsoft.AgFoodPlatform.FieldChanged|Publié lorsqu’un champ est créé, mis à jour ou supprimé.
|Microsoft.AgFoodPlatform.SeasonalFieldChanged|Publié lorsqu’un champ saisonnier est créé, mis à jour ou supprimé.
|Microsoft.AgFoodPlatform.SeasonChanged|Publié lorsqu’une saison est créée, mise à jour ou supprimée.
|Microsoft.AgFoodPlatform.CropChanged|Publié lorsqu’une culture est créée, mise à jour ou supprimée.
|Microsoft.AgFoodPlatform.CropVarietyChanged|Publié lorsqu’une variété de culture est créée, mise à jour ou supprimée.
|Microsoft.AgFoodPlatform.SatelliteDataIngestionJobStatusChanged| Publié lorsque l’état d’une tâche d’ingestion de données satellite évolue, par exemple, la tâche est créée, a progressé ou est terminée.
|Microsoft.AgFoodPlatform.WeatherDataIngestionJobStatusChanged|Publié lorsque l’état d’une tâche d’ingestion de données météo évolue, par exemple, la tâche est créée, a progressé ou est terminée.
|Microsoft.AgFoodPlatform.FarmOperationDataIngestionJobStatusChanged| Publié lorsque l’état d’une tâche d’ingestion de données liées aux opérations agricoles évolue, par exemple, la tâche est créée, a progressé ou est terminée.
|Microsoft.AgFoodPlatform.ApplicationDataChanged|Publié lorsque des données d’application sont créées, mises à jour ou supprimées. Cet événement est associé aux données liées aux opérations agricoles.
|Microsoft.AgFoodPlatform.HarvestingDataChanged|Publié lorsque des données de récolte sont créées, mises à jour ou supprimées. Cet événement est associé aux données liées aux opérations agricoles.
|Microsoft.AgFoodPlatform.TillageDataChanged|Publié lorsque des données de labourage sont créées, mises à jour ou supprimées. Cet événement est associé aux données liées aux opérations agricoles.
|Microsoft.AgFoodPlatform.PlantingDataChanged|Publié lorsque des données de plantation sont créées, mises à jour ou supprimées. Cet événement est associé aux données liées aux opérations agricoles.

## <a name="event-properties"></a>Propriétés de l’événement
Chaque événement FarmBeats présente deux parties : l’une commune à plusieurs événements et l’autre (un objet de données) contenant des propriétés spécifiques à chaque événement. 

La partie commune aux événements est élaborée dans le schéma suivant.

### <a name="event-grid-event-schema"></a>Schéma d’événement Event Grid
Un événement contient les données générales suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| `topic` | string | Chemin d’accès complet à la source de l’événement. Ce champ n’est pas modifiable. Event Grid fournit cette valeur. |
| `subject` | string | Chemin de l’objet de l’événement, défini par le serveur de publication. |
| `eventType` | string | Un des types d’événements inscrits pour cette source d’événement. |
| `eventTime` | string | L’heure à quelle l’événement est généré selon l’heure UTC du fournisseur. |
| `id` | string | Identificateur unique de l’événement. |
| `data` | object | Données de l’événement App Configuration. |
| `dataVersion` | string | Version du schéma de l’objet de données. Le serveur de publication définit la version du schéma. |
| `metadataVersion` | string | Version du schéma des métadonnées d’événement. Event Grid définit le schéma des propriétés de niveau supérieur. Event Grid fournit cette valeur. |


Les tableaux ci-dessous sont élaborés sur les propriétés de l’objet de données pour chaque événement.

*Pour les événements FarmerChanged, FarmChanged, SeasonChanged, CropChanged, CropVarietyChanged FarmBeats, l’objet de données contient les propriétés suivantes :*

|Propriété | Type| Description|
|----| ----| ----|
id| string| ID défini par l’utilisateur de la ressource, tel que l’ID de l’agriculteur, etc.
actionType| string| Indique la modification déclenchée lors de la publication de l’événement. Les valeurs applicables sont Créé, Mis à jour, Supprimé
status| string| Contient l’état défini par l’utilisateur de la ressource.
properties| object| Contient des paires clé/valeur définies par l'utilisateur.
modifiedDateTime| date-time|Date/Heure de la dernière modification de la ressource, exemple de format : aaaa-MM-jjTHH:mm:ssZ.
createdDateTime|date-time|Date/Heure de création de la ressource, exemple de format : aaaa-MM-jjTHH:mm:ssZ.
eTag|   string| Implémente l'accès concurrentiel optimiste
description| string|    Description textuelle de la ressource


*Les événements BoundaryChanged FarmBeats présentent l’objet de données suivant :*

|Propriété | Type| Description|
|----| ----| ----|
id| string| ID de limite défini par l’utilisateur
actionType| string| Indique la modification déclenchée lors de la publication de l’événement. Les valeurs applicables sont Créé, Mis à jour, Supprimé.
parentId|   string| ID d’appartenance de la limite parente.
parentType| string| Type d’appartenance de la limite parente.
isPrimary|  boolean|    Indique s’il s’agit d’une limite principale.
farmerId|   string| Contient l’ID de l’agriculteur associé à la limite.
properties| object| Contient des paires clé/valeur définies par l'utilisateur.
modifiedDateTime| date-time|Date/Heure de la dernière modification de la ressource, exemple de format : aaaa-MM-jjTHH:mm:ssZ.
createdDateTime|date-time|Date/Heure de création de la ressource, exemple de format : aaaa-MM-jjTHH:mm:ssZ.
status| string| Contient l’état défini par l’utilisateur de la ressource.
eTag|   string| Implémente l'accès concurrentiel optimiste.
description| string|    Description textuelle de la ressource.

*Les événements FieldChanged FarmBeats présentent l’objet de données suivant :*

Propriété|   Type|   Description
|----| ----| ----|
id| string| ID défini par l’utilisateur du champ
farmId| string| ID défini par l’utilisateur de la ferme à laquelle le champ est associé
farmerId|   string| ID défini par l’utilisateur de l’agriculteur auquel le champ est associé
name|   string| Nom défini par l'utilisateur du champ.
actionType| string| Indique la modification qui a déclenché la publication de l’événement. Les valeurs applicables sont Créé, Mis à jour, Supprimé
properties| object| Contient des paires clé/valeur définies par l'utilisateur.
modifiedDateTime| date-time|Date/Heure de la dernière modification de la ressource, exemple de format : aaaa-MM-jjTHH:mm:ssZ.
createdDateTime|date-time|Date/Heure de création de la ressource, exemple de format : aaaa-MM-jjTHH:mm:ssZ.
status| string| Contient l’état défini par l’utilisateur de la ressource.
eTag|   string| Implémente l'accès concurrentiel optimiste
description|string| Description textuelle de la ressource

*Les événements SeasonalFieldChanged FarmBeats présentent l’objet de données suivant :*

Propriété|   Type|   Description
|----| ----| ----|
id| string| ID défini par l’utilisateur du champ saisonnier
farmId| string| ID défini par l’utilisateur de la ferme à laquelle le champ saisonnier est associé
farmerId|   string| ID défini par l’utilisateur de l’agriculteur auquel le champ saisonnier est associé
seasonId|   string| ID défini par l’utilisateur de la saison à laquelle le champ saisonnier est associé
fieldId|    string| ID défini par l’utilisateur du champ auquel le champ saisonnier est associé
name|   string| Nom défini par l’utilisateur du champ saisonnier
actionType| string| Indique la modification qui a déclenché la publication de l’événement. Les valeurs applicables sont Créé, Mis à jour, Supprimé
properties| object| Contient des paires clé/valeur définies par l'utilisateur.
modifiedDateTime| date-time|Date/Heure de la dernière modification de la ressource, exemple de format : aaaa-MM-jjTHH:mm:ssZ.
createdDateTime|date-time|Date/Heure de création de la ressource, exemple de format : aaaa-MM-jjTHH:mm:ssZ.
status| string| Contient l’état défini par l’utilisateur de la ressource.
eTag|   string| Implémente l'accès concurrentiel optimiste
description| string|    Description textuelle de la ressource

*Les événements SatelliteDataIngestionJobChanged, WeatherDataIngestionJobChanged, and FarmOperationsDataIngestionJobChanged FarmBeats l’objet de données suivant :*

Propriété|   Type|   Description
|----|----|----|
id|String| ID unique du travail.
name| string| Nom défini par l'utilisateur de la tâche.
status|string|Différents états dans lesquels une tâche peut se trouver.
isCancellationRequested| boolean|Indicateur défini lorsque l’annulation de la tâche est demandée.
description|string| Description textuelle de la tâche.
farmerId|string| ID de l’agriculteur pour lequel la tâche a été créée.
message|string| Message d’état pour capturer plus de détails sur la tâche.
lastActionDateTime|date-time|Date/Heure de la dernière action effectuée sur la tâche, exemple de format : aaaa-MM-jjTHH:mm:ssZ.
createdDateTime|date-time|Date/Heure de création de la ressource, exemple de format : aaaa-MM-jjTHH:mm:ssZ.


*Les événements de modification des données liées aux opérations agricoles FarmBeats comme ApplicationDataChanged, HarvestingDataChanged, PlantingDataChanged et TillageDataChanged présentent l’objet de données suivant :*

Propriété|   Type|   Description
|----|----|----|
id| string| ID défini par l’utilisateur de la ressource, tel que l’ID de l’agriculteur, etc.
status| string| Contient l'état de la tâche. 
actionType|string|
source| string| Message de FarmBeats fournissant des détails sur la tâche.    
modifiedDateTime| date-time|Date/Heure de la dernière modification de la ressource, exemple de format : aaaa-MM-jjTHH:mm:ssZ.
createdDateTime|date-time|Date/Heure de création de la ressource, exemple de format : aaaa-MM-jjTHH:mm:ssZ.
eTag|   string| Implémente l'accès concurrentiel optimiste
description|string| Description textuelle de la ressource


## <a name="sample-events"></a>Échantillonner les événements 
Ces exemples d’événements représentent une notification d’événements.

**Type d’événement : Microsoft.AgFoodPlatform.FarmerChanged**

```json
{
    "data": {
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T10:53:28Z",
      "eTag": "860197cc-0000-0700-0000-60420da80000",
      "id": "UNIQUE-FARMER-ID",
      "name": "sample farmer",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T10:53:28Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "81fbe1de-4ae4-4284-964f-59da80a6bfe7",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID",
    "eventType": "Microsoft.AgFoodPlatform.FarmerChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T10:53:28.2783745Z"
  }
```

**Type d’événement : Microsoft.AgFoodPlatform.FarmChanged**

```json
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T10:55:57Z",
      "eTag": "8601e3d5-0000-0700-0000-60420e3d0000",
      "id": "UNIQUE-FARM-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T10:55:57Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "31a31be7-51fb-48f3-adfd-6fb4400be002",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/farms/UNIQUE-FARM-ID",
    "eventType": "Microsoft.AgFoodPlatform.FarmChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T10:55:57.6026173Z"
  }
```
**Type d’événement : Microsoft.AgFoodPlatform.BoundaryChanged**

```json
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "parentId": "OPTIONAL-UNIQUE-FIELD-ID",
      "isPrimary": true,
      "actionType": "Created",
      "modifiedDateTime": "2021-03-05T11:15:29Z",
      "eTag": "860109f7-0000-0700-0000-604212d10000",
      "id": "UNIQUE-BOUNDARY-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:15:29Z"
    },
    "id": "3d3453b2-5a94-45a7-98eb-fc2979a00317",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/boundaries/UNIQUE-BOUNDARY-ID",
    "eventType": "Microsoft.AgFoodPlatform.BoundaryChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:15:29.4797354Z"
  }
  ```

**Type d’événement : Microsoft.AgFoodPlatform.FieldChanged**

```json
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "farmId": "UNIQUE-FARM-ID",
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T10:58:43Z",
      "eTag": "860124dc-0000-0700-0000-60420ee30000",
      "id": "UNIQUE-FIELD-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T10:58:43Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "1ad04ed0-ac05-4c4e-aa3d-87facb3cc97c",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/fields/UNIQUE-FIELD-ID",
    "eventType": "Microsoft.AgFoodPlatform.FieldChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T10:58:43.3222921Z"
  }
  ```
**Type d’événement : Microsoft.AgFoodPlatform.SeasonalFieldChanged**
```json
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "seasonId": "UNIQUE-SEASON-ID",
      "fieldId": "UNIQUE-FIELD-ID",
      "farmId": "UNIQUE-FARM-ID",
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T11:24:56Z",
      "eTag": "8701300b-0000-0700-0000-604215080000",
      "id": "UNIQUE-SEASONAL-FIELD-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:24:56Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "ff59a0a3-6226-42c0-9e70-01da55efa797",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/seasonalFields/UNIQUE-SEASONAL-FIELD-ID",
    "eventType": "Microsoft.AgFoodPlatform.SeasonalFieldChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:24:56.4210287Z"
  }
```
**Type d’événement : Microsoft.AgFoodPlatform.SeasonChanged**
```json
  {
    "data": {
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T11:18:38Z",
      "eTag": "86019afd-0000-0700-0000-6042138e0000",
      "id": "UNIQUE-SEASON-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:18:38Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "63989475-397b-4b92-8160-8743bf8e5804",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/seasons/UNIQUE-SEASON-ID",
    "eventType": "Microsoft.AgFoodPlatform.SeasonChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:18:38.5804699Z"
  }
  ```

  **Type d’événement : Microsoft.AgFoodPlatform.CropChanged**

```json
  {
    "data": {
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T11:03:48Z",
      "eTag": "8601c4e5-0000-0700-0000-604210150000",
      "id": "UNIQUE-CROP-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:03:48Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "4c59a797-b76d-48ec-8915-ceff58628f35",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/crops/UNIQUE-CROP-ID",
    "eventType": "Microsoft.AgFoodPlatform.CropChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:03:49.0590658Z"
  }
  ```

**Type d’événement : Microsoft.AgFoodPlatform.CropVarietyChanged**

```json
  {
    "data": {
      "cropId": "UNIQUE-CROP-ID",
      "actionType": "Created",
      "status": "string",
      "modifiedDateTime": "2021-03-05T11:10:21Z",
      "eTag": "860130ef-0000-0700-0000-6042119d0000",
      "id": "UNIQUE-CROP-VARIETY-ID",
      "name": "Sample status",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:10:21Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "29aefdb9-d648-442c-81f8-694f3f47583c",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/cropVarieties/UNIQUE-CROP-VARIETY-ID",
    "eventType": "Microsoft.AgFoodPlatform.CropVarietyChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:10:21.4572495Z"
  }
```
**Type d’événement : Microsoft.AgFoodPlatform.SatelliteDataIngestionJobStatusChanged**
```json
[
  {
    "data": {
      "farmerId": "UNIQUE - FARMER - ID",
      "message": "Created job 'job1' to fetch satellite data for boundary 'boundary1' from startDate '06/01/2021' to endDate '06/01/2021' (both inclusive).",
      "status": "Waiting",
      "lastActionDateTime": "2021-06-01T11:25:37.8634096Z",
      "isCancellationRequested": false,
      "id": "UNIQUE - JOB - ID",
      "name": "samplejob",
      "description": "Sample for testing events",
      "createdDateTime": "2021-06-01T11:25:32.3421173Z",
      "properties": {
        "key1": "testvalue1",
        "key2": 123.45
      }
    },
    "id": "925c6be2-6561-4572-b7dd-0f3084a54567",
    "topic": "/subscriptions/{Subscription -ID}/resourceGroups/{RESOURCE - GROUP - NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/{UNIQUE-FARMER-ID}/satelliteDataIngestionJobs/{UNIQUE-JOB-ID}",
    "eventType": "Microsoft.AgFoodPlatform.SatelliteDataIngestionJobStatusChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-06-01T11:25:37.8634764Z"
  }
]
```
**Type d’événement : Microsoft.AgFoodPlatform.WeatherDataIngestionJobStatusChanged**
```json
[
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "message": "Created job to fetch weather data for job name 'job2', farmer id 'farmer2' and boundary id 'boundary2'.",
      "status": "Running",
      "lastActionDateTime": "2021-06-01T11:22:27.9031003Z",
      "isCancellationRequested": false,
      "id": "UNIQUE-JOB-ID",
      "createdDateTime": "2021-06-01T07:13:54.8843617Z"
    },
    "id": "ec30313a-ff2f-4b50-882b-31188113c15b",
    "topic": "/subscriptions/{Subscription -ID}/resourceGroups/{RESOURCE - GROUP - NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/weatherDataIngestionJobs/UNIQUE-JOB-ID",
    "eventType": "Microsoft.AgFoodPlatform.WeatherDataIngestionJobStatusChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-06-01T11:22:27.9031302Z"
  }
]

```
**Type d’événement : Microsoft.AgFoodPlatform.FarmOperationDataIngestionJobStatusChanged**
```json
[
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "message": "Job completed successfully. Data statistics:{ Processed operations count = 6, Organizations count = 1, Processed organizations count = 1, Processed fields count = 2, Operations count = 6, ShapefileAttachmentsCount = 0, Fields count = 2 }",
      "status": "Succeeded",
      "lastActionDateTime": "2021-06-01T11:30:54.733625Z",
      "isCancellationRequested": false,
      "id": "UNIQUE-JOB-ID",
      "name": "sample-job",
      "description": "sample description",
      "createdDateTime": "2021-06-01T11:30:39.0905288Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "ebdbb7a1-ad28-4af7-b3a2-a4a3a2dd1b4f",
    "topic": "/subscriptions/{Subscription -ID}/resourceGroups/{RESOURCE - GROUP - NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/farmOperationDataIngestionJobs/UNIQUE-JOB-ID",
    "eventType": "Microsoft.AgFoodPlatform.FarmOperationDataIngestionJobStatusChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-06-01T11:30:54.733671Z"
  }
]

```
**Type d’événement : Microsoft.AgFoodPlatform.ApplicationDataChanged**

```json
  {
    "data": {
      "actionType": "Updated",
      "farmerId": "UNIQUE-FARMER-ID",
      "source": "Sample source",
      "modifiedDateTime": "2021-03-05T11:27:24Z",
      "eTag": "87011311-0000-0700-0000-6042159c0000",
      "id": "UNIQUE-APPLICATION-DATA-ID",
      "status": "Sample status",
      "name": "sample name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:27:24Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "e499f6c4-63ba-4217-8261-0c6cb0e398d2",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/applicationData/UNIQUE-APPLICATION-DATA-ID",
    "eventType": "Microsoft.AgFoodPlatform.ApplicationDataChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:27:24.164612Z"
  }
```

**Type d’événement : Microsoft.AgFoodPlatform.HarvestDataChanged**
```json
  {
    "data": {
      "actionType": "Created",
      "farmerId": "UNIQUE-FARMER-ID",
      "source": "Sample source",
      "modifiedDateTime": "2021-03-05T11:33:41Z",
      "eTag": "8701141b-0000-0700-0000-604217150000",
      "id": "UNIQUE-HARVEST-DATA-ID",
      "status": "Sample status",
      "name": "sample name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:33:41Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "dc3837c0-1eed-4bfa-88b6-d018cf6af4db",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/harvestData/UNIQUE-HARVEST-DATA-ID",
    "eventType": "Microsoft.AgFoodPlatform.HarvestDataChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:33:41.3434992Z"
  }
```
**Type d’événement : Microsoft.AgFoodPlatform.TillageDataChanged**
```json
  {
    "data": {
      "actionType": "Updated",
      "farmerId": "UNIQUE-FARMER-ID",
      "source": "sample source",
      "modifiedDateTime": "2021-06-15T10:31:07Z",
      "eTag": "6405f027-0000-0100-0000-60c8816b0000",
      "id": "c9858c3f-fb94-474a-a6de-103b453df976",
      "createdDateTime": "2021-06-15T10:31:07Z",
      "name": "sample name",
      "description":"sample description"
      "properties": {
        "_orgId": "498221",
        "_fieldId": "e61b83f4-3a12-431e-8010-596f2466dc27",
        "_cropSeason": "2010"
      }
    },
    "id": "f06f6686-1fa8-41fd-be99-46f40f495cce",
    "topic": "/subscriptions/da9091ec-d18f-456c-9c21-5783ee7f4645/resourceGroups/internal-farmbeats-resources/providers/Microsoft.AgFoodPlatform/farmBeats/internal-eus",
    "subject": "/farmers/10e3d7bf-c559-48be-af31-4e00df83bfcd/tillageData/c9858c3f-fb94-474a-a6de-103b453df976",
    "eventType": "Microsoft.AgFoodPlatform.TillageDataChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-06-15T10:31:07.6778047Z"
  }
```

**Type d’événement : Microsoft.AgFoodPlatform.PlantingDataChanged**
```json
  {
    "data": {
      "actionType": "Created",
      "farmerId": "UNIQUE-FARMER-ID",
      "source": "Sample source",
      "modifiedDateTime": "2021-03-05T11:41:18Z",
      "eTag": "8701242a-0000-0700-0000-604218de0000",
      "id": "UNIQUE-PLANTING-DATA-ID",
      "status": "Sample status",
      "name": "sample name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:41:18Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "42589c7f-4e16-4a4d-9314-d611c822f7ac",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/plantingData/UNIQUE-PLANTING-DATA-ID",
    "eventType": "Microsoft.AgFoodPlatform.PlantingDataChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:41:18.1744322Z"
  }
```



## <a name="next-steps"></a>Étapes suivantes
* Pour une présentation d’Azure Event Grid, consultez [Présentation d’Event Grid](overview.md).
