---
title: Format Common Data Model
description: Transformer des données à l’aide du système de métadonnées Common Data Model
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: daperlov
ms.openlocfilehash: 3c4f2df074bc7feaa42704942a3fd238ab4b333a
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86083778"
---
# <a name="common-data-model-format-in-azure-data-factory"></a>Format Common Data Model dans Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Le système de métadonnées Common Data Model (CDM) permet de partager facilement des données et leur signification entre des applications et des processus d’entreprise. Pour plus d’informations, consultez la vue d’ensemble de [Common Data Model](https://docs.microsoft.com/common-data-model/).

Dans Azure Data Factory, les utilisateurs peuvent transformer, vers et depuis, des entités CDM stockées dans [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) (ADLS Gen2) à l’aide de flux de données de mappage. Choisissez entre modèle.json et des sources de style de manifeste CDM et écrivez dans les fichiers de manifeste CDM.

> [!NOTE]
> Le connecteur de format Common Data Model (CDM) pour les flux de données ADF est actuellement disponible en préversion publique.

## <a name="mapping-data-flow-properties"></a>Propriétés du mappage de flux de données

Le modèle Common Data Model est disponible en tant que [jeu de données inline](data-flow-source.md#inline-datasets) dans le flux de données de mappage, en tant que source et récepteur.

> [!NOTE]
> Lorsque vous écrivez des entités CDM, vous devez avoir une définition d’entité CDM existante (schéma de métadonnées) déjà définie. Le récepteur de flux de données ADF lira ce fichier d’entité CDM et importera le schéma dans votre récepteur pour le mappage de champs.

### <a name="source-properties"></a>Propriétés de sources

Le tableau ci-dessous répertorie les propriétés prises en charge par une source CDM. Vous pouvez modifier ces propriétés sous l’onglet **Options de source**.

| Nom | Description | Obligatoire | Valeurs autorisées | Propriété du script de flux de données |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | Le format doit être `cdm` | Oui | `cdm` | format |
| Format de métadonnées | Où se trouve la référence d’entité aux données. Si vous utilisez la version 1.0 de CDM, choisissez manifeste. Si vous utilisez une version CDM antérieure à 1.0, choisissez model.json. | Oui | `'manifest'` ou `'model'` | manifestType |
| Emplacement racine : conteneur | Nom de conteneur du dossier CDM | Oui | String | fileSystem |
| Emplacement racine : chemin d’accès au dossier | Emplacement du dossier racine du dossier CDM | Oui | String | folderPath |
| Fichier manifeste : Chemin d’accès de l'entité | Chemin d’accès du dossier de l’entité dans le dossier racine | non | String | entityPath |
| Fichier manifeste : Nom du manifeste | Nom du fichier manifeste. La valeur par défaut est « Default »  | Non | String | manifestName |
| Filtrer par date de dernière modification | Choisir pour filtrer les fichiers en fonction de leur date de dernière modification | non | Timestamp | modifiedAfter <br> modifiedBefore | 
| Service lié au schéma | Le service lié dans lequel se trouve le corpus | oui, en cas d’utilisation du manifeste | `'adlsgen2'` ou `'github'` | corpusStore | 
| Conteneur de référence d’entité | Le corpus du conteneur est dans | oui, si vous utilisez le manifeste et le corpus dans ADLS Gen2 | String | adlsgen2_fileSystem |
| Référentiel de référence d’entité | Nom du référentiel GitHub | oui, si vous utilisez le manifeste et le corpus dans GitHub | String | github_repository |
| Branche de référence d’entité | Branche du référentiel GitHub | oui, si vous utilisez le manifeste et le corpus dans GitHub | String |  github_branch |
| Dossier de corpus | l’emplacement racine du corpus | oui, en cas d’utilisation du manifeste | String | corpusPath |
| Entité de corpus | Chemin d’accès vers la référence d’entité | Oui | String | entité |
| N’autoriser aucun fichier trouvé | Si la valeur est true, aucune erreur n’est levée si aucun fichier n’est trouvé | non | `true` ou `false` | ignoreNoFilesFound |

#### <a name="import-schema"></a>Importer un schéma

CDM est uniquement disponible en tant que jeu de données inline et, par défaut, n’a pas de schéma associé. Pour récupérer les métadonnées des colonnes, cliquez sur le bouton **Importer le schéma** sous l’onglet **Projection**. Cela vous permet de référencer les noms de colonnes et les types de données spécifiés par le corpus. Pour importer le schéma, une [session de débogage de flux de données](concepts-data-flow-debug-mode.md) doit être active et vous devez disposer d’un fichier de définition d’entité CDM à pointer.

> [!NOTE]
>  Lorsque vous utilisez le type de source model.json qui provient de flux de données Power BI ou Power Platform, vous pouvez rencontrer des erreurs « le chemin d’accès du corpus est Null ou vide » de la transformation source. Cela est probablement dû à des problèmes de mise en forme du chemin d’accès de l’emplacement de la partition dans le fichier model.json. Pour résoudre ce problème, procédez comme suit : 

1. Ouvrez le fichier model.json dans un éditeur de texte
2. Recherchez les partitions.Location, propriété 
3. Remplacez « blob.core.windows.net » par « dfs.store.core.windows.net »
4. Corrigez tout encodage « % 2F » dans l’URL en « / »
 

### <a name="cdm-source-data-flow-script-example"></a>Exemple de script de flux de données sources CDM

```
source(output(
        ProductSizeId as integer,
        ProductColor as integer,
        CustomerId as string,
        Note as string,
        LastModifiedDate as timestamp
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    manifestType: 'manifest',
    manifestName: 'ProductManifest',
    entityPath: 'Product',
    corpusPath: 'Products',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    folderPath: 'ProductData',
    fileSystem: 'data') ~> CDMSource
```

### <a name="sink-properties"></a>Propriétés du récepteur

Le tableau ci-dessous répertorie les propriétés prises en charge par un récepteur CDM. Vous pouvez modifier ces propriétés sous l’onglet **Paramètres**.

| Nom | Description | Obligatoire | Valeurs autorisées | Propriété du script de flux de données |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | Le format doit être `cdm` | Oui | `cdm` | format |
| Emplacement racine : conteneur | Nom de conteneur du dossier CDM | Oui | String | fileSystem |
| Emplacement racine : chemin d’accès au dossier | Emplacement du dossier racine du dossier CDM | Oui | String | folderPath |
| Fichier manifeste : Chemin d’accès de l'entité | Chemin d’accès du dossier de l’entité dans le dossier racine | non | String | entityPath |
| Fichier manifeste : Nom du manifeste | Nom du fichier manifeste. La valeur par défaut est « Default » | Non | String | manifestName |
| Service lié au schéma | Le service lié dans lequel se trouve le corpus | Oui | `'adlsgen2'` ou `'github'` | corpusStore | 
| Conteneur de référence d’entité | Le corpus du conteneur est dans | oui, si corpus dans ADLS Gen2 | String | adlsgen2_fileSystem |
| Référentiel de référence d’entité | Nom du référentiel GitHub | oui, si corpus dans GitHub | String | github_repository |
| Branche de référence d’entité | Branche du référentiel GitHub | oui, si corpus dans GitHub | String |  github_branch |
| Dossier de corpus | l’emplacement racine du corpus | Oui | String | corpusPath |
| Entité de corpus | Chemin d’accès vers la référence d’entité | Oui | String | entité |
| Chemin d’accès de partition | Emplacement où la partition sera écrite | non | String | partitionPath |
| Vider le dossier | Si le dossier de destination est vidé avant l’écriture | non | `true` ou `false` | truncate |
| Type de format | Choisir pour spécifier le format parquet | non | `parquet` si spécifié | subformat |
| Délimiteur de colonne | En cas d’écriture dans DelimitedText, comment délimiter des colonnes | oui, en cas d’écriture dans DelimitedText | String | columnDelimiter |
| Première ligne comme en-tête | En cas d’utilisation de DelimitedText, si les noms des colonnes sont ajoutés en tant qu’en-tête | non | `true` ou `false` | columnNamesAsHeader |

### <a name="cdm-sink-data-flow-script-example"></a>Exemple de script de flux de données de récepteur CDM

Le script de flux de données associé est :

```
CDMSource sink(allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    entityPath: 'ProductSize',
    manifestName: 'ProductSizeManifest',
    corpusPath: 'Products',
    partitionPath: 'adf',
    folderPath: 'ProductSizeData',
    fileSystem: 'cdm',
    subformat: 'parquet',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CDMSink

```

## <a name="next-steps"></a>Étapes suivantes

Créez une [Transformation de la source](data-flow-source.md) dans le flux de données de mappage.
