---
title: 'Démarrage rapide : Créer un classifieur de charge de travail - Portail'
description: Utilisez le portail Azure pour créer un classifieur de charge de travail avec une importance haute.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 05/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 691cdcb525f8e9e3d1fb914372b9f62366f4bfba
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "85213021"
---
# <a name="quickstart-create-a-synapse-sql-pool-workload-classifier-using-the-azure-portal"></a>Démarrage rapide : Créer un classifieur de charge de travail d’un pool SQL Synapse avec le portail Azure

Dans ce guide de démarrage rapide, vous allez créer un [classifieur de charge de travail](sql-data-warehouse-workload-classification.md) pour attribuer des requêtes à un groupe de charge de travail.  Le classifieur attribue les demandes de l’utilisateur SQL `ELTLogin` au groupe de charge de travail `DataLoads`.   Suivez le tutoriel [Démarrage rapide : Configurer l’isolation de la charge de travail](quickstart-configure-workload-isolation-portal.md) pour créer le groupe de charge de travail `DataLoads`.  Ce tutoriel crée un classifieur de charge de travail avec l’option WLM_LABEL pour faciliter la bonne classification des demandes.  Le classifieur attribue également une [importance de charge de travail](sql-data-warehouse-workload-importance.md) `HIGH` à ces demandes.


Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.


## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

