---
title: Retrait du niveau de service Premium RS
description: 'Le niveau de service Premium RS pour Azure SQL Database est en cours de retrait et sa prise en charge se termine : consultez les options de migration.'
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 02/07/2019
ms.openlocfilehash: e277c2660107e7bb741157b7d8c573ff69b9186e
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84039910"
---
# <a name="azure-sql-database-premium-rs-service-tier-preview-is-being-retired---options-for-migration"></a>Le niveau de service Azure SQL Database Premium RS (préversion) est en cours de retrait – Options de migration
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

En février 2018, Microsoft a annoncé que le niveau de service Premium RS dans Azure SQL Database ne sera pas lancé en disponibilité générale et ne sera plus pris en charge après le 31 janvier 2019. Cette date de fin du support a été repoussée au 30 juin 2019. Cet article présente les options de migration du niveau de service Premium RS à un autre niveau de service. Après le 30 juin 2019, Microsoft migrera automatiquement vos bases de données Premium RS vers le niveau de service en disponibilité générale le mieux adapté à leurs exigences de performances.

Voici les destinations de migration et les options tarifaires envisageables pour les clients Premium RS :

- Niveaux de service vCore

  Il s’agit des niveaux de service **Usage général** et **Critique pour l’entreprise** du [Modèle d’achat vCore](service-tiers-vcore.md). Ces deux niveaux de service sont en disponibilité générale. Le modèle d’achat vCore offre également le niveau de service **Hyperscale** qui s’adapte à la demande aux besoins de la charge de travail avec une mise à l’échelle automatique jusqu’à 100 To par base de données. Le niveau de service Hyperscale assure des performances d’E/S comparables à celles du niveau de service Premium du [modèle d’achat DTU](service-tiers-dtu.md) à un prix plus proche du niveau de service Premium RS.
