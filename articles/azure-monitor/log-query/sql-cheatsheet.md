---
title: Aide-mémoire sur les requêtes de journal SQL vers Azure Monitor | Microsoft Docs
description: Aide destinée aux utilisateurs qui connaissent le langage SQL pour écrire des requêtes de journal dans Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: bwren
ms.openlocfilehash: b756b9484273c098dbeb6685430f70626b3af787
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65789231"
---
# <a name="sql-to-azure-monitor-log-query-cheat-sheet"></a>Aide-mémoire sur les requêtes de journal SQL vers Azure Monitor 

Le tableau ci-dessous permet aux utilisateurs qui connaissent le langage SQL d’apprendre le langage de requête Kusto pour écrire des requêtes de journal dans Azure Monitor. Jetez un œil à la commande T-SQL pour résoudre des scénarios courants et leur équivalent dans une requête de journal Azure Monitor.

## <a name="sql-to-azure-monitor"></a>SQL vers Azure Monitor

Description                              |Requête SQL                                                                                          |Requête de journal Azure Monitor
----------------------------------------|---------------------------------------------------------------------------------------------------|----------------------------------------
Sélectionner toutes les données d’une table            |`SELECT * FROM dependencies`                                                                       |<code>dependencies</code>
Sélectionner des colonnes spécifiques d’une table    |`SELECT name, resultCode FROM dependencies`                                                        |<code>dependencies <br>&#124; project name, resultCode</code>
Sélectionner 100 enregistrements dans une table         |`SELECT TOP 100 * FROM dependencies`                                                               |<code>dependencies <br>&#124; take 100</code>
Évaluation de la valeur null                         |`SELECT * FROM dependencies WHERE resultCode IS NOT NULL`                                          |<code>dependencies <br>&#124; where isnotnull(resultCode)</code>
Comparaison de chaînes : égalité             |`SELECT * FROM dependencies WHERE name = "abcde"`                                                  |<code>dependencies <br>&#124; where name == "abcde"</code>
Comparaison de chaînes : sous-chaîne            |`SELECT * FROM dependencies WHERE name like "%bcd%"`                                                   |<code>dependencies <br>&#124; where name contains "bcd"</code>
Comparaison de chaînes : caractère générique             |`SELECT * FROM dependencies WHERE name like "abc%"`                                                |<code>dependencies <br>&#124; where name startswith "abc"</code>
Comparaison de dates : dernier jour             |`SELECT * FROM dependencies WHERE timestamp > getdate()-1`                                         |<code>dependencies <br>&#124; where timestamp > ago(1d)</code>
Comparaison de dates : plage de dates             |`SELECT * FROM dependencies WHERE timestamp BETWEEN '2016-10-01' AND '2016-11-01'`                 |<code>dependencies <br>&#124; where timestamp between (datetime(2016-10-01) .. datetime(2016-10-01))</code>
Comparaison booléenne                      |`SELECT * FROM dependencies WHERE !(success)`                                                      |<code>dependencies <br>&#124; where success == "False"</code>
Trier                                    |`SELECT name, timestamp FROM dependencies ORDER BY timestamp asc`                                  |<code>dependencies <br>&#124; order by timestamp asc</code>
Différencier                                |`SELECT DISTINCT name, type  FROM dependencies`                                                    |<code>dependencies <br>&#124; summarize by name, type</code>
Regroupement, agrégation                   |`SELECT name, AVG(duration) FROM dependencies GROUP BY name`                                       |<code>dependencies <br>&#124; summarize avg(duration) by name</code>
Alias de colonne, étendre                  |`SELECT operation_Name as Name, AVG(duration) as AvgD FROM dependencies GROUP BY name`             |<code>dependencies <br>&#124; summarize AvgD=avg(duration) by operation_Name <br>&#124; project Name=operation_Name, AvgD</code>
Enregistrements TOP n par mesure                |`SELECT TOP 100 name, COUNT(*) as Count FROM dependencies GROUP BY name ORDER BY Count asc`        |<code>dependencies <br>&#124; summarize Count=count() by name <br>&#124; top 100 by Count asc</code>
Union                                   |`SELECT * FROM dependencies UNION SELECT * FROM exceptions`                                        |<code>union dependencies, exceptions</code>
Union : avec conditions                  |`SELECT * FROM dependencies WHERE value > 4 UNION SELECT * FROM exceptions WHERE value < 5`                |<code>dependencies <br>&#124; where value > 4 <br>&#124; union (exceptions <br>&#124; where value < 5)</code>
Join                                    |`SELECT * FROM dependencies JOIN exceptions ON dependencies.operation_Id = exceptions.operation_Id`|<code>dependencies <br>&#124; join (exceptions) on operation_Id == operation_Id</code>


## <a name="next-steps"></a>Étapes suivantes

- Parcourez les leçons [écriture de requêtes de journal dans Azure Monitor](get-started-queries.md).
