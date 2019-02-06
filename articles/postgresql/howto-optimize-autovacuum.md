---
title: Optimiser le nettoyage automatique sur un serveur Azure Database pour PostgreSQL
description: Cet article décrit comment optimiser le nettoyage automatique sur un serveur Azure Database pour PostgreSQL.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 21ac48ff473dcf494f96f87210bdfe09e4d82646
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55103392"
---
# <a name="optimizing-autovacuum-on-azure-database-for-postgresql-server"></a>Optimisation du nettoyage automatique sur un serveur Azure Database pour PostgreSQL 
Cet article décrit comment optimiser le nettoyage automatique sur Azure Database pour PostgreSQL.

## <a name="overview-of-autovacuum"></a>Vue d’ensemble du nettoyage automatique
PostgreSQL utilise MVCC pour bénéficier d’un meilleur accès simultané à la base de données. Chaque mise à jour entraîne une insertion et une suppression, et chaque suppression entraîne le marquage logiciel des lignes pour la suppression. Le marquage logiciel entraîne l’identification des tuples morts qui seront vidés ultérieurement. PostgreSQL réalise tout cela en exécutant un travail de nettoyage.

Vous pouvez déclencher un travail de nettoyage manuellement ou automatiquement. Il existe davantage de tuples morts quand la base de données subit une quantité importante d’opérations de mise à jour ou de suppression, et moins quand elle est inactive.  Il est plus nécessaire d’exécuter fréquemment un nettoyage quand la base de données est soumise à une charge importante, ce qui rend l’exécution **manuelle** des travaux de nettoyage peu pratique.

Vous pouvez configurer le nettoyage automatique et l’optimiser grâce à un réglage. Les valeurs par défaut fournies par PostgreSQL tentent de s’assurer que le produit fonctionne sur tous les types d’appareils, notamment les Raspberry Pi, et les valeurs de configuration idéales dépendent de plusieurs facteurs :
- Quantité totale de ressources disponibles : taille de stockage et référence (SKU)
- Utilisation des ressources
- Caractéristiques des différents objets

## <a name="autovacuum-benefits"></a>Avantages du nettoyage automatique
Si vous n’effectuez pas de nettoyage de temps en temps, les tuples morts qui s’accumulent peuvent avoir les conséquences suivantes :
- Ballonnement des données : tables et bases de données plus volumineuses
- Accroissement de la taille des index non optimaux
- Augmentation des E/S

## <a name="monitoring-bloat-with-autovacuum-queries"></a>Supervision du ballonnement avec des requêtes de nettoyage automatique
L’exemple de requête suivant est conçu pour identifier le nombre de tuples vivants et morts dans une table nommée « XYZ » : 'SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;'

## <a name="autovacuum-configurations"></a>Configurations de nettoyage automatique
Les paramètres de configuration qui contrôlent le nettoyage automatique sont axés sur deux questions clés :
- Quand doit-il commencer ?
- Quelle quantité doit-il nettoyer une fois commencé ?

Vous trouverez ci-dessous certains des paramètres de configuration du nettoyage automatique que vous pouvez mettre à jour en fonction des réponses aux questions ci-dessus, ainsi que quelques conseils :
Paramètre|Description|Valeur par défaut
---|---|---
autovacuum_vacuum_threshold|Spécifie le nombre minimal de tuples mis à jour ou supprimés nécessaires pour déclencher un nettoyage dans une table. La valeur par défaut est de 50 tuples. Ce paramètre peut être défini uniquement dans le fichier postgresql.conf ou sur la ligne de commande du serveur. Il peut être substitué pour une table spécifique en modifiant les paramètres de stockage de table.|50
autovacuum_vacuum_scale_factor|Spécifie une fraction de la taille de la table à ajouter à autovacuum_vacuum_threshold quand vous décidez s’il faut déclencher un nettoyage. La valeur par défaut est 0,2 (20 pour cent de la taille de la table). Ce paramètre peut être défini uniquement dans le fichier postgresql.conf ou sur la ligne de commande du serveur. Il peut être substitué pour une table spécifique en modifiant les paramètres de stockage de table.|5 pour cent
autovacuum_vacuum_cost_limit|Spécifie la valeur limite du coût qui sera utilisée dans les opérations de nettoyage automatique. Si vous spécifiez -1 (la valeur par défaut), la valeur régulière de vacuum_cost_limit est utilisée. La valeur est répartie proportionnellement parmi les workers de nettoyage automatique en cours d’exécution, s’il existe plusieurs workers. La somme des limites pour chaque worker ne dépasse pas la valeur de cette variable. Ce paramètre peut être défini uniquement dans le fichier postgresql.conf ou sur la ligne de commande du serveur. Il peut être substitué pour une table spécifique en modifiant les paramètres de stockage de table.|-1
autovacuum_vacuum_cost_delay|Spécifie la valeur de délai du coût qui sera utilisée dans les opérations de nettoyage automatique. Si vous spécifiez -1, la valeur régulière de vacuum_cost_delay est utilisée. La valeur par défaut est de 20 millisecondes. Ce paramètre peut être défini uniquement dans le fichier postgresql.conf ou sur la ligne de commande du serveur. Il peut être substitué pour une table spécifique en modifiant les paramètres de stockage de table.|20 ms
autovacuum_nap_time|Spécifie le délai minimal entre les exécutions de nettoyage automatique sur une base de données spécifique. À chaque cycle, le démon examine la base de données et émet des commandes de nettoyage et d’analyse en fonction des besoins pour les tables de cette base de données. Le délai est exprimé en secondes, et la valeur par défaut est d’une minute (1 min). Ce paramètre peut être défini uniquement dans le fichier postgresql.conf ou sur la ligne de commande du serveur.|15 s
autovacuum_max_workers|Spécifie le nombre maximal de processus de nettoyage automatique (autres que le lanceur de nettoyage automatique) qui peuvent s’exécuter à tout moment. La valeur par défaut est trois. Ce paramètre peut être défini uniquement au démarrage du serveur.|3
Les paramètres ci-dessus peuvent être substitués pour une table spécifique en modifiant les paramètres de stockage de table.  