- Tarifs Dev/Test

  Les [tarifs Dev/Test ](https://azure.microsoft.com/pricing/dev-test/) permet d’économiser jusqu’à 55 % par rapport aux tarifs licence incluse avec un abonnement Visual Studio.
- Tarifs Azure Hybrid Benefit et Capacité réservée

  Les [tarifs Azure Hybrid Benefit et Capacité réservée](https://azure.microsoft.com/pricing/details/sql-database/) offrent jusqu’à 80 % d’économies par rapport aux tarifs licence incluse. Pour plus d’informations sur ces options, voir [Azure Hybrid Benefit pour SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) et [Capacité réservée Azure SQL Database](reserved-capacity-overview.md).

## <a name="act-now-to-migrate-your-premium-rs-databases-to-alternative-sql-database-service-tiers"></a>Migrer des bases de données Premium RS vers un autre niveau de service SQL Database

Lisez cet article d’aide, ainsi que nos tarifs et notre documentation, pour déterminer la ou les destinations de migration adaptées à vos charges de travail Premium RS.

## <a name="migrate-compute-intensive-workloads-and-save"></a>Migrer des charges de travail nécessitant beaucoup de ressources système et faire des économies

Pour des charges de travail Premium RS nécessitant beaucoup de ressources système, nous recommandons une migration vers notre niveau de service Usage général par vCore en disponibilité générale, ce qui permet de faire des économies par rapport aux tarifs licence incluse des offres Azure Hybrid Benefit pour SQL Server et Capacité réservée. Si vous préférez conserver une option d’achat par DTU, vous pouvez migrer vos bases de données Premium RS nécessitant beaucoup de ressources système vers un niveau de service Standard, qui reste avantageux par rapport aux tarifs de la disponibilité générale Premium RS (si elle avait existé).

> [!WARNING]
> Le fait de migrer des charges de travail Premium RS vers des niveaux de service Premium DTU peut avoir pour effet d’augmenter les coûts mensuels par rapport aux tarifs Premium RS actuels. Nous vous recommandons d’étudier le niveau Hyperscale ou le niveau Critique pour l’entreprise avec les tarifs Azure Hybrid Benefit et Capacité réservée pour maintenir des coûts similaires ou inférieurs à Premium RS.

### <a name="premium-rs-databases"></a>Bases de données Premium RS

|**Si vous êtes actuellement sur…**|**Migrez vers le niveau par vCore comparable…**|**Migrez vers le niveau par DTU comparable…**|
|---|---|---|
|Premium RS 1|Usage général 1 vCore (Gen4)|Standard 3|
|Premium RS 2|Usage général 2 vCore (Gen4)|Standard 4|
|Premium RS 4|Usage général 4 vCore (Gen4)|Standard 6|
|Premium RS 6|Usage général 6 vCore (Gen4)|Standard 7|

### <a name="premium-rs-pools"></a>Pools Premium RS

|**Si vous êtes actuellement sur…**|**Migrez vers le niveau par vCore comparable…**|**Migrez vers le niveau par DTU comparable…**|
|---|---|---|
|Pool Premium RS 125 DTU|Usage général 1 vCore (Gen4)|Pool Standard 100 eDTU|
|Pool Premium RS 250 DTU|Usage général 2 vCore (Gen4)|Pool Standard 250 eDTU|
|Pool Premium RS 500 DTU|Usage général 4 vCore (Gen4)|Pool Standard 500 eDTU|
|Pool Premium RS 1 000 DTU|Usage général 8 vCore (Gen4)|Pool Standard 1 000 eDTU|

## <a name="optimize-savings-and-performance-for-your-io-intensive-workloads"></a>Trouver l’équilibre idéal entre économies et performances pour des charges de travail nécessitant beaucoup de ressources d’E/S

Nous recommandons de migrer les bases de données uniques nécessitant beaucoup de ressources d’E/S vers notre niveau Hyperscale par vCore, actuellement en préversion, et les pools de bases de données nécessitant beaucoup de ressources d’E/S vers notre niveau Critique pour l’entreprise en disponibilité générale, afin d’obtenir un équilibre optimal entre performances et coût.  Les options par vCore suivantes maintiendront ou amélioreront les performances que vous connaissez actuellement. Elles peuvent être sources d’économies si elles sont combinées avec les tarifs Azure Hybrid Benefit et Capacité réservée.

|**Si vous êtes actuellement sur…**|**Migrez vers le niveau par vCore comparable…**|**Migrez vers le niveau par DTU comparable…**|
|---|---|---|
|Premium RS 1| Hyperscale 1 vCore (Gen4) ou Critique pour l’entreprise 1 vCore (Gen4)|Premium 1|
|Premium RS 2| Hyperscale 2 vCores (Gen4) ou Critique pour l’entreprise 2 vCores (Gen4|Premium 2|
|Premium RS 4| Hyperscale 4 vCores (Gen4) ou Critique pour l’entreprise 4 vCores (Gen4)|Premium 4
|Premium RS 6| Hyperscale 6 vCores (Gen4) ou Critique pour l’entreprise 6 vCores (Gen4)|Premium 6|

|**Si vous êtes actuellement sur…**|**Migrez vers le niveau par vCore comparable…**|**Migrez vers le niveau par DTU comparable…**|
|---|---|---|
|Pool Premium RS 125 DTU|Critique pour l’entreprise 2 vCore (Gen4)|Pool Premium 125 eDTU|
|Pool Premium RS 250 DTU|Critique pour l’entreprise 2 vCore (Gen4)|Pool Premium 250 eDTU|
|Pool Premium RS 500 DTU|Critique pour l’entreprise 4 vCore (Gen4)|Pool Premium 500 eDTU|
|Pool Premium RS 1 000 DTU|Critique pour l’entreprise 8 vCore (Gen4)|Pool Premium 1 000 eDTU|

## <a name="take-advantage-of-our-new-offers"></a>Tirer parti de nos nouvelles offres

Nos niveaux de service du modèle d’achat vCore sont compatibles avec les offres spéciales qui peuvent faire gagner jusqu’à 80 % par rapport aux tarifs licence incluse. Utilisez vos licences pour les éditions SQL Server Standard ou Enterprise avec Software Assurance actif pour économiser jusqu’à 55 % par rapport aux tarifs de licence incluse avec [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) pour SQL Server. Vous pouvez combiner Hybrid Benefit avec les tarifs [Capacité réservée Azure SQL Database](reserved-capacity-overview.md) et économiser jusqu’à 80 % en vous engageant dès le début pour une durée comprise entre un et trois ans.  Profitez dès aujourd’hui de ces deux avantages sur le Portail Azure.

Si vous avez des questions au sujet de ce changement ou si vous avez besoin d’aide pour la migration, contactez [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="migration-from-a-premium-rs-service-tier-to-a-service-tier-in-either-the-dtu-or-the-vcore-model"></a>Migration d’un niveau de service Premium RS vers un niveau de service du modèle DTU ou vCore

### <a name="migration-of-a-database"></a>Migration d’une base de données

L’opération de migration d’une base de données d’un niveau de service Premium RS à un niveau de service du modèle DTU ou vCore est similaire à une mise à niveau entre les niveaux de service Premium RS.

### <a name="using-database-copy-to-convert-a-premium-rs-database-to-a-dtu-based-or-vcore-based-database"></a>Utiliser la copie de base de données pour convertir une base de données Premium RS en base de données DTU ou vCore

Vous pouvez copier n’importe quelle base de données possédant une taille de calcul Premium RS en une base de données avec une taille de calcul DTU ou vCore, sans aucune restriction ni aucun séquencement spécial, tant que la taille de calcul cible accepte la taille maximale de la base de données source. La copie de base de données crée un instantané des données dès que commence l’opération de copie, et elle n’effectue pas de synchronisation des données entre la source et la cible.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les tailles de calcul et les tailles de stockage disponibles pour les bases de données uniques, consultez [Limites des ressources vCore SQL Database pour les bases de données uniques](resource-limits-vcore-single-databases.md).
- Pour plus d’informations sur les tailles de calcul et les tailles de stockage disponibles pour les pools élastiques, consultez [Limites des ressources vCore SQL Database pour les pools élastiques](resource-limits-vcore-elastic-pools.md).
