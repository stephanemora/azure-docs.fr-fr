---
title: Base de données partagée Azure Synapse Analytics
description: Azure Synapse Analytics fournit un modèle de métadonnées partagées, où le fait de créer une base de données dans Apache Spark la rend accessible à partir de ses moteurs de pools SQL et SQL à la demande (préversion).
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: e3651467de86d3b026ab348675249f93ebf3a86a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420213"
---
# <a name="azure-synapse-analytics-shared-database"></a>Base de données partagée Azure Synapse Analytics

Azure Synapse Analytics permet aux différents moteurs d’espace de travail de calcul de partager des bases de données et des tables entre leurs pools Spark (préversion), le moteur SQL à la demande (préversion) et les pools SQL.

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Une base de données créée avec un travail Spark devient visible avec le même nom pour tous les pools Spark actuels et futurs (préversion) dans l’espace de travail ainsi que le moteur SQL à la demande.

Si l’espace de travail contient des pools SQL pour lesquels la synchronisation des métadonnées est activée, ou si vous créez un pool SQL avec la synchronisation des métadonnées activée, ces bases de données créées avec Spark sont mappées automatiquement à des schémas spéciaux dans la base de données du pool SQL. 

Chaque schéma est nommé d’après le nom de la base de données Spark avec un préfixe `$` supplémentaire. Les tables externes et gérées dans la base de données générée par Spark sont exposées en tant que tables externes dans le schéma spécial correspondant.

La base de données Spark par défaut, appelée `default`, est également visible dans le contexte SQL à la demande en tant que base de données appelée `default`, et dans toutes les bases de données du pool SQL pour lesquelles la synchronisation des métadonnées est activée en tant que schéma `$default`.

Étant donné que les bases de données sont synchronisées avec SQL à la demande et les pools SQL de manière asynchrone, il y aura un délai avant leur apparition.

## <a name="manage-a-spark-created-database"></a>Gérer une base de données créée avec Spark

Utilisez Spark pour gérer les bases de données créées avec Spark. Par exemple, supprimez-les par le biais d’un travail de pool Spark et créez-y des tables à partir de Spark.

Si vous créez des objets dans une base de données créée avec Spark à l’aide de SQL à la demande, ou si vous essayez de supprimer la base de données, l’opération réussit, mais la base de données Spark d’origine ne sera pas modifiée.

Si vous tentez de supprimer le schéma synchronisé dans un pool SQL, ou tentez d’y créer une table, Azure retourne une erreur.

## <a name="handling-of-name-conflicts"></a>Gestion des conflits de noms

Si le nom d’une base de données Spark est en conflit avec le nom d’une base de données SQL à la demande existante, un suffixe est ajouté dans SQL à la demande à la base de données Spark. Le suffixe dans SQL à la demande est `_<workspace name>-ondemand-DefaultSparkConnector`.

Par exemple, si une base de données Spark nommée `mydb` est créée dans l’espace de travail Azure Synapse `myws` et qu’une base de données SQL à la demande portant ce nom existe déjà, la base de données Spark dans SQL à la demande doit être référencée à l’aide du nom `mydb_myws-ondemand-DefaultSparkConnector`.

> [!CAUTION]
> Attention : Vous ne devez pas créer de dépendance envers ce comportement.

## <a name="security-model"></a>Modèle de sécurité

Les bases de données et les tables Spark, ainsi que leurs représentations synchronisées dans les moteurs SQL, sont sécurisées au niveau du stockage sous-jacent.

Le principal de sécurité qui crée une base de données est considéré comme le propriétaire de cette base de données, et dispose de tous les droits sur la base de données et ses objets.

Pour donner à un principal de sécurité, tel qu’un utilisateur ou un groupe de sécurité, l’accès à une base de données, fournissez les autorisations de fichier et de dossier POSIX appropriées aux dossiers et fichiers sous-jacents dans le répertoire `warehouse`. 

Par exemple, pour qu’un principal de sécurité puisse lire une table dans une base de données, les autorisations `X` et `R` doivent lui être attribuées pour tous les dossiers à partir du dossier de la base de données dans le répertoire `warehouse`. En outre, les fichiers (tels que les fichiers de données sous-jacents de la table) nécessitent des autorisations `R`. 

Si un principal de sécurité nécessite la capacité à créer ou à supprimer des objets dans une base de données, des autorisations `W` supplémentaires sont requises sur les dossiers et les fichiers du dossier `warehouse`.

## <a name="examples"></a>Exemples

### <a name="create--connect-to-spark-database---sql-on-demand"></a>Créer une base de données Spark et s’y connecter - SQL à la demande

Commencez par créer une base de données Spark nommée `mytestdb` à l’aide d’un cluster Spark que vous avez déjà créé dans votre espace de travail. Pour cela, vous pouvez par exemple utiliser un notebook C# Spark avec l’instruction .NET pour Spark suivante :

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Après un bref délai, la base de données de SQL à la demande est visible. Exécutez par exemple l’instruction suivante à partir de SQL à la demande.

```sql
SELECT * FROM sys.databases;
```

Vérifiez que `mytestdb` est inclus dans les résultats.

### <a name="exposing-a-spark-database-in-a-sql-pool"></a>Exposition d’une base de données Spark dans un pool SQL

Avec la base de données créée dans l’exemple précédent, créez maintenant un pool SQL dans votre espace de travail nommé `mysqlpool` qui active la synchronisation des métadonnées.

Exécutez l’instruction suivante sur le pool SQL `mysqlpool` :

```sql
SELECT * FROM sys.schema;
```

Vérifiez le schéma de la base de données que vous venez de créer dans les résultats.

## <a name="next-steps"></a>Étapes suivantes

- [Apprenez-en davantage sur les métadonnées partagées Azure Synapse Analytics](overview.md)
- [Apprenez-en davantage sur les tables de métadonnées partagées Azure Synapse Analytics](table.md)

<!-- - [Learn more about the Synchronization with SQL Analytics on-demand](overview.md)
- [Learn more about the Synchronization with SQL Analytics pools](overview.md)-->
