---
title: Vue d’ensemble - Interroger des données dans le stockage avec SQL à la demande (préversion)
description: Cette section contient des exemples de requêtes que vous pouvez utiliser pour tester la ressource SQL à la demande (préversion) dans Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: cdad95b1a910a45629e85bcc716218b272afd9de
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421293"
---
# <a name="overview-query-data-in-storage"></a>Présentation : Interroger des données dans le stockage

Cette section contient des exemples de requêtes que vous pouvez utiliser pour tester la ressource SQL à la demande (préversion) dans Azure Synapse Analytics.
Actuellement les fichiers pris en charge sont les suivants : 
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

Avant d’utiliser les exemples contenus plus loin dans cet article, vous devez suivre deux étapes :

- Créer une base de données pour vos vues (au cas où vous souhaiteriez utiliser des vues)
- Créer les informations d’identification à utiliser par SQL à la demande pour accéder aux fichiers dans le stockage

### <a name="create-database"></a>Créer une base de données

Vous avez besoin d’une base de données pour créer des vues. Vous utiliserez cette base de données pour certains exemples de requêtes de cette documentation.

> [!NOTE]
> Les bases de données sont utilisées uniquement pour voir les métadonnées, et non pour les données réelles.  Notez le nom de la base de données que vous utilisez, vous en aurez besoin plus tard.

```sql
CREATE DATABASE mydbname;
```

### <a name="create-credentials"></a>Créer des informations d’identification

Vous devez créer des informations d’identification avant de pouvoir exécuter des requêtes. Ces informations d’identification sont utilisées par le service SQL à la demande pour accéder aux fichiers dans le stockage.

> [!NOTE]
> Pour pouvoir exécuter correctement les procédures de cette section, vous devez utiliser un jeton SAS.
>
> Pour commencer à utiliser des jetons SAS, vous devez supprimer UserIdentity, ce qui est expliqué dans l’article [suivant](develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through).
>
> Par défaut, SQL à la demande utilise toujours le transfert AAD.

Pour plus d’informations sur la gestion du contrôle d’accès au stockage, consultez ce [lien](develop-storage-files-storage-access-control.md).

> [!WARNING]
> Vous devez créer des informations d’identification pour un compte de stockage qui se trouve dans votre région de point de terminaison. Bien que SQL à la demande puisse accéder aux stockages de différentes régions, le fait d’avoir le stockage et le point de terminaison dans la même région procure une meilleure expérience en matière de performances.

Pour créer des informations d’identification destinées aux conteneurs CSV, JSON et Parquet, exécutez le code ci-dessous :

```sql
-- create credentials for CSV container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/csv')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for JSON container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/json')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for PARQUET container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/parquet')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO
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

## <a name="validation"></a>Validation

Exécutez les trois requêtes suivantes et vérifiez si les informations d’identification sont créées correctement.

> [!NOTE]
> Tous les URI des exemples de requêtes utilisent un compte de stockage situé dans la région Azure Europe Nord. Assurez-vous d’avoir créé les informations d’identification appropriées. Exécutez la requête ci-dessous et vérifiez que le compte de stockage est listé.

```sql
SELECT name
FROM sys.credentials
WHERE
     name IN ( 'https://sqlondemandstorage.blob.core.windows.net/csv',
     'https://sqlondemandstorage.blob.core.windows.net/parquet',
     'https://sqlondemandstorage.blob.core.windows.net/json');
```

Si vous ne trouvez pas les informations d’identification appropriées, consultez [Première configuration](#first-time-setup).

### <a name="sample-query"></a>Exemple de requête

La dernière étape de la validation consiste à exécuter la requête suivante :

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
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
