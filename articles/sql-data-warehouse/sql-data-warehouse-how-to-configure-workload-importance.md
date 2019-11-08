---
title: Configurer l’importance des charges de travail
description: Découvrez comment définir l’importance au niveau de la requête.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 59ba4b936f6098b0d0b3f5e571f107af088206e0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692697"
---
# <a name="configure-workload-importance-in-azure-sql-data-warehouse"></a>Configurer l’importance de la charge de travail dans Azure SQL Data Warehouse

Le fait de définir l’importance dans Azure SQL Data Warehouse permet d’influencer la planification des requêtes. Les requêtes plus importantes seront programmées de manière à s’exécuter avant les requêtes moins importantes. Pour affecter une importance aux requêtes, vous devez créer un classifieur de charges de travail.

## <a name="create-a-workload-classifier-with-importance"></a>Créer un classifieur de charges de travail avec une importance

Dans un scénario d’entrepôt de données, certains utilisateurs ont besoin que leurs requêtes s’exécutent rapidement.  Les utilisateurs peuvent être des responsables d’une société ayant besoin d’exécuter des rapports ou l’utilisateur peut être un analyste exécutant une requête ad hoc. Pour affecter une importance à une requête, vous devez créer un classifieur de charges de travail.  L’exemple ci-dessous utilise la nouvelle syntaxe de [création de classifieur de charges de travail](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) pour créer deux classifieurs.  Le nom de membre peut être un utilisateur ou un groupe unique. Les classifications d’utilisateurs individuels sont prioritaires sur les classifications de rôles. Pour rechercher les utilisateurs d’entrepôt de données existants, exécutez :

```sql
Select name from sys.sysusers
```

Pour créer un classifieur de charges de travail pour un utilisateur avec une importance haute, exécutez :

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  above_normal);  

```

Pour créer un classifieur de charges de travail pour un utilisateur exécutant des requêtes adhoc avec une importance inférieure, exécutez :  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  below_normal);  
```

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur la gestion des charges de travail, consultez [Classification des charges de travail](sql-data-warehouse-workload-classification.md)
- Pour plus d’informations sur l’importance, consultez [Importance de la charge de travail](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Accéder à Gérer et superviser l’importance de la charge de travail](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
