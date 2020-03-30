---
title: Optimisation des performances avec la mise en cache des jeux de résultats
description: Vue d’ensemble de la fonctionnalité de mise en cache du jeu de résultats pour SQL Analytics dans Azure Synapse Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: azure-synapse
ms.openlocfilehash: 0c2190c29054301a8e21a9a27eb078802fbc9612
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350860"
---
# <a name="performance-tuning-with-result-set-caching"></a>Optimisation des performances avec la mise en cache des jeux de résultats  
Quand la mise en cache du jeu de résultats est activée, SQL Analytics met automatiquement en cache les résultats de la requête dans la base de données utilisateur, ce qui permet de les utiliser de façon répétée.  Ainsi, les exécutions de requêtes suivantes obtiennent les résultats directement à partir du cache persistant de sorte que le recalcul n’est pas nécessaire.   La mise en cache des jeux de résultats améliore les performances des requêtes et réduit l’utilisation des ressources de calcul.  De plus, les requêtes qui recourent au cache du jeu de résultats n’utilisent pas d’emplacements de concurrence et ne sont donc pas prises en compte pour l’application des limites de concurrence existantes. Pour des raisons de sécurité, les utilisateurs ne peuvent accéder aux résultats mis en cache que s’ils ont les mêmes autorisations d’accès aux données que les utilisateurs qui créent les résultats mis en cache.  

## <a name="key-commands"></a>Commandes clés
[Activer/désactiver la mise en cache du jeu de résultats pour une base de données utilisateur](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)

[Activer/désactiver la mise en cache du jeu de résultats pour une session](https://docs.microsoft.com/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)

[Vérifier la taille du jeu de résultats mis en cache](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest)  

[Nettoyer le cache](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>Éléments non mis en cache  

Une fois la mise en cache du jeu de résultats activée pour une base de données, les résultats sont mis en cache pour toutes les requêtes jusqu’à ce que le cache soit plein, à l’exception des requêtes suivantes :
- Requêtes utilisant des fonctions non déterministes telles que DateTime.Now()
- Requêtes utilisant des fonctions définies par l’utilisateur
- Requêtes utilisant des tables avec une sécurité au niveau des lignes ou une sécurité au niveau des colonnes activée
- Requêtes qui retournent des données avec une taille de ligne supérieure à 64 Ko

> [!IMPORTANT]
> Les opérations de création d’un cache de jeu de résultats et de récupération des données à partir du cache ont lieu sur le nœud de contrôle d’une instance SQL Analytics.
> Quand la mise en cache des jeux de résultats est activée (ON), l’exécution de requêtes qui retournent un jeu de résultats volumineux (par exemple, > 1 million de lignes) peut entraîner une utilisation intensive du processeur sur le nœud de contrôle et allonger ainsi le temps de réponse de toutes les requêtes sur l’instance.  Ces requêtes sont couramment utilisées lors de l’exploration de données et des opérations ETL. Pour éviter une utilisation trop intensive du nœud de contrôle et les problèmes de performances qui en découlent, les utilisateurs doivent désactiver (OFF) la mise en cache des jeux de résultats sur la base de données avant d’exécuter ces types de requêtes.  

Exécutez cette requête pendant toute la durée des opérations de mise en cache du jeu de résultats pour une requête :

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command 
FROM sys.dm_pdw_request_steps 
WHERE request_id  = <'request_id'>; 
```

Voici un exemple de sortie pour une requête exécutée avec la mise en cache du jeu de résultats désactivée.

![Étapes-requête-avec-RSC-désactivée](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Voici un exemple de sortie pour une requête exécutée avec la mise en cache du jeu de résultats activée.

![Étapes-requête-avec-RSC-activée](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>Quand les résultats mis en cache sont utilisés

Le jeu de résultats mis en cache est réutilisé pour une requête si toutes les conditions suivantes sont réunies :
- L’utilisateur qui exécute la requête a accès à toutes les tables référencées dans la requête.
- Il existe une correspondance exacte entre la nouvelle requête et la requête précédente qui a généré le cache du jeu de résultats.
- Il n’y a aucune modification de données ou de schéma dans les tables à partir desquelles le jeu de résultats mis en cache a été généré.

Exécutez cette commande pour vérifier si une requête a été exécutée avec un accès ou un échec du cache de résultats. En cas de présence d’un cache, result_cache_hit retourne 1.

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests 
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Gérer les résultats mis en cache 

La taille maximale du cache du jeu de résultats est de 1 To par base de données.  Les résultats mis en cache sont automatiquement invalidés lorsque les données de requête sous-jacentes sont modifiées.  

L’éviction du cache est gérée automatiquement par SQL Analytics selon la planification suivante : 
- Toutes les 48 heures si le jeu de résultats n’a pas été utilisé ou a été invalidé. 
- Lorsque le cache du jeu de résultats approche la taille maximale.

Les utilisateurs peuvent vider manuellement l’intégralité du cache du jeu de résultats à l’aide de l’une des options suivantes : 
- Désactiver la fonctionnalité cache du jeu de résultats pour la base de données 
- Exécutez DBCC DROPRESULTSETCACHE tout en étant connecté à la base de données

La suspension d’une base de données n’a pas pour effet de vider le jeu de résultats mis en cache.  

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des conseils supplémentaires, consultez la [vue d’ensemble du développement](sql-data-warehouse-overview-develop.md). 
