---
title: Gérer et superviser l’importance de la charge de travail
description: Découvrez comment gérer et surveiller l’importance du niveau de la requête dans Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: fdac8ebc56291292559f6718de5f0092c7d8e063
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350470"
---
# <a name="manage-and-monitor-workload-importance-in-azure-synapse-analytics"></a>Gérer et surveiller l’importance de la charge de travail dans Azure Synapse Analytics

Gérez et surveillez l’importance du niveau de la requête SQL Analytics dans Azure Synapse à l’aide des DMV et affichages catalogue.

## <a name="monitor-importance"></a>Surveiller l’importance

Surveillez l’importance à l’aide de la nouvelle colonne d’importance dans la vue de gestion dynamique [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest).
La requête de surveillance ci-dessous montre l’heure d’envoi et l’heure de début des requêtes. Passez en revue l’heure d’envoi et l’heure de début ainsi que l’importance pour voir son effet sur la planification.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Pour en savoir plus sur la planification des requêtes, utilisez les affichages catalogue.

## <a name="manage-importance-with-catalog-views"></a>Gérer l’importance avec les affichages catalogue

L’affichage catalogue sys.workload_management_workload_classifiers contient des informations sur les classifieurs. Pour exclure les classifieurs définis par le système qui mappent aux classes de ressources, exécutez le code suivant :

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

L’affichage catalogue [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest) contient des informations sur les paramètres utilisés lors de la création du classifieur.  La requête ci-dessous montre que ExecReportsClassifier a été créé sur le paramètre ```membername``` pour des valeurs avec ExecutiveReports :

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![Résultats de la requête](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Pour simplifier la résolution des problèmes de classification, nous vous recommandons de supprimer les mappages de rôle de classe de ressources car vous créez des classifieurs de charge de travail. Le code ci-dessous retourne des appartenances existantes aux rôles de classe de ressources. Exécutez sp_droprolemember pour chaque ```membername``` retourné par la classe de ressource correspondante.
Voici un exemple de vérification de l’existence avant de déposer un classifieur de charge de travail :

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur la Classification, consultez [Classification de charge de travail](sql-data-warehouse-workload-classification.md).
- Pour plus d’informations sur l’importance, consultez [Importance de la charge de travail](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Accédez à Configurer l’importance des charges de travail](sql-data-warehouse-how-to-configure-workload-importance.md)
