---
title: Configurer l’importance de la charge de travail dans Azure SQL Data Warehouse | Microsoft Docs
description: Découvrez comment définir l’importance de niveau demande
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 04abf69faf5388122c08b5ad2998438b5d83334b
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003385"
---
# <a name="configure-workload-importance-in-azure-sql-data-warehouse"></a>Configurer l’importance de la charge de travail dans Azure SQL Data Warehouse

Définition importance dans l’entrepôt de données SQL vous permet d’influencer la planification des requêtes. Requêtes avec une importance supérieure seront programmées pour s’exécuter avant les requêtes avec une importance inférieure. Pour affecter l’importance aux requêtes, vous devez créer un classifieur de charge de travail.

## <a name="create-a-workload-classifier-with-importance"></a>Créer un classifieur de charge de travail avec une Importance

Dans un scénario d’entrepôt de données vous avez souvent les utilisateurs qui ont besoin de leurs requêtes s’exécutent rapidement.  L’utilisateur peut être responsables de la société ayant besoin d’exécuter des rapports ou l’utilisateur peut être un analyste d’exécution d’une requête ad hoc. Vous créez un classifieur de charge de travail pour affecter l’importance à une requête.  Les exemples ci-dessous utilisent la nouvelle [créer classifieur de charge de travail](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) syntaxe pour créer deux classifieurs.  Nom de membre peut être un seul utilisateur ou un groupe. Classifications des utilisateurs individuels sont prioritaires sur les classifications de rôle. Pour rechercher les utilisateurs existants de l’entrepôt de données, exécutez :

```sql
Select name from sys.sysusers
```

Pour créer un classifieur de charge de travail, pour un utilisateur avec une importance supérieure, exécutez :

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  above_normal);  

```

Pour créer un classifieur de charge de travail pour un utilisateur qui exécute des requêtes ad hoc avec une importance inférieure exécuter :  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  below_normal);  
```

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur la gestion de la charge de travail, consultez [Classification de la charge de travail](sql-data-warehouse-workload-classification.md)
- Pour plus d’informations sur l’Importance, consultez [Importance de la charge de travail](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Accédez à gérer et surveiller l’Importance de la charge de travail ](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
