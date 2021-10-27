---
title: Mettre les ressources à l’échelle
description: Cet article explique comment mettre à l'échelle votre base de données dans Azure SQL Database et SQL Managed Instance en ajoutant ou en supprimant des ressources allouées.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma, urmilano, wiassaf
ms.date: 06/25/2019
ms.openlocfilehash: d6dd794a9eb0a7af1ed2e91a04c27d5321e14ca3
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130179333"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Mettre à l’échelle de façon dynamique les ressources de base de données moyennant un temps d’arrêt minimal
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database et SQL Managed Instance vous permet d’ajouter de manière dynamique plus de ressources à votre base de données avec un [temps d’arrêt](https://azure.microsoft.com/support/legal/sla/azure-sql-database) minimal ; toutefois, il existe un délai de basculement durant lequel la connectivité à la base de données est perdue pendant un court laps de temps, qui peut être atténué à l’aide d’une logique de nouvelle tentative.

## <a name="overview"></a>Vue d’ensemble

Lorsque la demande ciblant votre application s’accroît de quelques appareils et clients à plusieurs millions, Azure SQL Database et SQL Managed Instance se mettent à l’échelle immédiatement avec un temps d’arrêt minimal. La scalabilité est une des caractéristiques les plus importantes de PaaS (plateforme en tant que service) qui vous permet d’ajouter plus de ressources de façon dynamique à votre service, si besoin. Azure SQL Database vous permet de modifier en toute simplicité vos ressources (alimentation processeur, mémoire, débit E/S et stockage) allouées à vos bases de données.

Vous pouvez limiter les problèmes de performances dus à une utilisation croissante de votre application qui ne peuvent être résolus en utilisant des méthodes de réécriture de requête ou d’indexation. L’ajout de ressources vous permet de réagir rapidement lorsqu’une base de données atteint la limite de ressources actuelles et a besoin de plus de puissance pour gérer les charges entrantes. Azure SQL Database vous permet aussi de diminuer la taille des ressources lorsqu’elles ne sont pas nécessaires afin de réduire les coûts.

Vous n’avez pas à vous inquiéter de l’achat de matériel et du changement d’infrastructure sous-jacente. La mise à l'échelle d’une base de données peut être effectuée en toute simplicité via le portail Azure à l’aide d’un curseur.

![Performances de SQL Database](./media/scale-resources/scale-performance.svg)

Azure SQL Database offre le modèle d’achat DTU et le [modèle d’achat DTU](service-tiers-dtu.md) et le [modèle d'achat vCore](service-tiers-vcore.md), tandis que Azure SQL Managed Instance offre uniquement le [modèle d’achat vCore](service-tiers-vcore.md). 

- Le [modèle d’achat DTU](service-tiers-dtu.md) offre une combinaison de ressources de calcul, de mémoire et d’E/S réparties sur trois niveaux de service pour prendre en charge les charges de travail de base de données, aussi bien légères qu’importantes : De base, Standard et Premium. Les niveaux de performance de chaque niveau fournissent une combinaison différente de ces ressources, à laquelle vous pouvez ajouter d’autres ressources de stockage.
- Le [modèle d’achat vCore](service-tiers-vcore.md) vous permet de choisir le nombre de vCores, la quantité de mémoire et de stockage, ainsi que la vitesse de stockage. Ce modèle d’achat propose trois niveaux de service : Usage général, Critique pour l’entreprise et Hyperscale.

Le niveau de service, le niveau de calcul et les limites de ressources d’une base de données, d’un pool élastique ou d’une instance managée peuvent être modifiés à tout moment. Par exemple, vous pouvez créer votre première application sur une seule base de données avec le niveau de calcul serverless. Vous pouvez à tout moment remplacer son niveau de service, manuellement ou automatiquement, par le niveau de calcul provisionné pour répondre aux besoins de votre solution.

> [!NOTE]
> Les exceptions notables pour lesquelles vous ne pouvez pas modifier le niveau de service d’une base de données sont les suivantes :
> - Les bases de données dans le niveau de service Hyperscale ne peuvent pas être remplacées par un niveau de service différent.
> - Les bases de données qui utilisent des fonctionnalités [uniquement disponibles](features-comparison.md#features-of-sql-database-and-sql-managed-instance) dans les niveaux de service Critique pour l’entreprise/Premium ne peuvent pas être modifiées pour utiliser le niveau de service Usage général/Standard.

Vous pouvez ajuster les ressources allouées à votre base de données en modifiant l’objectif de service, ou la mise à l’échelle, pour répondre aux demandes de la charge de travail. De cette façon, vous payez uniquement pour les ressources dont vous avez besoin, quand vous en avez besoin. Reportez-vous à la [remarque](#impact-of-scale-up-or-scale-down-operations) concernant l’impact potentiel d’une opération de mise à l’échelle sur une application.

> [!NOTE]
> La scalabilité dynamique est différente de la mise à l’échelle automatique. La mise à l’échelle automatique survient quand un service se met à l’échelle automatiquement en fonction de critères, tandis que l’extensibilité dynamique permet la mise à l’échelle manuelle avec un temps d’arrêt minimal. Les bases de données uniques dans Azure SQL Database peuvent être mises à l’échelle manuellement ou, dans le cas du [niveau serverless](serverless-tier-overview.md), configurées pour mettre automatiquement à l’échelle les ressources de calcul. Les [pools élastiques](elastic-pool-overview.md), qui permettent aux bases de données de partager des ressources dans un pool, peuvent uniquement être mis à l’échelle manuellement pour l’instant.

Azure SQL Database offre la possibilité de mettre à l'échelle vos bases de données de façon dynamique :

- Avec une [base de données unique](single-database-scale.md), vous pouvez utiliser des modèles [DTU](resource-limits-dtu-single-databases.md) ou [vCore](resource-limits-vcore-single-databases.md) pour définir le volume maximal de ressources assignées à chaque base de données.
- Les [pools élastiques](elastic-pool-scale.md) vous permettent de définir la limite de ressources maximale par groupe de bases de données dans le pool.

Azure SQL Managed Instance vous permet également une mise à l’échelle : 

- [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) utilise le mode [vCores](../managed-instance/sql-managed-instance-paas-overview.md#vcore-based-purchasing-model) et vous permet de définir le nombre maximum de cœurs UC et la quantité maximale de stockage alloués à votre instance. Toutes les bases de données au sein de l’instance gérée partageront les ressources allouées à l’instance.

## <a name="impact-of-scale-up-or-scale-down-operations"></a>Impact des opérations de scale-up ou de scale-down

Quelle que soit la saveur, une action de scale-up ou de scale-down redémarre le processus du moteur de base de données et le déplace si nécessaire vers une autre machine virtuelle. Le déplacement du processus du moteur de base de données vers une nouvelle machine virtuelle est un **processus en ligne** durant lequel vous pouvez continuer à utiliser votre service Azure SQL Database existant. Une fois que le moteur de base de données cible est prêt à traiter les requêtes, les connexions ouvertes au moteur de base de données actif sont [terminées](single-database-scale.md#impact) et les transactions non validées sont restaurées. De nouvelles connexions sont établies au moteur de base de données cible.

> [!NOTE]
> Il n’est pas recommandé de mettre votre instance gérée à l’échelle si une transaction durable, telle que l’importation de données, les travaux de traitement de données, la régénération d’index, etc., est en cours d’exécution, ou si vous avez une connexion active sur l’instance. Pour éviter que la mise à l’échelle ne prenne plus de temps que d’habitude, vous devez mettre l’instance à l’échelle à la fin de toutes les opérations durables.

> [!NOTE]
> Une courte interruption de la connexion risque de se produire à la fin du processus de mise à l’échelle. Si vous avez implémenté une [Logique de nouvelles tentatives pour les erreurs temporaires standard](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors), vous ne remarquerez pas le basculement.

## <a name="alternative-scale-methods"></a>Autres méthodes de mise à l’échelle

La mise à l'échelle des ressources reste la façon la plus facile et efficace pour améliorer les performances de votre base de données sans changer le code de la base de données ou de l’application. Dans certains cas, même les niveaux de service, les tailles de calcul et les optimisations de performances les plus élevés peuvent ne pas suffire à gérer votre charge de travail correctement et de façon économique. Dans ce cas, vous disposez d’autres options pour mettre à l’échelle votre base de données :

- [Lecture du Scale-out](read-scale-out.md) est une fonctionnalité disponible qui vous offre un réplica en lecture seule de vos données, sur lequel vous pouvez exécutez des requêtes en lecture seule exigeantes telles que les rapports. Un réplica en lecture seule gère votre charge de travail en lecture seule sans affecter l’utilisation des ressources sur votre base de données principale.
- Le [partitionnement de base de données](elastic-scale-introduction.md) est un ensemble de techniques qui vous permet de diviser vos données en plusieurs bases de données pour les mettre à l'échelle indépendamment.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur l’amélioration des performances d’une base de données en modifiant son code, consultez [Trouver et appliquer des recommandations de performances](database-advisor-find-recommendations-portal.md).
- Pour en savoir sur l’optimisation de votre base de données par une intelligence intégrée, consultez [Ajustement automatique](automatic-tuning-overview.md).
- Pour en savoir plus sur la fonctionnalité Lecture du Scale-out dans Azure SQL Database, consultez comment [utiliser des réplicas en lecture seule pour équilibrer les charges de travail de requêtes en lecture seule](read-scale-out.md).
- Pour en savoir plus sur le partitionnement de base de données, consultez [Scale-out avec Azure SQL Database](elastic-scale-introduction.md).
- Pour obtenir un exemple d’utilisation de scripts pour superviser et mettre à l’échelle une base de données unique, consultez [Utiliser PowerShell pour superviser et mettre à l’échelle une instance SQL Database unique](scripts/monitor-and-scale-database-powershell.md).