> [!NOTE]
> La création d’une instance de pool SQL dans Azure Synapse Analytics peut donner lieu à un nouveau service facturable.  Pour plus d’informations, consultez [Tarification Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Prérequis

Ce guide de démarrage rapide suppose que vous avez déjà une instance de pool SQL dans SQL Synapse et que vous disposez d’autorisations CONTROL DATABASE. Si vous devez en créer un, utilisez la section [Créer et connecter - Portail](create-data-warehouse-portal.md) pour créer un entrepôt de données nommé **mySampleDataWarehouse**.
<br><br>
Un groupe de charge de travail `DataLoads` existe.  Consultez le tutoriel [Démarrage rapide : Configurer l’isolation de la charge de travail](quickstart-configure-workload-isolation-portal.md) pour créer le groupe de charge de travail.
<br><br>
>[!IMPORTANT] 
>Votre pool SQL doit être en ligne pour que vous puissiez configurer la gestion de charge de travail. 


## <a name="create-a-login-for-eltlogin"></a>Créer une connexion pour ELTLogin

Créez une connexion d’authentification SQL Server dans la base de données `master` à l’aide de [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) pour `ELTLogin`.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user-and-grant-permissions"></a>Créer un utilisateur et accorder des autorisations

Une fois la connexion créée, un utilisateur doit être créé dans la base de données.  Utilisez [CREATE USER](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) pour créer l’utilisateur SQL `ELTRole` dans la base de données **mySampleDataWarehouse**.  Étant donné que nous allons tester la classification au cours de ce tutoriel, accordez à `ELTLogin` des autorisations sur **mySampleDataWarehouse**. 

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
GRANT CONTROL ON DATABASE::mySampleDataWarehouse TO ELTLogin 
END
;
```

## <a name="configure-workload-classification"></a>Configurer la classification de la charge de travail
La classification vous permet de router les demandes, en fonction d’un ensemble de règles, vers un groupe de charge de travail.  Dans le tutoriel [Démarrage rapide : Configurer l’isolation de la charge de travail](quickstart-configure-workload-isolation-portal.md), nous avons créé le groupe de charge de travail `DataLoads`.  Vous allez maintenant créer un classifieur de charge de travail pour router les requêtes vers le groupe de charges de travail `DataLoads`.


1.  Cliquez sur **Azure Synapse Analytics (anciennement SQL DW)** dans la page de gauche du portail Azure.
2.  Sélectionnez **mySampleDataWarehouse** dans la page **Azure Synapse Analytics (anciennement SQL DW)** . Le pool SQL s’ouvre.
3.  Cliquez sur **Gestion des charges de travail**.

    ![Cliquer sur le menu](./media/quickstart-create-a-workload-classifier-portal/menu.png)

4.  Cliquez sur **Paramètres & classifieurs** sur le côté droit du groupe de charge de travail `DataLoads`.

    ![Cliquez sur Create.](./media/quickstart-create-a-workload-classifier-portal/settings-classifiers.png)

5. Cliquez sur **Classifieurs**.
6. Cliquez sur **Ajouter un classifieur**.

    ![Cliquez sur Ajouter.](./media/quickstart-create-a-workload-classifier-portal/add-wc.png)

7.  Entrez `ELTLoginDataLoads` pour **Nom**.
8.  Entrez `ELTLogin` pour **Membre**.
9.  Choisissez `High` pour **Importance de la demande**.  (*Facultatif*) Par défaut, le niveau d’importance est normal.
10. Entrez `fact_loads` pour **Étiquette**.
11. Cliquez sur **Add**.
12. Cliquez sur **Enregistrer**.

    ![Cliquer sur Configurer](./media/quickstart-create-a-workload-classifier-portal/config-wc.png)

## <a name="verify-and-test-classification"></a>Vérifier et tester la classification
Consultez la vue de catalogue [sys.workload_management_workload_classifiers](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifiers-transact-sql?view=azure-sqldw-latest) pour vérifier l’existence du classifieur `ELTLoginDataLoads`.

```sql
SELECT * FROM sys.workload_management_workload_classifiers WHERE name = 'ELTLoginDataLoads'
```

Consultez la vue de catalogue [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest) pour vérifier les détails du classifieur.

```sql
SELECT c.[name], c.group_name, c.importance, cd.classifier_type, cd.classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ELTLoginDataLoads'
```

Exécutez les instructions suivantes pour tester la classification.  Assurez-vous que vous êtes connecté en tant qu’``ELTLogin`` et que ``Label`` est utilisé dans la requête.
```sql
CREATE TABLE factstaging (ColA int)
INSERT INTO factstaging VALUES(0)
INSERT INTO factstaging VALUES(1)
INSERT INTO factstaging VALUES(2)
GO

CREATE TABLE testclassifierfact WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT * FROM factstaging
OPTION (LABEL='fact_loads')
```

Vérifiez que l’instruction `CREATE TABLE` a effectué une classification dans le groupe de charge de travail `DataLoads` à l’aide du classifieur de charge de travail `ELTLoginDataLoads`.
```sql 
SELECT TOP 1 request_id, classifier_name, group_name, resource_allocation_percentage, submit_time, [status], [label], command 
FROM sys.dm_pdw_exec_requests 
WHERE [label] = 'fact_loads'
ORDER BY submit_time DESC
```



## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour supprimer le classifieur de charge de travail `ELTLoginDataLoads` créé dans ce tutoriel :

1. Cliquez sur **1 classifieur** sur le côté droit du groupe de charge de travail `DataLoads`.

    ![Cliquer sur Supprimer](./media/quickstart-create-a-workload-classifier-portal/delete-wc.png)

2. Cliquez sur **Classifieurs**.
3. Cliquez sur **`...`** à droite du classifieur de charge de travail `ELTLoginDataLoads`.
4. Cliquez sur **Supprimer**.
5. Cliquez sur **Save**(Enregistrer).

    ![Cliquez sur Enregistrer.](./media/quickstart-create-a-workload-classifier-portal/delete-save-wc.png)

Vous êtes facturé pour les unités d’entrepôt de données et les données stockées dans votre entrepôt de données. Ces ressources de calcul et de stockage sont facturées séparément.

- Si vous voulez conserver les données dans le stockage, vous pouvez suspendre le calcul quand vous n’utilisez pas l’entrepôt de données. Quand vous suspendez le calcul, vous êtes facturé uniquement pour le stockage des données. Quand vous êtes prêt à utiliser les données, reprenez le calcul.
- Si vous voulez éviter des frais futurs, vous pouvez supprimer l’entrepôt de données.

Suivez ces étapes pour nettoyer les ressources.

1. Connectez-vous au [portail Azure](https://portal.azure.com) et sélectionnez votre entrepôt de données.

    ![Nettoyer les ressources](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Pour suspendre le calcul, sélectionnez le bouton **Suspendre**. Quand l’entrepôt de données est suspendu, un bouton **Démarrer** est visible.  Pour reprendre le calcul, sélectionnez **Démarrer**.

3. Pour supprimer l’entrepôt de données afin de ne pas être facturé pour le calcul ou le stockage, sélectionnez **Supprimer**.

4. Pour supprimer le serveur SQL que vous avez créé, sélectionnez **sqlpoolservername.database.windows.net** dans l’image précédente, puis **Supprimer**.  N’oubliez pas que la suppression du serveur supprime également toutes les bases de données qui lui sont attribuées.

5. Pour supprimer le groupe de ressources, sélectionnez **myResourceGroup**, puis **Supprimer le groupe de ressources**.

## <a name="next-steps"></a>Étapes suivantes

Supervisez votre charge de travail à l’aide des métriques de supervision du portail Azure.  Pour plus d’informations, consultez [Gérer et superviser l’importance de la charge de travail](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
