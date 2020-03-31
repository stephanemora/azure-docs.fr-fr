---
title: Format JSON dans Azure Data Factory
description: Cette rubrique décrit comment traiter le format JSON dans Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: jingwang
ms.openlocfilehash: 7dac8d21e3b45307284ece15ca5ddbcc69db909b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79229981"
---
# <a name="json-format-in-azure-data-factory"></a>Format JSON dans Azure Data Factory

Suivez cet article si vous souhaitez **analyser des fichiers JSON ou écrire des données au format JSON**. 

Le format JSON est pris en charge pour les connecteurs suivants : [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Système de fichiers](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md) et [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données JSON.

| Propriété         | Description                                                  | Obligatoire |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | La propriété type du jeu de données doit être définie sur **Json**. | Oui      |
| location         | Paramètres d’emplacement du ou des fichiers. Chaque connecteur basé sur un fichier possède ses propres type d’emplacement et propriétés prises en charge sous `location`. **Consultez les détails dans l’article du connecteur -> section des propriétés du jeu de données**. | Oui      |
| encodingName     | Le type de codage utilisé pour lire/écrire des fichiers de test. <br>Les valeurs autorisées sont les suivantes : « UTF-8 », « UTF-16 », « UTF-16BE », « UTF-32 », « UTF-32BE », « US-ASCII », « UTF-7 », « BIG5 », « EUC-JP », « EUC-KR », « GB2312 », « GB18030 », « JOHAB », « SHIFT-JIS », « CP875 », « CP866 », « IBM00858 », « IBM037 », « IBM273 », « IBM437 », « IBM500 », « IBM737 », « IBM775 », « IBM850 », « IBM852 », « IBM855 », « IBM857 », « IBM860 », « IBM861 », « IBM863 », « IBM864 », « IBM865 », « IBM869 », « IBM870 », « IBM01140 », « IBM01141 », « IBM01142 », « IBM01143 », « IBM01144 », « IBM01145 », « IBM01146 », « IBM01147 », « IBM01148 », « IBM01149 », « ISO-2022-JP », « ISO-2022-KR », « ISO-8859-1 », « ISO-8859-2 », « ISO-8859-3 », « ISO-8859-4 », « ISO-8859-5 », « ISO-8859-6 », « ISO-8859-7 », « ISO-8859-8 », « ISO-8859-9 », « ISO-8859-13 », « ISO-8859-15 », « WINDOWS-874 », « WINDOWS-1250 », « WINDOWS-1251 », « WINDOWS-1252 », « WINDOWS-1253 », » WINDOWS-1254 », « WINDOWS-1255 », « WINDOWS-1256 », « WINDOWS-1257 », « WINDOWS-1258 ».| Non       |
| compressionCodec | Le codec de compression utilisé pour lire/écrire des fichiers texte. <br>Les valeurs autorisées sont **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **snappy**, ou **lz4**. À utiliser lors de l’enregistrement du fichier. <br>Notez que l’activité de copie ne prend actuellement pas en charge « snappy » et « lz4 ».<br>Notez que lorsque vous utilisez l’activité de copie pour décompresser un ou plusieurs fichiers ZipDeflate et écrire dans le magasin de données du récepteur basé sur des fichiers, les fichiers sont extraits dans le dossier : `<path specified in dataset>/<folder named as source zip file>/`. | Non       |
| compressionLevel | Le taux de compression. <br>Les valeurs autorisées sont **Optimal** ou **Fastest**.<br>- **Fastest (le plus rapide) :** l’opération de compression doit se terminer le plus rapidement possible, même si le fichier résultant n’est pas compressé de façon optimale.<br>- **Optimal** : l’opération de compression doit aboutir à une compression optimale, même si elle prend plus de temps. Pour plus d’informations, consultez la rubrique [Niveau de compression](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | Non       |

Voici un exemple de jeu de données JSON sur Stockage Blob Azure :

```json
{
    "name": "JSONDataset",
    "properties": {
        "type": "Json",
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
            "compression": {
                "type": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source et le récepteur JSON.

### <a name="json-as-source"></a>JSON en tant que source

Les propriétés prises en charge dans la section ***\*source\**** de l’activité de copie sont les suivantes.

| Propriété      | Description                                                  | Obligatoire |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La propriété type de la source d’activité de copie doit être **JSONSource**. | Oui      |
| storeSettings | Un groupe de propriétés sur la façon de lire les données d’un magasin de données. Chaque connecteur basé sur un fichier possède ses propres paramètres de lecture pris en charge sous `storeSettings`. **Consultez les détails dans l’article du connecteur -> section des propriétés de l’activité de copie**. | Non       |

### <a name="json-as-sink"></a>JSON en tant que récepteur

Les propriétés prises en charge dans la section ***\*récepteur\**** de l’activité de copie sont les suivantes.

| Propriété      | Description                                                  | Obligatoire |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La propriété type de la source d’activité de copie doit être définie sur **JSONSink**. | Oui      |
| formatSettings | Un groupe de propriétés. Reportez-vous au tableau **Paramètres d’écriture JSON** ci-dessous. | Non       |
| storeSettings | Groupe de propriétés sur la méthode d’écriture de données dans un magasin de données. Chaque connecteur basé sur un fichier possède ses propres paramètres d’écriture pris en charge sous `storeSettings`. **Consultez les détails dans l’article du connecteur -> section des propriétés de l’activité de copie**. | Non       |

**Paramètres d’écriture JSON** pris en charge sous `formatSettings` :

| Propriété      | Description                                                  | Obligatoire                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Le type de formatSettings doit être défini sur **JsonWriteSettings**. | Oui                                                   |
| filePattern |Indiquez le modèle des données stockées dans chaque fichier JSON. Les valeurs autorisées sont les suivantes : **setOfObjects** et **arrayOfObjects**. La valeur **par défaut** est **setOfObjects**. Consultez la section [Modèles de fichiers JSON](#json-file-patterns) pour en savoir plus sur ces modèles. |Non |

### <a name="json-file-patterns"></a>Modèles de fichiers JSON

L’activité de copie peut détecter et analyser automatiquement les modèles de fichiers JSON ci-dessous. 

- **Type I : setOfObjects**

    Chaque fichier contient un objet unique, ou plusieurs objets concaténés/délimités par des lignes. 
    Quand cette option est sélectionnée dans un récepteur de l’activité de copie, l’activité de copie produit un seul fichier JSON contenant un objet par ligne (format délimité par des lignes).

    * **Exemple de fichier JSON à un seul objet**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **Exemple de fichier JSON incluant des objets délimités par des lignes**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **Exemple de fichier JSON incluant des objets concaténés**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **Type II : arrayOfObjects**

    Chaque fichier contient un tableau d’objets.

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

## <a name="mapping-data-flow-properties"></a>Propriétés du mappage de flux de données

Les types de fichiers JSON peuvent être utilisés à la fois comme récepteur et comme source dans le flux de données de mappage.

### <a name="creating-json-structures-in-a-derived-column"></a>Création de structures JSON dans une colonne dérivée

Vous pouvez ajouter une colonne complexe à votre flux de données à l’aide du générateur d’expressions de la colonne dérivée. Dans la transformation de colonne dérivée, ajoutez une nouvelle colonne et ouvrez le générateur d’expressions en cliquant sur la zone bleue. Pour rendre une colonne complexe, vous pouvez entrer la structure JSON manuellement ou utiliser l’expérience utilisateur pour ajouter des sous-colonnes de manière interactive.

#### <a name="using-the-expression-builder-ux"></a>Utilisation de l’expérience utilisateur pour le générateur d’expressions

Dans le volet latéral du schéma de sortie, pointez sur une colonne, puis cliquez sur l’icône plus. Sélectionnez **Ajouter une sous-colonne** pour transformer la colonne en type complexe.

![Ajouter une sous-colonne](media/data-flow/addsubcolumn.png "Ajouter une sous-colonne")

Vous pouvez ajouter des colonnes et des sous-colonnes supplémentaires de la même façon. Pour chaque champ non complexe, une expression peut être ajoutée dans l’éditeur d’expressions vers la droite.

![Colonne complexe](media/data-flow/complexcolumn.png "Colonne complexe")

#### <a name="entering-the-json-structure-manually"></a>Saisie manuelle de la structure JSON

Pour ajouter manuellement une structure JSON, ajoutez une nouvelle colonne et entrez l’expression dans l’éditeur. L’expression suit le format général suivant :

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

Si cette expression était entrée pour une colonne nommée « complexColumn », elle serait écrite dans le récepteur sous la forme du code JSON suivant :

```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Exemple de script manuel pour une définition hiérarchique complète
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

### <a name="source-format-options"></a>Options de format source

L’utilisation d’un jeu de données JSON comme source dans votre flux de données vous permet de définir cinq paramètres supplémentaires. Ces paramètres se trouvent sous l’accordéon de **paramètres JSON**, sous l’onglet **Options de la source**.  

![Paramètres JSON](media/data-flow/json-settings.png "Paramètres JSON")

#### <a name="default"></a>Default

Par défaut, les données JSON sont lues dans le format suivant.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

#### <a name="single-document"></a>Document individuel

Si **Document unique** est sélectionné, le mappage de flux de données lit un document JSON à partir de chaque fichier. 

``` json
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```

#### <a name="unquoted-column-names"></a>Noms de colonnes sans guillemets

Si **Noms de colonnes sans guillemets** est sélectionné, le mappage des flux de données lit les colonnes JSON qui ne sont pas entourées de guillemets. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>Comporte des commentaires

Sélectionnez **Comporte des commentaires** si les données JSON ont des commentaires de style C ou C++.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>Apostrophes simples

Sélectionnez **Apostrophes simples** si les champs et valeurs JSON utilisent des guillemets simples au lieu de guillemets doubles.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>Barres obliques inverses d’échappement

Sélectionnez **Apostrophes simples** si les barres obliques inverses sont utilisées pour échapper les caractères dans les données JSON.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble des activités de copie](copy-activity-overview.md)
- [Mappage de flux de données](concepts-data-flow-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité GetMetadata](control-flow-get-metadata-activity.md)
