---
title: Vue d’ensemble - Interroger des données dans le stockage avec SQL à la demande (préversion)
description: Cette section contient des exemples de requêtes que vous pouvez utiliser pour tester la ressource SQL à la demande (préversion) dans Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: d42dac0899c5ab9c4f817df40095dd08891fb8e7
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85207700"
---
# <a name="overview-query-data-in-storage"></a>Présentation : Interroger des données dans le stockage

Cette section contient des exemples de requêtes que vous pouvez utiliser pour tester la ressource SQL à la demande (préversion) dans Azure Synapse Analytics.
Les formats de fichier actuellement pris en charge sont :  
- CSV
- Parquet
- JSON

## <a name="prerequisites"></a>Prérequis

Les outils dont vous avez besoin pour émettre des requêtes :

- Client SQL de votre choix :
    - Azure Synapse Studio (préversion)
    - Azure Data Studio
    - SQL Server Management Studio

Les paramètres sont également les suivants :

| Paramètre                                 | Description                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Adresse de point de terminaison de service SQL à la demande    | Est utilisé comme nom de serveur.                                   |
| Région de point de terminaison de service SQL à la demande     | Est utilisé pour déterminer le stockage utilisé dans les exemples. |
| Nom d’utilisateur et mot de passe pour l’accès au point de terminaison | Est utilisé pour accéder au point de terminaison.                               |
| La base de données que vous allez utiliser pour créer des vues     | Cette base de données est utilisée comme point de départ pour les exemples.       |

## <a name="first-time-setup"></a>Première configuration

La première étape consiste à **créer la base de données** dans laquelle seront exécutées les requêtes. Ensuite, initialisez les objets en exécutant le [script d’installation](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) sur cette base de données. Ce script d’installation crée les sources de données, les informations d’identification limitées à la base de données et les formats de fichiers externes utilisés pour la lecture des données dans ces exemples.

> [!NOTE]
> Les bases de données sont utilisées uniquement pour voir les métadonnées, et non pour les données réelles.  Notez le nom de la base de données que vous utilisez, vous en aurez besoin plus tard.

```sql
CREATE DATABASE mydbname;
```

## <a name="provided-demo-data"></a>Données de démonstration fournies

Les données de démonstration contiennent les jeux de données suivants :

- Taxi de New York - Enregistrements de trajet de taxi jaune - partie du jeu de données publiques de New York
  - Format CSV
  - Format Parquet
- Jeu de données Population
  - Format CSV
- Exemples de fichiers Parquet avec colonnes imbriquées
  - Format Parquet
- Livres JSON
  - Format JSON

| Chemin d’accès du dossier                                                  | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /csv/                                                        | Dossier parent des données au format CSV                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | Dossiers contenant les fichiers de données Population dans différents formats CSV. |
| /csv/taxi/                                                   | Dossier contenant les fichiers de données publiques de New York au format CSV              |
| /parquet/                                                    | Dossier parent des données au format Parquet                     |
| /parquet/taxi                                                | Fichiers de données publiques de New York au format Parquet, partitionnés par année et par mois à l’aide du schéma de partitionnement Hive/Hadoop. |
| /parquet/nested/                                             | Exemples de fichiers Parquet avec colonnes imbriquées                     |
| /json/                                                       | Dossier parent des données au format JSON                        |
| /json/books/                                                 | Fichiers JSON avec des données de livres                                   |

### <a name="sample-query"></a>Exemple de requête

La dernière étape de la validation consiste à exécuter la requête suivante :

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'parquet/taxi/year=2017/month=9/*.parquet',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT='PARQUET'
    ) AS nyc;
```

La requête ci-dessus doit retourner ce nombre : **8945574**.

## <a name="next-steps"></a>Étapes suivantes

Vous êtes maintenant prêt à poursuivre avec les articles de guide pratique suivants :

- [Interroger un fichier CSV](query-single-csv-file.md)

- [Interroger des dossiers et plusieurs fichiers CSV](query-folders-multiple-csv-files.md)

- [Interroger des fichiers spécifiques](query-specific-files.md)

- [Interroger des fichiers Parquet](query-parquet-files.md)

- [Interroger des types imbriqués Parquet](query-parquet-nested-types.md)

- [Interroger des fichiers JSON](query-json-files.md)

- [Créer et utiliser des vues](create-use-views.md)
