---
title: Format de texte délimité dans Azure Data Factory
description: Cette rubrique décrit comment traiter le format de texte délimité dans Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: jingwang
ms.openlocfilehash: 77d2daf3fa17632d8a1c633c23815e0035e45481
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931256"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Format de texte délimité dans Azure Data Factory

Suivez cet article si vous souhaitez **analyser des fichiers de texte délimité ou écrire des données au format de texte délimité**. 

Le format de texte délimité est pris en charge pour les connecteurs suivants : [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Système de fichiers](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md) et [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données de texte délimité.

| Propriété         | Description                                                  | Obligatoire |
| ---------------- | ------------------------------------------------------------ | -------- |
| Type             | La propriété de type du jeu de données doit être définie sur **DelimitedText**. | OUI      |
| location         | Paramètres d’emplacement du ou des fichiers. Chaque connecteur basé sur un fichier possède ses propres type d’emplacement et propriétés prises en charge sous `location`.  | OUI      |
| columnDelimiter  | Caractère(s) utilisé(s) pour séparer les colonnes dans un fichier. Actuellement, le délimiteur multi-char est uniquement pris en charge pour le flux de données de mappage, mais pas l’activité de copie. <br>La valeur par défaut est **virgules  `,`** , lorsque le délimiteur de colonne est défini comme une chaîne vide, ce qui signifie aucun délimiteur, la ligne entière est considérée comme une seule colonne. | Non       |
| rowDelimiter     | Caractère unique ou « \r\n » utilisé pour séparer les lignes dans un fichier.<br>La valeur par défaut est l’une des valeurs suivantes **lors de la lecture : [« \r\n », « \r », « \n »]** et **« \n » ou « \r\n » en écriture** respectivement par le flux de données de mappage et l’activité de copie. <br>Lorsque `rowDelimiter` est défini sur aucun délimiteur (chaîne vide), `columnDelimiter` doit être également défini comme aucun délimiteur (chaîne vide), ce qui signifie le fait de traiter l’intégralité du contenu comme une valeur unique. | Non       |
| quoteChar        | Le caractère unique pour mettre entre guillemets les valeurs de colonne si elle contient un délimiteur de colonne. <br>La valeur par défaut est **guillemets doubles** `"`. <br>Pour le flux de données de mappage, `quoteChar` ne peut pas être une chaîne vide. <br>Pour l’activité de copie, quand `quoteChar` est défini comme une chaîne vide, cela signifie qu’il n’existe aucun caractère de citation et la valeur de la colonne n’est pas mise entre guillemets, et `escapeChar` est utilisé pour soustraire le délimiteur de colonne et lui-même. | Non       |
| escapeChar       | Le caractère unique pour placer les guillemets dans une séquence d’échappement à l’intérieur d’une valeur entre guillemets.<br>La valeur par défaut est **Barre oblique inverse`\`** . <br>Pour le flux de données de mappage, `escapeChar` ne peut pas être une chaîne vide. <br/>Pour l’activité de copie, quand `escapeChar` est défini comme une chaîne vide, `quoteChar` doit également être défini comme une chaîne vide, auquel cas vous devez vous assurer que toutes les valeurs de colonne ne contiennent pas de délimiteurs. | Non       |
| firstRowAsHeader | Spécifie s’il faut considérer/faire de la première ligne une ligne d’en-tête avec les noms des colonnes.<br>Les valeurs autorisées sont **True** et **False** (par défaut). | Non       |
| nullValue        | Spécifie la représentation sous forme de chaîne de la valeur null. <br>La valeur par défaut est une **chaîne vide**. | Non       |
| encodingName     | Le type de codage utilisé pour lire/écrire des fichiers de test. <br>Les valeurs autorisées sont les suivantes : « UTF-8 », « UTF-16 », « UTF-16BE », « UTF-32 », « UTF-32BE NE », « US-ASCII », « UTF-7 », « BIG5 », « EUC-JP », « EUC-KR », « GB2312 », « GB18030 », « JOHAB », « SHIFT-JIS », « CP875 », « CP866 », « IBM00858 », « IBM037 », « IBM273 », « IBM437 », « IBM500 », « IBM737 », « IBM775 », « IBM850 », » IBM852 », « IBM855 », « IBM857 », « IBM860 », « IBM861 », « IBM863 », « IBM864 », « IBM865 », « IBM869 », « IBM870 », « IBM01140 », « IBM01141 », « IBM01142 », « IBM01143 », « IBM01144 », « IBM01145 », « IBM01146 », « IBM01147 », « IBM01148 », « IBM01149 », « ISO-2022-JP », « ISO-2022-KR «, « ISO-8859-1 », « ISO-8859-2 », « ISO-8859-3", « ISO-8859-4 », « ISO-8859-5 », « ISO-8859-6 », « ISO-8859-7 », « ISO-8859-8 », « ISO-8859-9 », « ISO-8859-13 », « ISO-8859-15 », « WINDOWS-874 », « WINDOWS-1250 », « WINDOWS-1251 », « WINDOWS-1252 », « WINDOWS-1253 », » WINDOWS-1254 », « WINDOWS-1255 », « WINDOWS-1256 », « WINDOWS-1257 », « WINDOWS-1258 ».<br>Notez que le flux de données de mappage ne prend pas en charge le codage UTF-7. | Non       |
| compressionCodec | Le codec de compression utilisé pour lire/écrire des fichiers texte. <br>Les valeurs autorisées sont **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **snappy**, ou **lz4**. À utiliser lors de l’enregistrement du fichier. <br>Notez que pour l’instant, l’activité de copie ne prend pas en charge « snappy » et « lz4 » et le flux de données de mappage ne prend pas en charge « ZipDeflate ». <br>Notez que lorsque vous utilisez l’activité de copie pour décompresser un ou plusieurs fichiers ZipDeflate et écrire dans le magasin de données du récepteur basé sur des fichiers, les fichiers sont extraits dans le dossier : `<path specified in dataset>/<folder named as source zip file>/`. | Non       |
| compressionLevel | Le taux de compression. <br>Les valeurs autorisées sont **Optimal** ou **Fastest**.<br>- **Fastest (le plus rapide) :** l’opération de compression doit se terminer le plus rapidement possible, même si le fichier résultant n’est pas compressé de façon optimale.<br>- **Optimal** : l’opération de compression doit aboutir à une compression optimale, même si elle prend plus de temps. Pour plus d’informations, consultez la rubrique [Niveau de compression](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | Non       |

Voici un exemple de jeu de données de texte délimité sur Stockage Blob Azure :

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
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
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source et le récepteur de texte délimité.

### <a name="delimited-text-as-source"></a>Texte délimité en tant que source 

Les propriétés prises en charge dans la section ***\*source\**** de l’activité de copie sont les suivantes.

| Propriété       | Description                                                  | Obligatoire |
| -------------- | ------------------------------------------------------------ | -------- |
| Type           | La propriété type de la source d’activité de copie doit être définie sur **DelimitedTextSource**. | OUI      |
| formatSettings | Un groupe de propriétés. Reportez-vous au tableau **Paramètres de lecture du texte délimité** ci-dessous. | Non       |
| storeSettings  | Un groupe de propriétés sur la façon de lire les données d’un magasin de données. Chaque connecteur basé sur un fichier possède ses propres paramètres de lecture pris en charge sous `storeSettings`. | Non       |

Prise en charge des **paramètres de lecture du texte délimité** sous `formatSettings`:

| Propriété      | Description                                                  | Obligatoire |
| ------------- | ------------------------------------------------------------ | -------- |
| Type          | Le type de formatSettings doit être défini sur **DelimitedTextReadSetting**. | OUI      |
| skipLineCount | Indique le nombre de lignes **non vides** à ignorer lors de la lecture des données à partir des fichiers d’entrée. <br>Si skipLineCount et firstRowAsHeader sont spécifiés, les lignes sont d’abord ignorées, puis les informations d’en-tête sont lues à partir du fichier d’entrée. | Non       |

### <a name="delimited-text-as-sink"></a>Texte délimité en tant que récepteur

Les propriétés prises en charge dans la section ***\*récepteur\**** de l’activité de copie sont les suivantes.

| Propriété       | Description                                                  | Obligatoire |
| -------------- | ------------------------------------------------------------ | -------- |
| Type           | La propriété type de la source d’activité de copie doit être définie sur **DelimitedTextSink**. | OUI      |
| formatSettings | Un groupe de propriétés. Reportez-vous au tableau **Paramètres d’écriture du texte délimité** ci-dessous. |          |
| storeSettings  | Groupe de propriétés sur la méthode d’écriture de données dans un magasin de données. Chaque connecteur basé sur un fichier possède ses propres paramètres d’écriture pris en charge sous `storeSettings`.  | Non       |

Prise en charge des **paramètres d’écriture du texte délimité** sous `formatSettings`:

| Propriété      | Description                                                  | Obligatoire                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| Type          | Le type de formatSettings doit être défini sur **DelimitedTextWriteSetting**. | OUI                                                   |
| fileExtension | L’extension de fichier utilisée pour nommer les fichiers de sortie, par exemple, `.csv`, `.txt`. Elle doit être spécifiée lorsque `fileName` n’est pas spécifié dans le jeu de données de sortie DelimitedText. | Oui lorsque le nom de fichier n’est pas spécifié dans le jeu de données de sortie |

## <a name="mapping-data-flow-properties"></a>Propriétés du mappage de flux de données

Découvrez plus de détails sur la [transformation de la source](data-flow-source.md) et la [transformation du récepteur](data-flow-sink.md) dans la section sur le mappage de flux de données.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble des activités de copie](copy-activity-overview.md)
- [Mappage de flux de données](concepts-data-flow-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité GetMetadata](control-flow-get-metadata-activity.md)
