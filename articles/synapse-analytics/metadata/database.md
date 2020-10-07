---
title: Base de données partagée
description: Azure Synapse Analytics fournit un modèle de métadonnées partagées, où le fait de créer une base de données dans Apache Spark la rend accessible à partir de ses moteurs de pools SQL et SQL à la demande (préversion).
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 58c1aea944d89872a79d0672a925b1696791c1a8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91260850"
---
# <a name="azure-synapse-analytics-shared-database"></a>Base de données partagée Azure Synapse Analytics

Azure Synapse Analytics permet aux différents moteurs de calcul d’espace de travail de partager des bases de données et des tables entre leurs pools Spark (préversion) et le moteur SQL à la demande (préversion).

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Une base de données créée avec un travail Spark devient visible avec le même nom pour tous les pools Spark actuels et futurs (préversion) dans l’espace de travail, y compris le moteur SQL à la demande.

La base de données Spark par défaut, nommée `default`, est également visible dans le contexte SQL à la demande sous le nom `default`.

Comme elles sont synchronisées avec SQL à la demande de manière asynchrone, les bases de données apparaissent au bout d’un certain délai.

## <a name="manage-a-spark-created-database"></a>Gérer une base de données créée avec Spark

Utilisez Spark pour gérer les bases de données créées avec Spark. Par exemple, supprimez-les par le biais d’un travail de pool Spark et créez-y des tables à partir de Spark.

Si vous créez des objets dans une base de données créée avec Spark à l’aide de SQL à la demande, ou si vous essayez de supprimer la base de données, l’opération réussit, mais la base de données Spark d’origine ne sera pas modifiée.

## <a name="how-name-conflicts-are-handled"></a>Gestion des conflits de noms

Si le nom d’une base de données Spark est en conflit avec le nom d’une base de données SQL à la demande existante, un suffixe est ajouté dans SQL à la demande à la base de données Spark. Le suffixe dans SQL à la demande est `_<workspace name>-ondemand-DefaultSparkConnector`.

Par exemple, si une base de données Spark nommée `mydb` est créée dans l’espace de travail Azure Synapse `myws` et qu’une base de données SQL à la demande portant ce nom existe déjà, la base de données Spark dans SQL à la demande doit être référencée à l’aide du nom `mydb_myws-ondemand-DefaultSparkConnector`.

> [!CAUTION]
> Attention : Vous ne devez pas créer de dépendance envers ce comportement.

## <a name="security-model"></a>Modèle de sécurité

Les bases de données et les tables Spark, ainsi que leurs représentations synchronisées dans le moteur SQL, sont sécurisées au niveau du stockage sous-jacent.

Le principal de sécurité qui crée une base de données est considéré comme le propriétaire de cette base de données, et dispose de tous les droits sur la base de données et ses objets.

Pour donner à un principal de sécurité, tel qu’un utilisateur ou un groupe de sécurité, l’accès à une base de données, fournissez les autorisations de fichier et de dossier POSIX appropriées aux dossiers et fichiers sous-jacents dans le répertoire `warehouse`. 

Par exemple, pour qu’un principal de sécurité puisse lire une table dans une base de données, les autorisations `X` et `R` doivent lui être attribuées pour tous les dossiers à partir du dossier de la base de données dans le répertoire `warehouse`. En outre, les fichiers (tels que les fichiers de données sous-jacents de la table) nécessitent des autorisations `R`. 

Si un principal de sécurité nécessite la capacité à créer ou à supprimer des objets dans une base de données, des autorisations `W` supplémentaires sont requises sur les dossiers et les fichiers du dossier `warehouse`.

## <a name="examples"></a>Exemples

### <a name="create-and-connect-to-spark-database-with-sql-on-demand"></a>Créer une base de données Spark et s’y connecter avec SQL à la demande

Commencez par créer une base de données Spark nommée `mytestdb` à l’aide d’un cluster Spark que vous avez déjà créé dans votre espace de travail. Pour cela, vous pouvez par exemple utiliser un notebook C# Spark avec l’instruction .NET pour Spark suivante :

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Après un bref délai, la base de données de SQL à la demande est visible. Exécutez par exemple l’instruction suivante à partir de SQL à la demande.

```sql
SELECT * FROM sys.databases;
```

Vérifiez que `mytestdb` est inclus dans les résultats.

## <a name="next-steps"></a>Étapes suivantes

- [Apprenez-en davantage sur les métadonnées partagées Azure Synapse Analytics](overview.md)
- [Apprenez-en davantage sur les tables de métadonnées partagées Azure Synapse Analytics](table.md)
