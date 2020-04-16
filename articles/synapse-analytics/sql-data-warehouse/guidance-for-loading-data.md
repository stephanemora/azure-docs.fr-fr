---
title: Bonnes pratiques en matière de chargement de données pour le pool SQL Synapse
description: Recommandations et optimisations des performances pour le chargement de données à l’aide du pool SQL Synapse.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: e170a789727fb0de36705895245cc638d30ee3d7
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745499"
---
# <a name="best-practices-for-loading-data-using-synapse-sql-pool"></a>Bonnes pratiques en matière de chargement de données à l’aide du pool SQL Synapse

Dans cet article, vous allez découvrir des recommandations et des optimisations des performances pour le chargement de données à l’aide du pool SQL.

## <a name="preparing-data-in-azure-storage"></a>Préparation des données dans Stockage Azure

Pour réduire la latence, colocalisez votre couche de stockage et votre pool SQL.

Lorsque vous exportez des données dans un format de fichier ORC, vous pouvez obtenir des erreurs Java de mémoire insuffisante lorsqu’il existe des colonnes de texte de grande taille. Pour contourner cette limitation, exportez uniquement un sous-ensemble de ces colonnes.

PolyBase ne peut pas charger de lignes comptant plus de 1 000 000 octets de données. Lorsque vous placez des données dans des fichiers texte dans le stockage Blob Azure ou Azure Data Lake Store, elles doivent être inférieures à 1 000 000 octets. Cette limitation du nombre d’octets est valable, quel que soit le schéma de table.

Tous les formats de fichier présentent des caractéristiques de performances différentes. Pour un chargement plus rapide, utilisez des fichiers texte délimités compressés. La différence entre les performances des formats UTF-8 et UTF-16 est minime.

Fractionnez les fichiers compressés volumineux en plusieurs petits fichiers compressés.

## <a name="running-loads-with-enough-compute"></a>Exécution de charges avec suffisamment de ressources de calcul

Pour une vitesse de chargement plus élevée, exécutez un seul travail de chargement à la fois. Si ce n’est pas possible, exécutez simultanément un nombre minimal de charges. Si vous prévoyez un travail de chargement volumineux, envisagez l’augmentation de l’échelle de votre pool SQL avant le chargement.

Pour exécuter des charges avec des ressources de calcul appropriées, créez des utilisateurs de chargement désignés pour cette tâche. Attribuez chaque utilisateur de chargement à une classe de ressources ou à un groupe de charge de travail spécifique. Pour exécuter une charge, connectez-vous en tant qu’utilisateur de chargement, puis exécutez la charge. La charge s’exécute avec la classe de ressources de l’utilisateur.  

> [!NOTE]
> Cette méthode est plus simple que d’essayer de modifier la classe de ressources d’un utilisateur pour répondre au besoin de la classe de ressources actuelle.

### <a name="example-of-creating-a-loading-user"></a>Exemple de création d’un utilisateur de chargement

Ce code crée un utilisateur de chargement pour la classe de ressources staticrc20. La première étape consiste à **se connecter au maître** et à créer une connexion.

```sql
   -- Connect to master
   CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
```

Connectez-vous au pool SQL et créez un utilisateur. Le code suivant suppose que vous êtes connecté à la base de données appelée mySampleDataWarehouse. Il montre comment créer un utilisateur nommé LoaderRC20 et donne à cet utilisateur l’autorisation de contrôle d’une base de données. Il ajoute ensuite l’utilisateur en tant que membre du rôle de base de données staticrc20.  

```sql
   -- Connect to the database
   CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
   GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
   EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
```

Pour exécuter une charge avec des ressources pour les classes de ressources staticRC20, connectez-vous en tant que LoaderRC20 et exécutez la charge.

