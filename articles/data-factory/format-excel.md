---
title: Format Excel dans Azure Data Factory
description: Cette rubrique décrit comment traiter le format Excel dans Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/08/2020
ms.author: jingwang
ms.openlocfilehash: a937548c9318d98e8832720706626b74167d32d9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044392"
---
# <a name="excel-format-in-azure-data-factory"></a>Format Excel dans Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Suivez cet article lorsque vous souhaitez **analyser les fichiers Excel**. Azure Data Factory prend en charge « .xls » et « .xlsx ».

Le format Excel est pris en charge pour les connecteurs suivants : [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Système de fichiers](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md) et [SFTP](connector-sftp.md). Il est pris en charge en tant que source, mais pas en tant que récepteur.

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données Excel.

| Propriété         | Description                                                  | Obligatoire |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | La propriété type du jeu de données doit être définie sur **Excel**.   | Oui      |
| location         | Paramètres d’emplacement du ou des fichiers. Chaque connecteur basé sur un fichier possède ses propres type d’emplacement et propriétés prises en charge sous `location`. | Oui      |
| sheetName        | Nom de la feuille de calcul Excel pour lire les données.                       | Oui      |
| range            | Plage de cellules dans la feuille de calcul donnée pour localiser les données sélectives, par exemple `A3:H5` (tableau de A3 à H5), `A3` (tableau commençant à partir de la cellule A3), `A3:A3` (cellule unique). Si cela n’est pas spécifié, ADF lit la totalité de la feuille de calcul en tant que tableau. | Non       |
| firstRowAsHeader | Spécifie s’il faut considérer la première ligne dans la feuille de calcul/plage donnée comme une ligne d’en-tête avec les noms des colonnes.<br>Les valeurs autorisées sont **True** et **False** (par défaut). | Non       |
| nullValue        | Spécifie la représentation sous forme de chaîne de la valeur null. <br>La valeur par défaut est une **chaîne vide**. | Non       |
| compression | Groupe de propriétés pour configurer la compression de fichier. Configurez cette section lorsque vous souhaitez effectuer la compression/décompression lors de l’exécution de l’activité. | Non |
| type<br/>(*sous `compression`* ) | Le codec de compression utilisé pour lire/écrire des fichiers JSON. <br>Les valeurs autorisées sont **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **snappy**, ou **lz4**. À utiliser lors de l’enregistrement du fichier. La valeur par défaut n’est pas compressée.<br>**Notez** que pour l’instant, l’activité de copie ne prend pas en charge « snappy » et « lz4 » et le flux de données de mappage ne prend pas en charge « ZipDeflate ».<br>**Remarque** lorsque vous utilisez l’activité de copie pour décompresser un ou plusieurs fichiers **ZipDeflate** et écrire dans le magasin de données du récepteur basé sur fichier, les fichiers sont extraits dans le dossier : `<path specified in dataset>/<folder named as source zip file>/`. | Non.  |
| level<br/>(*sous `compression`* ) | Le taux de compression. <br>Les valeurs autorisées sont **Optimal** ou **Fastest**.<br>- **Fastest (le plus rapide) :** l’opération de compression doit se terminer le plus rapidement possible, même si le fichier résultant n’est pas compressé de façon optimale.<br>- **Optimal** : l’opération de compression doit aboutir à une compression optimale, même si elle prend plus de temps. Pour plus d’informations, consultez la rubrique [Niveau de compression](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | Non       |

Voici un exemple de jeu de données Excel sur Stockage Blob Azure :

```json
{
    "name": "ExcelDataset",
    "properties": {
        "type": "Excel",
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
            "sheetName": "MyWorksheet",
            "range": "A3:H5",
            "firstRowAsHeader": true
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source Excel.

### <a name="excel-as-source"></a>Excel en tant que source 

Les propriétés prises en charge dans la section ***\*source\**** de l’activité de copie sont les suivantes.

| Propriété      | Description                                                  | Obligatoire |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La propriété type de la source de l’activité de copie doit être définie sur **ExcelSource**. | Oui      |
| storeSettings | Un groupe de propriétés sur la façon de lire les données d’un magasin de données. Chaque connecteur basé sur un fichier possède ses propres paramètres de lecture pris en charge sous `storeSettings`. | Non       |

```json
"activities": [
    {
        "name": "CopyFromExcel",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "ExcelSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            ...
        }
        ...
    }
]
```

## <a name="mapping-data-flow-properties"></a>Propriétés du mappage de flux de données

Dans les flux de données de mappage, vous pouvez lire le format Excel dans les magasins de données suivants : [Stockage Blob Azure](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) et [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties). Vous pouvez pointer vers des fichiers Excel à l’aide d’un jeu de données Excel ou d’un [jeu de données Inline](data-flow-source.md#inline-datasets).

### <a name="source-properties"></a>Propriétés de source

Le tableau ci-dessous répertorie les propriétés prises en charge par une source Excel. Vous pouvez modifier ces propriétés sous l’onglet **Options de la source**. Lorsque vous utilisez un jeu de données Inline, vous verrez des paramètres de fichier supplémentaires qui sont les mêmes que les propriétés décrites dans la section des [propriétés du jeu de données](#dataset-properties).

| Nom                      | Description                                                  | Obligatoire | Valeurs autorisées                                            | Propriété du script de flux de données         |
| ------------------------- | ------------------------------------------------------------ | -------- | --------------------------------------------------------- | --------------------------------- |
| Chemins génériques           | Tous les fichiers correspondant au chemin générique seront traités. Remplace le chemin du dossier et du fichier défini dans le jeu de données. | non       | String[]                                                  | wildcardPaths                     |
| Chemin racine de la partition       | Pour les données de fichier qui sont partitionnées, vous pouvez entrer le chemin racine d’une partition pour pouvoir lire les dossiers partitionnés comme des colonnes. | non       | String                                                    | partitionRootPath                 |
| Liste de fichiers             | Si votre source pointe ou non vers un fichier texte qui liste les fichiers à traiter | non       | `true` ou `false`                                         | fileList                          |
| Colonne où stocker le nom du fichier | Crée une colonne avec le nom et le chemin du fichier source       | non       | String                                                    | rowUrlColumn                      |
| Après l’exécution          | Supprime ou déplace les fichiers après le traitement. Le chemin du fichier commence à la racine du conteneur | non       | Supprimer : `true` ou `false` <br> Déplacer : `['<from>', '<to>']` | purgeFiles <br> moveFiles         |
| Filtrer par date de dernière modification   | Pour filtrer les fichiers en fonction de leur date de dernière modification | non       | Timestamp                                                 | modifiedAfter <br> modifiedBefore |

### <a name="source-example"></a>Exemple de source

L’image ci-dessous est un exemple de configuration de source Excel dans des flux de données de mappage utilisant le mode du jeu de données.

![Source Excel](media/data-flow/excel-source.png)

Le script de flux de données associé est le suivant :

```
source(allowSchemaDrift: true,
    validateSchema: false,
    wildcardPaths:['*.xls']) ~> ExcelSource
```

Si vous utilisez un jeu de données Inline, les options de source suivantes s’affichent dans le flux de données de mappage.

![Jeu de données Inline de la source Excel](media/data-flow/excel-source-inline-dataset.png)

Le script de flux de données associé est le suivant :

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'excel',
    fileSystem: 'container',
    folderPath: 'path',
    fileName: 'sample.xls',
    sheetName: 'worksheet',
    firstRowAsHeader: true) ~> ExcelSourceInlineDataset
```

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble des activités de copie](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité GetMetadata](control-flow-get-metadata-activity.md)
