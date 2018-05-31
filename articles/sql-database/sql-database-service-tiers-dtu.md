---
title: Service Azure SQL Database - DTU | Microsoft Docs
description: Découvrez les niveaux de service des bases de données uniques du pool qui permettent de fournir divers niveaux de performance et diverses tailles de stockage.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/09/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 6e282291a6e6e219bb275dd4da91f3815590adc1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32196002"
---
# <a name="dtu-based-purchasing-model-for-azure-sql-database"></a>Modèle d’achat DTU pour Azure SQL Database 


[Azure SQL Database](sql-database-technical-overview.md) propose deux modèles d’achat pour les ressources de calcul, de stockage et d’E/S : un modèle d’achat DTU et un modèle d’achat vCore (en préversion). Le tableau et le graphique suivants comparent ces deux modèles d’achat.

> [!IMPORTANT]
> Pour le modèle d’achat vCore (préversion), consultez [Modèle d’achat vCore](sql-database-service-tiers-vcore.md)


|**Modèle d’achat**|**Description**|**Idéal pour**|
|---|---|---|
|Modèle DTU|Ce modèle est basé sur une mesure regroupant des ressources de calcul, de stockage et d’E/S. Les niveaux de performance en termes d’unités de transaction de base de données (DTU) pour des bases de données uniques et d’unités de transaction de base de données élastique (eDTU) pour les pools élastiques. Pour en savoir plus sur les DTU et les eDTU, consultez [Définition des DTU et des eDTU](sql-database-what-is-a-dtu.md).|Idéal pour les clients qui souhaitent des options de ressources préconfigurées et simples.| 
|Modèle vCore|Ce modèle vous permet de faire évoluer indépendamment les ressources de calcul et de stockage. Il permet également d’utiliser Azure Hybrid Benefit pour SQL Server afin de réduire les coûts.|Idéal pour les clients qui donnent la priorité à la flexibilité, au contrôle et à la transparence.|
||||  

![Modèle de prix](./media/sql-database-service-tiers/pricing-model.png)

## <a name="dtu-based-purchasing-model"></a>Modèle d’achat DTU

L’unité DTU (Database Throughput Unit) correspond à une mesure mixte d’UC, de mémoire, de lectures et d’écritures. Le modèle d’achat DTU offre un ensemble préconfiguré de ressources de calcul et de stockage inclus pour obtenir différents niveaux de performance d’application. Les clients qui préfèrent la simplicité d’un ensemble préconfiguré et de versements mensuels peuvent trouver le modèle DTU plus adapté à leurs besoins. Dans le modèle d’achat DTU, les clients peuvent choisir parmi les niveaux de service **De base**, **Standard** et **Premium** pour [chaque base de données](sql-database-single-database-resources.md) et pour les [pools élastiques](sql-database-elastic-pool.md). Chaque niveau de service a son propre niveau de performance, ainsi qu’une quantité fixe de stockage inclus, une période de rétention fixe pour les sauvegardes et un prix fixe. Tous les niveaux de service permettent de changer de niveau de performance sans nécessiter de temps d’arrêt. Les bases de données et les pools élastiques sont facturés en fonction du niveau de service et du niveau de performance.

> [!IMPORTANT]
> SQL Database Managed Instance, qui est actuellement en préversion publique, ne prend pas en charge le modèle d’achat DTU. Pour plus d’informations, consultez [Azure SQL Database Managed Instance](sql-database-managed-instance.md). 

## <a name="choosing-a-service-tier-in-the-dtu-based-purchasing-model"></a>Choix d’un niveau de service dans le modèle d’achat DTU

