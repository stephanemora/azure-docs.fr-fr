---
title: Mettre les ressources à l’échelle
description: Cet article explique comment mettre à l'échelle votre base de données en ajoutant ou en supprimant des ressources allouées.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
ms.date: 06/25/2019
ms.openlocfilehash: c4366b2718271b1e27325e6946c5016e9230cea4
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76835910"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Mettre à l’échelle de façon dynamique les ressources de base de données moyennant un temps d’arrêt minimal

Azure SQL Database vous permet d’ajouter de manière dynamique des ressources à votre base de données avec un [temps d’arrêt](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/) minimal ; toutefois, il existe un délai de basculement durant lequel la connectivité à la base de données est perdue pendant un court laps de temps, qui peut être atténué à l’aide d’une logique de nouvelle tentative.

## <a name="overview"></a>Vue d’ensemble

Lorsque la demande ciblant votre application s’accroît de quelques appareils et clients à plusieurs millions, Azure SQL Database se met à l’échelle immédiatement avec un temps d’arrêt minimal. L’extensibilité est une des caractéristiques les plus importantes de PaaS qui vous permet d’ajouter plus de ressources de façon dynamique à votre service, si besoin. Azure SQL Database vous permet de modifier en toute simplicité vos ressources (alimentation processeur, mémoire, débit E/S et stockage) allouées à vos bases de données.

Vous pouvez limiter les problèmes de performances dus à une utilisation croissante de votre application qui ne peuvent être résolus en utilisant des méthodes de réécriture de requête ou d’indexation. L’ajout de ressources vous permet de réagir rapidement lorsqu’une base de données atteint la limite de ressources actuelles et a besoin de plus de puissance pour gérer les charges entrantes. Azure SQL Database vous permet aussi de diminuer la taille des ressources lorsqu’elles ne sont pas nécessaires afin de réduire les coûts.

Vous n’avez pas à vous inquiéter de l’achat de matériel et du changement de l’infrastructure sous-jacente. La mise à l'échelle de base de données peut être effectuée en toute simplicité via le portail Azure à l’aide d’un curseur.

![Performances de SQL Database](media/sql-database-scalability/scale-performance.svg)

Azure SQL Database propose le [modèle d’achat DTU](sql-database-service-tiers-dtu.md) ou le [modèle d’achat vCore (préversion)](sql-database-service-tiers-vcore.md).

- Le [modèle d’achat DTU](sql-database-service-tiers-dtu.md) offre une combinaison de ressources de calcul, de mémoire et d’E/S réparties sur trois niveaux de service pour prendre en charge les charges de travail de base de données, aussi bien légères qu’importantes : De base, Standard et Premium. Les niveaux de performance de chaque niveau fournissent une combinaison différente de ces ressources, à laquelle vous pouvez ajouter d’autres ressources de stockage.
- Le [modèle d’achat vCore](sql-database-service-tiers-vcore.md) vous permet de choisir le nombre de vCores, la quantité de mémoire et de stockage, ainsi que la vitesse de stockage. Ce modèle d’achat propose trois niveaux de service : Usage général, Critique pour l’entreprise et Hyperscale.

Vous pouvez créer votre première application dans une petite base de données unique pour un coût mensuel modique avec le niveau de service De base, Standard ou Usage général, puis remplacer ce niveau de service manuellement ou par programme à tout moment par le niveau de service Premium ou Critique pour l’entreprise, afin de répondre aux besoins de votre solution. Vous pouvez ajuster les performances sans perturber le fonctionnement de votre application, ni l’expérience de vos clients. L’évolutivité dynamique permet à votre base de données de répondre en toute transparence aux besoins en ressources qui évoluent sans cesse et de payer uniquement les ressources dont vous avez besoin, lorsque vous en avez besoin.

> [!NOTE]
> La scalabilité dynamique est différente de la mise à l’échelle automatique. La mise à l’échelle automatique survient quand un service se met à l’échelle automatiquement en fonction de critères, tandis que l’extensibilité dynamique permet la mise à l’échelle manuelle avec un temps d’arrêt minimal.

Une base de données Azure SQL unique prend en charge la scalabilité dynamique, mais pas la mise à l’échelle automatique. Pour plus expérience plus *automatique*, envisagez d’utiliser des pools élastiques, ce qui permet aux bases de données de partager des ressources dans un pool en fonction de leurs besoins individuels.
Toutefois, il existe des scripts qui peuvent aider à automatiser la scalabilité pour une base de données Azure SQL unique. Pour obtenir un exemple, consultez la rubrique [Utiliser PowerShell pour surveiller et mettre à l’échelle une base de données SQL](scripts/sql-database-monitor-and-scale-database-powershell.md).

