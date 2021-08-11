---
title: Guide de migration de MySQL local vers Azure Database pour MySQL - Optimisation
description: En plus des journaux d’audit et d’activité, les performances du serveur peuvent également être supervisées avec des métriques Azure.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 05/26/2021
ms.openlocfilehash: 982001fa8150efaa1e5a268e7044f9b50694c767
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111958624"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-optimization"></a>Guide de migration de MySQL local vers Azure Database pour MySQL - Optimisation

### <a name="monitoring-hardware-and-query-performance"></a>Supervision des performances du matériel et des requêtes

En plus des journaux d’audit et d’activité, les performances du serveur peuvent également être supervisées avec des [métriques Azure](../../azure-monitor/essentials/data-platform-metrics.md). Les métriques Azure sont fournies à une fréquence d’une minute et des alertes peuvent être configurées à partir de celles-ci. Pour plus d’informations, consultez [Supervision dans Azure Database pour MySQL](../concepts-monitoring.md) pour des détails sur les types de métriques qui peuvent être supervisées.

Comme mentionné précédemment, les métriques de supervision, comme le \_pourcentage d’utilisation du processeur ou de la mémoire\_, peuvent être importantes pour décider de changer le niveau de la base de données. Des valeurs constamment élevées peuvent indiquer qu’un changement de niveau est nécessaire.

De plus, si le processeur et la mémoire ne semblent pas être le problème, les administrateurs peuvent explorer les options en lien avec les bases de données, comme l’indexation et les modifications des requêtes pour les requêtes avec des performances médiocres.

Pour rechercher les requêtes avec des performances médiocres, exécutez la commande suivante :

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DBFORMYSQL"
| where Category == 'MySqlSlowLogs'
| project TimeGenerated, LogicalServerName\_s, 
event\_class\_s, start\_time\_t , q uery\_time\_d, 
sql\_text\_s | top 5 by query\_time\_d desc
```

### <a name="query-performance-insight"></a>Query Performance Insight

En plus des aspects fondamentaux de la supervision du serveur, Azure fournit des outils permettant de superviser les performances des requêtes des applications. La correction ou l’amélioration des requêtes peut entraîner une augmentation significative du débit des requêtes. Utilisez l’[outil Query Performance Insight](../concepts-query-performance-insight.md) pour analyser les requêtes les plus longues et déterminer s’il est possible de mettre en cache ces éléments s’ils sont déterministes dans une période définie, ou de modifier les requêtes pour augmenter leurs performances.

`slow\_query\_log` peut être défini de façon à montrer les requêtes lentes dans les fichiers journaux de MySQL (la valeur par défaut est OFF). Le paramètre de serveur `long\_query\_time` peut alerter les utilisateurs sur les durées longues des requêtes (la valeur par défaut est de 10 secondes).

### <a name="upgrading-the-tier"></a>Changement de niveau

Le portail Azure peut être utilisé pour effectuer une mise à l’échelle entre `General Purpose` et `Memory Optimized`. Si un niveau `Basic` est choisi, il n’est pas possible de changer de niveau ultérieurement pour `General Purpose` ou `Memory Optimized`. Il est cependant possible d’utiliser d’autres techniques pour effectuer une migration/mise à niveau vers une nouvelle instance Azure Database pour MySQL.

Pour obtenir un exemple de script qui migre du niveau De base vers un autre niveau de serveur, consultez [Passer du niveau De base au niveau Usage général ou Optimisé en mémoire dans Azure Database pour MySQL.](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404)

### <a name="scale-the-server"></a>Mettre à l’échelle le serveur

Au sein du niveau, il est possible de mettre à l’échelle les cœurs et la mémoire dans les limites minimales et maximales autorisées dans ce niveau. Si la supervision montre que les limites maximales du processeur ou de la mémoire sont atteintes en permanence, suivez les étapes requise pour [effectuer un scale-up pour répondre à votre demande.](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404)

### <a name="moving-regions"></a>Déplacement vers d’autres régions

Le déplacement d’une base de données vers une autre région Azure dépend de l’approche et de l’architecture. Selon l’approche, il peut entraîner un temps d’arrêt du système.

Le processus recommandé est le même que celui de l’utilisation de réplicas en lecture pour le basculement de maintenance. Cependant, par rapport à la méthode de maintenance planifiée mentionnée ci-dessus, le basculement est beaucoup plus rapide quand une couche de basculement a été implémentée dans l’application. L’application ne doit être à l’arrêt que quelques instants pendant le processus de basculement vers le réplica en lecture. Pour plus d’informations, reportez-vous à la section Continuité d’activité et reprise d’activité.

### <a name="wwi-scenario"></a>Scénario WWI

Les utilisateurs métier et les utilisateurs des applications de WWI ont exprimé un fort intérêt quant à la possibilité de mettre à l’échelle la base de données à la demande. Ils étaient également intéressés par l’utilisation de Query Performance Insight pour déterminer si les performances des requêtes de longue durée devaient être traitées.

Ils ont opté pour l’utilisation d’un serveur de réplica en lecture pour les scénarios potentiels de basculement ou de lecture seule nécessaires.

L’équipe de migration, en collaboration avec les ingénieurs Azure, configurent des requêtes KQL pour superviser les problèmes potentiels liés aux performances du serveur MySQL. Les requêtes KQL ont été configurées avec des alertes pour envoyer des e-mails à l’équipe Base de données et Conférence.

Ils ont choisi de commencer par superviser les problèmes potentiels et d’implémenter ultérieurement si nécessaire des runbooks Azure Automation pour améliorer l’efficacité opérationnelle.

### <a name="optimization-checklist"></a>Check-list pour l’optimisation

  - Supervisez les requêtes lentes.

  - Consultez régulièrement le tableau de bord Performance Insight.

  - Utilisez la supervision pour décider des mise à niveau et des mises à l’échelle.

  - Envisagez de changer de région si les besoins des utilisateurs ou des applications changent.  


> [!div class="nextstepaction"]  
> [Continuité d’activité et reprise d’activité](./business-continuity-and-disaster-recovery.md)