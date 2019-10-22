---
title: Optimisation des performances avec la mise en cache des jeux de résultats | Microsoft Docs
description: Présentation des fonctionnalités
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.openlocfilehash: f6323501fc0078677c4c0e2cd0e43a15583df29b
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513988"
---
# <a name="performance-tuning-with-result-set-caching"></a>Optimisation des performances avec la mise en cache des jeux de résultats  
Lorsque la mise en cache du jeu de résultats est activée, Azure SQL Data Warehouse met automatiquement en cache les résultats de la requête dans la base de données utilisateur ce qui permet de les utiliser de façon répétée.  Ainsi, les exécutions de requêtes suivantes obtiennent les résultats directement à partir du cache persistant de sorte que le recalcul n’est pas nécessaire.   La mise en cache des jeux de résultats améliore les performances des requêtes et réduit l’utilisation des ressources de calcul.  De plus, les requêtes qui recourent au cache du jeu de résultats n’utilisent pas d’emplacements de concurrence et ne sont donc pas prises en compte pour l’application des limites de concurrence existantes. Pour des raisons de sécurité, les utilisateurs ne peuvent accéder aux résultats mis en cache que s’ils ont les mêmes autorisations d’accès aux données que les utilisateurs qui créent les résultats mis en cache.  

## <a name="key-commands"></a>Commandes clés
[Activer/désactiver la mise en cache du jeu de résultats pour une base de données utilisateur](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)

[Activer/désactiver la mise en cache du jeu de résultats pour une session](https://docs.microsoft.com/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)

[Vérifier la taille du jeu de résultats mis en cache](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest)  

[Nettoyer le cache](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>Éléments non mis en cache  

Une fois la mise en cache du jeu de résultats activée pour une base de données, les résultats sont mis en cache pour toutes les requêtes jusqu’à ce que le cache soit plein, à l’exception des requêtes suivantes :
- Requêtes utilisant des fonctions non déterministes telles que DateTime.Now()
- Requêtes utilisant des fonctions définies par l’utilisateur
- Requêtes qui retournent des données avec une taille de ligne supérieure à 64 Ko

Les requêtes avec de grands jeux de résultats (de plus de 1 million de lignes, par exemple) peuvent être ralenties lors de la première exécution, au moment de la création du cache des résultats.

La sécurité au niveau des lignes n’est pas prise en charge par la mise en cache du jeu de résultats.  

## <a name="when-cached-results-are-used"></a>Quand les résultats mis en cache sont utilisés

Le jeu de résultats mis en cache est réutilisé pour une requête si toutes les conditions suivantes sont réunies :
- L’utilisateur qui exécute la requête a accès à toutes les tables référencées dans la requête.
- Il existe une correspondance exacte entre la nouvelle requête et la requête précédente qui a généré le cache du jeu de résultats.
- Il n’y a aucune modification de données ou de schéma dans les tables à partir desquelles le jeu de résultats mis en cache a été généré.

Exécutez cette commande pour vérifier si une requête a été exécutée avec un accès ou un échec du cache de résultats. En cas de présence d’un cache, result_cache_hit retourne 1.

```sql
SELECT request_id, command, result_cache_hit FROM sys.pdw_exec_requests 
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Gérer les résultats mis en cache 

La taille maximale du cache du jeu de résultats est de 1 To par base de données.  Les résultats mis en cache sont automatiquement invalidés lorsque les données de requête sous-jacentes sont modifiées.  

L’éviction du cache est gérée par Azure SQL Data Warehouse automatiquement après cette planification : 
- Toutes les 48 heures si le jeu de résultats n’a pas été utilisé ou a été invalidé. 
- Lorsque le cache du jeu de résultats approche la taille maximale.

Les utilisateurs peuvent vider manuellement l’intégralité du cache du jeu de résultats à l’aide de l’une des options suivantes : 
- Désactiver la fonctionnalité cache du jeu de résultats pour la base de données 
- Exécutez DBCC DROPRESULTSETCACHE tout en étant connecté à la base de données

La suspension d’une base de données n’a pas pour effet de vider le jeu de résultats mis en cache.  

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, consultez l’article [Vue d’ensemble sur le développement SQL Data Warehouse](sql-data-warehouse-overview-develop.md). 