Le choix d’un niveau de service dépend principalement des exigences de continuité d’activité, de stockage et de performance.
||De base|standard|Premium|
| :-- | --: |--:| --:| --:| 
|Charge de travail cible|Développement et production|Développement et production|Développement et production||
|Contrat SLA de durée de fonctionnement|99,99 %|99,99 %|99,99 %|N/A pendant la version préliminaire|
|Rétention des sauvegardes|7 jours|35 jours|35 jours|
|UC|Faible|Faible, moyen, élevé|Faible, élevé|
|Débit d’E/S (approximatif) |2,5 IOPS par DTU| 2,5 IOPS par DTU | 48 IOPS par DTU|
|Latence d’E/S (approximative)|5 ms (lecture), 10 ms (écriture)|5 ms (lecture), 10 ms (écriture)|2 ms (lecture/écriture)|
|Indexation Columnstore |N/A|S3 et supérieur|Prise en charge|
|OLTP en mémoire|N/A|N/A|Prise en charge|
|||||

## <a name="performance-level-and-storage-size-limits-in-the-dtu-based-purchasing-model"></a>Niveau de performance et limites de taille de stockage dans le modèle d’achat DTU

Les niveaux de performance en termes d’unités de transaction de base de données (DTU) pour des bases de données uniques et d’unités de transaction de base de données élastique (eDTU) pour les pools élastiques. Pour en savoir plus sur les DTU et les eDTU, consultez [Définition des DTU et des eDTU](sql-database-what-is-a-dtu.md).

### <a name="single-databases"></a>Bases de données uniques

||De base|standard|Premium|
| :-- | --: | --: | --: | --: |
| Taille de stockage maximale* | 2 Go | 1 To | 4 To  | 
| DTU maximales | 5. | 3000 | 4000 | |
||||||

Pour plus d’informations sur les niveaux de performance et les options de taille de stockage disponibles pour les bases de données uniques, consultez [Limites des ressources DTU SQL Database pour les bases de données uniques](sql-database-dtu-resource-limits.md#single-database-storage-sizes-and-performance-levels).

### <a name="elastic-pools"></a>Pools élastiques

| | **De base** | **Standard** | **Premium** | 
| :-- | --: | --: | --: | --: |
| Taille de stockage maximale par base de données*  | 2 Go | 1 To | 1 To | 
| Taille de stockage maximale par pool* | 156 Go | 4 To | 4 To | 
| Nombre maximal d’eDTU par base de données | 5. | 3000 | 4000 | 
| eDTU maximales par pool | 1 600 | 3000 | 4000 | 
| Nombre maximal de bases de données par pool | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> -  Les tailles de stockage supérieures à la quantité de stockage inclue sont en version préliminaire et des coûts supplémentaires s’appliquent. Pour en savoir plus, voir [Tarification de la base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/). 
> -  Au niveau Premium, plus de 1 To de stockage est actuellement disponible dans les régions suivantes : Est de l’Australie, Sud-Est de l’Australie, Sud du Brésil, Centre du Canada, Est du Canada, Centre des États-Unis, France-Centre, Centre de l’Allemagne, Est du Japon, Ouest du Japon, Corée Centre, Nord du centre des États-Unis, Europe du Nord, Sud du centre des États-Unis, Sud-Est asiatique, Royaume-Uni Sud, Royaume-Uni Ouest, Est des États-Unis 2, Ouest des États-Unis, Gouvernement des États-Unis – Virginie et Europe de l’Ouest. Consultez [Limitations actuelles P11-P15](sql-database-dtu-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

Pour plus d’informations sur les niveaux de performance et les options de taille de stockage disponibles pour les pools élastiques, consultez [Limites des ressources DTU SQL Database pour les pools élastiques](sql-database-dtu-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).



## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les niveaux de performance et les options de taille de stockage disponibles, consultez [Limites des ressources DTU SQL Database](sql-database-dtu-resource-limits.md) et [Limites des ressources vCore SQL Database](sql-database-vcore-resource-limits.md).
- Consultez [SQL Database FAQ](sql-database-faq.md) (FAQ de SQL Database) pour obtenir des réponses aux questions fréquemment posées.
- En savoir plus sur [l’abonnement Azure et les limites, quotas et contraintes des services](../azure-subscription-service-limits.md)