Exécutez des charges sous des classes de ressources statiques plutôt que dynamiques. L’utilisation des classes de ressources statiques garantit les mêmes ressources, quel que soit vos valeurs [Data Warehouse Unit](what-is-a-data-warehouse-unit-dwu-cdwu.md). Si vous utilisez une classe de ressources dynamique, les ressources varient en fonction de votre niveau de service.

Pour les classes dynamiques, un niveau de service inférieur signifie que vous devrez probablement utiliser une classe de ressources supérieure pour votre utilisateur de chargement.

## <a name="allowing-multiple-users-to-load"></a>Autoriser le chargement par plusieurs utilisateurs

Il est souvent nécessaire que plusieurs utilisateurs chargent des données dans un pool SQL. Le chargement avec [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) nécessite des autorisations CONTROL de la base de données.  L’autorisation CONTROL permet de contrôler l’accès à tous les schémas.

Vous ne souhaiterez peut-être pas attribuer aux utilisateurs de chargement le contrôle d’accès sur tous les schémas. Pour limiter les autorisations, utilisez l’instruction DENY CONTROL.

Par exemple, les schémas de base de données schema_A pour le service A et schema_B pour le service B laissent les utilisateurs de base de données user_A et user_B être utilisateurs du chargement PolyBase dans les services A et B, respectivement. Ils ont tous deux reçu des autorisations de base de données CONTROL. Les créateurs des schémas A et B verrouillent maintenant leurs schémas à l’aide de DENY :

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A et user_B ne doivent maintenant plus avoir accès au schéma de l’autre service.

## <a name="loading-to-a-staging-table"></a>Chargement dans une table de mise en lots

Pour atteindre la vitesse de chargement la plus élevée pour le déplacement des données dans une table du pool SQL, chargez les données dans une table de mise en lots.  Définissez la table de mise en lots comme segment de mémoire et utilisez le tourniquet (round-robin) pour l’option de distribution.

Considérez que le chargement est généralement un processus en deux étapes, dans lequel vous effectuez tout d’abord le chargement dans une table de mise en lots puis insérez les données dans une table du pool SQL de production. Si la table de production utilise une distribution par hachage, la durée totale pour le chargement et l’insertion peut être plus rapide si vous définissez la table de mise en lots avec la distribution par hachage.

Le chargement dans la table de mise en lots prend plus de temps, mais la deuxième étape d’insertion des lignes dans la table de production n’entraîne pas de déplacement des données dans les distributions.

## <a name="loading-to-a-columnstore-index"></a>Chargement dans un index columnstore

Les index columnstore ont besoin de beaucoup de mémoire pour compresser les données dans des rowgroups de haute qualité. Pour une meilleure compression et un index plus efficace, l’index columnstore doit compresser au maximum 1 048 576 lignes dans chaque rowgroup.

En cas de sollicitation de la mémoire, l’index columnstore peut ne pas être en mesure d’atteindre les taux de compression maximum. Ce scénario, à son tour, affecte les performances des requêtes. Pour une présentation approfondie, consultez [Columnstore memory optimizations](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md) (Optimisation de mémoire Columstore).

- Pour garantir que l’utilisateur de chargement dispose de suffisamment de mémoire pour atteindre des taux de compression maximum, utilisez des utilisateurs de chargement qui sont membres d’une classe de ressources moyenne ou grande.
- Chargez suffisamment de lignes pour remplir complètement de nouveaux rowgroups. Lors d’un chargement en masse, les 1 048 576 lignes sont compressées directement dans le columnstore en tant que groupe de lignes complet. Les charges de moins de 102 400 lignes envoient les lignes dans le deltastore, où les lignes sont conservées dans un index b-tree.

> [!NOTE]
> Si vous chargez trop peu de lignes, elles risquent d’être toutes routées vers le deltastore et de ne pas être compressées immédiatement au format columnstore.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>Augmenter la taille de lot lors de l’utilisation de l’API SqLBulkCopy ou de bcp

