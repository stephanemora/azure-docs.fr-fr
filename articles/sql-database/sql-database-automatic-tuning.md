---
title: Azure SQL Database - Réglage automatique | Microsoft Docs
description: Azure SQL Database analyse la requête SQL et s’adapte automatiquement aux charges de travail utilisateur.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: b13becf8530f478a5e58b46a1b422593051c95cf
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478166"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Réglage automatique dans Azure SQL Database

Le réglage automatique dans Azure SQL Database fournit une optimisation des performances et une stabilisation des charges de travail grâce à un réglage continu des performances basé sur l’intelligence artificielle et l’apprentissage automatique.

Le réglage automatique est un service de performances intelligent entièrement géré qui utilise l’intelligence intégrée pour surveiller en continu les requêtes exécutées sur une base de données, dont il améliore automatiquement les performances. Pour cela, il adapte de manière dynamique la base de données à l’évolution des charges de travail et applique des recommandations de réglage. Le réglage automatique apprend horizontalement de toutes les bases de données sur Azure grâce à l’intelligence artificielle et améliore ses actions de réglage de manière dynamique. Plus une base de données SQL Azure s’exécute avec le réglage automatique activée, meilleures sont ses performances.

Le réglage automatique dans SQL Azure Database est peut-être l’une des fonctionnalités les plus importantes que vous pouvez activer pour fournir des charges de travail de base de données stables avec des performances maximales.

## <a name="what-can-automatic-tuning-do-for-you"></a>En quoi peut vous aider le réglage automatique ?

- Réglage automatisé des performances des bases de données SQL Azure
- Vérification automatisée des gains de performances
- Restauration automatisée et correction automatique
- Historique des réglages
- Scripts T-SQL d’action de réglage pour les déploiements manuels
- Surveillance proactive des performances des charges de travail
- Capacité de montée en charge sur des centaines de milliers de bases de données
- Impact positif sur les ressources de DevOps et le coût total de possession

## <a name="safe-reliable-and-proven"></a>Sécurité, fiabilité et efficacité avérée

Les opérations de réglage appliquées aux bases de données SQL Azure n’affectent en rien les performances, même celles de vos charges de travail les plus lourdes. Le système a été conçu avec soin afin de ne pas interférer avec les charges de travail utilisateur. Les recommandations de réglage automatique sont appliquées uniquement pendant les périodes d’utilisation normale. Le système peut également désactiver temporairement les opérations de réglage automatique afin de protéger les performances des charges de travail. Dans ce cas, le message « Désactivé par le système » s’affiche dans le portail Azure. Le réglage automatique concerne les charges de travail avec la priorité de ressources la plus élevée.

Les mécanismes de réglage automatique sont matures et ont été éprouvés sur des millions de bases de données exécutées sur Azure. Les opérations de réglage automatique appliquées sont vérifiées automatiquement afin de s’assurer qu’il y a une amélioration positive des performances des charges de travail. Les recommandations de performances ayant un impact négatif sont détectées de manière dynamique et inversées rapidement. Avec l’historique des réglages enregistré, il existe une trace claire montrant les améliorations de réglage apportées à chaque base de données Azure SQL. 