Vous pouvez changer les [niveaux de service DTU](sql-database-service-tiers-dtu.md) ou les [caractéristiques vCore](sql-database-vcore-resource-limits-single-databases.md) à tout moment, avec un temps d’arrêt minimal de votre application (généralement inférieur à quatre secondes). Pour de nombreuses entreprises et applications, la possibilité de créer des bases de données et d’augmenter ou ralentir les performances à la demande se révèle suffisante, surtout si les modèles d’utilisation sont relativement prévisibles. Mais si vous avez des modèles d'utilisation imprévisibles, il peut être difficile de gérer les coûts et votre modèle commercial. Pour ce scénario, vous utilisez un pool élastique avec un certain nombre d’eDTU qui sont partagées entre plusieurs bases de données dans le pool.

![Présentation de SQL Database : DTU de base de données unique par couche et niveau](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Ces trois possibilités d’Azure SQL Database offrent des capacités à mettre à l'échelle vos bases de données de façon dynamique :

- Avec une [base de données unique](sql-database-single-database-scale.md), vous pouvez utiliser des modèles [DTU](sql-database-dtu-resource-limits-single-databases.md) ou [vCore](sql-database-vcore-resource-limits-single-databases.md) pour définir le volume maximal de ressources assignées à chaque base de données.
- Une instance [Managed Instance](sql-database-managed-instance.md) utilise le modèle [vCores](sql-database-managed-instance.md#vcore-based-purchasing-model) et vous permet de définir la quantité maximale de cœurs de processeur et de stockage allouée à votre instance. Toutes les bases de données au sein de l’instance partageront les ressources allouées à l’instance.
- Les [pools élastiques](sql-database-elastic-pool-scale.md) vous permettent de définir la limite de ressources maximale par groupe de bases de données dans le pool.

Quelle que soit la version, une action de mise à l’échelle (scale up ou scale down) aurait pour effet de redémarrer le processus du moteur de base de données et de le déplacer si nécessaire vers une autre machine virtuelle. Le déplacement du processus du moteur de base de données vers une nouvelle machine virtuelle est un **processus en ligne**, ce qui vous permet de continuer à utiliser votre service Azure SQL Database existant pendant sa progression. Une fois le moteur de base de données cible entièrement initialisé et prêt à traiter les requêtes, les connexions sont [basculées du moteur de base de données source vers le moteur de base de données cible](sql-database-single-database-scale.md#impact). 


> [!NOTE]
> Une courte interruption de la connexion risque de se produire à la fin du processus de mise à l’échelle. Si vous avez implémenté une [Logique de nouvelles tentatives pour les erreurs temporaires standard](sql-database-connectivity-issues.md#retry-logic-for-transient-errors), vous ne remarquerez pas le basculement.

## <a name="alternative-scale-methods"></a>Autres méthodes de mise à l’échelle

La mise à l'échelle des ressources reste la façon la plus facile et efficace pour améliorer les performances de votre base de données sans changer le code de la base de données ou de l’application. Dans certains cas, même les niveaux de service, les tailles de calcul et les optimisations de performances les plus élevés peuvent ne pas suffire à gérer votre charge de travail correctement et de façon économique. Dans ce cas, vous disposez d’autres options pour mettre à l’échelle votre base de données :

- L’[échelle lecture](sql-database-read-scale-out.md) est une fonctionnalité disponible qui vous offre un réplica en lecture seule de vos données, sur lequel vous pouvez exécutez des requêtes en lecture seule exigeantes telles que les rapports. Le réplica en lecture seule gère votre charge de travail en lecture seule sans affecter l’utilisation des ressources sur votre base de données primaire.
- Le [partitionnement de base de données](sql-database-elastic-scale-introduction.md) est un ensemble de techniques qui vous permet de diviser vos données en plusieurs bases de données pour les mettre à l'échelle indépendamment.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur l’amélioration des performances d’une base de données en modifiant son code, consultez [Trouver et appliquer des recommandations de performances](sql-database-advisor-portal.md).
- Pour en savoir sur l’optimisation de votre base de données par une intelligence intégrée, consultez [Ajustement automatique](sql-database-automatic-tuning.md).
- Pour en savoir plus l’échelle lecture dans le service Azure SQL Database, consultez [Utiliser des réplicas en lecture seule pour équilibrer la charge de requêtes en lecture seule](sql-database-read-scale-out.md).
- Pour en savoir plus sur le partitionnement de base de données, consultez [Scale-out avec Azure SQL Database](sql-database-elastic-scale-introduction.md).
