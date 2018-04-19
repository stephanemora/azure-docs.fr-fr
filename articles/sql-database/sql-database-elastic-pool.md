---
title: Gérer plusieurs bases de données SQL avec les pools élastiques - Azure | Microsoft Docs
description: Gérez et mettez à l’échelle plusieurs bases de données SQL (des centaines et des milliers) avec des pools élastiques. Un prix unique pour des ressources que vous pouvez distribuer là où vous en avez besoin.
keywords: plusieurs bases de données, ressources des bases de données, performances des bases de données
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.date: 04/04/2018
ms.author: ninarn
ms.topic: article
ms.openlocfilehash: 6c2e4e7f99aeec3028e8df520dc6896234b5c969
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2018
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Les pools élastiques vous aident à gérer et à mettre à l’échelle plusieurs bases de données Microsoft Azure SQL

Les pools élastiques SQL Database représentent une solution simple et rentable de gestion et de mise à l’échelle de plusieurs bases de données qui ont des demandes d’utilisation variables et imprévisibles. Les bases de données d’un pool élastique se trouvent sur un seul serveur Azure SQL Database et partagent un nombre défini de ressources à prix fixe. Les pools élastiques dans Azure SQL Database permettent aux développeurs SaaS d’optimiser le rapport performance/prix pour un groupe de bases de données dans un budget prescrit tout en offrant une élasticité des performances pour chaque base de données.

## <a name="what-are-sql-elastic-pools"></a>Que sont les pools élastiques SQL ?

Les développeurs SaaS créent des applications qui reposent sur des couches de données à grande échelle composées de plusieurs bases de données. Un modèle d’application courant consiste à approvisionner une base de données pour chaque client. Toutefois, les modèles d’utilisation sont souvent imprévisibles et varient d’un client à l’autre, et il est difficile de prévoir les besoins en ressources de chaque utilisateur de base de données spécifique. Vous disposez généralement de deux options :

- Sur-approvisionner les ressources en fonction de l’utilisation maximale et payer trop, ou
- Sous-approvisionner pour réduire les coûts, au détriment des performances et de la satisfaction des clients au moment des pics d’utilisation.

