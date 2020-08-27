---
title: Meilleures pratiques de développement d’applications – Azure Database pour MySQL
description: Découvrez les meilleures pratiques pour créer une application avec Azure Database pour MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 36f9cfa2369032351f6ed2948fc0c98c1648bcb6
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2020
ms.locfileid: "88259232"
---
# <a name="best-practices-for-building-applications-with-azure-database-for-mysql"></a>Meilleures pratiques pour la création d’applications avec Azure Database pour MySQL 

Voici quelques meilleures pratiques pour vous aider à créer des applications prêtes pour le cloud à l’aide d’Azure Database pour MySQL qui peuvent réduire le temps de développement de votre application. 

## <a name="application-and-database-resource-configuration"></a>Configuration des ressources d’application et de base de données

### <a name="application-and-database-in-the-same-region"></a>Application et base de données dans la même région
Assurez-vous que **toutes vos dépendances se trouvent dans la même région** lors du déploiement de votre application dans Azure. La répartition des instances sur différentes régions ou zones de disponibilité crée une latence réseau, ce qui peut avoir un impact sur les performances globales de votre application. 

### <a name="keep-your-mysql-server-secure"></a>Sécuriser votre serveur MySQL
Votre serveur MySQL doit être configuré pour être [sécurisé](https://docs.microsoft.com/azure/mysql/concepts-security) et non accessible au public. Utilisez l’une des options suivantes pour sécuriser votre serveur : 
- [Règles de pare-feu](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules) ;
- [Réseaux virtuels](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet) ; ou 
- [Liaison privée](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)

Pour des raisons de sécurité, vous devez toujours vous connecter à votre serveur MySQL via **SSL** et configurer votre serveur MySQL et votre application pour utiliser **TLS1.2**. Voir [Configurer le protocole SSL](https://docs.microsoft.com/azure/mysql/concepts-ssl-connection-security). 

### <a name="tune-your-server-parameters"></a>Régler les paramètres de votre serveur
Pour les charges de travail nécessitant beaucoup de lectures, le réglage des paramètres « tmp_table_size » et « max_heap_table_size » peut contribuer à optimiser les performances. Pour calculer les valeurs requises pour tmp_table_size et max_heap_table_size, examinez les valeurs totales de la mémoire par connexion et de la mémoire de base. La somme des paramètres de mémoire par connexion, à l’exclusion de tmp_table_size, combinée à la mémoire de base, représente la mémoire totale du serveur.

Pour calculer la plus grande taille possible de tmp_table_size et max_heap_table_size, utilisez la formule suivante :

```(total memory - (base memory + (sum of per-connection memory * # of connections)) / # of connections```

>[!NOTE]
> Mémoire totale indique la quantité totale de mémoire dont dispose le serveur sur les vCores approvisionnés.  Par exemple, dans un serveur Azure Database pour MySQL de vCores v2 universelles, la mémoire totale sera de 5 Go * 2.  Vous trouverez plus de détails sur la mémoire pour chaque niveau de service dans la documentation relative aux [niveaux tarifaires](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers).
> La mémoire de base indique les variables de mémoire, comme query_cache_size et innodb_buffer_pool_size, que MySQL initialise et alloue au démarrage du serveur.  La mémoire par connexion, comme sort_buffer_size et join_buffer_size, est la mémoire qui est allouée uniquement lorsqu’une requête l’exige.

### <a name="create-a-non-admin-user"></a>Créer un utilisateur non-administrateur 
[Créez des utilisateurs non-administrateurs](https://docs.microsoft.com/azure/mysql/howto-create-users) pour chacune des bases de données. En règle générale, les noms des utilisateurs sont identifiés comme les noms des bases de données.

### <a name="resetting-your-password"></a>Réinitialisation de votre mot de passe
Vous pouvez [réinitialiser votre mot de passe](https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password) pour votre serveur MySQL à l’aide de Portail Azure. 

La réinitialisation du mot de passe de votre serveur pour une base de données de production peut rendre votre application inactive. Il est bon de réinitialiser le mot de passe pour toute charge de travail de production aux heures creuses afin de minimiser l’impact sur les utilisateurs finaux de votre application.

## <a name="performance-and-resiliency"></a>Performance et résilience 
Voici quelques outils et modèles que vous pouvez utiliser pour vous aider à déboguer les problèmes de performance de votre application.

### <a name="enable-slow-query-logs-identify-performance-issues"></a>Activer les journaux de requêtes lentes pour identifier les problèmes de performance
Vous pouvez activer les [journaux de requêtes lentes](https://docs.microsoft.com/azure/mysql/concepts-server-logs) et les [journaux d’audit](https://docs.microsoft.com/azure/mysql/concepts-audit-logs) sur votre serveur. L’analyse de journaux de requêtes lentes peut aider à identifier les goulots d’étranglement en matière de performances, afin de les résoudre. 

Les journaux d’audit sont également disponibles par le biais des journaux de diagnostic Azure dans les journaux Azure Monitor, Event Hubs et le compte de stockage. Consultez [comment résoudre les problèmes de performances des requêtes](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance).

### <a name="use-connection-pooling"></a>Utiliser le regroupement de connexions
La gestion des connexions de base de données peut avoir un impact significatif sur les performances de l’application dans son ensemble. Pour optimiser le niveau de performance, vous devez réduire le nombre de fois où les connexions sont établies et le temps nécessaire à l’établissement des connexions dans les chemins de code clés.  Utilisez le [regroupement de connexions](https://docs.microsoft.com/azure/mysql/concepts-connectivity#access-databases-by-using-connection-pooling-recommended) pour vous connecter à Azure Database pour MySQL afin d’améliorer la résilience et les performances. 

[ProxySQL](https://proxysql.com/), qui est une fonction de regroupement de connexions, peut être utilisée efficacement pour gérer les connexions. L’utilisation d’une fonction de regroupement de connexions peut réduire le nombre de connexions inactives et la réutilisation des connexions existantes permettra d’éviter tout problème. Pour plus d’informations, consultez [Configuration de ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/connecting-efficiently-to-azure-database-for-mysql-with-proxysql/ba-p/1279842). 

### <a name="retry-logic-to-handle-transient-errors"></a>Logique de nouvelle tentative pour traiter les erreurs temporaires
Votre application peut rencontrer des [erreurs temporaires](https://docs.microsoft.com/azure/mysql/concepts-connectivity#handling-transient-errors) où les connexions à la base de données sont interrompues ou perdues par intermittence. Dans ce cas, le serveur est opérationnel après une ou deux nouvelles tentatives en 5 à 10 secondes. 

Un bon modèle à suivre pour les nouvelles tentatives est d’attendre 5 secondes avant la première tentative, puis de suivre chaque nouvelle tentative en augmentant progressivement l’attente jusqu’à 60 secondes. Limitez le nombre maximal de nouvelles tentatives au-delà duquel votre application considère que l’opération a échoué afin que vous puissiez ensuite poursuivre votre enquête. Pour plus d’informations, consultez [comment résoudre les erreurs de connexion](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-common-connection-issues). 

### <a name="enable-read-replication-to-mitigate-failovers"></a>Activer le réplica en lecture pour atténuer les basculements
Vous pouvez utiliser la [réplication de données entrantes](https://docs.microsoft.com/azure/mysql/howto-data-in-replication) pour les scénarios de basculement. Lors de l’utilisation de réplicas de lecture, aucun basculement automatique entre les serveurs maître et réplica n’a lieu. Vous remarquerez un décalage entre le serveur maître et le réplica, car la réplication est asynchrone. Le décalage du réseau dépend d’un grand nombre de facteurs, comme la charge de travail exécutée sur le serveur maître et la latence qui existe entre les centres de données. Dans la plupart des cas, le décalage du réplica va de quelques secondes à quelques minutes.

## <a name="database-deployment"></a>Déploiement de base de données 

### <a name="configure-azure-database-for-mysql-task-in-your-cicd-deployment-pipeline"></a>Configurer une tâche Azure Database pour MySQL dans votre pipeline de déploiement CI/CD
Parfois, vous devez déployer des modifications dans votre base de données. Dans ce cas, vous pouvez créer une intégration continue (CI) et de une livraison continue (CD) via les [pipelines Azure](https://azure.microsoft.com/services/devops/pipelines/) et utiliser une tâche pour [votre serveur MySQL](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-mysql-deployment?view=azure-devops) afin de mettre à jour la base de données en exécutant un script personnalisé sur votre base de données.

### <a name="manual-database-deployment"></a>Déploiement manuel d’une base de données 
Lors du déploiement manuel d’une base de données, voici un bon modèle à suivre pour minimiser le temps d’arrêt ou réduire le risque d’échec du déploiement : 

1. Créez une copie de la base de données de production sur une nouvelle base de données en utilisant [mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) ou [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html). 
2. Mettez à jour la nouvelle base de données avec vos nouvelles modifications de schéma ou les mises à jour nécessaires pour votre base de données. 
3. Placez la base de données de production en lecture seule. Vous ne devez pas avoir d’opérations d’écriture sur la base de données de production tant que le déploiement n’est pas terminé. 
4. Testez votre application avec la base de données que vous venez de mettre à jour à l’étape 1.
5. Déployez les modifications apportées à votre application et assurez-vous que l’application utilise désormais la nouvelle base de données contenant les dernières mises à jour. 
6. Conservez l’ancienne base de données de production afin de pouvoir restaurer les modifications. Vous pouvez alors décider de supprimer l’ancienne base de données de production ou de l’exporter sur le stockage d’Azure si nécessaire. 

>[!NOTE]
>  - Si l’application est semblable à une application d’e-commerce et que vous ne pouvez pas la mettre en lecture seule, déployez les modifications directement sur la base de données de production après avoir effectué une sauvegarde.  Ces changements doivent avoir lieu pendant les heures creuses avec un faible trafic vers l’application afin de minimiser l’impact, car certains utilisateurs peuvent rencontrer un échec de requête. 
>  - Assurez-vous que votre code d’application traite également toutes les requêtes ayant échoué.

### <a name="use-mysql-native-metrics-to-see-if-your-workload-is-exceeding-in-memory-temporary-table-sizes"></a>Utiliser les métriques natives de MySQL pour déterminer si votre charge de travail dépasse la taille des tables temporaires en mémoire
Avec une charge de travail nécessitant beaucoup de lectures, les requêtes qui s’exécutent sur votre serveur MySQL peuvent dépasser la taille des tables temporaires en mémoire. Cela peut amener votre serveur à passer à l’écriture de tables temporaires sur le disque, ce qui perturbe le niveau de performance de votre application. Pour déterminer si votre serveur écrit sur le disque en cas de dépassement de la taille des tables temporaires, examinez les métriques suivantes :

```
show global status like 'created_tmp_disk_tables';
show global status like 'created_tmp_tables';
```
La métrique created_tmp_disk_tables indique le nombre de tables qui ont été créées sur le disque, tandis que la métrique created_tmp_table indique le nombre de tables temporaires qui doivent être formées en mémoire en fonction de votre charge de travail. Pour déterminer si l’exécution d’une requête spécifique utilisera des tables temporaires, exécutez EXPLAIN sur la requête. Les détails de la colonne « supplémentaire » indiquent « Utilisation temporaire » si la requête s’exécute à l’aide de tables temporaires.

Pour calculer le pourcentage de votre charge de travail avec des requêtes débordant sur des disques, utilisez vos valeurs de métriques dans la formule ci-dessous :

```(created_tmp_disk_tables / (created_tmp_disk_tables + created_tmp_tables)) * 100```

Dans l’idéal, ce pourcentage doit être inférieur à 25 %. Si vous constatez que le pourcentage est supérieur ou égal à 25 %, nous vous suggérons de modifier deux paramètres de serveur, tmp_table_size et max_heap_table_si.


## <a name="database-schema-and-queries"></a>Schéma de la base de données et requêtes

Voici quelques trucs et astuces à garder à l’esprit lorsque vous créez votre schéma de la base de données et vos requêtes.

### <a name="always-use-the-right-datatype-for-your--table-columns"></a>Toujours utiliser le type de données approprié pour les colonnes de votre table
L’utilisation des types de données appropriés en fonction du type de données que vous souhaitez stocker peut optimiser le stockage et réduire les erreurs qui peuvent se produire en raison de types de données incorrects.

### <a name="use-indexes"></a>Utiliser des index
Pour éviter les requêtes lentes, vous pouvez utiliser des index. Les index permettent de trouver rapidement des lignes avec des colonnes spécifiques. Consultez [How to use Indexes in MySQL](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html).

### <a name="explain-your-select-queries"></a>EXPLAIN sur vos requêtes SELECT
Utilisez l’instruction [EXPLAIN](https://dev.mysql.com/doc/refman/8.0/en/explain.html) pour obtenir des informations sur ce que MySQL fait pour exécuter votre requête. Cela peut vous aider à détecter les goulots d’étranglement ou les problèmes liés à votre requête. Voir [Guide pratique pour utiliser l’instruction EXPLAIN visant à profiler les performances](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance).