![Fonctionnement du réglage automatique](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Le réglage automatique dans Azure SQL Database partage sa logique de base avec le moteur de réglage automatique de SQL Server. Pour obtenir des informations techniques supplémentaires sur le mécanisme d’intelligence intégré, consultez [Réglage automatique dans SQL Server](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

## <a name="use-automatic-tuning"></a>Utiliser le réglage automatique

Le réglage automatique doit être activé manuellement sur votre abonnement. Pour activer le réglage automatique à l’aide du portail Azure, consultez [Activer le réglage automatique](sql-database-automatic-tuning-enable.md).

Le réglage automatique peut fonctionner de façon autonome par l’application automatique de recommandations de réglage, notamment une vérification automatisée des gains de performances. 

Pour plus de contrôle, vous pouvez désactiver l’application automatique des recommandations de réglage et appliquer manuellement les recommandations par le biais du portail Azure. Vous pouvez également utiliser la solution uniquement pour afficher les recommandations de réglage automatique et les appliquer manuellement par le biais de scripts et d’outils de votre choix. 

Pour obtenir une vue d’ensemble du fonctionnement du réglage automatique et des scénarios d’utilisation classiques, regardez la vidéo incorporée :


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>Options de réglage automatique

Les options de réglage automatique disponibles dans Azure SQL Database sont les suivantes :

 1. **CREATE INDEX** identifie les index qui peuvent améliorer les performances de votre charge de travail, crée des index et vérifie automatiquement que les performances des requêtes sont améliorées.
 2. **DROP INDEX** : identifie quotidiennement les index redondants et en double, excepté pour les index uniques, ainsi que les index qui n’ont pas été utilisés depuis longtemps (>90 jours). Notez que l’option à ce stade n’est pas compatible avec les applications utilisant la commutation de partition et les conseils d’index.
 3. **FORCE LAST GOOD PLAN** identifie les requêtes SQL utilisant le plan d’exécution qui est plus lent que le plan correct précédent, ainsi que les requêtes utilisant le dernier plan correct connu au lieu du plan de régression.

Le réglage automatique identifie les recommandations**CREATE INDEX**, **DROP INDEX** et **FORCE LAST GOOD PLAN** qui peuvent optimiser les performances de votre base de données, les affiche dans le [portail Azure](sql-database-advisor-portal.md), puis les expose à l’aide de [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) et de l’[API REST](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).

Vous pouvez soit appliquer manuellement les recommandations de réglage à l’aide du portail, soit laisser le réglage automatique les appliquer en toute autonomie pour vous. L’avantage de laisser le système appliquer de manière autonome les recommandations de réglage pour vous est qu’il valide automatiquement qu’il existe un accroissement des performances de la charge de travail. En revanche, si une régression est détectée, il annule automatiquement les recommandations de réglage. Notez qu’en cas de requêtes affectées par des recommandations de réglage qui ne sont pas exécutées fréquemment, la phase de validation peut, par nature, prendre jusqu’à 72 heures. Si vous appliquez manuellement les recommandations de réglage, les mécanismes de validation automatique des performances et d’annulation ne sont pas disponibles.

Vous pouvez activer ou désactiver les options de réglage automatique par base de données ou vous pouvez les configurer sur des serveurs SQL Database et les appliquer sur chaque base de données qui hérite des paramètres du serveur. Les serveurs SQL Database peuvent hériter des valeurs Azure par défaut pour les paramètres de réglage automatique. Actuellement, les valeurs Azure par défaut sont FORCE_LAST_GOOD_PLAN activé, CREATE_INDEX activé et DROP_INDEX désactivé.

Une méthode recommandée consiste à configurer les options de réglage automatique sur un serveur et à hériter des paramètres des bases de données appartenant au serveur parent. Cette méthode simplifie la gestion des options de réglage automatique pour un grand nombre de bases de données.

## <a name="next-steps"></a>Étapes suivantes

- Pour activer le réglage automatique dans Azure SQL Database afin de gérer votre charge de travail, consultez [Activer le réglage automatique](sql-database-automatic-tuning-enable.md).
- Pour consulter et appliquer manuellement des recommandations de réglage d’automatique, consultez [Rechercher et appliquer des recommandations en matière de performances](sql-database-advisor-portal.md).
- Pour découvrir comment utiliser T-SQL pour appliquer et afficher les recommandations de réglage automatique, consultez [Gérer le réglage automatique par le biais de T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/).
- Pour en savoir plus sur la création de notifications par e-mail pour les suggestions de réglage automatique, voir [Notifications par e-mail pour le réglage automatique](sql-database-automatic-tuning-email-notifications.md).
- Pour en savoir plus sur l’intelligence intégrée utilisée dans le réglage automatique, consultez [Artificial Intelligence tunes Azure SQL Databases](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Pour en savoir plus sur le fonctionnement du réglage automatique dans Azure SQL Database et SQL Server 2017, consultez [Réglage automatique dans SQL Server](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).