## <a name="autovacuum-cost"></a>Coût du nettoyage automatique
Vous trouverez ci-dessous les « coûts » d’exécution d’une opération de nettoyage automatique :
- Un verrou est placé sur les pages de données sur lesquelles le nettoyage s’exécute.
- Des ressources de calcul et de mémoire sont utilisées lors de l’exécution du nettoyage.

Cela implique que vous ne devez pas exécuter le nettoyage trop souvent ou trop rarement ; il doit être adapté à la charge de travail. Nous vous recommandons de tester toutes les modifications des paramètres de nettoyage automatique, à cause des compromis liés à chacune d’elles.

## <a name="autovacuum-start-trigger"></a>Déclencheur de démarrage du nettoyage automatique
Le nettoyage automatique est déclenché quand le nombre de tuples morts dépasse autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples, reltuples étant ici une constante.

Le nettoyage effectué par autovacuum doit suivre la charge de la base de données, sinon vous risquez de manquer de stockage et constater un ralentissement général des requêtes. Amortie au fil du temps, la vitesse de nettoyage des tuples morts doit être égale au taux de création des tuples morts.

Les bases de données contenant de nombreuses mises à jour/suppressions ont plus de tuples morts et ont besoin de davantage d’espace. En général, avec les bases de données soumises à de nombreuses mises à jour/suppressions, il est préférable d’affecter des valeurs faibles à autovacuum_vacuum_scale_factor et à autovacuum_vacuum_threshold afin d’empêcher l’accumulation prolongée de tuples morts. Vous pouvez utiliser des valeurs plus élevées pour ces deux paramètres avec les bases de données plus petites, car la nécessité d’un nettoyage est moins urgente. N’oubliez pas qu’un nettoyage fréquent a un coût en termes de calcul et de mémoire.

Le facteur d’échelle par défaut de 20 pour cent convient bien aux tables présentant un faible pourcentage de tuples morts, mais pas à celle présentant un pourcentage élevé. Par exemple, sur une table de 20 Go, cela se traduit par 4 Go de tuples morts, et sur une table de 1 To, cela se traduit par 200 Go de tuples morts.

Avec PostgreSQL, vous pouvez définir ces paramètres au niveau de l’instance ou de la table. Aujourd’hui, ces paramètres peuvent être définis au niveau de la table uniquement dans Azure Database pour PostgreSQL.

## <a name="estimating-the-cost-of-autovacuum"></a>Estimation du coût du nettoyage automatique
L’exécution du nettoyage automatique est « coûteuse », et il existe des paramètres pour contrôler l’exécution des opérations de nettoyage. Les paramètres suivants aident à estimer le coût d’exécution du nettoyage :
- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

Le processus de nettoyage lit les pages physiques et vérifie la présence de tuples morts. Chaque page dans shared_buffers est considérée comme ayant un coût de 1 (vacuum_cost_page_hit), et toutes les autres pages sont considérées comme ayant un coût de 20 (vacuum_cost_page_dirty) s’il existe des tuples morts, ou de 10 (vacuum_cost_page_miss) s’il n’existe aucun tuple mort. L’opération de nettoyage s’arrête quand le processus dépasse autovacuum_vacuum_cost_limit.  

Une fois la limite atteinte, le processus bascule en veille pendant la durée spécifiée par le paramètre autovacuum_vacuum_cost_delay, avant d’être redémarré. Si la limite n’est pas atteinte, le nettoyage automatique démarre après la valeur spécifiée par le paramètre autovacuum_nap_time.

En résumé, les paramètres autovacuum_vacuum_cost_delay et autovacuum_vacuum_cost_limit contrôlent la quantité de nettoyage de données autorisée par unité de temps. Notez que les valeurs par défaut sont trop faibles pour la plupart des niveaux de tarification. Les valeurs optimales pour ces paramètres dépendent du niveau de tarification, et doivent être configurées en conséquence.

Le paramètre autovacuum_max_workers détermine le nombre maximal de processus de nettoyage automatique pouvant être exécutés simultanément.

Avec PostgreSQL, vous pouvez définir ces paramètres au niveau de l’instance ou de la table. Aujourd’hui, ces paramètres peuvent être définis au niveau de la table uniquement dans Azure Database pour PostgreSQL.

## <a name="optimizing-autovacuum-per-table"></a>Optimisation du nettoyage automatique par table
Tous les paramètres de configuration ci-dessus peuvent être configurés par table, par exemple :
```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

Le nettoyage automatique est un processus synchrone exécuté par table. Plus une table contient un pourcentage élevé de tuples morts, plus le « coût » du nettoyage automatique est élevé.  Fractionner les tables qui ont un taux élevé de mises à jour/suppressions en plusieurs tables vous aidera à paralléliser le nettoyage automatique et à réduire le « coût » d’exécution du nettoyage automatique sur une table. Vous pouvez également augmenter le nombre de workers parallèles de nettoyage automatique afin de garantir une planification libérale des workers.

## <a name="next-steps"></a>Étapes suivantes
Consultez la documentation PostgreSQL suivante pour en savoir plus sur l’utilisation et le réglage du nettoyage automatique :
 - Documentation PostgreSQL : [Chapter 18, Server Configuration](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
 - Documentation PostgreSQL : [Chapter 24, Routine Database Maintenance Tasks](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)
