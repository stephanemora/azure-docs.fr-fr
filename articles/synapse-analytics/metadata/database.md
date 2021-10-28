---
title: Base de données partagée
description: Azure Synapse Analytics fournit un modèle de métadonnées partagées avec lequel la création d’une base de données dans le pool Apache Spark serverless rend celle-ci accessible depuis ses moteurs de pool SQL serverless et pool SQL.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 10/05/2021
author: ma77b
ms.author: maburd
ms.reviewer: wiassaf
ms.custom: devx-track-csharp
ms.openlocfilehash: 6144afdca350e8e7ff609eec273f84a39b84d2a3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130228484"
---
# <a name="azure-synapse-analytics-shared-database"></a>Base de données partagée Azure Synapse Analytics

Azure Synapse Analytics permet aux différents moteurs de calcul d’espace de travail de partager des bases de données et des tables. Actuellement, les bases de données et les tables (Parquet ou CSV) créées sur les pools Apache Spark sont automatiquement partagées avec le moteur du pool SQL serverless.

Une base de données créée avec un travail Spark devient visible avec le même nom pour tous les pools Spark actuels et futurs dans l’espace de travail, y compris le moteur du pool SQL serverless. Vous ne pouvez pas ajouter d’objets personnalisés (tables externes, vues, procédures) directement dans cette base de données synchronisée en utilisant le pool SQL serverless.

La base de données Spark par défaut, nommée `default`, est également visible dans le contexte SQL du pool SQL serverless sous le nom `default`. Vous ne pouvez pas créer une base de données dans Spark puis créer une autre base de données portant le même nom dans un pool SQL serverless.

Comme les bases de données sont synchronisées avec le pool SQL serverless de façon asynchrone, elles apparaissent après un certain délai.

## <a name="manage-a-spark-created-database"></a>Gérer une base de données créée avec Spark

Pour gérer les bases de données créées avec Spark, vous devez utiliser des pools Apache Spark. Par exemple, créez-les ou supprimez-les par le biais d’un travail de pool Spark.

Les objets des bases de données synchronisées ne peuvent pas être modifiés à partir d’un pool SQL serverless.

>[!NOTE]
>Vous ne pouvez pas créer plusieurs bases de données portant le même nom à partir de pools différents. Si une base de données de pool SQL serverless est créée, vous ne pouvez pas créer une base de données Spark portant le même nom. De même, si une base de données est créée dans Spark, vous ne pouvez pas créer une base de données de pool SQL serverless portant le même nom.

## <a name="security-model"></a>Modèle de sécurité

Les bases de données et les tables Spark, ainsi que leurs représentations synchronisées dans le moteur SQL, sont sécurisées au niveau du stockage sous-jacent.

Le principal de sécurité qui crée une base de données est considéré comme le propriétaire de cette base de données, et dispose de tous les droits sur la base de données et ses objets. L’administrateur Synapse et l’administrateur Synapse SQL disposent également de toutes les autorisations sur les objets synchronisés dans le pool SQL serverless par défaut. La création d’objets personnalisés (y compris des utilisateurs) dans des bases de données SQL synchronisées n’est pas autorisée. 

Pour accorder à un principal de sécurité, tel qu’un utilisateur, une application Azure AD ou un groupe de sécurité, l’accès aux données sous-jacentes utilisées pour les tables externes, vous devez leur accorder des autorisations `read (R)` sur les fichiers (tels que les fichiers de données sous-jacents de la table) et `execute (X)` sur le dossier où les fichiers sont stockés ainsi que sur chaque dossier parent jusqu’à la racine. Pour plus d’informations sur ces autorisations, consultez la page [Listes de contrôle d’accès (ACL)](../../storage/blobs/data-lake-storage-access-control.md). 

Par exemple, dans `https://<storage-name>.dfs.core.windows.net/<fs>/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/`, les principaux de sécurité doivent avoir des autorisations `X` sur tous les dossiers de `<fs>` jusqu’à `myparquettable`, et des autorisations `R` sur `myparquettable` et sur les fichiers qui se trouvent dans ce dossier, pour pouvoir lire une table dans une base de données (synchronisée ou d’origine).

Si un principal de sécurité nécessite la capacité à créer ou à supprimer des objets dans une base de données, des autorisations `W` supplémentaires sont requises sur les dossiers et les fichiers du dossier `warehouse`. La modification des objets d’une base de données n’est pas possible à partir d’un pool SQL serverless, mais uniquement à partir de Spark.

## <a name="examples"></a>Exemples

### <a name="create-and-connect-to-spark-database-with-serverless-sql-pool"></a>Créer une base de données Spark et s’y connecter avec le pool SQL serverless

Commencez par créer une base de données Spark nommée `mytestdb` à l’aide d’un cluster Spark que vous avez déjà créé dans votre espace de travail. Pour cela, vous pouvez par exemple utiliser un notebook C# Spark avec l’instruction .NET pour Spark suivante :

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Après un bref délai, vous pouvez voir la base de données depuis le pool SQL serverless. Exécutez par exemple l’instruction suivante à partir du pool SQL serverless.

```sql
SELECT * FROM sys.databases;
```

Vérifiez que `mytestdb` est inclus dans les résultats.

## <a name="next-steps"></a>Étapes suivantes

- [Apprenez-en davantage sur les métadonnées partagées Azure Synapse Analytics](overview.md)
- [Apprenez-en davantage sur les tables de métadonnées partagées Azure Synapse Analytics](table.md)