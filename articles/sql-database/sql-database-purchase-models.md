---
title: Modèles d’achat Azure SQL Database | Microsoft Docs
description: En savoir plus sur les modèles d’achat qui sont disponibles pour la base de données SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 04/26/2019
ms.openlocfilehash: 98c19732c372fbcda3ca8e746d002f94c2687b22
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431372"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>Choisir entre la vCore et le nombre de modèles d’achat DTU

Base de données SQL Azure vous permet de facilement en acquérir une plateforme entièrement gérée comme un moteur de base de données de service (PaaS) adaptée à vos besoins de performances et de coûts. Selon le modèle de déploiement que vous avez choisie pour la base de données SQL Azure, vous pouvez sélectionner le modèle d’achat qui vous convient :

- [Noyau virtuel (vCore)-en fonction de modèle d’achat](sql-database-service-tiers-vcore.md) (recommandé). Ce modèle d’achat propose le choix entre un niveau de calcul provisionné et un niveau de calcul sans serveur (version préliminaire). Avec le niveau calcul provisionné, vous choisissez la quantité exacte de ressources de calcul qui sont toujours configurées pour votre charge de travail. Avec la couche de calcul sans serveur, vous spécifiez la mise à l’échelle des ressources de calcul sur une plage de calcul configurable. Avec ce niveau de calcul, vous pouvez également automatiquement suspendre et reprendre la base de données en fonction des activités de la charge de travail. Le prix unitaire de vCore par unité de temps est inférieur au niveau de calcul provisionné il se trouve dans la couche de calcul sans serveur.
- [Unité de transaction de base de données (DTU)-en fonction de modèle d’achat](sql-database-service-tiers-dtu.md). Ce modèle d’achat fournit des packages de calcul et de stockage groupés à charge équilibrées pour les charges de travail courantes.

Différents modèles d’achat sont disponibles pour différents modèles de déploiement de base de données SQL Azure :

- Les options de déploiement [Base de données unique](sql-database-single-databases-manage.md) et [Pool élastique](sql-database-elastic-pool.md) d’[Azure SQL Database](sql-database-technical-overview.md) proposent à la fois le [modèle d’achat DTU](sql-database-service-tiers-dtu.md) et le [modèle d’achat vCore](sql-database-service-tiers-vcore.md).
- Le [instance managée](sql-database-managed-instance.md) option de déploiement dans Azure SQL Database offre uniquement la [modèle d’achat vCore](sql-database-service-tiers-vcore.md).
- Le [niveau de service hyperscale](sql-database-service-tier-hyperscale.md) est disponible pour les bases de données uniques qui utilisent le [modèle d’achat vCore](sql-database-service-tiers-vcore.md).

Le tableau et graphique suivants comparent et comparez le-basé sur des Vcores et les modèles d’achat DTU :

