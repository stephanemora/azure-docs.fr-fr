---
title: Format Binaire dans Azure Data Factory
description: Cette rubrique décrit comment traiter le format Binaire dans Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: jingwang
ms.openlocfilehash: 4560560b3677030a66e277e96eb552d39f5c82c1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416339"
---
# <a name="binary-format-in-azure-data-factory"></a>Format Binaire dans Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Le format Binaire est pris en charge pour les connecteurs suivants : [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Système de fichiers](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md) et [SFTP](connector-sftp.md).

Vous pouvez utiliser un jeu de données Binaire dans l’[activité Copie](copy-activity-overview.md), l'[activité GetMetadata](control-flow-get-metadata-activity.md) ou l’[activité Suppression](delete-activity.md). Lors de l’utilisation d’un jeu de données binaire, ADF n’analyse pas le contenu du fichier, mais le traite tel quel. 

>[!NOTE]
>Lors de l’utilisation d’un jeu de données Binaire dans l’activité de copie, vous pouvez uniquement effectuer une copie à partir d’un jeu de données binaire vers un jeu de données binaire.

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données Binaire.

| Propriété         | Description                                                  | Obligatoire |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | La propriété type du jeu de données doit être définie sur **Binaire**. | Oui      |
| location         | Paramètres d’emplacement du ou des fichiers. Chaque connecteur basé sur un fichier possède ses propres type d’emplacement et propriétés prises en charge sous `location`. **Consultez les détails dans l’article du connecteur -> section des propriétés du jeu de données**. | Oui      |
| compression | Groupe de propriétés pour configurer la compression de fichier. Configurez cette section lorsque vous souhaitez effectuer la compression/décompression lors de l’exécution de l’activité. | Non |
| type | Le codec de compression utilisé pour lire/écrire des fichiers binaires. <br>Les valeurs autorisées sont **bzip2**, **gzip**, **deflate**, **ZipDeflate**. À utiliser lors de l’enregistrement du fichier.<br>Notez que lorsque vous utilisez l’activité de copie pour décompresser un ou plusieurs fichiers ZipDeflate et écrire dans le magasin de données du récepteur basé sur des fichiers, les fichiers sont extraits dans le dossier : `<path specified in dataset>/<folder named as source zip file>/`. | Non       |
| level | Le taux de compression. Appliquez quand le jeu de données est utilisé dans le récepteur d’activité de copie.<br>Les valeurs autorisées sont **Optimal** ou **Fastest**.<br>- **Fastest (le plus rapide) :** l’opération de compression doit se terminer le plus rapidement possible, même si le fichier résultant n’est pas compressé de façon optimale.<br>- **Optimal** : l’opération de compression doit aboutir à une compression optimale, même si elle prend plus de temps. Pour plus d’informations, consultez la rubrique [Niveau de compression](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | Non       |

Voici un exemple de jeu de données Binaire sur Stockage Blob Azure :

```json
{
    "name": "BinaryDataset",
    "properties": {
        "type": "Binary",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source et le récepteur binaires.

>[!NOTE]
>Lors de l’utilisation d’un jeu de données Binaire dans l’activité de copie, vous pouvez uniquement effectuer une copie à partir d’un jeu de données binaire vers un jeu de données binaire.

### <a name="binary-as-source"></a>Binaire en tant que source

Les propriétés prises en charge dans la section ***\*source\**** de l’activité de copie sont les suivantes.

| Propriété      | Description                                                  | Obligatoire |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La propriété type de la source de l’activité de copie doit être définie sur **BinarySource**. | Oui      |
| storeSettings | Un groupe de propriétés sur la façon de lire les données d’un magasin de données. Chaque connecteur basé sur un fichier possède ses propres paramètres de lecture pris en charge sous `storeSettings`. **Consultez les détails dans l’article du connecteur -> section des propriétés de l’activité de copie**. | Non       |

### <a name="binary-as-sink"></a>Binaire en tant que récepteur

Les propriétés prises en charge dans la section ***\*récepteur\**** de l’activité de copie sont les suivantes.

| Propriété      | Description                                                  | Obligatoire |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La propriété type de la source d’activité de copie doit être définie sur **BinarySink**. | Oui      |
| storeSettings | Groupe de propriétés sur la méthode d’écriture de données dans un magasin de données. Chaque connecteur basé sur un fichier possède ses propres paramètres d’écriture pris en charge sous `storeSettings`. **Consultez les détails dans l’article du connecteur -> section des propriétés de l’activité de copie**. | Non       |

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble des activités de copie](copy-activity-overview.md)
- [Activité GetMetadata](control-flow-get-metadata-activity.md)
- [Supprimer l’activité](delete-activity.md)
