---
title: Gérer et surveiller l’importance de la charge de travail dans Azure SQL Data Warehouse | Microsoft Docs
description: Découvrez comment gérer et surveiller l’importance de niveau demande.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: a39d71e0f8b8072cab6a3af9a2f0913973f303ee
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235921"
---
# <a name="manage-and-monitor-workload-importance-in-azure-sql-data-warehouse"></a>Gérer et surveiller l’importance de la charge de travail dans Azure SQL Data Warehouse

Gérer et surveiller l’importance de niveau demande dans Azure SQL Data Warehouse à l’aide des affichages catalogue et vues de gestion dynamique.

## <a name="monitor-importance"></a>Importance de l’analyse

Surveiller l’importance à l’aide de la nouvelle colonne importance dans le [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) vue de gestion dynamique.
Le sous surveillance requête montre heure d’envoi et l’heure de début pour les requêtes. Passez en revue l’heure d’envoi et l’heure, ainsi que d’importance afin de voir comment importance a influencé la planification de début.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Pour une analyse supplémentaire comment les requêtes sont en cours planification, utilisez les affichages catalogue.

## <a name="manage-importance-with-catalog-views"></a>Gérer l’importance des vues de catalogue

La vue de catalogue sys.workload_management_workload_classifiers contient des informations sur les classifieurs dans votre instance Azure SQL Data Warehouse. Pour exclure les classifieurs définie par le système qui mappent aux classes de ressources exécutez le code suivant :

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

L’affichage catalogue, [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest), contient des informations sur les paramètres utilisés dans la création du classifieur.  Le ci-dessous montre la requête qui ExecReportsClassifier a été créé sur le ```membername``` paramètre pour des valeurs avec ExecutiveReports :

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![Résultats de la requête](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Pour simplifier la résolution des problèmes de classification incorrecte, nous vous recommandons de que supprimer mappages de rôle de classe de ressource que vous créez les classifieurs de charge de travail. Le code ci-dessous retourne des appartenances aux rôles de classe ressource existante. Exécutez sp_droprolemember pour chaque ```membername``` retourné à partir de la classe de ressource correspondante.
Voici un exemple de vérification de l’existence avant de supprimer un classifieur de charge de travail :

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur la Classification, consultez [Classification de la charge de travail](sql-data-warehouse-workload-classification.md).
- Pour plus d’informations sur l’Importance, consultez [Importance de la charge de travail](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Accéder à configurer l’Importance de la charge de travail ](sql-data-warehouse-how-to-configure-workload-importance.md)
