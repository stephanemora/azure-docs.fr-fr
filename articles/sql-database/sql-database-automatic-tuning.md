---
title: Présentation du réglage automatique
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
ms.date: 03/06/2019
ms.openlocfilehash: 34f102b43de669b5ea03324db47ac4dfcb554133
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190763"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Réglage automatique dans Azure SQL Database

Le réglage automatique dans Azure SQL Database fournit une optimisation des performances et une stabilisation des charges de travail grâce à un réglage continu des performances basé sur l’intelligence artificielle et le machine learning.

Le réglage automatique est un service de performances intelligent entièrement géré qui utilise l’intelligence intégrée pour surveiller en continu les requêtes exécutées sur une base de données, dont il améliore automatiquement les performances. Pour cela, il adapte de manière dynamique la base de données à l’évolution des charges de travail et applique des recommandations de réglage. Le réglage automatique apprend horizontalement de toutes les bases de données sur Azure grâce à l’intelligence artificielle et améliore ses actions de réglage de manière dynamique. Plus une base de données Azure SQL s’exécute avec le réglage automatique activée, meilleures sont ses performances.

Le réglage automatique Azure SQL Database est peut-être l’une des fonctionnalités les plus importantes que vous pouvez activer pour fournir des charges de travail de base de données stables avec des performances maximales.

## <a name="what-can-automatic-tuning-do-for-you"></a>En quoi peut vous aider le réglage automatique ?

- Réglage automatisé des performances des bases de données Azure SQL
- Vérification automatisée des gains de performances
- Restauration automatisée et correction automatique
- Historique des réglages
- Scripts T-SQL d’action de réglage pour les déploiements manuels
- Surveillance proactive des performances des charges de travail
- Capacité de montée en charge sur des centaines de milliers de bases de données
- Impact positif sur les ressources de DevOps et le coût total de possession

## <a name="safe-reliable-and-proven"></a>Sécurité, fiabilité et efficacité avérée

Les opérations de réglage appliquées aux bases de données Azure SQL n’affectent en rien les performances, même celles de vos charges de travail les plus lourdes. Le système a été conçu avec soin afin de ne pas interférer avec les charges de travail utilisateur. Les recommandations de réglage automatique sont appliquées uniquement pendant les périodes d’utilisation normale. Le système peut également désactiver temporairement les opérations de réglage automatique afin de protéger les performances des charges de travail. Dans ce cas, le message « Désactivé par le système » s’affiche dans le portail Azure. Le réglage automatique concerne les charges de travail avec la priorité de ressources la plus élevée.

Les mécanismes de réglage automatique sont matures et ont été éprouvés sur des millions de bases de données exécutées sur Azure. Les opérations de réglage automatique appliquées sont vérifiées automatiquement afin de s’assurer qu’il y a une amélioration positive des performances des charges de travail. Les recommandations de performances ayant un impact négatif sont détectées de manière dynamique et inversées rapidement. Avec l’historique des réglages enregistré, il existe une trace claire montrant les améliorations de réglage apportées à chaque base de données Azure SQL. 

