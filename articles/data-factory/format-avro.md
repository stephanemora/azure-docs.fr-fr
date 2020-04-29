---
title: Format Avro dans Azure Data Factory
description: Cette rubrique décrit comment traiter le format Avro dans Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jingwang
ms.openlocfilehash: 931287fa2a4104069b101236bec9f76bb7193e8d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416349"
---
# <a name="avro-format-in-azure-data-factory"></a>Format Avro dans Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Suivez cet article si vous souhaitez **analyser des fichiers Avro ou écrire des données au format Avro**. 

Le format Avro est pris en charge pour les connecteurs suivants : [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Système de fichiers](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md) et [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données Avro.

| Propriété         | Description                                                  | Obligatoire |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | La propriété type du jeu de données doit être définie sur **Avro**. | Oui      |
| location         | Paramètres d’emplacement du ou des fichiers. Chaque connecteur basé sur un fichier possède ses propres type d’emplacement et propriétés prises en charge sous `location`. **Consultez les détails dans l’article du connecteur -> section des propriétés du jeu de données**. | Oui      |
| avroCompressionCodec | Codec de compression à utiliser lors de l’écriture dans des fichiers Avro. Lors de la lecture de fichiers Avro, Data Factory détermine automatiquement le codec de compression sur la base des métadonnées de fichier.<br>Les types pris en charge sont « **None** » (par défaut), « **deflate** » et « **snappy**». Notez que l’activité de copie ne prend pas en charge Snappy lors de la lecture ou de l’écriture des fichiers Avro. | Non       |

> [!NOTE]
> Les espaces blancs dans le nom de colonne ne sont pas pris en charge pour les fichiers Avro.

Voici un exemple de jeu de données Avro sur Stockage Blob Azure :

```json
{
    "name": "AvroDataset",
    "properties": {
        "type": "Avro",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "avroCompressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source et le récepteur Avro.

### <a name="avro-as-source"></a>Avro en tant que source

Les propriétés prises en charge dans la section ***\*source\**** de l’activité de copie sont les suivantes.

| Propriété      | Description                                                  | Obligatoire |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La propriété de type de la source d’activité de copie doit être définie sur **AvroSource**. | Oui      |
| storeSettings | Un groupe de propriétés sur la façon de lire les données d’un magasin de données. Chaque connecteur basé sur un fichier possède ses propres paramètres de lecture pris en charge sous `storeSettings`. **Consultez les détails dans l’article du connecteur -> section des propriétés de l’activité de copie**. | Non       |

### <a name="avro-as-sink"></a>Avro en tant que récepteur

Les propriétés prises en charge dans la section ***\*récepteur\**** de l’activité de copie sont les suivantes.

| Propriété      | Description                                                  | Obligatoire |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La propriété type de la source d’activité de copie doit être définie sur **AvroSink**. | Oui      |
| storeSettings | Groupe de propriétés sur la méthode d’écriture de données dans un magasin de données. Chaque connecteur basé sur un fichier possède ses propres paramètres d’écriture pris en charge sous `storeSettings`. **Consultez les détails dans l’article du connecteur -> section des propriétés de l’activité de copie**. | Non       |

## <a name="data-type-support"></a>Prise en charge des types de données

### <a name="copy-activity"></a>Activité de copie
Les [types de données complexes](https://avro.apache.org/docs/current/spec.html#schema_complex) Avro ne sont pas pris en charge (enregistrements, enums, tables, cartes, unions et fixes) par l’activité de copie.

### <a name="data-flows"></a>Flux de données
Lorsque vous utilisez des fichiers Avro dans des flux de données, vous pouvez lire et écrire des types de données complexes, mais vous devez d’abord effacer le schéma physique du jeu de données. Dans les flux de données, vous pouvez définir votre projection logique et dériver les colonnes qui ont des structures complexes, puis mapper automatiquement ces champs à un fichier Avro.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble des activités de copie](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité GetMetadata](control-flow-get-metadata-activity.md)