Le chargement avec PolyBase fournit le débit le plus élevé avec le pool SQL. Si vous ne pouvez pas utiliser PolyBase pour charger et que vous devez utiliser l’[API SQLBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ou [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), songez à augmenter la taille de lot pour bénéficier d’un meilleur débit.

> [!TIP]
> Une taille de lot comprise entre 100 000 et 1 million de lignes est la base de référence recommandée pour déterminer la capacité de taille de lot optimale.

## <a name="handling-loading-failures"></a>Gestion des échecs de chargement

Une charge qui utilise une table externe peut échouer avec l’erreur suivante : *« Requête abandonnée : le seuil de rejet maximal a été atteint durant la lecture d’une source externe »* . Ce message indique que vos données externes contiennent des enregistrements à l’intégrité compromise.

L’intégrité d’un enregistrement de données est considérée comme compromise si l’une des conditions suivantes est remplie :

- Les types de données et le nombre de colonnes ne correspondent pas aux définitions de colonne de la table externe.
- Les données ne sont pas conformes au format de fichier externe spécifié.

Pour corriger les enregistrements compromis, assurez-vous que les définitions de format de votre table externe et de votre fichier externe sont correctes et que vos données externes sont conformes à ces définitions.

Si un sous-ensemble d’enregistrements de données externes est compromis, vous pouvez choisir de rejeter ces enregistrements pour vos requêtes en utilisant les options de rejet dans [CREATE EXTERNAL TABLE (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="inserting-data-into-a-production-table"></a>Insertion de données dans une table de production

Une charge unique dans une petite table à l’aide d’une instruction [INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) ou même un rechargement périodique d’une recherche peut suffire avec une instruction comme `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Cependant, des insertions uniques ne sont pas aussi efficaces qu’un chargement en masse.

Si vous avez au minimum plusieurs milliers d’insertions uniques pendant la journée, regroupez les insertions pour les charger en masse.  Développez votre processus pour ajouter les insertions uniques à un fichier, puis créez un autre processus qui charge régulièrement le fichier.

## <a name="creating-statistics-after-the-load"></a>Création de statistiques après le chargement

Pour améliorer les performances de vos requêtes, il est important de créer les statistiques sur toutes les colonnes de toutes les tables après le premier chargement ou après toute modification substantielle dans les données. Vous pouvez effectuer manuellement la création de statistiques ou vous pouvez activer [AUTO_CREATE_STATISTICS](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic).

Pour plus d’informations sur les statistiques, voir [Statistiques](sql-data-warehouse-tables-statistics.md). L’exemple suivant montre comment créer manuellement des statistiques sur cinq colonnes de la table Customer_Speed.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>Passer d’une clé de stockage à une autre

En matière de sécurité, il est recommandé de modifier régulièrement la clé d’accès à votre stockage d’objets blob. Vous avez deux clés de stockage pour votre compte de stockage blob, ce qui vous permet de passer d’une clé à l’autre.

Pour passer d’une clé de compte de stockage Azure à une autre :

Pour chaque compte de stockage dont la clé a changé, exécutez l’instruction [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Exemple :

Une clé d’origine est créée

```sql
CREATE DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key1'
```

Passer de la clé 1 à la clé 2

```sql
ALTER DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key2'
```

Aucune autre modification des sources de données externes sous-jacentes n’est nécessaire.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur PolyBase et la conception d’un processus d’extraction, de chargement et transformation (ELT), consultez [Designing Extract, Load, and Transform (ELT) for Azure SQL Data Warehouse](design-elt-data-loading.md) (Conception d’un processus d’extraction, de chargement et de transformation (ELT) pour Azure SQL Data Warehouse).
- Pour un didacticiel sur le chargement, consultez [Utiliser PolyBase pour charger des données du Stockage Blob Azure dans Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).
- Pour surveiller les charges de données, consultez [Surveiller votre charge de travail à l’aide de vues de gestion dynamique](sql-data-warehouse-manage-monitor.md).
