---
title: Guide pratique pour lire et écrire des données partitionnées dans Azure Data Factory | Microsoft Docs
description: Découvrez comment lire et écrire des données partitionnées dans Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: shlo
ms.openlocfilehash: 24464d110b00508cfb3fde4ab1a050773511e255
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091047"
---
# <a name="how-to-read-or-write-partitioned-data-in-azure-data-factory"></a>Guide pratique pour lire ou écrire des données partitionnées dans Azure Data Factory

Dans Azure Data Factory version 1, vous pouvez lire ou écrire des données partitionnées à l’aide des variables système **SliceStart**, **SliceEnd**, **WindowStart** et **WindowEnd**. Dans la version actuelle de Data Factory, ce comportement est obtenu à l’aide d’un paramètre de pipeline ayant comme valeur l’heure de début ou l’heure planifiée d’un déclencheur. 

## <a name="use-a-pipeline-parameter"></a>Utiliser un paramètre de pipeline 

Dans Data Factory version 1, vous pouviez utiliser la propriété **partitionedBy** et la variable système **SliceStart** comme indiqué dans l’exemple suivant : 

```json
"folderPath": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/{Year}/{Month}/{Day}/",
"partitionedBy": [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "%M" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "%d" } }
],
```

Pour plus d’informations sur la propriété **partitonedBy**, consultez [Copier des données vers ou depuis le stockage Blob Azure à l’aide d’Azure Data Factory](v1/data-factory-azure-blob-connector.md#dataset-properties). 

Pour obtenir ce comportement dans la version actuelle de Data Factory : 

1. Définissez un *paramètre de pipeline* de type **chaîne**. Dans l’exemple suivant, le nom du paramètre de pipeline est **windowStartTime**. 
2. Dans la définition du jeu de données, définissez **folderPath** de façon à référencer la valeur du paramètre de pipeline. 
3. Passez la valeur réelle du paramètre lors de l’appel du pipeline à la demande. Vous pouvez également passer dynamiquement l’heure de début ou l’heure planifiée d’un déclencheur à l’exécution. 

```json
"folderPath": {
      "value": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/@{formatDateTime(pipeline().parameters.windowStartTime, 'yyyy/MM/dd')}/",
      "type": "Expression"
},
```

## <a name="pass-in-a-value-from-a-trigger"></a>Passer une valeur à partir d’un déclencheur

Dans la définition de déclencheur de fenêtre bascule suivante, l’heure de début de la fenêtre du déclencheur est passée comme valeur du paramètre de pipeline **windowStartTime** : 

```json
{
    "name": "MyTrigger",
    "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": "1",
            "startTime": "2018-05-15T00:00:00Z",
            "delay": "00:10:00",
            "maxConcurrency": 10
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipeline"
            },
            "parameters": {
                "windowStartTime": "@trigger().outputs.windowStartTime"
            }
        }
    }
}
```

## <a name="example"></a>Exemples

Voici un exemple de définition de jeu de données :

```json
{
  "name": "SampleBlobDataset",
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "value": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/@{formatDateTime(pipeline().parameters.windowStartTime, 'yyyy/MM/dd')}/",
      "type": "Expression"
    },
    "format": {
      "type": "TextFormat",
      "columnDelimiter": ","
    }
  },
  "structure": [
    { "name": "ProfileID", "type": "String" },
    { "name": "SessionStart", "type": "String" },
    { "name": "Duration", "type": "Int32" },
    { "name": "State", "type": "String" },
    { "name": "SrcIPAddress", "type": "String" },
    { "name": "GameType", "type": "String" },
    { "name": "Multiplayer", "type": "String" },
    { "name": "EndRank", "type": "String" },
    { "name": "WeaponsUsed", "type": "Int32" },
    { "name": "UsersInteractedWith", "type": "String" },
    { "name": "Impressions", "type": "String" }
  ],
  "linkedServiceName": {
    "referenceName": "churnStorageLinkedService",
    "type": "LinkedServiceReference"
  }
}
```

Définition du pipeline : 

```json
{
    "properties": {
        "activities": [{
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": {
                    "value": "@concat(pipeline().parameters.blobContainer, '/scripts/', pipeline().parameters.partitionHiveScriptFile)",
                    "type": "Expression"
                },
                "scriptLinkedService": {
                    "referenceName": "churnStorageLinkedService",
                    "type": "LinkedServiceReference"
                },
                "defines": {
                    "RAWINPUT": {
                        "value": "@concat('wasb://', pipeline().parameters.blobContainer, '@', pipeline().parameters.blobStorageAccount, '.blob.core.windows.net/logs/', pipeline().parameters.inputRawLogsFolder, '/')",
                        "type": "Expression"
                    },
                    "Year": {
                        "value": "@formatDateTime(pipeline().parameters.windowStartTime, 'yyyy')",
                        "type": "Expression"
                    },
                    "Month": {
                        "value": "@formatDateTime(pipeline().parameters.windowStartTime, 'MM')",
                        "type": "Expression"
                    },
                    "Day": {
                        "value": "@formatDateTime(pipeline().parameters.windowStartTime, 'dd')",
                        "type": "Expression"
                    }
                }
            },
            "linkedServiceName": {
                "referenceName": "HdiLinkedService",
                "type": "LinkedServiceReference"
            },
            "name": "HivePartitionGameLogs"
        }],
        "parameters": {
            "windowStartTime": {
                "type": "String"
            },
            "blobStorageAccount": {
                "type": "String"
            },
            "blobContainer": {
                "type": "String"
            },
            "inputRawLogsFolder": {
                "type": "String"
            }
        }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une description complète de la création d’une fabrique de données avec un pipeline, consultez [Démarrage rapide : créer une fabrique de données](quickstart-create-data-factory-powershell.md). 

