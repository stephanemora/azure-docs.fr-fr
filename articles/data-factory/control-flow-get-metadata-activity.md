---
title: Activité d’obtention des métadonnées dans Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser l'activité de procédure stockée SQL Server pour appeler une procédure stockée dans une base de données SQL Azure ou un entrepôt Azure SQL Data Warehouse à partir d'un pipeline Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: shlo
ms.openlocfilehash: 4188fb413cc1001b6e4813fe69518a016c8c0656
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54354261"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Activité d’obtention des métadonnées dans Azure Data Factory
L’activité GetMetadata peut être utilisée pour récupérer les **métadonnées** de n’importe quelle donnée dans Azure Data Factory. Cette activité peut être utilisée dans les scénarios suivants :

- Valider les informations de métadonnées de n’importe quelle donnée
- Déclencher un pipeline lorsque des données sont prêtes/disponibles

La fonctionnalité suivante est disponible dans le flux de contrôle :

- La sortie de l’activité d’obtention des métadonnées peut être utilisée dans des expressions conditionnelles pour effectuer la validation.
- Un pipeline peut être déclenché lorsque la condition est remplie via une bouche Do-Until

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

L’activité GetMetadata sélectionne un jeu de données comme entrée requise, puis génère les informations de métadonnées disponibles comme sortie. Pour l’instant, les connecteurs suivants avec les métadonnées récupérables correspondantes sont pris en charge, et la taille de métadonnée maximale prise en charge est de **1 Mo**.

>[!NOTE]
>Si vous exécutez l’activité GetMetadata sur un runtime d’intégration auto-hébergé, la dernière fonctionnalité est prise en charge sur la version 3.6 ou ultérieure. 

### <a name="supported-connectors"></a>Connecteurs pris en charge

**Stockage de fichiers :**

| Connecteur/Métadonnées | itemName<br>(fichier/dossier) | itemType<br>(fichier/dossier) | size<br>(fichier) | created<br>(fichier/dossier) | lastModified<br>(fichier/dossier) |childItems<br>(dossier) |contentMD5<br>(fichier) | structure<br/>(fichier) | columnCount<br>(fichier) | exists<br>(fichier/dossier) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Amazon S3 | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| Objets blob Azure | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| Azure Data Lake Store | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| Stockage Fichier Azure | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| Système de fichiers | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| SFTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| FTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |

- Pour Amazon S3, `lastModified` s’applique à un compartiment et à une clé, mais pas à un dossier virtuel ; `exists` s’applique à un compartiment et à une clé, mais pas à un préfixe ou à un dossier virtuel.
- Pour Azure Stockage Blob, `lastModified` s’applique à un conteneur et à un objet blob, mais pas à un dossier virtuel.

**Base de données relationnelle :**

| Connecteur/Métadonnées | structure | columnCount | exists |
|:--- |:--- |:--- |:--- |
| Azure SQL Database | √ | √ | √ |
| Azure SQL Database Managed Instance | √ | √ | √ |
| Azure SQL Data Warehouse | √ | √ | √ |
| SQL Server | √ | √ | √ |

### <a name="metadata-options"></a>Options de métadonnées

Les types de métadonnées suivants peuvent être spécifiés dans la liste de champs d’activité GetMetadata à récupérer :

| Type de métadonnées | Description |
|:--- |:--- |
| itemName | Nom du fichier ou dossier. |
| itemType | Type du fichier ou dossier. La valeur de sortie est `File` ou `Folder`. |
| size | Taille du fichier en octets. S’applique aux fichiers uniquement. |
| created | Date/heure de création du fichier ou du dossier. |
| lastModified | Date/heure de dernière modification du fichier ou du dossier. |
| childItems | Liste des sous-dossiers et fichiers à l’intérieur du dossier donné. S’applique aux dossiers uniquement. La valeur de sortie est une liste de noms et de types de tous les éléments enfants. |
| contentMD5 | MD5 du fichier. S’applique aux fichiers uniquement. |
| structure | Structure de données dans le fichier ou la table de base de données relationnelle. La valeur de sortie est une liste de noms et de types de colonnes. |
| columnCount | Nombre de colonnes dans le fichier ou la table relationnelle. |
| exists| Indique si un fichier/un dossier/une table existe ou non. Notez que si « exists » est spécifié dans la liste de champs GetMetadata, l’activité n’échoue pas même quand l’élément (fichier/dossier/table) n’existe pas ; au lieu de cela, `exists: false` est retourné dans la sortie. |

>[!TIP]
>Quand vous voulez vérifier si un fichier/un dossier/une table existe ou non, spécifiez `exists` dans la liste de champs d’activité GetMetadata, puis examinez le résultat `exists: true/false` de la sortie de l’activité. Si `exists` n’est pas configuré dans la liste de champs, l’activité GetMetadata échoue quand l’objet est introuvable.

## <a name="syntax"></a>Syntaxe

**Activité GetMetadata :**

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```

**Jeu de données :**

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "filename": "file.json",
            "format":{
                "type":"JsonFormat"
            }
        }
    }
}
```

## <a name="type-properties"></a>Propriétés type

Actuellement, l’activité GetMetadata peut extraire les types d’informations de métadonnées suivants.

Propriété | Description | Obligatoire
-------- | ----------- | --------
fieldList | Répertorie les types d’informations de métadonnées requis. Consultez les détails dans la section [Options de métadonnées](#metadata-options) sur les métadonnées prises en charge. | Oui 
dataset | Jeu de données de référence à partir duquel l’activité de métadonnées doit être récupérée par l’activité d’obtention des métadonnées. Consultez la section [Fonctionnalités prises en charge](#supported-capabilities) sur les connecteurs pris en charge et reportez-vous à la rubrique des connecteurs pour plus d’informations sur la syntaxe de jeu de données. | Oui

## <a name="sample-output"></a>Exemple de sortie

Le résultat de GetMetadata est illustré dans la sortie de l’activité. Voici deux exemples avec des options de métadonnées exhaustives sélectionnées dans la liste de champs en tant que référence. Pour utiliser le résultat dans une activité suivante, utilisez le modèle `@{activity('MyGetMetadataActivity').output.itemName}`.

### <a name="get-a-files-metadata"></a>Obtenir les métadonnées d’un fichier

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>Obtenir les métadonnées d’un dossier

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes
Consultez les autres activités de flux de contrôle prises en charge par Data Factory : 

- [Activité d’exécution du pipeline](control-flow-execute-pipeline-activity.md)
- [Pour chaque activité](control-flow-for-each-activity.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité Web](control-flow-web-activity.md)