Les pools élastiques résolvent ce problème en vous assurant que les bases de données disposent des ressources de performances nécessaires au moment où elles en ont besoin. Ils représentent un mécanisme simple d’allocation de ressources avec un budget prévisible. Pour en savoir plus sur les modèles de conception pour les applications SaaS avec des pools élastiques, consultez [Modèles de conception pour les applications SaaS multilocataires avec Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

Un pool élastique est un pool que partagent plusieurs bases de données, et pour lequel le développeur peut acheter des ressources lorsque les bases de données l’utilisent de manière imprévisible. Vous pouvez configurer les ressources du pool avec le [modèle d’achat DTU (en préversion)](sql-database-service-tiers.md#dtu-based-purchasing-model) ou le [modèle d’achat vCore (en préversion)](sql-database-service-tiers.md#vcore-based-purchasing-model-preview). Le besoin en ressources d’un pool est déterminé par l’utilisation globale de ses bases de données. La quantité de ressources disponibles pour le pool dépend du budget du développeur. Le développeur ajoute des bases de données au pool, définit le nombre minimal et maximal de ressources pour les bases de données (le nombre minimal ou maximal de DTU ou de vCore, selon le modèle d’allocation des ressources choisi), puis définit les ressources du pool en fonction du budget. Un développeur peut utiliser des pools pour faire évoluer en toute transparence son service en passant d’une lean startup à une entreprise mature à une vitesse sans cesse croissante.

Au sein du pool, les différentes bases de données peuvent en toute souplesse s’adapter automatiquement en fonction des paramètres définis. Si la charge est élevée, une base de données peut consommer plus de ressources pour répondre à la demande. Les bases de données soumises à des charges légères en consomment moins, et celles qui ne sont soumises à aucune charge n’en consomment pas du tout. L’approvisionnement des ressources pour l’ensemble du pool plutôt que pour des bases de données uniques simplifie vos tâches de gestion. En outre, vous disposez d’un budget prévisible pour le pool. Vous pouvez ajouter des ressources à un pool existant sans arrêter les bases de données. Toutefois, un tel ajout peut nécessiter le déplacement des bases de données pour la nouvelle réservation d’eDTU. De même, si les ressources supplémentaires ne sont plus nécessaires, elles peuvent être supprimées du pool existant à tout moment. De plus, vous pouvez ajouter des bases de données au pool ou en retirer. Si une base de données finit par sous-utiliser les ressources, retirez-la.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Quand devez-vous envisager d’utiliser un pool élastique SQL Database ?

Les pools sont idéaux dans le cas de nombreuses bases de données avec des modèles d’utilisation spécifiques. Pour une base de données indiquée, ce modèle se caractérise par une faible utilisation moyenne avec des pics d'utilisation relativement rares.

Plus vous ajoutez de bases de données à un pool, plus vous faites d'économies. En fonction de votre modèle d’utilisation de l’application, il est possible de faire des économies avec seulement deux bases de données S3.

Les sections suivantes vous aident à comprendre comment évaluer si votre collection de bases de données spécifique peut tirer profit de l’utilisation d’un pool. Les exemples utilisent les pools Standard, mais ces principes s’appliquent également aux pools De base et Premium.

### <a name="assessing-database-utilization-patterns"></a>Évaluation des modèles d'utilisation de base de données

La figure suivante montre l’exemple d'une base de données qui est très souvent inactive, mais qui connaît de temps en temps des pics d’activité. Il s’agit d’un modèle d’utilisation particulièrement adapté à un pool :

   ![une base de données unique adaptée à un pool](./media/sql-database-elastic-pool/one-database.png)

Pour la période de cinq minutes illustrée, DB1 culmine à 90 DTU, mais son utilisation moyenne totale est inférieure à cinq DTU. Un niveau de performance S3 est requis pour exécuter cette charge de travail dans une base de données unique. Cependant, cela laisse la plupart des ressources inutilisées pendant les périodes de faible activité.

Un pool permet de partager ces DTU inutilisées entre plusieurs bases de données, ce qui réduit la quantité totale de DTU nécessaires et le coût global.

En s'appuyant sur l'exemple précédent, supposons qu'il y ait des bases de données supplémentaires avec des modèles d'utilisation similaires comme DB1. Dans les deux figures ci-dessous, l’utilisation de quatre bases de données et celle de 20 bases de données sont représentées sur le même graphe pour montrer qu’avec le modèle d’achat DTU, leur utilisation ne se chevauche pas dans le temps :

   ![quatre bases de données avec un modèle d’utilisation adapté à un pool](./media/sql-database-elastic-pool/four-databases.png)

   ![vingt bases de données avec un modèle d’utilisation adapté à un pool](./media/sql-database-elastic-pool/twenty-databases.png)

L’utilisation globale des DTU pour les 20 bases de données est illustrée par la ligne noire dans la figure ci-dessus. Cela montre que l'utilisation globale des DTU ne dépasse jamais 100 DTU et que les 20 bases de données peuvent partager 100 eDTU sur cette période. Le nombre de DTU est diminué par 20 et le prix par 13 par rapport au placement de chacune des bases de données dans des niveaux de performances S3 pour les bases de données uniques.

Cet exemple est idéal pour les raisons suivantes :

* Il existe de grandes différences entre les pics d’utilisation et l'utilisation moyenne par base de données.
* Les pics d’utilisation de chaque base de données se produisent à différents moments dans le temps.
* Les eDTU sont partagées entre plusieurs bases de données.

Le prix d’un pool dépend des eDTU du pool. Alors que le prix unitaire d’une eDTU pour un pool est 1,5 fois supérieur au prix unitaire d’une DTU pour une base de données unique, les **eDTU de pool peuvent être partagées avec de nombreuses bases de données ; un nombre moins important d’eDTU est donc requis au total**. Ces différences en matière de prix et de partage des eDTU constituent la base du potentiel d'économies que les pools peuvent présenter.

Les règles élémentaires suivantes relatives au nombre de bases de données et à l’utilisation des bases de données permettent de s’assurer qu’un pool coûte moins cher que l’utilisation de niveaux de performances pour des bases de données uniques.

### <a name="minimum-number-of-databases"></a>Nombre minimal de bases de données

Si la quantité de ressources des bases de données est supérieure à une fois et demi celle des ressources nécessaires pour le pool, l’utilisation d’un pool élastique est plus rentable.

***Exemple de modèle d’achat DTU***<br>
Au moins deux bases de données S3 ou au moins 15 bases de données S0 sont nécessaires pour qu’un pool de 100 eDTU soit plus rentable que l’utilisation de niveaux de performances pour des bases de données uniques.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Nombre maximal de bases de données connaissant un pic simultané

Lorsque les ressources sont partagées, toutes les bases de données d’un pool ne peuvent pas utiliser simultanément ces ressources et atteindre la limite définie pour chaque base de données unique. Plus le nombre de bases de données connaissant un pic simultané est faible, plus le nombre de ressources du pool peut être revu à la baisse et plus le pool devient rentable. En général, pas plus de deux tiers (ou 67 %) des bases de données du pool ne doivent connaître un pic simultané et atteindre la limite de ressources.

***Exemple de modèle d’achat DTU***<br>
Pour réduire les coûts pour trois bases de données S3 dans un pool de 200 eDTU, au moins deux de ces bases de données peuvent connaître un pic simultané au niveau de leur utilisation. Sinon, si plus de deux de ces quatre bases de données S3 connaissent un pic simultané, le pool devra être redimensionné à plus de 200 eDTU. Si le pool est redimensionné à plus de 200 eDTU, vous devez ajouter plusieurs bases de données S3 au pool pour maintenir des coûts inférieurs aux niveaux de performances pour les bases de données uniques.

Notez que cet exemple ne tient pas compte de l'utilisation des autres bases de données dans le pool. Si toutes les bases de données connaissent une utilisation à un moment donné, moins de 2/3 (ou 67 %) des bases de données peuvent connaître un pic simultané.

### <a name="resource-utilization-per-database"></a>Utilisation des ressources par base de données
Une différence importante entre le pic d’utilisation et l'utilisation moyenne d’une base de données indique de longues périodes de faible utilisation et de courtes périodes d'utilisation intensive. Ce modèle d'utilisation est idéal pour partager des ressources entre les bases de données. Une base de données doit être envisagée pour un pool quand son pic d’utilisation est environ 1,5 fois supérieur à son utilisation moyenne.

***Exemple de modèle d’achat DTU***<br>
Une base de données S3 qui culmine à 100 DTU et qui utilise en moyenne 67 DTU ou moins est un bon candidat pour le partage des eDTU dans un pool. Sinon, une base de données S1 qui culmine à 20 DTU et qui utilise en moyenne 13 DTU ou moins est un bon candidat à un pool.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Comment choisir la bonne taille de pool ?

Pour un pool, la taille optimale dépend du nombre global de ressources nécessaires pour toutes les bases de données du pool. Cela implique de déterminer ce qui suit :

* Nombre maximal de ressources utilisées par toutes les bases de données du pool (c’est-à-dire, le nombre maximal de DTU ou de vCore, selon le modèle d’allocation des ressources choisi)
* Nombre maximal d’octets de stockage que se partagent toutes les bases de données du pool.

Pour connaître les niveaux de service disponibles pour chacun des modèles de ressources, consultez le [modèle d’achat DTU ](sql-database-service-tiers.md#dtu-based-purchasing-model) ou le [modèle d’achat vCore (en préversion)](sql-database-service-tiers.md#vcore-based-purchasing-model-preview).

La base de données SQL évalue automatiquement l’historique d’utilisation en ressources des bases de données dans un serveur de base de données SQL existant et recommande la configuration de pool appropriée dans le portail Azure. En plus des recommandations, une expérience intégrée estime l’utilisation d’eDTU pour un groupe personnalisé de bases de données sur le serveur. Cela vous permet de faire une analyse de scénarios en ajoutant de façon interactive des bases de données au pool et en les supprimant pour obtenir l’analyse de l’utilisation des ressources et des conseils de dimensionnement avant de valider vos modifications. Pour afficher une procédure, consultez [Surveiller, gérer et dimensionner un pool élastique](sql-database-elastic-pool-manage-portal.md).

Dans les cas où vous ne pouvez pas utiliser les outils, la procédure pas à pas suivante peut vous aider à estimer si un pool est plus économique que les bases de données uniques :

1. Pour estimer le nombre d’eDTU ou de vCore nécessaires pour le pool, procédez de la manière suivante :

   Pour le modèle d’achat DTU : MAX(<*nombre total de bases de données* X *utilisation DTU moyenne par base de données*>,<br>
   <*Nombre de bases de données connaissant un pic simultané* X *utilisation DTU maximale par base de données*)

   Pour le modèle d’achat vCore : MAX(<*nombre total de bases de données* X *utilisation vCore moyenne par base de données*>,<br>
   <*Nombre de bases de données connaissant un pic simultané* X *utilisation vCore maximale par base de données*)

2. Estimez l’espace de stockage nécessaire pour le pool en ajoutant le nombre d’octets nécessaires pour toutes les bases de données du pool. Déterminez ensuite la taille du pool d’eDTU qui fournit cette quantité de stockage.
3. Pour le modèle d’achat DTU, prenez la plus grande des estimations d’eDTU de l’étape 1 et de l’étape 2. Pour le modèle d’achat vCore, prenez l’estimation vCore de l’étape 1.
4. Dans la [page des prix de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/), recherchez le plus petit pool dont la taille est supérieure à l’estimation de l’étape 3.
5. Comparez le prix du pool trouvé à l’étape 5 à celui de l’utilisation des niveaux de performances appropriés pour les bases de données uniques.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Utilisation d’autres fonctionnalités SQL Database avec des pools élastiques

### <a name="elastic-jobs-and-elastic-pools"></a>Travaux élastiques et pools élastiques

Un pool simplifie les tâches de gestion grâce à l’exécution des scripts dans des **[tâches élastiques](sql-database-elastic-jobs-overview.md)**. Un travail élastique élimine pratiquement le caractère fastidieux d’un nombre élevé de bases de données. Pour commencer, consultez l’article [Prise en main de Tâches de bases de données élastiques](sql-database-elastic-jobs-getting-started.md).

Pour en savoir plus sur les autres outils de base de données permettant d’utiliser plusieurs bases de données, consultez l’article [Montée en charge avec la base de données SQL Azure](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Options de continuité de l’activité pour les bases de données d’un pool élastique
Les bases de données regroupées prennent généralement en charge les mêmes [fonctionnalités de continuité d’activité](sql-database-business-continuity.md) que celles disponibles pour les bases de données uniques.

- **Limite de restauration dans le temps** : la limite de restauration dans le temps utilise les sauvegardes automatiques de base de données pour récupérer une base de données d’un pool à un moment précis dans le temps. Voir [Limite de restauration dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore)

- **Géorestauration** : la géorestauration constitue l’option de récupération par défaut lorsqu’une base de données est indisponible en raison d’un incident survenu dans la région où elle est hébergée. Voir [Restaurer une base de données SQL Azure ou basculer vers une base de données secondaire](sql-database-disaster-recovery.md)

- [Géoréplication active](sql-database-geo-replication-overview.md) : pour les applications qui ont des exigences de récupération plus agressives que ce qu’offre la géorestauration, configurez la **géoréplication active**.

## <a name="manage-elastic-pools-and-databases-using-the-azure-portal"></a>Gérer des pools élastiques et des bases de données à l’aide du portail Azure

### <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Création d’un pool élastique SQL Database à l’aide du portail Azure

Vous pouvez créer un pool élastique dans le portail Azure de deux façons.
1. Vous pouvez également créer un pool élastique en recherchant **pool élastique SQL** dans la **Place de marché** ou en cliquant sur **+Ajouter** dans le panneau de recherche des pools élastiques SQL. Ce workflow d’approvisionnement de pool vous permet d’indiquer un nouveau serveur ou un serveur existant.
2. Vous pouvez également créer un pool élastique en accédant à un serveur SQL existant, puis en cliquant sur **Créer un pool** pour créer un pool directement sur ce serveur. Ici, la seule différence est que vous n’avez pas à effectuer l’étape où vous spécifiez le serveur pendant le provisionnement du pool.

> [!NOTE]
> Vous pouvez créer plusieurs pools sur un serveur, mais il est impossible d’ajouter des bases de données de différents serveurs dans le même pool.

Le niveau de service du pool détermine les fonctionnalités disponibles pour les bases de données élastiques du pool, ainsi que le nombre maximal de ressources pour chaque base de données. Pour plus d’informations, consultez les limites de ressources des pools élastiques dans le [modèle DTU](sql-database-dtu-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels) et le [modèle vCore](sql-database-vcore-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

Pour configurer les ressources et les prix du pool, cliquez sur **Configurer le pool**. Ensuite, sélectionnez un niveau de service, ajoutez les bases de données au pool, puis configurez les limites de ressources pour le pool et ses bases de données.

Lorsque vous avez terminé la configuration du pool, vous pouvez cliquer sur Appliquer, nommer le pool, puis cliquer sur OK pour créer le pool.

### <a name="monitor-an-elastic-pool-and-its-databases"></a>Surveiller un pool élastique et ses bases de données

Dans le portail Azure, vous pouvez surveiller l’utilisation d’un pool élastique et des bases de données que contient ce pool. Vous pouvez également apporter un ensemble de modifications à votre pool élastique et soumettre toutes les modifications en même temps. Ces modifications incluent l’ajout ou la suppression de bases de données, ainsi que le changement des paramètres du pool élastique ou des bases de données.

Pour commencer le monitoring d’un pool élastique, ouvrez un pool dans le portail. Vous verrez d’abord une vue d’ensemble de l’état du pool élastique. notamment :

* Graphiques de surveillance illustrant l’utilisation des ressources du pool élastique
* Alertes et recommandations récentes, si disponibles, pour le pool élastique

L’illustration suivante montre un exemple de pool élastique :

![Affichage du pool](./media/sql-database-elastic-pool-manage-portal/basic.png)

Si vous souhaitez plus d’informations sur le pool, vous pouvez cliquer sur les informations fournies dans la vue d’ensemble. Lorsque vous cliquez sur le graphique **Utilisation des ressources**, vous êtes dirigé vers la vue Surveillance d’Azure, où vous pouvez personnaliser les métriques et la fenêtre de temps indiquées dans le graphique. Lorsque vous cliquez sur les notifications disponibles, vous êtes dirigé vers un panneau comprenant les détails de cette alerte ou recommandation.

Si vous souhaitez surveiller les bases de données à l’intérieur du pool, vous pouvez cliquer sur **Utilisation des ressources de base de données** dans la section **Surveillance** du menu de ressources situé sur la gauche.

![Page Utilisation des ressources de base de données](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

#### <a name="to-customize-the-chart-display"></a>Pour personnaliser l’affichage du graphique

Vous pouvez modifier le graphique et la page Métrique pour afficher d’autres métriques, telles que le pourcentage d’UC, le pourcentage d’E/S des données et le pourcentage d’E/S des fichiers journaux utilisés.

Dans le formulaire **Modifier le graphique**, vous pouvez sélectionner un intervalle de temps fixe ou cliquer sur **Personnalisé** pour sélectionner n’importe quelle fenêtre de 24 heures des deux semaines passées, puis sélectionner les ressources à surveiller.

#### <a name="to-select-databases-to-monitor"></a>Pour sélectionner les bases de données à surveiller

Par défaut, le graphique du panneau **Utilisation des ressources de base de données** affiche les 5 premières bases de données par quantité de DTU ou d’UC (en fonction du niveau de service). Dans la liste située sous le graphique, vous pouvez activer et désactiver les bases de données en cochant et en décochant les cases situées sur la gauche.

Vous pouvez également sélectionner plus de métriques et les afficher côte à côte dans la table de base de données pour obtenir une vue plus complète des performances de vos bases de données.

Pour plus d'informations, voir [Créer des alertes SQL Database dans le portail Azure](sql-database-insights-alerts-portal.md).

### <a name="manage-an-elastic-pool-and-its-databases"></a>Gérer un pool élastique et ses bases de données

Tous les paramètres du pool se trouvent au même endroit : le panneau **Configurer le pool**. Pour accéder au panneau, recherchez un pool élastique dans le portail, puis cliquez sur **Configurer le pool** en haut du panneau ou dans le menu des ressources situé sur la gauche.

Vous pouvez effectuer n’importe quelle combinaison de modifications parmi les suivantes, et les enregistrer dans un même ensemble :
1. Modifier le niveau de service du pool
2. Mettre à l’échelle les performances (DTU ou vCore) et le stockage
3. Ajouter ou supprimer des bases de données dans le pool
4. Définir un niveau minimal (garanti) et maximal de performance pour les bases de données des pools
5. Examiner le récapitulatif des coûts pour voir toutes les modifications apportées à votre facture depuis vos nouvelles sélections

![Panneau de configuration du pool élastique](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="manage-elastic-pools-and-databases-using-powershell"></a>Gérer des pools élastiques et des bases de données à l’aide de PowerShell

Pour créer et gérer les pools élastiques SQL Database avec Azure PowerShell, utilisez les cmdlets PowerShell suivants. Si vous devez installer ou mettre à niveau PowerShell, consultez la section relative à [l’installation du module Azure PowerShell](/powershell/azure/install-azurerm-ps). Pour créer et gérer des bases de données, des serveurs et des règles de pare-feu, consultez [Gérer les serveurs, les bases de données et les pare-feu SQL Azure à l’aide de PowerShell](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-powershell).

> [!TIP]
> Pour obtenir des exemples de scripts PowerShell, consultez [Créer des pools élastiques et déplacer les bases de données entre les pools et en dehors d’un pool à l’aide de PowerShell](scripts/sql-database-move-database-between-pools-powershell.md) et [Utiliser PowerShell pour surveiller et mettre à l’échelle un pool élastique SQL dans Azure SQL Database](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Applet de commande | Description |
| --- | --- |
|[New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool)|Crée un pool de bases de données élastique sur un serveur SQL logique.|
|[Get-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/get-azurermsqlelasticpool)|Obtient les pools élastiques et leurs valeurs de propriété sur un serveur SQL logique.|
|[Set-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)|Modifie les propriétés d’un pool de bases de données élastique sur un serveur SQL logique. Par exemple, utilisez la propriété **StorageMB** pour modifier le stockage maximal d’un pool élastique.|
|[Remove-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/remove-azurermsqlelasticpool)|Supprime un pool de bases de données élastique sur un serveur SQL logique.|
|[Get-AzureRmSqlElasticPoolActivity](/powershell/module/azurerm.sql/get-azurermsqlelasticpoolactivity)|Obtient l’état des opérations sur un pool élastique sur un serveur SQL logique.|
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Crée une base de données dans un pool existant ou en tant que base de données seule. |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Obtient une ou plusieurs bases de données.|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Définit les propriétés d’une base de données, ou déplace une base de données existante dans un pool élastique, en dehors de celui-ci ou entre des pools élastiques.|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Supprime une base de données.|


> [!TIP]
> La création d’un grand nombre de bases de données dans un pool élastique peut prendre du temps si elle se fait par le biais du portail ou d’applets de commande PowerShell qui créent une seule base de données à la fois. Pour automatiser la création dans un pool élastique, consultez [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).
>

## <a name="manage-elastic-pools-and-databases-using-the-azure-cli"></a>Gérer des pools élastiques et des bases de données à l’aide de l’interface de ligne de commande Azure

Pour créer et gérer un serveur des pools élastiques avec [Azure CLI](/cli/azure), utilisez les commandes [Azure CLI SQL Database](/cli/azure/sql/db) suivantes. Utilisez [Cloud Shell](/azure/cloud-shell/overview) pour exécuter l’interface CLI dans votre navigateur ou [l’installer](/cli/azure/install-azure-cli) sur macOS, Linux ou Windows.

> [!TIP]
> Pour obtenir des exemples de scripts Azure CLI , consultez [Utiliser l’interface CLI afin de déplacer une base de données SQL Azure dans un pool élastique SQL](scripts/sql-database-move-database-between-pools-cli.md) et [Utiliser Azure CLI pour mettre un pool élastique SQL à l’échelle dans Azure SQL Database](scripts/sql-database-scale-pool-cli.md).
>

| Applet de commande | Description |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create)|Crée un pool élastique.|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list)|Renvoie une liste de pools élastiques dans un serveur.|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_dbs)|Renvoie une liste des bases de données dans un pool élastique.|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_editions)|Inclut également les paramètres DTU de pool, les limites de stockage et les paramètres par base de données disponibles. Pour réduire les détails, les limites de stockage supplémentaires et les paramètres par base de données sont masqués par défaut.|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)|Met à jour un pool élastique.|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_delete)|Supprime le pool élastique.|

## <a name="manage-databases-within-elastic-pools-using-transact-sql"></a>Gérer des bases de données dans les pools élastiques à l’aide de Transact-SQL

Pour créer et déplacer des bases de données dans les pools élastiques existants ou pour renvoyer des informations sur un pool élastique SQL Database avec Transact-SQL, utilisez les commandes T-SQL suivantes. Vous pouvez entrer ces commandes à l’aide du portail Azure, de [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), de [Visual Studio Code](https://code.visualstudio.com/docs), ou de tout autre programme pouvant se connecter à un serveur Azure SQL Database et transmettre des commandes Transact-SQL. Pour créer et gérer des bases de données, des serveurs et des règles de pare-feu, consultez [Gérer les serveurs, les bases de données et les pare-feu SQL Azure à l’aide de Transact-SQL](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-transact-sql).

> [!IMPORTANT]
> Vous ne pouvez pas créer, mettre à jour ou supprimer un pool élastique SQL Database à l’aide de Transact-SQL. Vous pouvez ajouter ou supprimer des bases de données à partir d’un pool élastique, et vous pouvez utiliser des vues de gestion dynamiques pour renvoyer des informations sur les pools élastiques existants.
>

| Commande | Description |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Crée une base de données dans un pool existant ou en tant que base de données seule. Vous devez être connecté à la base de données MASTER pour créer une base de données.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Déplace une base de données dans un pool élastique, en dehors de celui-ci ou entre des pools élastiques.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Supprime une base de données.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Renvoie les statistiques d’utilisation de ressources pour tous les pools de bases de données élastiques dans un serveur logique. Pour chaque pool de bases de données élastique, il existe une ligne pour chaque fenêtre de création de rapports de 15 secondes (quatre lignes par minute). Cela inclut la consommation de stockage, le journal, les E/S, l’UC et l’utilisation de session/requête simultanée par toutes les bases de données du pool.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Renvoie l’édition (niveau de service), l’objectif de service (niveau tarifaire) et, le cas échéant, le nom du pool élastique Azure SQL Database ou d’un entrepôt de données Azure SQL Data Warehouse. Si vous êtes connecté à la base de données MASTER d’un serveur Azure SQL Database, renvoie les informations au sujet de toutes les bases de données. Pour Azure SQL Data Warehouse, vous devez être connecté à la base de données MASTER.|

## <a name="manage-elastic-pools-and-databases-using-the-rest-api"></a>Gérer des pools élastiques et des bases de données à l’aide de l’API REST

Pour créer et gérer des pools élastiques SQL Database, utilisez ces demandes d’API REST.

| Commande | Description |
| --- | --- |
|[Pools élastiques - Créer ou mettre à jour](/rest/api/sql/elasticpools/createorupdate)|Crée un pool élastique ou met à jour un pool élastique existant.|
|[Pools élastiques - Supprimer](/rest/api/sql/elasticpools/delete)|Supprime le pool élastique.|
|[Pools élastiques - Obtenir](/rest/api/sql/elasticpools/get)|Obtenir un pool élastique.|
|[Pools élastiques - Lister par serveur](/rest/api/sql/elasticpools/listbyserver)|Renvoie une liste de pools élastiques dans un serveur.|
|[Pools élastiques - Mettre à jour](/rest/api/sql/elasticpools/update)|Met à jour un pool élastique existant.|
|[Pools élastiques recommandés - Obtenir](/rest/api/sql/recommendedelasticpools/get)|Obtient un pool élastique recommandé.|
|[Pools élastiques recommandés - Lister par serveur](/rest/api/sql/recommendedelasticpools/listbyserver)|Retourne les pools élastiques recommandés.|
|[Pools élastiques recommandés - Lister par métrique](/rest/api/sql/recommendedelasticpools/listmetrics)|Retourne les métriques des pools élastiques recommandés.|
|[Activités de pool élastique](/rest/api/sql/elasticpoolactivities)|Retourne les activités de pool élastique.|
|[Activités de bases de données du pool élastique](/rest/api/sql/elasticpooldatabaseactivities)|Retourne l’activité sur les bases de données à l’intérieur d’un pool élastique.|
|[Bases de données - Créer ou mettre à jour](/rest/api/sql/databases/createorupdate)|Crée une base de données ou met à jour une base de données existante.|
|[Bases de données - Obtenir](/rest/api/sql/databases/get)|Obtient une base de données.|
|[Bases de données - Obtenir par pool élastique](/rest/api/sql/databases/getbyelasticpool)|Obtient une base de données à l’intérieur d’un pool élastique.|
|[Bases de données - Obtenir par pool élastique recommandé](/rest/api/sql/databases/getbyrecommendedelasticpool)|Obtient une base de données à l’intérieur d’un pool élastique recommandé.|
|[Bases de données - Lister par pool élastique](/rest/api/sql/databases/listbyelasticpool)|Renvoie une liste des bases de données dans un pool élastique.|
|[Bases de données - Lister par pool élastique recommandé](/rest/api/sql/databases/listbyrecommendedelasticpool)|Retourne une liste de bases de données à l’intérieur d’un pool élastique recommandé.|
|[Bases de données - Lister par serveur](/rest/api/sql/databases/listbyserver)|Retourne une liste de bases de données d’un serveur.|
|[Bases de données - Mettre à jour](/rest/api/sql/databases/update)|Met à jour une base de données existante.|

## <a name="next-steps"></a>Étapes suivantes

* Vous pouvez aussi regarder la vidéo [Formation vidéo Microsoft Virtual Academy sur les fonctions de bases de données élastiques dans Azure SQL Database](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* Pour en savoir plus sur les modèles de conception pour les applications SaaS avec des pools élastiques, voir [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)(Modèles de conception pour les applications SaaS mutualisées avec la base de données SQL Azure).
* Pour obtenir un didacticiel SaaS utilisant des pools élastiques, consultez [Présentation de l’application SaaS Wingtip](sql-database-wtp-overview.md).
