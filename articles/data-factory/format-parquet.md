---
title: Format parquet dans Azure Data Factory | Microsoft Docs
description: Cette rubrique décrit comment traiter format Parquet dans Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 5b711fac2a7bc41d11bcc80927f460390888cc3e
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64878057"
---
# <a name="parquet-format-in-azure-data-factory"></a>Format parquet dans Azure Data Factory

Suivez cet article lorsque vous souhaitez **analyser des fichiers Parquet ou écrire les données dans le format Parquet**. 

Format parquet est pris en charge pour les connecteurs suivants : [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [stockage fichier Azure](connector-azure-file-storage.md), [Système de fichiers](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)et [ SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Jeux de données](concepts-datasets-linked-services.md). Cette section fournit une liste des propriétés prises en charge par le jeu de données Parquet.

| Propriété         | Description                                                  | Obligatoire |
| ---------------- | ------------------------------------------------------------ | -------- |
| Type             | La propriété de type du jeu de données doit être définie sur **Parquet**. | Oui      |
| location         | Paramètres de l’emplacement du ou des fichiers. Chaque connecteur basé sur fichier possède son propre type d’emplacement et prise en charge des propriétés sous `location`. **Consultez les détails dans l’article traitant du connecteur -> section des propriétés de jeu de données**. | Oui      |
| compressionCodec | Le codec de compression à utiliser lors de l’écriture dans des fichiers Parquet. Lors de la lecture à partir de fichiers Parquet, Data Factory déterminer automatiquement le codec de compression basé sur les métadonnées de fichier.<br>Types pris en charge sont «**none**«, »**gzip**«, »**snappy**» (valeur par défaut), et »**lzo**». Notez actuellement l’activité de copie ne prend pas en charge LZO. | Non        |

> [!NOTE]
> Espace blanc dans le nom de colonne n’est pas pris en charge pour les fichiers Parquet.

Voici un exemple de jeu de données Parquet sur stockage Blob Azure :

```json
{
    "name": "ParquetDataset",
    "properties": {
        "type": "Parquet",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit une liste des propriétés prises en charge par la source de Parquet et le récepteur.

### <a name="parquet-as-source"></a>Parquet en tant que source

Les propriétés suivantes sont prises en charge dans l’activité de copie ***\*source\**** section.

| Propriété      | Description                                                  | Obligatoire |
| ------------- | ------------------------------------------------------------ | -------- |
| Type          | La propriété de type de la source d’activité de copie doit être définie sur **ParquetSource**. | Oui      |
| storeSettings | Un groupe de propriétés sur la façon de lire les données à partir d’un magasin de données. Chaque connecteur basé sur fichier possède ses propres paramètres de lecture pris en charge sous `storeSettings`. **Consultez les détails dans l’article traitant du connecteur -> section de propriétés d’activité copie**. | Non        |

### <a name="parquet-as-sink"></a>Parquet comme récepteur

Les propriétés suivantes sont prises en charge dans l’activité de copie ***\*récepteur\**** section.

| Propriété      | Description                                                  | Obligatoire |
| ------------- | ------------------------------------------------------------ | -------- |
| Type          | La propriété de type de la source d’activité de copie doit être définie sur **ParquetSink**. | Oui      |
| storeSettings | Un groupe de propriétés sur la façon d’écrire des données dans un magasin de données. Chaque connecteur basé sur fichier possède ses propres paramètres de prise en charge d’écriture sous `storeSettings`. **Consultez les détails dans l’article traitant du connecteur -> section de propriétés d’activité copie**. | Non        |

## <a name="mapping-data-flow-properties"></a>Mappage de propriétés de flux de données

Découvrez plus de détails à partir de [transformation source](data-flow-source.md) et [récepteur transformation](data-flow-sink.md) dans le mappage de flux de données.

## <a name="data-type-support"></a>Prise en charge des types de données

Parquet types de données complexes ne sont actuellement pas pris en charge (par exemple, carte, liste, STRUCT).

## <a name="using-self-hosted-integration-runtime"></a>À l’aide du Runtime d’intégration auto-hébergé

> [!IMPORTANT]
> Dans le cas de copies permises par Integration Runtime (auto-hébergé), par exemple, entre des magasins de données locaux et cloud, si vous ne copiez pas les fichiers Parquet **tels quels**, vous devez installer **JRE 8 64 bits (Java Runtime Environment) ou OpenJDK** sur votre machine de runtime d’intégration. Consultez le paragraphe suivant pour plus de détails.

Dans le cas de copies s’exécutant sur l’IR auto-hébergé avec sérialisation/désérialisation des fichiers Parquet, ADF localise le runtime Java en vérifiant d’abord le registre *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* pour JRE puis, s’il ne le trouve pas, en vérifiant la variable système *`JAVA_HOME`* pour OpenJDK.

- **Pour utiliser JRE** : Le runtime d’intégration de 64 bits requiert la version 64 bits de JRE. Vous pouvez la récupérer [ici](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Pour utiliser OpenJDK** : il est pris en charge depuis la version 3.13 du runtime d’intégration. Empaquetez jvm.dll avec tous les autres assemblys requis d’OpenJDK dans la machine d’IR auto-hébergé et définissez la variable d’environnement système JAVA_HOME en conséquence.

> [!TIP]
> Si, en copiant des données au format Parquet avec le runtime d’intégration auto-hébergé, vous obtenez une erreur indiquant « An error occurred when invoking java, message: **java.lang.OutOfMemoryError:Java heap space** », vous pouvez ajouter une variable d’environnement `_JAVA_OPTIONS` sur l’ordinateur qui héberge le runtime d’intégration auto-hébergé afin d’ajuster la taille de segment de mémoire minimale/maximale nécessaire pour que la machine virtuelle Java puisse effectuer une copie de ce type, puis réexécuter le pipeline.

![Définir la taille de segment de mémoire JVM sur le runtime d’intégration auto-hébergé](C:/AzureContent/azure-docs-pr/articles/data-factory/media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exemple : donnez la valeur `-Xms256m -Xmx16g` à la variable `_JAVA_OPTIONS`. L’indicateur `Xms` spécifie le pool d’allocation de mémoire initial pour une Machine virtuelle Java (JVM), tandis que `Xmx` spécifie le pool d’allocation de mémoire maximal. En d’autres termes, JVM démarrera avec la quantité de mémoire `Xms` et pourra au maximum utiliser la quantité de mémoire `Xmx`. Par défaut, ADF applique un minimum de 64 Mo et un maximum de 1 Go.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble des activités de copie](copy-activity-overview.md)
- [Flux de données de mappage](concepts-data-flow-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité GetMetadata](control-flow-get-metadata-activity.md)