---
title: Modèles d’achat Azure SQL Database | Microsoft Docs
description: En savoir plus sur les modèles d’achat disponibles dans le service Azure SQL Database
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
ms.date: 02/08/2019
ms.openlocfilehash: 46a620900896d07273da22e53171330b85d3f1ec
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59360194"
---
# <a name="azure-sql-database-purchasing-models"></a>Modèles d’achat Azure SQL Database

Azure SQL Database vous permet d’acheter facilement un moteur de base de données PaaS adapté à vos exigences en terme de performances et de coûts. Selon le modèle de déploiement Azure SQL Database, vous pouvez sélectionner le modèle d’achat adapté à vos besoins :

- [Modèle d’achat vCore](sql-database-service-tiers-vcore.md) (recommandé), qui vous permet de choisir la capacité de stockage et de calcul exacte dont vous avez besoin pour votre charge de travail.
- [Modèle d’achat DTU](sql-database-service-tiers-dtu.md), qui vous permet de choisir des packages de calcul et de stockage groupés et équilibrés pour des charges de travail courantes.

Différents modèles d’achat sont disponibles dans les modèles de déploiement Azure SQL Database :

- Les options de déploiement [Base de données unique](sql-database-single-databases-manage.md) et [Pool élastique](sql-database-elastic-pool.md) d’[Azure SQL Database](sql-database-technical-overview.md) proposent à la fois le [modèle d’achat DTU](sql-database-service-tiers-dtu.md) et le [modèle d’achat vCore](sql-database-service-tiers-vcore.md).
- L'option de déploiement [Instance managée](sql-database-managed-instance.md) d'Azure SQL Database propose uniquement le [modèle d'achat vCore](sql-database-service-tiers-vcore.md).

> [!IMPORTANT]
> Le [niveau de service Hyperscale (préversion)](sql-database-service-tier-hyperscale.md) est uniquement disponible en préversion publique pour les bases de données uniques qui utilisent le modèle d'achat vCore.

Le tableau et le graphique suivants comparent ces deux modèles d’achat.

|**Modèle d’achat**|**Description**|**Idéal pour**|
|---|---|---|
|Modèle DTU|Ce modèle est basé sur une mesure regroupant des ressources de calcul, de stockage et d’E/S. Les tailles de calcul sont exprimées en unités de transaction de base de données (DTU) pour les bases de données uniques, et en unités de transaction de base de données élastique (eDTU) pour les pools élastiques. Pour en savoir plus sur les DTU et les eDTU, voir [Explication des unités de transaction de base de données (DTU) et des unités de transaction de base de données élastique (eDTU)](sql-database-purchase-models.md#dtu-based-purchasing-model).|Idéal pour les clients qui souhaitent des options de ressources préconfigurées et simples.|
|Modèle vCore|Ce modèle vous permet de sélectionner indépendamment les ressources de calcul et de stockage. Le modèle d’achat vCore vous permet également d’utiliser [Azure Hybrid Benefit pour SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) afin de réduire les coûts.|Idéal pour les clients qui donnent la priorité à la flexibilité, au contrôle et à la transparence.|
||||  

![Modèle de prix](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Coûts de calcul

Le coût associé au calcul reflète la capacité de calcul totale provisionnée pour l’application. Dans le niveau de service Critique pour l’entreprise, nous allouons automatiquement au moins 3 réplicas. Pour refléter cette allocation supplémentaire de ressources de calcul, le prix du modèle d’achat vCore du niveau de service Critique pour l'entreprise est approximativement 2,7 fois supérieur à celui du niveau de service Usage général. Pour la même raison, le prix plus élevé du stockage par Go dans le niveau de service Critique pour l’entreprise reflète le nombre important d’E/S et la faible latence du stockage SSD. Dans le même temps, le coût du stockage de sauvegarde n’est pas différent entre ces deux niveaux de service, car dans les deux cas, nous utilisons une classe de stockage standard.

## <a name="storage-costs"></a>Coûts de stockage

Les divers types de stockage sont facturés différemment. Pour le stockage des données, vous êtes facturé en fonction du stockage provisionné, sur la base de la taille maximum de la base de données ou du pool que vous sélectionnez. Le coût ne change pas, sauf si vous réduisez ou augmentez cette taille maximum. Le stockage de sauvegarde est associé aux sauvegardes automatisées de votre instance et il est alloué de manière dynamique. L’allongement de la période de rétention des sauvegardes a pour effet d’augmenter le volume de stockage de sauvegarde que votre instance utilise.

7 jours de sauvegardes automatisées de vos bases de données sont copiés par défaut dans le stockage blob RA-GRS standard. Le stockage est utilisé pour des sauvegardes complètes hebdomadaires, des sauvegardes différentielles quotidiennes et des sauvegardes de fichiers journaux copiés toutes les 5 minutes. La taille du journal des transactions dépend la fréquence de changement de la base de données. Une quantité de stockage minimal égale à 100 % de la taille de la base de données est fourni sans frais supplémentaires. Toute consommation supérieure de stockage de sauvegarde est facturée en Go/mois.

Pour plus d'informations sur les prix du stockage, consultez la page [Tarification](https://azure.microsoft.com/pricing/details/sql-database/single/).

## <a name="vcore-based-purchasing-model"></a>Modèle d’achat vCore

Un vCore représente l’UC logique offerte avec une option permettant de choisir entre plusieurs générations de matériel et de caractéristiques physiques du matériel (par exemple, le nombre de cœurs, la mémoire, la taille de stockage). Le modèle d’achat vCore apporte flexibilité, contrôle et transparence pour la consommation des ressources individuelles. C’est aussi un moyen facile de traduire les exigences des charges de travail locales pour le cloud. Ce modèle permet de sélectionner le calcul, la mémoire et le stockage en fonction des besoins des charges de travail. Avec le modèle d’achat vCore, vous avez le choix entre les niveaux de service [Usage général](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) et [Critique pour l'entreprise](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) pour les [bases de données uniques](sql-database-single-database-scale.md), les [pools élastiques](sql-database-elastic-pool.md) et les [instances managées](sql-database-managed-instance.md). Pour les bases de données uniques, vous pouvez également choisir le [niveau de service Hyperscale (préversion)](sql-database-service-tier-hyperscale.md).

Le modèle d’achat vCore vous permet de sélectionner les ressources de calcul et de stockage indépendamment les unes des autres, d’égaler les performances d’une exécution locale et d’optimiser les coûts. Dans le modèle d’achat vCore, les clients paient pour :

- Le calcul (niveau de service + nombre de vCore + quantité de mémoire + génération du matériel)
- Le type et la quantité de stockage des journaux et des données
- Le stockage de sauvegarde (RA-GRS)

> [!IMPORTANT]
> Le calcul, les E/S, ainsi que le stockage des données et des journaux, sont facturés au niveau de chaque base de données ou au niveau du pool élastique. Le stockage des sauvegardes est facturé au niveau de chaque base de données. Pour plus d'informations sur les frais liés aux instances gérées, consultez [Instances gérées](sql-database-managed-instance.md).
> **Limitations concernant les régions :** Pour obtenir la liste des régions prises en charge, consultez [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Si vous voulez créer une instance Managed Instance dans la région qui n’est actuellement pas prise en charge, vous pouvez [envoyer demande de support par le biais du portail Azure](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance).
.

Si votre base de données unique ou votre pool élastique consomme plus de 300 DTU, le passage au modèle d'achat vCore peut réduire les coûts. Si vous optez pour la conversion, vous pouvez passer au modèle vCore à l’aide de l’API de votre choix ou du portail Azure, sans aucun temps d’arrêt. Toutefois, ce passage n'est ni obligatoire ni automatique. Si le modèle d’achat DTU répond à vos besoins métier et à vos besoins en performance, vous devez continuer de l’utiliser. Si vous décidez de passer du modèle d’achat DTU au modèle d’achat vCore, sélectionnez la taille de calcul en suivant les règles élémentaires ci-dessous :

- Chaque groupe de 100 DTU du niveau Standard nécessite au moins 1 vCore dans le niveau Usage général
- Chaque groupe de 125 DTU du niveau Premium nécessite au moins 1 vCore dans le niveau Critique pour l'entreprise

## <a name="dtu-based-purchasing-model"></a>Modèle d’achat DTU

L’unité DTU (Database Transaction Unit) correspond à un mélange de mesures d’UC, de mémoire, de lectures et d’écritures. Le modèle d’achat DTU offre un ensemble préconfiguré de ressources de calcul et de stockage inclus pour obtenir différents niveaux de performance d’application. Les clients qui préfèrent la simplicité d’une offre groupée préconfigurée et de versements mensuels peuvent trouver le modèle DTU mieux adapté à leurs besoins. Avec le modèle d’achat DTU, les clients ont le choix entre les niveaux de service **De base**, **Standard** et **Premium** pour les [bases de données uniques](sql-database-single-database-scale.md) et les [pools élastiques](sql-database-elastic-pool.md). Ce modèle d’achat n’est pas disponible dans les [instances gérées](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>Unités de transaction de base de données (DTU)

Pour les bases de données uniques ayant une taille de calcul spécifique et appartenant à [niveau de service](sql-database-single-database-scale.md), Microsoft garantit un certain niveau de ressources (indépendamment de toute autre base de données dans le cloud Azure), et donc un niveau de performances prévisible. Le volume de ressources est calculé sous forme d’unités de transactions de base de données ou DTU. Il s’agit d’une mesure groupée de calcul, de stockage et de ressources d’E/S. Le ratio entre ces ressources a été déterminé à l’origine par une [charge de travail d’évaluation OLTP](sql-database-benchmark-overview.md), conforme aux charges de travail OLTP réelles standard. Quand votre charge de travail dépasse la quantité de ces ressources, le débit est limité, ce qui entraîne un ralentissement des performances et des délais d’attente. Les ressources utilisées par votre charge de travail n’impactent pas les ressources disponibles pour les autres bases de données SQL dans le cloud Azure, et les ressources utilisées par d’autres charges de travail n’impactent pas les ressources disponibles pour votre base de données SQL.

![cadre englobant](./media/sql-database-what-is-a-dtu/bounding-box.png)

Les DTU sont particulièrement utiles pour comprendre la quantité relative de ressources des bases de données Azure SQL ayant différentes tailles de calcul et différents niveaux de service. Par exemple, le fait de doubler les DTU en augmentant la taille de calcul d’une base de données revient à doubler l’ensemble des ressources disponibles pour cette base de données. Par exemple, une base de données Premium P11 comprenant 1 750 DTU fournit une puissance de calcul DTU 350 fois plus importante qu’une base de données de base comprenant 5 DTU.  

Afin d'avoir une idée plus précise de la consommation de ressources (DTU) de votre charge de travail, utilisez l'[analyse des performances des requêtes](sql-database-query-performance.md) pour :

- Identifier les principales requêtes par UC/durée/nombre d’exécutions qui peuvent être réglées pour améliorer les performances. Par exemple, une requête utilisant beaucoup d’E/S peut tirer profit de l’utilisation de [techniques d’optimisation en mémoire](sql-database-in-memory.md) pour mieux utiliser la mémoire disponible avec un certain niveau de service et une certaine taille de calcul.
- Connaître les détails d’une requête, afficher son texte et l’historique d’utilisation des ressources.
- Accéder aux recommandations de réglage des performances qui indiquent les actions effectuées par [SQL Database Advisor](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>Unités de transaction de base de données élastique (eDTU)

Plutôt que de fournir un ensemble de ressources (DTU) dédié qui peut ne pas s’avérer toujours nécessaire pour une base de données SQL Database qui est toujours disponible, vous pouvez placer les bases de données dans un [pool élastique](sql-database-elastic-pool.md) sur un serveur SQL Database qui partage un pool de ressources entre ces bases de données. Les ressources partagées dans un pool élastique sont mesurées en eDTU (elastic Database Transaction Unit). Les pools élastiques offrent une solution simple et économique pour gérer les objectifs de performance de plusieurs bases de données ayant des modèles d’utilisation variables et non prévisibles. Un pool élastique garantit que les ressources ne peuvent pas être consommées par une base de données dans le pool, mais également que chaque base de données dans le pool dispose toujours d’une quantité de ressources minimale.

Un pool bénéficie d’un nombre défini d’eDTU pour un prix donné. Au sein du pool élastique, les différentes bases de données peuvent s’adapter facilement et automatiquement aux limites définies. Une base de données soumise à une charge plus élevée consomme plus d’eDTU pour répondre à la demande. Les bases de données soumises à des charges plus légères consomment moins d’eDTU. Les bases de données qui ne subissent aucune charge ne consomment aucune eDTU. Le provisionnement des ressources pour l’ensemble du pool, plutôt que pour chaque base de données, simplifie les tâches de gestion, fournissant un aperçu du budget nécessaire pour le pool.

Vous pouvez ajouter des eDTU à un pool existant sans que les bases de données du pool ne connaissent de temps d’arrêt et ne soient affectées. De même, si les eDTU supplémentaires ne sont plus nécessaires, elles peuvent être supprimées à partir d’un pool existant à tout moment. Vous pouvez ajouter des bases de données dans le pool ou en supprimer. Vous pouvez également limiter la quantité d’eDTU qu’une base de données peut utiliser en cas de charge importante pour réserver des eDTU pour les autres bases de données. Si vous prévoyez qu’une base de données sous-utilise des ressources, vous pouvez la déplacer hors du pool et la configurer en tant que base de données unique avec une quantité prévisible des ressources nécessaires.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Déterminer le nombre de DTU requises par une charge de travail

Si vous souhaitez migrer une charge de travail de machine virtuelle SQL Server ou locale existante vers Azure SQL Database, vous pouvez utiliser l'outil [DTU Calculator](https://dtucalculator.azurewebsites.net/) pour évaluer approximativement le nombre de DTU requises. Dans le cas d'une charge de travail Azure SQL Database existante, vous pouvez utiliser l'[analyse des performances des requêtes](sql-database-query-performance.md) pour évaluer la consommation de ressources (DTU) de votre base de données et obtenir une analyse plus approfondie afin d'optimiser votre charge de travail. Vous pouvez également utiliser la DMV [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) pour voir la consommation des ressources au cours de la dernière heure. Sinon, la vue de catalogue [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) affiche la consommation des ressources pour les 14 derniers jours, mais avec une précision inférieure (moyennes de cinq minutes).

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Charges de travail tirant avantage d’un pool élastique de ressources

Les pools sont idéaux dans le cas de nombreuses bases de données avec des modèles d’utilisation spécifiques. Pour une base de données indiquée, ce modèle se caractérise par une moyenne d’utilisation faible avec des pics d’utilisation relativement rares. SQL Database évalue automatiquement l’historique d’utilisation en ressources des bases de données dans un serveur SQL Database existant et recommande la configuration de pool appropriée dans le portail Azure. Pour plus d’informations, consultez l’article [Quand utiliser un pool élastique ?](sql-database-elastic-pool.md).

## <a name="purchase-model-frequently-asked-questions-faq"></a>Forum Aux Questions (FAQ) - Modèle d'achat

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-database-to-a-vcore-based-service-tier"></a>Dois-je mettre mon application hors connexion pour convertir une base de données basée sur des DTU en niveau de service basé sur des cœurs virtuels ?

Les nouveaux niveaux de service offrent une méthode de conversion en ligne simple qui est similaire au processus actuel de mise à niveau des bases de données du niveau de service Standard au niveau Premium et inversement. Cette conversion peut être lancée à l’aide du portail Azure, de PowerShell, d’Azure CLI ou des API REST. Consultez [Gérer les ressources pour une base de données unique dans Azure SQL Database](sql-database-single-database-scale.md) et [Les pools élastiques vous aident à gérer et à mettre à l’échelle plusieurs bases de données Microsoft Azure SQL](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-using-the-vcore-based-purchase-to-a-service-tier-using-the-dtu-based-purchasing-model"></a>Puis-je convertir une base de données d’un niveau de service utilisant le modèle d’achat vCore vers un niveau de service utilisant le modèle d’achat DTU ?

Oui, vous pouvez facilement convertir votre base de données vers des objectifs de performance pris en charge à l’aide du portail ou par programme en utilisant le portail Azure, PowerShell, Azure CLI, T-SQL ou les API REST. Consultez [Gérer des bases de données uniques](sql-database-single-database-scale.md) et [Gérer des pools élastiques](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour le modèle d’achat vCore, voir [Modèle d’achat vCore](sql-database-service-tiers-vcore.md).
- Pour le modèle d’achat DTU, consultez [Modèle d’achat DTU](sql-database-service-tiers-dtu.md).
