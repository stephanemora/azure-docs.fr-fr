---
title: 'Démarrage rapide : Créer un classifieur de charge de travail - T-SQL | Microsoft Docs'
description: Utiliser T-SQL pour créer un classifieur de charge de travail avec une importance haute
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 198faf6791a4a2caa2cefee2181a13ed8185310e
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617335"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql-preview"></a>Démarrage rapide : Créer un classifieur de charge de travail à l’aide de T-SQL (préversion)

Dans ce guide de démarrage rapide, vous créez rapidement un classifieur de charge de travail avec une importance haute pour le PDG (CEO) de votre organisation. Ce classifieur de charge de travail donne la priorités aux requêtes du PDG sur les autres requêtes avec une importance inférieure dans la file d’attente.

> [!Note]
> La classification de charge de travail est disponible en préversion dans SQL Data Warehouse Gen2. Les informations relatives à l’importance et à la classification de la gestion des charges de travail (préversion) concernent les builds publiées au plus tôt le 9 avril 2019.  Les utilisateurs doivent éviter d’utiliser les builds antérieures à cette date à des fins de test de la gestion des charges de travail.  Pour déterminer si votre build est compatible avec la gestion des charges de travail, exécutez select @@version quand vous êtes connecté à votre instance SQL Data Warehouse.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

> [!NOTE]
> La création d’un entrepôt SQL Data Warehouse peut entraîner un nouveau service facturable.  Pour en savoir plus, voir [SQL Data Warehouse - Tarification](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Prérequis

Ce guide de démarrage rapide part du principe que vous avez déjà un entrepôt de données SQL ainsi que des autorisations CONTROL DATABASE. Si vous devez en créer un, utilisez la section [Créer et connecter - Portail](create-data-warehouse-portal.md) pour créer un entrepôt de données nommé **mySampleDataWarehouse**.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="create-login-for-theceo"></a>Créer une connexion pour TheCEO

Créez une connexion d’authentification SQL Server dans la base de données `master` à l’aide de [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql) pour « TheCEO ».

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Créer un utilisateur

[Créez l’utilisateur](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest), « TheCEO », dans mySampleDataWarehouse.

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>Créer un classifieur de charge de travail

Créez un [classifieur de charge de travail](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) pour « TheCEO » avec une importance haute.

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO];
CREATE WORKLOAD CLASSIFIER [wgcTheCEO]
WITH (WORKLOAD_GROUP = 'xlargerc'
      ,MEMBERNAME = 'TheCEO'
      ,IMPORTANCE = HIGH);
```

## <a name="view-existing-classifiers"></a>Voir les classifieurs existants

```sql
SELECT * FROM sys.workload_management_workload_classifiers
```

## <a name="clean-up-resources"></a>Supprimer des ressources

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO]
DROP USER [TheCEO]
;
```

Vous êtes facturé pour les unités d’entrepôt de données et les données stockées dans votre entrepôt de données. Ces ressources de calcul et de stockage sont facturées séparément.

- Si vous voulez conserver les données dans le stockage, vous pouvez suspendre le calcul quand vous n’utilisez pas l’entrepôt de données. Quand vous suspendez le calcul, vous êtes facturé uniquement pour le stockage des données. Quand vous êtes prêt à utiliser les données, reprenez le calcul.
- Si vous voulez éviter des frais futurs, vous pouvez supprimer l’entrepôt de données.

Suivez ces étapes pour nettoyer les ressources.

1. Connectez-vous au [portail Azure](https://portal.azure.com) et sélectionnez votre entrepôt de données.

    ![Supprimer des ressources](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Pour suspendre le calcul, sélectionnez le bouton **Suspendre**. Quand l’entrepôt de données est suspendu, un bouton **Démarrer** est visible.  Pour reprendre le calcul, sélectionnez **Démarrer**.

3. Pour supprimer l’entrepôt de données afin de ne pas être facturé pour le calcul ou le stockage, sélectionnez **Supprimer**.

4. Pour supprimer le serveur SQL que vous avez créé, sélectionnez **mynewserver-20180430.database.windows.net** dans l’image précédente, puis **Supprimer**.  N’oubliez pas que la suppression du serveur supprime également toutes les bases de données qui lui sont attribuées.

5. Pour supprimer le groupe de ressources, sélectionnez **myResourceGroup**, puis **Supprimer le groupe de ressources**.

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé un classifieur de charge de travail. Exécutez quelques requêtes avec l’identité TheCEO pour voir comment elles fonctionnent. Consultez [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) pour voir les requêtes et l’importance attribuée.

Pour plus d’informations sur la gestion de charge de travail SQL Data Warehouse, consultez [Importance de la charge de travail SQL Data Warehouse](sql-data-warehouse-workload-importance.md) et [Classification de la charge de travail SQL Data Warehouse](sql-data-warehouse-workload-classification.md).
