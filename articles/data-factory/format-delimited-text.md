---
title: Format de texte délimité dans Azure Data Factory | Microsoft Docs
description: Cette rubrique décrit comment traiter avec le format de texte délimité dans Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 4e365ef01b8c7a89d61efad25bd18943e7b2d1a4
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64877967"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Format de texte délimité dans Azure Data Factory

Suivez cet article lorsque vous souhaitez **analyser des fichiers texte délimité ou écrire les données dans un format de texte délimité**. 

Format de texte délimité est pris en charge pour les connecteurs suivants : [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [stockage fichier Azure](connector-azure-file-storage.md), [Système de fichiers](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)et [ SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Jeux de données](concepts-datasets-linked-services.md). Cette section fournit une liste des propriétés prises en charge par le jeu de données de texte délimité.

| Propriété         | Description                                                  | Obligatoire |
| ---------------- | ------------------------------------------------------------ | -------- |
| Type             | La propriété de type du jeu de données doit être définie sur **DelimitedText**. | Oui      |
| location         | Paramètres de l’emplacement du ou des fichiers. Chaque connecteur basé sur fichier possède son propre type d’emplacement et prise en charge des propriétés sous `location`. **Consultez les détails dans l’article traitant du connecteur -> section des propriétés de jeu de données**. | Oui      |
| columnDelimiter  | L’ou les caractères utilisé pour séparer les colonnes dans un fichier. Actuellement, char multi délimiteur est uniquement pris en charge pour le mappage de flux de données, mais pas les activités de copie. <br>La valeur par défaut est **virgules `,`** , lorsque le délimiteur de colonne est défini en tant que chaîne vide, ce qui signifie qu’aucun délimiteur, la ligne entière n’est considérée comme une seule colonne. | Non        |
| rowDelimiter     | Le caractère unique ou un « \r\n » utilisé pour séparer les lignes dans un fichier.<br>La valeur par défaut est une des valeurs suivantes **lors de la lecture : [« \r\n », « \r », « \n »]**, et **« \n » ou « \r\n » en écriture** par les données de mappage de flux et activité de copie respectivement. <br>Lorsque `rowDelimiter` est défini sur aucun délimiteur (chaîne vide), la `columnDelimiter` doit être défini comme aucun délimiteur (chaîne vide), ce qui signifie que de traiter l’intégralité du contenu en tant que valeur unique. | Non        |
| quoteChar        | Le caractère unique à placer entre guillemets les valeurs de colonne si elle contient le délimiteur de colonne. <br>La valeur par défaut est **guillemets doubles** `"`. <br>Pour le mappage de flux de données, `quoteChar` ne peut pas être une chaîne vide. <br>Pour l’activité de copie, quand `quoteChar` est défini en tant que chaîne vide, il signifie qu’il n’existe aucun caractère de citation et la valeur de la colonne n’est pas mis entre guillemets, et `escapeChar` est utilisé pour échapper le délimiteur de colonne et elle-même. | Non        |
| escapeChar       | Le caractère unique pour l’échappement des guillemets à l’intérieur d’une valeur entre guillemets.<br>La valeur par défaut est **barre oblique inverse `\`** . <br>Pour le mappage de flux de données, `escapeChar` ne peut pas être une chaîne vide. <br/>Pour l’activité de copie, quand `escapeChar` est défini en tant que chaîne vide, la `quoteChar` doit être défini en tant que chaîne vide également, auquel cas Assurez-vous que toutes les valeurs de colonne ne contiennent pas les délimiteurs. | Non        |
| firstRowAsHeader | Spécifie s’il faut considérer/marque la première ligne comme une ligne d’en-tête avec les noms de colonnes.<br>Valeurs autorisées sont **true** et **false** (valeur par défaut). | Non        |
| nullValue        | Spécifie la représentation sous forme de chaîne de valeur null. <br>La valeur par défaut est **une chaîne vide**. | Non        |
| encodingName     | Le type de codage utilisé pour lire/écrire des fichiers de test. <br>Valeurs autorisées sont les suivantes : « UTF-8 », « UTF-16 », « UTF-16BE », « UTF-32 », « UTF-32BE NE », « US-ASCII », « UTF-7 », « BIG5 », « EUC-JP », « EUC-KR », « GB2312 », « GB18030 », « JOHAB », « SHIFT-JIS », « CP875 », « CP866 », « IBM00858 », « IBM037 », « IBM273 », « IBM437 », « IBM500 », « IBM737 », « IBM775 », « IBM850 », » IBM852 », « IBM855 », « IBM857 », « IBM860 », « IBM861 », « IBM863 », « IBM864 », « IBM865 », « IBM869 », « IBM870 », « IBM01140 », « IBM01141 », « IBM01142 », « IBM01143 », « IBM01144 », « IBM01145 », « IBM01146 », « IBM01147 », « IBM01148 », « IBM01149 », « ISO-2022-JP », « ISO-2022-KR «, « ISO-8859-1 », « ISO-8859-2 », « ISO-8859-3", « ISO-8859-4 », « ISO-8859-5 », « ISO-8859-6 », « ISO-8859-7 », « ISO-8859-8 », « ISO-8859-9 », « ISO-8859-13 », « ISO-8859-15 », « WINDOWS-874 », « WINDOWS-1250 », « WINDOWS-1251 », « WINDOWS-1252 », « WINDOWS-1253 », » WINDOWS-1254 », « WINDOWS-1255 », « WINDOWS-1256 », « WINDOWS-1257 », « WINDOWS-1258 ».<br>Notez le que mappage de flux de données ne prend en charge dans le codage UTF-7. | Non        |
| compressionCodec | Le codec de compression utilisé pour lire/écrire des fichiers texte. <br>Valeurs autorisées sont **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **snappy**, ou **lz4**. à utiliser lors de l’enregistrement du fichier. <br>Notez actuellement activité de copie ne prend pas en charge la « rapide » & « lz4 », et mappage de flux de données ne prend en charge « ZipDeflate ». | Non        |
| compressionLevel | Le taux de compression. <br>Valeurs autorisées sont **Optimal** ou **Fastest**.<br>- **Le plus rapide :** l’opération de compression doit se terminer le plus rapidement possible, même si le fichier résultant n’est pas compressé de façon optimale.<br>- **Optimal**: l’opération de compression doit aboutir à une compression optimale, même si elle prend plus de temps. Pour plus d’informations, consultez la rubrique [Niveau de compression](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | Non        |

Voici un exemple de jeu de données de texte délimité sur stockage Blob Azure :

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
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
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit une liste des propriétés prises en charge par la source de texte délimité et le récepteur.

### <a name="delimited-text-as-source"></a>Texte délimité en tant que source 

Les propriétés suivantes sont prises en charge dans l’activité de copie ***\*source\**** section.

| Propriété       | Description                                                  | Obligatoire |
| -------------- | ------------------------------------------------------------ | -------- |
| Type           | La propriété de type de la source d’activité de copie doit être définie sur **DelimitedTextSource**. | Oui      |
| formatSettings | Un groupe de propriétés. Reportez-vous à **texte délimité lire les paramètres** tableau ci-dessous. | Non        |
| storeSettings  | Un groupe de propriétés sur la façon de lire les données à partir d’un magasin de données. Chaque connecteur basé sur fichier possède ses propres paramètres de lecture pris en charge sous `storeSettings`. **Consultez les détails dans l’article traitant du connecteur -> section de propriétés d’activité copie**. | Non        |

Prise en charge **texte délimité lire les paramètres** sous `formatSettings`:

| Propriété      | Description                                                  | Obligatoire |
| ------------- | ------------------------------------------------------------ | -------- |
| Type          | Le type de formatSettings doit être défini sur **DelimitedTextReadSetting**. | Oui      |
| skipLineCount | Indique le nombre de lignes **non vides** à ignorer lors de la lecture des données à partir des fichiers d’entrée. <br>Si skipLineCount et firstRowAsHeader sont spécifiés, les lignes sont d’abord ignorées, puis les informations d’en-tête sont lues à partir du fichier d’entrée. | Non        |

### <a name="delimited-text-as-sink"></a>Texte délimité en tant que récepteur

Les propriétés suivantes sont prises en charge dans l’activité de copie ***\*récepteur\**** section.

| Propriété       | Description                                                  | Obligatoire |
| -------------- | ------------------------------------------------------------ | -------- |
| Type           | La propriété de type de la source d’activité de copie doit être définie sur **DelimitedTextSink**. | Oui      |
| formatSettings | Un groupe de propriétés. Reportez-vous à **texte délimité écrire des paramètres** tableau ci-dessous. |          |
| storeSettings  | Un groupe de propriétés sur la façon d’écrire des données dans un magasin de données. Chaque connecteur basé sur fichier possède ses propres paramètres de prise en charge d’écriture sous `storeSettings`. **Consultez les détails dans l’article traitant du connecteur -> section de propriétés d’activité copie**. | Non        |

Prise en charge **texte délimité écrire des paramètres** sous `formatSettings`:

| Propriété      | Description                                                  | Obligatoire                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| Type          | Le type de formatSettings doit être défini sur **DelimitedTextWriteSetting**. | Oui                                                   |
| fileExtension | L’extension de fichier utilisée pour nommer les fichiers de sortie, par exemple, `.csv`, `.txt`. Il doit être spécifié lorsque le `fileName` n’est pas spécifié dans la sortie DelimitedText le jeu de données. | Oui lorsque le nom de fichier n’est pas spécifié dans le jeu de données de sortie |

## <a name="mapping-data-flow-properties"></a>Mappage de propriétés de flux de données

Découvrez plus de détails à partir de [transformation source](data-flow-source.md) et [récepteur transformation](data-flow-sink.md) dans le mappage de flux de données.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble des activités de copie](copy-activity-overview.md)
- [Flux de données de mappage](concepts-data-flow-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité GetMetadata](control-flow-get-metadata-activity.md)