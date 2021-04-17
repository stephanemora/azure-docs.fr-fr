---
title: Bloquer les commandes T-SQL pour créer ou modifier des ressources Azure SQL
titleSuffix: Block T-SQL commands to create or modify Azure SQL resources
description: Cet article détaille une fonctionnalité permettant aux administrateurs Azure de bloquer les commandes T-SQL pour créer ou modifier des ressources Azure SQL.
ms.service: sql-database
ms.subservice: security
ms.custom: ''
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.topic: article
ms.date: 03/31/2021
ms.reviewer: ''
ROBOTS: NOINDEX
ms.openlocfilehash: 4ecaa8a3ee1d11ea13563ae5c74835b8d62fd960
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106555842"
---
# <a name="what-is-block-t-sql-crud-feature"></a>Qu’est-ce que la fonctionnalité de blocage T-SQL CRUD ?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb-sqlmi.md)]


Cette fonctionnalité permet aux administrateurs Azure de bloquer la création ou la modification de ressources Azure SQL via T-SQL. Elle est appliquée au niveau de l’abonnement pour empêcher les commandes T-SQL d’affecter les ressources SQL dans une base de données Azure SQL ou une instance gérée.

## <a name="overview"></a>Vue d’ensemble

Pour bloquer la création ou la modification de ressources à l’aide de T-SQL et appliquer la gestion des ressources via un modèle Azure Resource Manager (modèle ARM) pour un abonnement donné, vous pouvez utiliser les fonctionnalités en préversion au niveau de l’abonnement dans le portail Azure. Cela s’avère particulièrement utile lorsque vous utilisez des [stratégies Azure](/azure/governance/policy/overview) pour appliquer des normes organisationnelles par le biais des modèles ARM. Comme T-SQL n’adhère pas aux stratégies Azure, il est possible d’appliquer un blocage sur les opérations de création ou de modification T-SQL. La syntaxe bloquée comprend les instructions CRUD (création, mise à jour, suppression) pour les bases de données dans Azure SQL, en particulier les instructions `CREATE DATABASE` `ALTER DATABASE` et `DROP DATABASE`. 

Les opérations CRUD T-SQL peuvent être bloquées via le portail Azure, [PowerShell](/powershell/module/az.resources/register-azproviderfeature)ou [Azure CLI](/cli/azure/feature#az_feature_register).

## <a name="permissions"></a>Autorisations

Pour pouvoir inscrire ou supprimer cette fonctionnalité, l’utilisateur Azure doit être membre du rôle propriétaire ou contributeur de l’abonnement.

## <a name="examples"></a>Exemples

La section suivante décrit comment vous pouvez inscrire ou désinscrire une fonctionnalité en préversion avec le fournisseur de ressources Microsoft.Sql dans le portail Azure : 

### <a name="register-block-t-sql-crud"></a>Inscrire le blocage T-SQL CRUD

1. Accédez à votre abonnement dans le portail Azure.
2. Sélectionnez l’onglet **Fonctionnalités en préversion**. 
3. Sélectionnez **Bloquer T-SQL CRUD**.
4. Une fois que vous avez sélectionné l’option **Bloquer T-SQL CRUD**, une nouvelle fenêtre s’ouvre, sélectionnez **Inscrire** pour inscrire ce blocage auprès du fournisseur de ressources Microsoft.Sql.

![Sélectionnez « Bloquer T-SQL CRUD » dans la liste des fonctionnalités en préversion](./media/block-tsql-crud/block-tsql-crud.png)

![Avec l’option « Bloquer T-SQL CRUD » activée, sélectionnez Inscrire](./media/block-tsql-crud/block-tsql-crud-register.png)

  
### <a name="re-register-microsoftsql-resource-provider"></a>Réinscrire le fournisseur de ressources Microsoft.Sql 
Après avoir inscrit le blocage T-SQL CRUD dans le fournisseur de ressources Microsoft.Sql, vous devez réinscrire le fournisseur de ressources Microsoft.Sql pour que les modifications soient prises en compte. Pour réinscrire le fournisseur de ressources Microsoft.Sql :

1. Accédez à votre abonnement dans le portail Azure.
2. Sélectionnez l’onglet **Fournisseurs de ressources**.
3. Recherchez et sélectionnez le fournisseur de ressources **Microsoft.Sql**.
4. Sélectionnez **Ré-inscrire**. 

> [!NOTE]
> L’étape de réinscription est obligatoire pour que le blocage T-SQL soit appliqué à votre abonnement. 

![Réinscrire le fournisseur de ressources Microsoft.Sql](./media/block-tsql-crud/block-tsql-crud-re-register.png)

### <a name="removing-block-t-sql-crud"></a>Suppression du blocage T-SQL CRUD
Pour supprimer le blocage des opérations de création ou de modification T-SQL dans votre abonnement, annulez d’abord l’inscription du blocage T-SQL précédemment enregistré. Ensuite, réinscrivez le fournisseur de ressources Microsoft.Sql comme indiqué ci-dessus pour que la suppression du blocage T-SQL prenne effet. 


## <a name="next-steps"></a>Étapes suivantes

- [Une vue d’ensemble des fonctionnalités de sécurité d’Azure SQL Database](security-overview.md)
- [Meilleures pratiques en matière de sécurité pour Azure SQL Database](security-best-practice.md)