|**Modèle d’achat**|**Description**|**Idéal pour**|
|---|---|---|
|Modèle DTU|Ce modèle est basé sur une mesure regroupant des ressources de calcul, stockage et d’e/s. Tailles de calcul sont exprimées en dtu pour les bases de données uniques et en unités de transaction de base de données élastique (Edtu) pour les pools élastiques. Pour plus d’informations sur les dtu et Edtu, consultez [quelles sont les dtu et Edtu ?](sql-database-purchase-models.md#dtu-based-purchasing-model).|Idéal pour les clients qui souhaitent simple, préconfigurée des options de ressources.|
|Modèle vCore|Ce modèle vous permet de sélectionner indépendamment les ressources de calcul et de stockage. Le modèle d’achat vCore vous permet également d’utiliser [Azure Hybrid Benefit pour SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) afin de réduire les coûts.|Idéal pour les clients qui donnent la priorité à la flexibilité, au contrôle et à la transparence.|
||||  

![comparaison des modèles de tarification](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Coûts de calcul

### <a name="provisioned-compute-costs"></a>Coûts de calcul provisionné

Dans le niveau calcul provisionné, le coût de calcul reflète la capacité de calcul total est configurée pour l’application.

Dans le niveau de service Critique pour l’entreprise, nous allouons automatiquement au moins 3 réplicas. Pour refléter cette allocation supplémentaire de ressources de calcul, le prix dans le modèle d’achat vCore est environ 2,7 x plus haut dans le niveau de service critique d’entreprise qu’il se trouve dans le niveau de service usage général. De même, le prix de stockage supérieure par Go dans le niveau de service critique d’entreprise reflète les e/s élevées et une faible latence du stockage SSD.

Le coût du stockage de sauvegarde est le même pour le niveau de service critique d’entreprise et le niveau de service usage général, car les deux niveaux utilisent le stockage standard.

### <a name="serverless-compute-costs"></a>Coûts de calcul sans serveur

Pour obtenir une description de la définition de la capacité de calcul et les coûts sont calculés pour la couche de calcul sans serveur, consultez [SQL de base de données sans serveur (version préliminaire)](sql-database-serverless.md).

## <a name="storage-costs"></a>Coûts de stockage

Les divers types de stockage sont facturés différemment. Pour le stockage de données, vous êtes facturé pour le stockage approvisionné en fonction de la taille maximale de base de données ou le pool que vous sélectionnez. Le coût ne change pas, sauf si vous réduisez ou augmentez cette taille maximum. Le stockage de sauvegarde est associé aux sauvegardes automatisées de votre instance et il est alloué de manière dynamique. Période de rétention de sauvegarde augmente le stockage de sauvegarde qui est consommé par votre instance.

Par défaut, les 7 jours de sauvegardes automatisées de vos bases de données sont copiées vers le compte stockage Blob standard de stockage géoredondant avec accès en lecture (RA-GRS). Ce stockage est utilisé par les sauvegardes complètes hebdomadaires, des sauvegardes différentielles quotidiennes et sauvegardes de journal des transactions, qui sont copiés toutes les 5 minutes. La taille des journaux des transactions varie selon le taux de variation de la base de données. Un volume de stockage minimal égal à 100 pour cent de la taille de la base de données est fourni sans frais supplémentaires. Toute consommation supérieure du stockage de sauvegarde est facturée en Go par mois.

Pour plus d'informations sur les prix du stockage, consultez la page [Tarification](https://azure.microsoft.com/pricing/details/sql-database/single/).

## <a name="vcore-based-purchasing-model"></a>Modèle d’achat vCore

Un noyau virtuel (vCore) représente un processeur logique et vous offre la possibilité de choisir entre plusieurs générations de matériel et les caractéristiques physiques du matériel (par exemple, le nombre de cœurs, mémoire et la taille de stockage). Le modèle d’achat vCore donne une certaine souplesse, de contrôle, de transparence de la consommation des ressources individuelles, un moyen simple de traduire locales et sur les exigences de charge de travail vers le cloud. Ce modèle vous permet de choisir les ressources de calcul, de mémoire et de stockage en fonction des besoins de votre charge de travail.

Dans le modèle d’achat basé sur vCore, vous pouvez choisir entre la [à usage général](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) et [critique pour l’entreprise](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) pour les niveaux de service [bases de données uniques](sql-database-single-database-scale.md), [ les pools élastiques](sql-database-elastic-pool.md), et [instances gérées](sql-database-managed-instance.md). Pour les bases de données uniques, vous pouvez également choisir le [niveau de service hyperscale](sql-database-service-tier-hyperscale.md).

Le modèle d’achat vCore vous permet de choisir les ressources de calcul et de stockage indépendamment, égaler les performances en local et l’optimisation des prix. Dans le modèle d’achat basé sur vCore, vous payez pour :

- Les ressources de calcul (le niveau de service + le nombre de vCores et la quantité de mémoire + la génération du matériel).
- Le type et la quantité de stockage des journaux et des données.
- Stockage de sauvegarde (RA-GRS).

> [!IMPORTANT]
> Ressources de calcul, d’e/s et stockage des journaux et les données sont facturés par pool élastique ou base de données. Stockage de sauvegarde est facturé par chaque base de données. Pour plus d'informations sur les frais liés aux instances gérées, consultez [Instances gérées](sql-database-managed-instance.md).
> **Limitations concernant les régions :** Pour obtenir la liste des régions prises en charge, consultez [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Pour créer une instance gérée dans une région n’est pas pris en charge, [envoyer une demande de support via le portail Azure](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance).

Si votre base de données unique ou d’un pool élastique consomme plus de 300 dtu, la conversion vers le modèle d’achat vCore peut réduire vos coûts. Vous pouvez convertir à l’aide des API de votre choix ou à l’aide du portail Azure, sans temps d’arrêt. Toutefois, la conversion n’est pas nécessaire et n’est pas effectuée automatiquement. Si le modèle d’achat DTU répond à vos besoins métier et à vos besoins en performance, vous devez continuer de l’utiliser.

Pour convertir le modèle d’achat DTU le modèle d’achat vCore, sélectionnez la taille de calcul en utilisant les règles élémentaires suivantes :

- Chaque 100 dtu dans le niveau standard nécessite au moins 1 vCore dans le niveau de service usage général.
- Chaque 125 dtu du niveau premium nécessite au moins 1 vCore dans le niveau de service critique d’entreprise.

## <a name="dtu-based-purchasing-model"></a>Modèle d’achat DTU

Une unité de transaction de base de données (DTU) représente une mesure mixte de processeur, mémoire, lectures et écritures. Le modèle d’achat DTU offre un ensemble préconfiguré de ressources de calcul et de stockage inclus pour obtenir différents niveaux de performance d’application. Si vous préférez la simplicité d’un ensemble préconfiguré et des paiements fixes chaque mois, le modèle basé sur DTU peut être plus adapté à vos besoins.

Dans le modèle d’achat basé sur DTU, vous pouvez choisir entre la base, standard et les niveaux de service premium pour les deux [bases de données uniques](sql-database-single-database-scale.md) et [pools élastiques](sql-database-elastic-pool.md). Le modèle d’achat DTU n’est pas disponible pour [instances gérées](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>Unités de transaction de base de données (DTU)

Pour une base de données à un spécifique à calculer la taille au sein d’un [niveau de service](sql-database-single-database-scale.md), Microsoft garantit un certain niveau de ressources pour cette base de données (indépendamment de toute autre base de données dans le cloud Azure). Cette garantie fournit un niveau de performances prévisible. La quantité de ressources allouées pour une base de données est calculée en tant que nombre de dtu et est une mesure groupée de ressources de calcul, stockage et d’e/s.

Le ratio entre ces ressources est déterminé à l’origine par une [charge de travail de traitement transactionnel en ligne (OLTP) banc d’essai](sql-database-benchmark-overview.md) conçu pour les charges de travail OLTP réelles standard. Lorsque votre charge de travail dépasse la quantité de ces ressources, le débit est limité, ce qui entraîne un ralentissement des performances et des délais d’attente.

Les ressources utilisées par votre charge de travail n’ont aucun impact les ressources disponibles pour les autres bases de données SQL dans le cloud Azure. De même, les ressources utilisées par les autres charges de travail ne pas avoir un impact sur les ressources disponibles pour votre base de données SQL.

![cadre englobant](./media/sql-database-what-is-a-dtu/bounding-box.png)

Dtu sont particulièrement utiles pour comprendre les ressources relatifs qui sont alloués pour les bases de données SQL Azure à des niveaux de service et de calcul différentes tailles. Exemple :

- Le fait de doubler les dtu en augmentant la taille de calcul d’une base de données revient à doubler l’ensemble de ressources disponibles pour cette base de données.
- Une base de P11 de niveau de service premium avec 1750 dtu fournit DTU 350 fois plus puissance de calcul qu’une base de données de niveau de service de base avec 5 dtu.  

Pour mieux connaître la consommation des ressources (DTU) de votre charge de travail, utilisez [insights des performances des requêtes](sql-database-query-performance.md) à :

- Identifier les principales requêtes par UC/durée/nombre d’exécutions qui peut être réglée pour améliorer les performances. Par exemple, une requête d’e/S intensives peut-être bénéficier de [techniques d’optimisation en mémoire](sql-database-in-memory.md) pour mieux utiliser la mémoire disponible à un certain niveau de service et la taille de calcul.
- Explorez les détails d’une requête pour afficher son texte et son historique d’utilisation des ressources.
- Accéder aux recommandations de réglage des performances qui indiquent les actions prises par [SQL Database Advisor](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>Unités de transaction de base de données élastique (Edtu)

Pour les bases de données SQL qui sont toujours disponibles, plutôt que de fournir un ensemble dédié de ressources (dtu) qui ne peut pas toujours être nécessaires, vous pouvez placer ces bases de données dans un [pool élastique](sql-database-elastic-pool.md). Les bases de données dans un pool élastique se trouvent sur un seul serveur de base de données SQL Azure et partagent un pool de ressources.

Les ressources partagées dans un pool élastique sont mesurées en unités de transaction de base de données élastique (Edtu). Les pools élastiques offrent une solution simple et économique pour gérer les objectifs de performances de plusieurs bases de données qui ont des modèles d’utilisation imprévisibles et varient largement. Un pool élastique garantit que toutes les ressources ne peut pas être consommés par une base de données dans le pool, tout en garantissant que chaque base de données dans le pool dispose toujours d’une quantité minimale de ressources nécessaires disponibles.

Un pool bénéficie d’un nombre défini d’eDTU pour un prix donné. Dans le pool élastique, bases de données individuelles peuvent être mise à l’échelle dans les limites configurées. Une base de données sous une charge plus importante consomme davantage d’Edtu pour répondre à la demande. Bases de données sous des charges moindres consommera moins Edtu. Les bases de données qui ne subissent aucune charge ne consomment aucune eDTU. Étant donné que les ressources sont configurées pour l’ensemble du pool, plutôt que par base de données, les pools élastiques simplifient vos tâches de gestion et fournissent un budget prévisible pour le pool.

Vous pouvez ajouter des Edtu à un pool existant sans temps d’arrêt de la base de données et sans aucun impact sur les bases de données dans le pool. De même, si vous n’avez plus besoin Edtu supplémentaires, supprimez-les à partir d’un pool existant à tout moment. Vous pouvez également ajouter des bases de données vers ou soustraire des bases de données à partir d’un pool à tout moment. Pour réserver des Edtu pour les autres bases de données, limitez le nombre d’Edtu qu’une base de données peut utiliser sous une charge importante. Si une base de données underuses régulièrement les ressources, déplacez-le hors du pool et le configurer comme une base de données unique avec une quantité prévisible des ressources requises.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Déterminer le nombre de DTU requises par une charge de travail

Si vous souhaitez migrer un existant en local ou la charge de travail de machine virtuelle SQL Server vers Azure SQL Database, utilisez le [DTU calculator](https://dtucalculator.azurewebsites.net/) pour estimer le nombre de dtu nécessaire. Pour une charge de travail de la base de données SQL Azure, utilisez [insights des performances des requêtes](sql-database-query-performance.md) pour comprendre votre consommation des ressources de base de données (dtu), ainsi que des informations plus approfondies pour optimiser votre charge de travail. Le [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) vue de gestion dynamique (DMV) vous permet d’afficher la consommation des ressources pour la dernière heure. Le [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) affichage catalogue affiche la consommation des ressources au cours des 14 derniers jours, mais avec une précision inférieure des moyennes de cinq minutes.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Charges de travail tirant avantage d’un pool élastique de ressources

Les pools sont parfaitement aux bases de données avec une moyenne de basse-utilisation des ressources et des pics d’utilisation relativement rares. SQL Database évalue l’utilisation de l’historique des ressources de bases de données sur un serveur de base de données SQL existant automatiquement et recommande la configuration de pool approprié dans le portail Azure. Pour plus d’informations, consultez [quand devez-vous envisager d’utiliser un pool élastique SQL Database ?](sql-database-elastic-pool.md).

## <a name="frequently-asked-questions-faqs"></a>Forum Aux Questions (FAQ)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>Dois-je mettre mon application hors ligne pour convertir d’un niveau de service basé sur DTU à un niveau de service basé sur vCore ?

Non. Vous n’avez pas besoin de mettre hors connexion de l’application. Les nouveaux niveaux de service proposent une méthode de conversion en ligne simple qui est similaire au processus existant de la mise à niveau des bases de données à partir de la norme pour le niveau de service premium et l’autre sens. Vous pouvez démarrer cette conversion à l’aide du portail Azure, PowerShell, l’interface CLI, T-SQL ou l’API REST. Consultez [Gérer les ressources pour une base de données unique dans Azure SQL Database](sql-database-single-database-scale.md) et [Les pools élastiques vous aident à gérer et à mettre à l’échelle plusieurs bases de données Microsoft Azure SQL](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Puis-je convertir une base de données à partir d’un niveau de service dans le modèle d’achat vCore vers un niveau de service dans le modèle d’achat DTU ?

Oui, vous pouvez facilement convertir votre base de données à des objectifs de performance pris en charge à l’aide du portail Azure, PowerShell, l’interface CLI, T-SQL ou l’API REST. Consultez [Gérer les ressources pour une base de données unique dans Azure SQL Database](sql-database-single-database-scale.md) et [Les pools élastiques vous aident à gérer et à mettre à l’échelle plusieurs bases de données Microsoft Azure SQL](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur le modèle d’achat vCore, consultez [modèle d’achat vCore](sql-database-service-tiers-vcore.md).
- Pour plus d’informations sur le modèle d’achat DTU, consultez [modèle d’achat DTU](sql-database-service-tiers-dtu.md).
