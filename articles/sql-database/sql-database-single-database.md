---
title: Qu’est-ce qu’une base de données unique ?
description: En savoir plus sur les bases de données uniques dans Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/08/2019
ms.openlocfilehash: fc63de4057def632d3ac1980e8cb3eaedbff2175
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500747"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>Qu’est-ce qu’une base de données Azure SQL Database unique

L’option de déploiement d’une base de données unique crée dans Azure SQL Database une base de données dotée de ses propres ressources, et celle-ci est gérée par le biais d’un serveur SQL Database. Avec une base de données unique, chaque base de données est portable et isolée de l’autre. Chacune a son propre niveau de service dans le [modèle d’achat DTU](sql-database-service-tiers-dtu.md) ou le [modèle d’achat vCore](sql-database-service-tiers-vcore.md), ainsi qu’une taille de calcul garantie.

> [!IMPORTANT]
> Une base de données unique est l’une des trois options de déploiement Azure SQL Database. Les deux autres sont [les pools élastiques](sql-database-elastic-pool.md) et [l’instance gérée](sql-database-managed-instance.md).
> [!NOTE]
> Pour connaître la signification des termes dans Azure SQL Database, consultez le [Lexique SQL Database](sql-database-glossary-terms.md).

## <a name="dynamic-scalability"></a>Évolutivité dynamique

Vous pouvez créer votre première application sur une petite base de données unique à faible coût dans le niveau de calcul serverless ou une petite taille de calcul dans le niveau de calcul provisionné. Vous pouvez modifier le [niveau de calcul ou de service](sql-database-single-database-scale.md) manuellement ou programmatiquement à tout moment pour répondre aux besoins de votre solution. Vous pouvez ajuster les performances sans perturber le fonctionnement de votre application, ni l’expérience de vos clients. L’évolutivité dynamique permet à votre base de données de répondre en toute transparence aux besoins en ressources qui évoluent sans cesse et de payer uniquement les ressources dont vous avez besoin, lorsque vous en avez besoin.

## <a name="single-databases-and-elastic-pools"></a>Bases de données uniques et pools élastiques

Une base de données unique peut être déplacée dans et hors d’un [pool élastique](sql-database-elastic-pool.md) en vue du partage de ressources. Pour de nombreuses entreprises et applications, la possibilité de créer des bases de données uniques et d’augmenter ou de ralentir les performances à la demande se révèle suffisante, surtout si les modèles d’utilisation sont relativement prévisibles. Mais si vous avez des modèles d'utilisation imprévisibles, il peut être difficile de gérer les coûts et votre modèle commercial. Les pools élastiques sont conçus pour résoudre ce problème. Le concept est simple. Vous allouez des ressources de performance à un pool plutôt qu’à une base de données individuelle et vous payez pour les ressources de performance d’un pool plutôt que pour celles d’une base de données unique.

## <a name="monitoring-and-alerting"></a>Surveillance et alerte

Vous utilisez les outils intégrés de [surveillance](sql-database-performance-guidance.md) et [d’alerte](sql-database-insights-alerts-portal.md) de performances, combinées avec les évaluations de performance. Ces outils vous permettent d’évaluer rapidement l’impact des mises à l’échelle (montées ou descentes en charge) en fonction de vos besoins en performances actuels ou pour un projet. De plus, SQL Database peut [émettre des métriques et des journaux de ressources](sql-database-metrics-diag-logging.md) pour faciliter la supervision.

## <a name="availability-capabilities"></a>Fonctionnalités de disponibilité

Les bases de données uniques, les pools élastiques et les instances gérées fournissent un grand nombre de caractéristiques de disponibilité. Pour plus d’informations, consultez [Caractéristiques de disponibilité](sql-database-technical-overview.md#availability-capabilities).

## <a name="transact-sql-differences"></a>Différences Transact-SQL

La plupart des fonctionnalités Transact-SQL utilisées par les applications sont entièrement prises en charge dans Microsoft SQL Server et Azure SQL Database. Par exemple, les principaux composants SQL tels que les types de données, les opérateurs ainsi que les fonctions de chaîne, arithmétiques, logiques et de curseur, fonctionnent de la même façon dans SQL Server et SQL Database. Il existe toutefois quelques différences au niveau des éléments du langage de définition de données (DDL) et des éléments du langage de manipulation de données (DML). Ceux-ci génèrent en effet des instructions et des requêtes T-SQL qui ne sont que partiellement prises en charge (nous y reviendrons plus loin dans cet article).
Par ailleurs, Azure SQL Database étant conçu pour isoler les fonctionnalités des dépendances sur la base de données MASTER et le système d’exploitation, certaines fonctionnalités et syntaxes ne sont pas du tout prises en charge. Ainsi, de nombreuses activités de niveau serveur sont inappropriées pour SQL Database. Les instructions et les options T-SQL ne sont pas disponibles si elles configurent des options de niveau serveur et des composants du système d’exploitation, ou spécifient la configuration du système de fichiers. Si de telles fonctionnalités sont nécessaires, vous trouverez la plupart du temps une alternative appropriée en procédant différemment à partir de SQL Database ou d’un autre service/d’une autre fonctionnalité Azure.

Pour plus d’informations, voir [Résolution des différences de Transact-SQL durant la migration vers SQL Database](sql-database-transact-sql-information.md).

## <a name="security"></a>Sécurité

SQL Database fournit toute une gamme de [fonctionnalités intégrées de sécurité et conformité](sql-database-security-overview.md) pour que votre application réponde aux différentes exigences de conformité et de sécurité.

> [!IMPORTANT]
> La base de données Azure SQL Database (toutes les options de déploiement) a été certifiée par rapport à plusieurs normes de conformité. Pour en savoir plus, accédez au [Centre de confidentialité Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), qui inclut la liste la plus à jour des certifications de conformité de SQL Database.

## <a name="next-steps"></a>Étapes suivantes

- Pour commencer à utiliser une base de données unique, consultez la documentation [Guide de démarrage rapide sur les bases de données uniques](sql-database-single-database-quickstart-guide.md).
- Pour en savoir plus sur la migration d’une base de données SQL Server, consultez la section [Migrer une base de données Azure SQL](sql-database-single-database-migrate.md).
- Pour plus d’informations sur les fonctionnalités prises en charge, consultez la page [Fonctionnalités](sql-database-features.md).
