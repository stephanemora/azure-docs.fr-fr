---
title: Forum Aux Questions sur Azure SQL Database | Microsoft Docs
description: Réponses fréquemment posées sur les bases de données du cloud et Azure SQL Database, le système de gestion de base de données relationnelle Microsoft (SGBDR) et la base de données en tant que service dans cloud.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: carlrab
ms.openlocfilehash: 9bb79a2054a0e51ce435f51a52f964062427cea4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647994"
---
# <a name="sql-database-faq"></a>Forum Aux Questions de base de données SQL

## <a name="what-is-the-current-version-of-sql-database"></a>Quelle est la version actuelle de la base de données SQL ?
La version actuelle de la base de données SQL est la version 12. La version 11 a été retirée.

## <a name="what-is-the-sla-for-sql-database"></a>Quel est le contrat de niveau de service (SLA) de la base de données SQL ?
Nous garantissons qu’au moins 99,99 % du temps, vous disposerez d’une connectivité entre Microsoft Azure SQL Database et notre passerelle Internet, quel que soit votre niveau de service. Pour plus d’informations, consultez [Contrat de niveau de service](http://azure.microsoft.com/support/legal/sla/).

## <a name="whatis-the-new-vcore-based-purchasing-model-preview-for-azure-sql-database"></a>Quel est le nouveau modèle d’achat basé sur des vCores (préversion) d’Azure SQL Database ?

Le nouveau modèle d’achat vient compléter le modèle basé sur des unités DTU existant. Le modèle d’achat basé sur des vCores est conçu pour offrir aux clients de la flexibilité, du contrôle et de la transparence. Il permet de traduire de manière simple les exigences des charges de travail locales vers le cloud. Il permet également aux clients de mettre à l’échelle les ressources de calcul et de stockage en fonction des besoins de leur charge de travail. Des options de pool élastique et de base de données unique utilisant le modèle basé sur des vCores permettent également de réaliser jusqu’à 30 % d’économies avec [Azure Hybrid Use Benefit pour SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md). Pour plus d’informations, consultez [Modèle d’achat basé sur des DTU](sql-database-service-tiers-dtu.md) et [Modèle d’achat basé sur des vCores (préversion)](sql-database-service-tiers-vcore.md). 

## <a name="what-is-a-vcore"></a>Qu’est-ce qu’un vCore ? 
Un vCore est l’UC logique qui permet de choisir parmi plusieurs générations de matériel. Les UC logiques de 4e génération sont basées sur des processeurs Intel E5-2673 v3 (Haswell) de 2,4 GHz, et celles de 5e génération sur des processeurs Intel E5-2673 v4 (Broadwell) de 2,3 GHz.

## <a name="is-moving-to-the-vcore-based-model-required"></a>Est-il nécessaire de migrer vers le modèle basé sur des vCores ?
Non, l’introduction du modèle basé sur des vCores dans les options de déploiement de pool élastique et de base de données unique reflète notre engagement à proposer du choix et de la flexibilité aux clients. Si les clients souhaitent continuer à utiliser le modèle basé sur des unités DTU, aucune action n’est requise suite à cette annonce, et leur expérience et facturation restent inchangées. 

Dans de nombreux cas, les applications peuvent bénéficier de la simplicité d’un ensemble préconfiguré de ressources. Par conséquent, nous continuons de proposer et de prendre en charge ces options basées sur des unités DTU pour nos clients. Si vous les utilisez et qu’elles répondent aux besoins de votre entreprise, vous pouvez continuer à vous en servir.

Les modèles basés sur des unités DTU et des vCores continuent d’exister côte à côte. Nous lançons le modèle basé sur des vCores en réponse aux demandes des clients qui souhaitaient plus de transparence autour de leurs ressources de base de données et la possibilité de mettre à l’échelle les ressources de calcul et de stockage séparément. Le modèle basé sur des vCores permet également aux clients disposant d’une couverture Software Assurance active de réaliser des économies supplémentaires via Azure Hybrid Benefit pour SQL Server.

## <a name="how-should-i-choose-between-the-dtu-based-purchasing-model-vs-the-vcore-based-purchasing-model-preview"></a>Comment choisir entre le modèle d’achat basé sur des unités DTU et le modèle d’achat basé sur des vCores (préversion) ? 
L’unité DTU (Database Transaction Unit) repose sur une mesure mixte d’UC, de mémoire, de lectures et d’écritures. Les niveaux de performance basés sur les unités DTU représentent des ensembles de ressources préconfigurés permettant de piloter différents niveaux de performances d’applications. Les clients qui ne souhaitent pas avoir à se soucier des ressources sous-jacentes et qui préfèrent la simplicité d’un ensemble préconfiguré, tout en payant un montant fixe chaque mois, peuvent trouver le modèle basé sur les unités DTU plus adapté à leurs besoins. Néanmoins, pour les clients qui ont besoin de plus d’informations détaillées sur les ressources sous-jacentes ou qui doivent les mettre à l’échelle de manière indépendante pour obtenir des performances optimales, le modèle basé sur des vCores constitue le meilleur choix.  De plus, si un client dispose d’une couverture Software Assurance active pour SQL Server, il peut optimiser son investissement actuel et économiser jusqu’à 30 % avec [Azure Hybrid Use Benefit pour SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).  Les options proposées dans chaque modèle d’achat offrent les avantages d’un service entièrement managé, tels que des sauvegardes automatisées, des mises à jour logicielles et des correctifs. 

## <a name="what-is-the-azure-hybrid-benefit-for-sql-server"></a>Présentation d’Azure Hybrid Benefit pour SQL Server 
[Azure Hybrid Use Benefit pour SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md) vous aide à valoriser vos investissements actuels en licences et à accélérer la migration vers le cloud. Azure Hybrid Benefit pour SQL Server est un avantage Azure qui vous permet d’utiliser vos licences SQL Server avec une couverture Software Assurance et de payer SQL Database à un tarif réduit (taux de base). Azure Hybrid Benefit pour SQL Server est disponible en préversion publique du modèle d’achat basé sur des vCores (préversion) pour les pools élastiques et les bases de données uniques SQL Database. Vous pouvez appliquer cet avantage même si la référence SKU est active. Notez toutefois que le taux de base s’applique à partir du moment où vous le sélectionnez dans le portail Azure. Aucun crédit n’est émis rétroactivement.

## <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Y a-t-il des droits d’utilisation double avec Azure Hybrid Benefit pour SQL Server ?
Vous disposez de 180 jours de droits d’utilisation double de la licence pour vous assurer que les migrations s’exécutent en toute transparence. À l’issue de cette période de 180 jours, la licence SQL Server peut uniquement être utilisée dans le cloud dans SQL Database et n’a pas de droits d’utilisation double en local et dans le cloud.


## <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Quelle est la différence entre Azure Hybrid Benefit pour SQL Server et la mobilité de licence ?
Aujourd’hui, nous proposons aux clients SQL Server des avantages de mobilité de licence Software Assurance qui leur permettent de réattribuer leurs licences à des serveurs partagés tiers. Cet avantage peut être utilisé sur Azure IaaS et AWS EC2.
La différence entre Azure Hybrid Benefit pour SQL Server et la mobilité de licence se trouve dans deux domaines clés :
- Azure Hybrid Benefit pour SQL Server propose des avantages économiques pour la migration de charges de travail hautement virtualisées vers Azure. Les clients EE SQL peuvent obtenir 4 cœurs dans Azure dans la référence SKU Usage général pour chacun des cœurs qu’ils possèdent en local pour des applications hautement virtualisées. La mobilité de licence n’offre aucun avantage spécifique en termes de coûts pour la migration des charges de travail virtualisées vers le cloud.
- Elle fournit une destination PaaS sur Azure qui est hautement compatible avec SQL Server en local - SQL Database Managed Instance.

## <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Quels sont les droits spécifiques associés à Azure Hybrid Benefit pour SQL Server ?
Les clients SQL Database auront les droits associés à Azure Hybrid Benefit pour SQL Server suivants :

|Encombrement de licence|Que vous propose Azure Hybrid Benefit pour SQL Server ?|
|---|---|
|Clients avec cœurs SQL Server Enterprise Edition et SA|<li>Possibilité de payer un taux de base pour les références SKU Usage général ou Critique pour l’entreprise</li><br><li>1 cœur local = 4 cœurs dans la référence SKU Usage général</li><br><li>1 cœur local = 1 cœur dans la référence SKU Critique pour l’entreprise</li>|
|Clients avec cœurs SQL Server Standard Edition et SA|<li>Possibilité de payer un taux de base pour la référence SKU Usage général uniquement</li><br><li>1 cœur local = 1 cœur dans la référence SKU Usage général</li>|
|||

## <a name="is-the-vcore-based-model-available-to-sql-database-managed-instance"></a>Le modèle basé sur des vCores est-il disponible pour SQL Database Managed Instance ?
[Managed Instance](sql-database-managed-instance.md) est disponible uniquement avec le modèle basé sur des vCores. Pour plus d’informations, consultez également la [page de tarification SQL Database](https://azure.microsoft.com/pricing/details/sql-database/managed/). 

## <a name="does-the-cost-of-compute-and-storage-depend-on-the-service-tier-that-i-choose"></a>Les coûts associés au calcul et au stockage dépendent-ils du niveau de service que je choisis ?
Le coût associé au calcul reflète la capacité de calcul totale provisionnée pour l’application. Dans le niveau de service Critique pour l’entreprise, nous allouons automatiquement 3 réplicas Always ON minimum. Pour refléter cette allocation supplémentaire de ressources de calcul, le prix vCore est environ 2,7 fois plus élevé dans le niveau SKU Critique pour l’entreprise. Pour la même raison, le prix plus élevé du stockage par Go dans le niveau Critique pour l’entreprise reflète le nombre important d’E/S et la faible latence du stockage SSD. Dans le même temps, le coût du stockage de sauvegarde n’est pas différent, car dans les deux cas, nous utilisons une classe de stockage standard.

## <a name="how-am-i-charged-for-storage---based-on-what-i-configure-upfront-or-on-what-the-database-uses"></a>Comment suis-je facturé pour le stockage ? Cette facturation repose-t-elle sur ma configuration initiale ou sur ce que la base de données utilise ?
Les divers types de stockage sont facturés différemment. Pour le stockage des données, vous êtes facturé en fonction du stockage provisionné, sur la base de la taille maximum de la base de données ou du pool que vous sélectionnez. Le coût ne change pas, sauf si vous réduisez ou augmentez cette taille maximum. Le stockage de sauvegarde est associé aux sauvegardes automatisées de votre instance. L’allongement de la période de rétention des sauvegardes a pour effet d’augmenter le volume de stockage de sauvegarde que votre instance utilise. Un stockage de sauvegarde jusqu’à 100 % de la capacité de stockage totale provisionnée de votre serveur n’occasionne aucuns frais supplémentaires. Toute consommation supérieure du stockage de sauvegarde est facturée en Go par mois. Par exemple, si la taille de stockage de base de données dont vous disposez est de 100 Go, vous obtenez un volume de sauvegarde de 100 Go sans frais supplémentaires. Toutefois, si le volume de sauvegarde est de 110 Go, les 10 Go supplémentaires vous sont facturés.

Pour le stockage de sauvegarde d’une base de données unique, vous êtes facturé au prorata pour le stockage qui a été alloué aux sauvegardes de base de données moins la taille de la base de données. Pour le stockage de sauvegarde d’un pool élastique, vous êtes facturé au prorata pour le stockage qui a été alloué aux sauvegardes de base de données de toutes les bases de données dans le pool moins la taille maximum des données du pool élastique. Une augmentation de la taille de la base de données ou du pool élastique, ou une augmentation du taux de transaction, nécessite plus de stockage et augmente donc votre facture de stockage de sauvegarde.  Lorsque vous augmentez la taille maximum des données, cette nouvelle quantité est déduite de la taille du stockage de sauvegarde facturée.

## <a name="how-do-i-select-the-right-sku-when-converting-an-existing-database-to-the-new-service-tiers"></a>Comment puis-je sélectionner la référence SKU appropriée lorsque je convertis une base de données existante vers les nouveaux niveaux de service ? 
Pour les applications SQL Database existantes utilisant le modèle basé sur des unités DTU, le niveau de service Usage général est comparable au niveau Standard. Le niveau de service Critique pour l’entreprise est comparable au niveau Premium. Dans les deux cas, vous devez allouer au moins 1 vCore par tranche de 100 DTU utilisées par votre application dans le modèle basé sur des unités DTU.

## <a name="do-the-new-vcore-based-service-tiers-offer-the-performance-levels-compatible-with-all-existing-service-level-objectives-slos"></a>Les nouveaux niveaux de service basés sur des vCores offrent-ils des niveaux de performances compatibles avec tous les objectifs de niveau de service existants ?
Les nouveaux niveaux de service basés sur des vCores offrent des options de performances comparables pour l’ensemble des pools élastiques et des bases de données utilisant 100 DTU ou plus.  Nous continuerons d’ajouter plusieurs objectifs de niveau de service au fil du temps pour prendre en charge les charges de travail utilisant moins de 100 DTU.

## <a name="are-there-any-database-feature-differences-between-the-existing-dtu-based-and-new-vcore-based-service-tiers"></a>Y a-t-il des différences en matière de fonctionnalités de base de données entre les niveaux de services basés sur des unités DTU existants et les nouveaux niveaux de service basés sur des vCores ? 
Les nouveaux niveaux de service prennent en charge un sur-ensemble des fonctionnalités disponibles avec les offres basées sur des unités DTU actuelles. Les fonctionnalités supplémentaires comprennent un ensemble de vues de gestion dynamique (DMV) supplémentaires, ainsi que des options de configuration des ressources. 

## <a name="if-my-database-is-cpu-bound-and-does-not-use-much-storage-can-i-increase-the-compute-without-paying-for-extra-storage"></a>Si ma base de données utilise le processeur de manière intensive, mais n’utilise pas beaucoup de stockage, puis-je augmenter les ressources de calcul sans payer pour le stockage supplémentaire ?
Oui, vous pouvez sélectionner de façon indépendante le niveau de calcul de votre application et conserver le stockage tel qu’il est. La valeur minimum sur laquelle le stockage peut être définie est 32 Go. 

## <a name="will-the-new-vcore-based-tiers-support-point-in-time-restore-pitr-for-35-days-as-today"></a>Les nouveaux niveaux de service basés sur des vCores prendront-ils en charge la limite de restauration dans le temps de 35 jours, comme c’est le cas actuellement ? 
Vous pouvez configurer la rétention des sauvegardes pour la limite de restauration dans le temps entre 7 et 35 jours. Le stockage de sauvegarde sera facturé séparément, en fonction de la consommation de stockage réelle si elle dépasse la quantité de stockage égale à la taille maximum des données. Dans la préversion, la période de rétention de la limite de restauration dans le temps est définie sur 7 jours par défaut. Dans de nombreux cas, la taille maximum des données est suffisante pour stocker 7 jours de sauvegardes.

## <a name="why-do-you-allow-selection-of-the-hardware-generation-for-compute"></a>Pourquoi offrez-vous la possibilité de choisir la génération de matériel pour le calcul ?
Notre objectif est de permettre une flexibilité maximum, afin que vous puissiez choisir une configuration de performances qui correspond au mieux aux besoins de l’application. Le tableau ci-dessus montre les différences entre les générations Gen4 et Gen5. Le matériel Gen4 offre notamment davantage de mémoire par vCore. Toutefois, le matériel Gen5 vous permet de monter en puissance les ressources de calcul de façon plus importante. Nous voulons que ces différences soient transparentes pour vous, afin que vous puissiez obtenir le ratio prix/performances optimal pour votre application.

## <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-database-to-a-vcore-based-service-tier"></a>Dois-je mettre mon application hors ligne pour convertir une base de données basée sur des unités DTU en un niveau de service basé sur des vCores ? 
Les nouveaux niveaux de service offrent une méthode de conversion en ligne simple qui est similaire au processus actuel de mise à niveau des bases de données du niveau de service Standard au niveau Premium et inversement. Cette conversion peut être lancée à l’aide du portail, d’ARM, de PowerShell, d’Azure CLI ou de T-SQL. Consultez [Gérer les ressources pour une base de données unique dans Azure SQL Database](sql-database-single-database-resources.md) et [Les pools élastiques vous aident à gérer et à mettre à l’échelle plusieurs bases de données Microsoft Azure SQL](sql-database-elastic-pool.md).

## <a name="can-i-convert-a-database-from-a-vcore-based-service-tier-to-a-dtu-based-one"></a>Puis-je convertir une base de données d’un niveau de service basé sur des vCores à un niveau de service basé sur des unités DTU ? 
Oui, vous pouvez facilement convertir votre base de données vers des objectifs de performance pris en charge à l’aide du portail ou par programme en utilisant le portail, ARM, PowerShell, Azure CLI ou T-SQL. Consultez [Gérer les ressources pour une base de données unique dans Azure SQL Database](sql-database-single-database-resources.md) et [Les pools élastiques vous aident à gérer et à mettre à l’échelle plusieurs bases de données Microsoft Azure SQL](sql-database-elastic-pool.md).

## <a name="can-i-upgrade-or-downgrade-between-the-general-purpose-and-business-critical-service-tiers"></a>Puis-je effectuer une mise à niveau ou passer à une version antérieure entre les niveaux de service Usage général et Critique pour l’entreprise ? 
Oui, mais quelques restrictions s’appliquent. Votre référence SKU de destination doit respecter la taille maximum de base de données ou de pool élastique que vous avez configurée pour votre déploiement existant. Si vous utilisez [Azure Hybrid Use Benefit pour SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md), la référence SKU Critique pour l’entreprise est uniquement disponible pour les clients avec des licences Enterprise Edition. Seuls les clients qui ont migré du niveau de service local vers la référence SKU Usage général à l’aide d’Azure Hybrid Benefit pour SQL Server avec des licences Enterprise Edition peuvent effectuer une mise à niveau vers la référence Critique pour l’entreprise. Pour plus d’informations, consultez [Azure Hybrid Benefit pour Windows Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

Cette conversion n’entraîne pas de temps d’arrêt et peut être lancée à l’aide du portail, d’ARM, de PowerShell, d’Azure CLI ou de T-SQL. Consultez [Gérer les ressources pour une base de données unique dans Azure SQL Database](sql-database-single-database-resources.md) et [Les pools élastiques vous aident à gérer et à mettre à l’échelle plusieurs bases de données Microsoft Azure SQL](sql-database-elastic-pool.md).

## <a name="i-am-using-a-premium-rs-database-that-will-not-be-generally-available---can-i-upgrade-it-to-a-new-tier-and-achieve-a-similar-priceperformance-benefit"></a>J’utilise une base de données Premium RS qui ne sera pas mise à la disposition générale. Puis-je effectuer une mise à niveau vers un nouveau niveau et obtenir un ratio prix/performance similaire ?
Étant donné que le modèle vCore permet de contrôler indépendamment la quantité de calcul et de stockage provisionnée, vous pouvez gérer plus efficacement les coûts résultants, ce qui le rend intéressant pour les bases de données Premium RS. En outre, [Azure Hybrid Use Benefit pour SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md) propose une remise importante lorsque le modèle basé sur des vCores est utilisé. 

## <a name="how-often-can-i-adjust-the-resources-per-pool"></a>À quelle fréquence puis-je ajuster le nombre de ressources par pool ?
Aussi souvent que vous le souhaitez. Consultez [Les pools élastiques vous aident à gérer et à mettre à l’échelle plusieurs bases de données Microsoft Azure SQL](sql-database-elastic-pool.md).

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>Combien de temps faut-il pour modifier le niveau de service ou le niveau de performances d’une base de données ou pour déplacer une base de données dans ou hors du pool élastique ?
Le changement de niveau de service d’une base de données et le déplacement dans ou vers un pool exige que la base de données soit copiée sur la plateforme en tant qu’opération d’arrière-plan. Le changement de niveau de service peut prendre de quelques minutes à plusieurs heures en fonction de la taille de la base de données. Dans les deux cas, les bases de données restent en ligne et disponibles pendant le déplacement. Pour plus d’informations sur la modification de bases de données uniques, consultez [Modifier le niveau de service d’une base de données](sql-database-service-tiers-dtu.md). 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>Quand dois-je choisir une base de données unique plutôt que des bases de données élastiques ?
En général, les pools élastiques sont conçus pour un [modèle d’application logiciel en tant que service (SaaS)](sql-database-design-patterns-multi-tenancy-saas-applications.md) standard, où il existe une base de données par client ou par locataire. L’achat de bases de données individuelles et le sur-approvisionnement destiné à répondre aux pics de demandes variables de chaque base de données ne sont généralement pas rentables. Avec les pools, vous gérez les performances collectives du pool, et les bases de données se mettent à l’échelle automatiquement. Le moteur intelligent d’Azure recommande un pool pour les bases de données quand un modèle d’utilisation l’exige. Pour plus d’informations, consultez [Conseils pour les pools élastiques](sql-database-elastic-pool.md).

## <a name="how-does-the-usage-of-sql-database-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>Comment l’utilisation de SQL Database via le modèle d’achat basé sur des unités DTU s’affiche-t-elle sur ma facture ?
SQL Database facture sur la base d’un taux horaire prévisible reposant sur le [modèle d’achat](sql-database-service-tiers-dtu.md). L’utilisation réelle est calculée au pro-rata horaire, et il se peut que votre facture affiche des fractions d’heure. Par exemple, si une base de données existe pendant 12 heures au cours d’un mois, votre facture affiche une demi-journée d’utilisation. 

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>Que se passe-t-il si une base de données est active pendant moins d’une heure ou utilise un niveau de service plus élevé pendant moins d’une heure ?
Vous êtes facturé pour chaque heure d’existence de la base de données avec le niveau de service le plus élevé, le niveau de performance appliqué pendant cette heure quel que soit l’usage, ou si la base de données a été active pendant moins d’une heure. Par exemple, si vous avez créé une base de données unique et que vous l’avez supprimée cinq minutes après, votre facture mentionne le coût d’une heure de base de données. 

Exemples :

* Si vous créez une base de données de base, puis que vous la mettez à niveau immédiatement vers le niveau Standard S1, vous serez facturé au tarif Standard S1 pour la première heure.
* Si vous mettez à niveau une base de données De base vers la version Premium à 22 h 00 et que la mise à niveau se termine à 01 h 35 le jour suivant, vous serez facturé au tarif Premium à partir de 01 h 00. 
* Si vous passez d’une base de données Premium à une version De base à 11 h 00 et que le processus se termine à 14 h 15, la base de données sera facturée au tarif Premium jusqu’à 15 h 00, puis elle sera facturée au tarif De base.

## <a name="how-does-elastic-pool-usage-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>Comment l’utilisation du pool élastique via le modèle d’achat basé sur des unités DTU s’affiche-t-elle sur ma facture ?
Les frais de pool élastique s’affichent sur votre facture en tant que DTU élastiques (eDTU) ou vCores + stockage dans les incréments indiqués sur la [page de tarification](https://azure.microsoft.com/pricing/details/sql-database/). Il n’existe pas de facturation par base de données pour les pools élastiques. Vous êtes facturé pour chaque heure d’existence d’un pool selon le nombre d’eDTUs ou de vCores le plus élevé, indépendamment de l’utilisation ou si le pool a été actif de moins d’une heure. 

Exemples de modèles d’achat basés sur des unités DTU :

* Si vous créez un pool élastique standard avec 200 eDTU à 11 h 18, en ajoutant cinq bases de données au pool, vous êtes facturé pour 200 eDTU pendant une heure complète, à partir de 11 h 00 pour le reste de la journée.
* Le 2e jour, à 5 h 05, la base de données 1 commence à consommer 50 eDTU et reste stable toute la journée. Les bases de données 2-5 fluctuent entre 0 et 80 eDTU. Pendant la journée, vous ajoutez cinq autres bases de données qui utilisent des eDTU pendant la journée. Le jour 2 est un jour complet facturé à 200 eDTU. 
* Le troisième jour, à 5 h 00, vous ajoutez 15 autres bases de données. L’utilisation de la base de données augmente pendant la journée jusqu’au point où vous décidez d’augmenter le nombre d’eDTU du pool de 200 à 400 à 20 h 05. Les frais au niveau de 200 eDTU étaient en vigueur jusqu’à 8 h 00 et augmentent jusqu’à 400 eDTU pour les quatre heures restantes. 

## <a name="how-are-elastic-pool-billed-for-the-dtu-based-purchasing-model"></a>Comment sont facturés les pools élastiques pour le modèle d’achat basé sur des unités DTU ?
Les pools élastiques sont facturés en fonction des caractéristiques suivantes :

* Un pool élastique est facturé lors de sa création, même s’il n’inclut aucune base de données.
* Il est facturé toutes les heures. La fréquence de mesure est identique à celle des niveaux de performances associés aux bases de données uniques.
* Si un pool élastique est redimensionné, il n’est pas facturé selon la nouvelle quantité de ressources tant que l’opération de redimensionnement n’est pas terminée. Le processus est le même que lors de la modification du niveau de performances des bases de données uniques.
* Le prix d’un pool élastique est basé sur le nombre de ressources de ce pool. Le prix d’un pool élastique est indépendant du nombre et de l’utilisation des bases de données élastiques qu’il contient.

Pour plus d’informations, consultez [Tarification des bases de données SQL](https://azure.microsoft.com/pricing/details/sql-database/), [Modèle d’achat basé sur des DTU](sql-database-service-tiers-dtu.md) et [Modèle d’achat basé sur des vCores (préversion)](sql-database-service-tiers-vcore.md).

## <a name="how-does-the-vcore-based-usage-show-up-in-my-bill"></a>Comment l’utilisation basée sur des vCores s’affichera-t-elle sur ma facture ? 
Dans le modèle basé sur des vCores, le service est facturé à un tarif horaire prévisible en fonction du niveau de service, du calcul provisionné en vCores, du stockage provisionné en Go/mois et du stockage de sauvegarde consommé. Si le stockage pour les sauvegardes dépasse la taille totale de la base de données (autrement dit, 100 % de la taille de la base de données), des frais supplémentaires s’appliquent. Les heures de vCore, le stockage de base de données configuré, les E/S utilisées et le stockage de sauvegarde sont clairement indiqués sur la facture, pour que vous puissiez facilement déterminer les ressources que vous avez consommées. Le stockage de sauvegarde jusqu’à 100 % de la taille maximum de la base de données est inclus. Au-delà, vous êtes facturé en Go/mois consommés dans un mois donné.

Par exemple : 
- Si l’instance SQL Database fonctionne pendant 12 heures par mois, votre facture indique une utilisation de 12 heures de vCores. Si l’instance SQL Database a provisionné 100 Go de stockage supplémentaires, la facture présente l’utilisation du stockage en unités de Go/mois au prorata horaire et le nombre d’E/S consommées par mois.
- Si l’instance SQL Database est active pendant moins d’une heure, vous êtes facturé pour chaque heure où la base de données existe en utilisant le niveau de service le plus élevé qui a été sélectionné et le stockage et les E/S provisionnés qui sont appliqués pendant cette heure, indépendamment de l’utilisation ou du fait que la base de données était active pendant moins d’une heure.
- Si vous créez une instance Managed Instance et que vous la supprimez cinq minutes plus tard, vous êtes facturé pour une heure de base de données.
- Si vous créez une instance Managed Instance dans le niveau Usage général incluant 8 vCores, puis procédez immédiatement à une mise à niveau vers 16 vCores, vous êtes facturé pour 16 vCores pendant la première heure.

> [!NOTE]
> Pour une durée limitée, jusqu’au 30 juin 2018, les frais de sauvegarde et d’E/S ne sont pas facturés.

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>Comment l’utilisation de la géoréplication active dans un pool élastique apparaît-elle sur ma facture ?
Contrairement aux bases de données uniques, l’utilisation de la [géoréplication active](sql-database-geo-replication-overview.md) avec des bases de données élastiques n’a pas d’incidence directe sur la facturation.  Vous êtes uniquement facturé pour les ressources provisionnées pour chaque pool (pool principal et pool secondaire)

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>Quel sera l’impact de l’utilisation de la fonctionnalité d’audit sur ma facture ?
La fonctionnalité d’audit est intégrée au service SQL Database sans frais supplémentaires et est disponible pour tous les niveaux de service. Toutefois, pour stocker les journaux d’audit, la fonction d’audit utilise un compte Azure Storage et les tarifs des tables et files d’attente dans Azure Storage s’appliquent en fonction de la taille de votre journal d’audit.

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>Comment réinitialiser le mot de passe de l’administrateur du serveur ?
Dans le [portail Azure](https://portal.azure.com), cliquez sur **Serveurs SQL**, sélectionnez le serveur dans la liste, puis cliquez sur **Réinitialiser le mot de passe**.

## <a name="how-do-i-manage-databases-and-logins"></a>Comment gérer les bases de données et les connexions ?
Consultez [Gestion des bases de données et des connexions](sql-database-manage-logins.md).

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>Comment s’assurer que seules les adresses IP autorisées peuvent accéder à un serveur ?
Voir [Configuration des paramètres de pare-feu sur une base de données SQL](sql-database-configure-firewall-settings.md).

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>Quel décalage de réplication est attendu lors de la réplication géographique d’une base de données entre deux régions au sein de la même zone géographique Azure ?
Nous prenons actuellement en charge un RPO de cinq secondes et le décalage de réplication est inférieur à cette mesure quand la zone géographique secondaire est hébergée dans la région Azure associée recommandée et au même niveau de service.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>Quel est le décalage réplication attendu lorsque la zone géographique secondaire est créée dans la même région que la base de données primaire ?
En se basant sur des données empiriques, il y a peu de différence entre la réplication intra-région et inter-région si la région associée recommandée par Azure est utilisée. 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>En cas de défaillance du réseau entre deux régions, comment la logique de nouvelle tentative fonctionne-t-elle lorsque la géoréplication est configurée ?
En cas de déconnexion, une nouvelle tentative de rétablissement des connexions est effectuée toutes les 10 secondes.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>Que puis-je faire pour garantir qu’une modification critique de la base de données primaire est répliquée ?
La zone géographique secondaire est un réplica asynchrone et nous n’essayons pas de conserver une synchronisation complète avec la zone principale. Mais nous fournissons une méthode pour forcer la synchronisation afin de garantir la réplication des modifications critiques (par exemple, les mises à jour du mot de passe). La synchronisation forcée a un impact sur les performances, car elle bloque le thread appelant jusqu’à ce que toutes les transactions validées soient répliquées. Pour plus d’informations, consultez [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx). 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>Quels outils sont disponibles pour surveiller le décalage de réplication entre la base de données primaire et la zone géographique secondaire ?
Nous exposons le décalage de réplication en temps réel entre la base de données primaire et la zone géographique secondaire par le biais d’une vue de gestion dynamique (DMV). Pour plus d’informations, consultez [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx).

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Pour déplacer une base de données sur un autre serveur dans le même abonnement
Dans le [portail Azure](https://portal.azure.com), cliquez sur **Bases de données SQL**, sélectionnez une base de données dans la liste, puis cliquez sur **Copier**. Pour plus d’informations, consultez [Copier une base de données SQL Azure](sql-database-copy.md) .

## <a name="to-move-a-database-between-subscriptions"></a>Pour déplacer une base de données entre des abonnements
Dans le [portail Azure](https://portal.azure.com), cliquez sur **Serveurs SQL**, puis sélectionnez le serveur qui héberge votre base de données dans la liste. Cliquez sur **Déplacer**, puis sélectionnez les ressources à déplacer et l’abonnement cible.