![Fonctionnement du réglage automatique](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Le réglage automatique dans Azure SQL Database partage sa logique de base avec le moteur de réglage automatique de SQL Server. Pour obtenir des informations techniques supplémentaires sur le mécanisme d’intelligence intégré, consultez [Réglage automatique dans SQL Server](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

## <a name="use-automatic-tuning"></a>Utiliser le réglage automatique

Le réglage automatique doit être activé sur votre abonnement. Pour activer le réglage automatique à l’aide du portail Azure, consultez [Activer le réglage automatique](sql-database-automatic-tuning-enable.md).

Le réglage automatique peut fonctionner de façon autonome par l’application automatique de recommandations de réglage, notamment une vérification automatisée des gains de performances. 

Pour plus de contrôle, vous pouvez désactiver l’application automatique des recommandations de réglage et appliquer manuellement les recommandations par le biais du portail Azure. Vous pouvez également utiliser la solution uniquement pour afficher les recommandations de réglage automatique et les appliquer manuellement par le biais de scripts et d’outils de votre choix. 

Pour obtenir une vue d’ensemble du fonctionnement du réglage automatique et des scénarios d’utilisation classiques, regardez la vidéo incorporée :


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>Options de réglage automatique

Les options de réglage automatique disponibles dans Azure SQL Database sont les suivantes :

| Option de réglage automatique | Prise en charge d’une base de données unique et d’une base de données mise en pool | Prise en charge de la base de données d’instance |
| :----------------------------- | ----- | ----- |
| **CREATE INDEX** : identifie les index qui peuvent améliorer les performances de votre charge de travail, crée des index et vérifie automatiquement que les performances des requêtes sont améliorées. | Oui | Non | 
| **DROP INDEX** : identifie quotidiennement les index redondants et en double, excepté pour les index uniques, ainsi que les index qui n’ont pas été utilisés depuis longtemps (>90 jours). Notez que l’option n’est pas compatible avec les applications utilisant la commutation de partition et les indicateurs d’index. La suppression des index inutilisés n’est pas prise en charge pour les niveaux de service Premium et Critique pour l’entreprise. | Oui | Non |
| **FORCE LAST GOOD PLAN** (correction de plan automatique) : identifie les requêtes SQL utilisant le plan d’exécution qui est plus lent que le plan correct précédent, ainsi que les requêtes utilisant le dernier plan correct connu au lieu du plan de régression. | Oui | Oui |

Le réglage automatique identifie les recommandations**CREATE INDEX**, **DROP INDEX** et **FORCE LAST GOOD PLAN** qui peuvent optimiser les performances de votre base de données, les affiche dans le [portail Azure](sql-database-advisor-portal.md), puis les expose à l’aide de [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) et de l’[API REST](https://docs.microsoft.com/rest/api/sql/serverautomatictuning). Pour en savoir plus sur FORCE LAST GOOD PLAN et la configuration des options de réglage automatique par le biais de T-SQL, consultez [Le réglage automatique présente la correction de plan automatique](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/).

Vous pouvez soit appliquer manuellement les recommandations de réglage à l’aide du portail, soit laisser le réglage automatique les appliquer en toute autonomie pour vous. L’avantage de laisser le système appliquer de manière autonome les recommandations de réglage pour vous est qu’il valide automatiquement qu’il existe un accroissement des performances de la charge de travail. En revanche, si une régression est détectée, il annule automatiquement les recommandations de réglage. Notez qu’en cas de requêtes affectées par des recommandations de réglage qui ne sont pas exécutées fréquemment, la phase de validation peut, par nature, prendre jusqu’à 72 heures.

Si vous appliquez les recommandations de réglage via T-SQL, les mécanismes de validation automatique des performances et d’annulation ne sont pas disponibles. Les recommandations appliquées de cette manière restent actives et s’affichent dans la liste des recommandations de paramétrage pour 24-48 heures. avant que le système ne les retire automatiquement. Si vous souhaitez supprimer une recommandation avant cela, vous pouvez l’abandonner dans le portail Azure.

Vous pouvez activer ou désactiver les options de réglage automatique par base de données ou vous pouvez les configurer sur des serveurs SQL Database et les appliquer sur chaque base de données qui hérite des paramètres du serveur. Les serveurs SQL Database peuvent hériter des valeurs Azure par défaut pour les paramètres de réglage automatique. Actuellement, les valeurs Azure par défaut sont FORCE_LAST_GOOD_PLAN activé, CREATE_INDEX activé et DROP_INDEX désactivé.

> [!IMPORTANT]
> Depuis mars 2020, les modifications apportées aux paramètres par défaut Azure pour le réglage automatique prennent effet comme suit :
> - Les nouveaux paramètres par défaut Azure sont FORCE_LAST_GOOD_PLAN = enabled, CREATE_INDEX = disabled, and DROP_INDEX = disabled.
> - Les serveurs existants sans préférences de réglage automatique configurées sont automatiquement configurés avec les nouveaux paramètres par défaut Azure. Cela s’applique à tous les clients qui ont actuellement un réglage automatique dans un état non défini.
> - Les nouveaux serveurs créés sont automatiquement configurés avec les nouveaux paramètres par défaut Azure (contrairement à auparavant, où la configuration de réglage automatique était dans un état non défini lors de la création d’un serveur).
>

Une méthode recommandée consiste à configurer les options de réglage automatique sur un serveur et à hériter des paramètres des bases de données appartenant au serveur parent. Cette méthode simplifie la gestion des options de réglage automatique pour un grand nombre de bases de données.

## <a name="next-steps"></a>Étapes suivantes

- Pour activer le réglage automatique dans Azure SQL Database afin de gérer votre charge de travail, consultez [Activer le réglage automatique](sql-database-automatic-tuning-enable.md).
- Pour consulter et appliquer manuellement des recommandations de réglage d’automatique, consultez [Rechercher et appliquer des recommandations en matière de performances](sql-database-advisor-portal.md).
- Pour découvrir comment utiliser T-SQL pour appliquer et afficher les recommandations de réglage automatique, consultez [Gérer le réglage automatique par le biais de T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/).
- Pour en savoir plus sur la création de notifications par e-mail pour les suggestions de réglage automatique, voir [Notifications par e-mail pour le réglage automatique](sql-database-automatic-tuning-email-notifications.md).
- Pour en savoir plus sur l’intelligence intégrée utilisée dans le réglage automatique, consultez [L’intelligence artificielle règle les bases de données Azure SQL](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Pour en savoir plus sur le fonctionnement du réglage automatique dans Azure SQL Database et SQL Server 2017, consultez [Réglage automatique dans SQL Server](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).
