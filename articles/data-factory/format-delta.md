---
title: Format Delta dans Azure Data Factory
description: Transformer et déplacer des données à partir d’un lac Delta à l’aide du format Delta
author: kromerm
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: makromer
ms.openlocfilehash: b5f344d97813e5f8bc7099631b56760ca2115f1a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122641253"
---
# <a name="delta-format-in-azure-data-factory"></a>Format Delta dans Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article explique comment copier des données vers et depuis un lac Delta stocké dans [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) ou [Stockage Blob Azure](connector-azure-blob-storage.md) à l’aide du format Delta. Ce connecteur est disponible sous forme de [jeu de données inline](data-flow-source.md#inline-datasets) dans le flux de données de mappage, en tant que source et récepteur.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4ALTs]

## <a name="mapping-data-flow-properties"></a>Propriétés du mappage de flux de données

Ce connecteur est disponible sous forme de [jeu de données inline](data-flow-source.md#inline-datasets) dans le flux de données de mappage, en tant que source et récepteur.

### <a name="source-properties"></a>Propriétés de source

Le tableau ci-dessous répertorie les propriétés prises en charge par une source Delta. Vous pouvez modifier ces propriétés sous l’onglet **Options de la source**.

| Nom | Description | Obligatoire | Valeurs autorisées | Propriété du script de flux de données |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | Le format doit être `delta`. | Oui | `delta` | format |
| Système de fichiers | Conteneur/système de fichiers du lac Delta | Oui | String | fileSystem |
| Chemin d’accès du dossier | Chemin direct au lac Delta | Oui | String | folderPath |
| Type de compression | Type de compression de la table Delta | non | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | compressionType |
| Niveau de compression | Indiquez si la compression doit se terminer le plus rapidement possible ou si le fichier obtenu doit être compressé de façon optimale. | Obligatoire si `compressedType` est spécifié. | `Optimal` ou `Fastest` | compressionLevel |
| Voyage dans le temps | Indiquez si vous souhaitez interroger un instantané plus ancien de table Delta | non | Interroger par timestamp : Timestamp <br> Interroger par version : Integer | timestampAsOf <br> versionAsOf |
| N’autoriser aucun fichier trouvé | Si la valeur est true, aucune erreur n’est levée si aucun fichier n’est trouvé | non | `true` ou `false` | ignoreNoFilesFound |

#### <a name="import-schema"></a>Importer un schéma

Delta est uniquement disponible en tant que jeu de données inline et, par défaut, ne dispose pas de schéma associé. Pour récupérer les métadonnées des colonnes, cliquez sur le bouton **Importer le schéma** sous l’onglet **Projection**. Cela vous permet de référencer les noms de colonnes et les types de données spécifiés par le corpus. Pour importer le schéma, une [session de débogage de flux de données](concepts-data-flow-debug-mode.md) doit être active et vous devez disposer d’un fichier de définition d’entité CDM vers lequel pointer.
 

### <a name="delta-source-script-example"></a>Exemple de script source Delta

```
source(output(movieId as integer,
            title as string,
            releaseDate as date,
            rated as boolean,
            screenedOn as timestamp,
            ticketPrice as decimal(10,2)
            ),
    store: 'local',
    format: 'delta',
    versionAsOf: 0,
    allowSchemaDrift: false,
    folderPath: $tempPath + '/delta'
  ) ~> movies
```

### <a name="sink-properties"></a>Propriétés du récepteur

Le tableau ci-dessous répertorie les propriétés prises en charge par un récepteur Delta. Vous pouvez modifier ces propriétés sous l’onglet **Paramètres**.

| Nom | Description | Obligatoire | Valeurs autorisées | Propriété du script de flux de données |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | Le format doit être `delta`. | Oui | `delta` | format |
| Système de fichiers | Conteneur/système de fichiers du lac Delta | Oui | String | fileSystem |
| Chemin d’accès du dossier | Chemin direct au lac Delta | Oui | String | folderPath |
| Type de compression | Type de compression de la table Delta | non | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | compressionType |
| Niveau de compression | Indiquez si la compression doit se terminer le plus rapidement possible ou si le fichier obtenu doit être compressé de façon optimale. | Obligatoire si `compressedType` est spécifié. | `Optimal` ou `Fastest` | compressionLevel |
| Nettoyer | Spécifiez le seuil de rétention en heures pour les anciennes versions de table. Une valeur inférieure ou égale à 0 correspond par défaut à 30 jours | Oui | Integer | vacuum |
| Mettre à jour la méthode | Spécifiez les opérations de mise à jour autorisées sur le lac Delta. Pour les méthodes autres qu’insert, une transformation de modification de ligne précédente est requise afin de marquer des lignes. | Oui | `true` ou `false` | deletable <br> insertable <br> updateable <br> fusion |
| Écriture optimisée | Obtenez un débit plus élevé pour l’opération d’écriture par le biais de l’optimisation de la lecture aléatoire interne dans les exécuteurs Spark. Il peut en résulter moins de partitions et de fichiers de plus grande taille. | non | `true` ou `false` | optimizedWrite : true |
| Compactage automatique | Une fois qu’une opération d’écriture est terminée, Spark exécute automatiquement la commande ```OPTIMIZE``` pour réorganiser les données, ce qui entraîne davantage de partitions si nécessaire, pour une meilleure lecture des performances à l’avenir. | non | `true` ou `false` |    autoCompact : true |

### <a name="delta-sink-script-example"></a>Exemple de script de récepteur Delta

Le script de flux de données associé est le suivant :

```
moviesAltered sink(
          input(movieId as integer,
                title as string
            ),
           mapColumn(
                movieId,
                title
            ),
           insertable: true,
           updateable: true,
           deletable: true,
           upsertable: false,
           keys: ['movieId'],
            store: 'local',
           format: 'delta',
           vacuum: 180,
           folderPath: $tempPath + '/delta'
           ) ~> movieDB
```

### <a name="known-limitations"></a>Limitations connues

Lors de l’écriture dans un récepteur Delta, il existe une limitation connue au-delà de laquelle le nombre de lignes écrites ne sera pas retourné dans la sortie de la surveillance.

## <a name="next-steps"></a>Étapes suivantes

* Créer une [transformation de source](data-flow-source.md) dans le flux de données de mappage.
* Créer une [transformation de récepteur](data-flow-sink.md) dans le flux de données de mappage.
* Créer une [transformation de modification de ligne](data-flow-alter-row.md) pour marquer des lignes comme insert, update, upsert ou delete.
