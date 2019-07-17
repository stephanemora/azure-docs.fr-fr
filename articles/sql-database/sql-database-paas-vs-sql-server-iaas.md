---
title: Choisir parmi les choix SQL dans Azure | Microsoft Docs
description: Découvrez comment choisir parmi les options de déploiement au sein d’Azure SQL Database et entre Azure SQL Database et SQL Server sur des machines virtuelles Azure
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
keywords: Cloud SQL Server, SQL Server dans le cloud, base de données PaaS, cloud SQL Server, DBaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/11/2019
ms.openlocfilehash: 9e95569ba3fe65ea5bce7d6a95a24324235e9a7f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447750"
---
# <a name="choose-the-right-sql-server-option-in-azure"></a>Choisir l’option SQL Server appropriée dans Azure

Dans Azure, vos charges de travail SQL Server peuvent s’exécuter dans une infrastructure hébergée (IaaS) ou sous forme de service hébergé ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)). Dans PaaS, vous avez plusieurs options de déploiement et niveaux de service au sein de chaque option de déploiement. La question clé que vous devez poser quand vous choisissez entre PaaS ou IaaS est si vous voulez gérer votre base de données, appliquer des correctifs, effectuer des sauvegardes, ou plutôt déléguer ces opérations à Azure ?
En fonction de la réponse, vous avez les options suivantes :

- [Azure SQL Database](sql-database-technical-overview.md) : Un moteur de base de données SQL complètement managé, qui s’appuie sur la dernière édition Entreprise stable de SQL Server. Il s’agit d’une base de données relationnelle en tant que service (DBaaS) hébergée dans le cloud Azure qui appartient à la catégorie de secteur *Plateforme en tant que service (PaaS)* . SQL Database offre plusieurs options de déploiement, chacune reposant sur du matériel et des logiciels standardisés, qui sont détenus, hébergés et gérés par Microsoft. Avec SQL Database, vous pouvez utiliser les fonctionnalités intégrées ainsi que celles qui nécessitent une configuration étendue lorsqu’elles sont utilisées dans SQL Server (soit en local, soit dans une machine virtuelle Azure). Lorsque vous utilisez SQL Database, vous payez à l’utilisation, avec des possibilités de mise à l’échelle pour obtenir plus de puissance sans interruption du service. SQL Database a des fonctionnalités supplémentaires qui ne sont pas disponibles dans SQL Server, comme la haute disponibilité, l’intelligence et la gestion intégrées. Azure SQL Database propose les options de déploiement suivantes  :
  
  - En tant que [base de données unique](sql-database-single-database.md) avec son propre ensemble de ressources géré via un serveur SQL Database. Une base de données unique est similaire à une [base de données autonome](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) dans SQL Server. Cette option est optimisée pour le développement moderne de nouvelles applications issues du cloud.
  - Un [pool élastique](sql-database-elastic-pool.md), qui est une collection de bases de données avec un ensemble partagé de ressources gérées via un serveur SQL Database. Les bases de données uniques peuvent être déplacés dans et hors d’un pool élastique. Cette option est optimisée pour le développement moderne de nouvelles applications issues du cloud au moyen de l’application SaaS multi-locataires.
  - [Instance gérée](sql-database-managed-instance.md), qui est une collection de bases de données système et utilisateur avec un ensemble de ressources partagées. Une instance gérée est similaire à une instance des ressources partagées de l’offre [moteur de base de données Microsoft SQL Server] pour les bases de données et d’autres fonctionnalités limitées à une instance. L’instance gérée prend en charge la migration des bases de données locales avec des changements minimes, voire nuls, de base de données. Cette option fournit non seulement tous les avantages PaaS d’Azure SQL Database, mais ajoute également des fonctionnalités qui n’étaient auparavant disponibles que dans des machines virtuelles SQL. Parmi elles, un réseau virtuel natif (VNet) et une compatibilité proche de 100 % avec le serveur SQL local.
- [SQL Server sur les machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) est une *Infrastructure en tant que service (IaaS)* qui vous permet d’exécuter SQL Server dans une machine virtuelle complètement managée dans le cloud Azure. Les [machines virtuelles SQL Server](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) s’exécutent également sur du matériel standardisé détenu, hébergé et entretenu par Microsoft. Lorsque vous utilisez SQL Server sur une machine virtuelle, vous pouvez payer au fur et à mesure pour une licence SQL Server déjà incluse dans une image SQL Server ou utiliser simplement une licence existante. Vous pouvez aussi arrêter ou reprendre la machine virtuelle selon vos besoins. SQL Server installé et hébergé dans le cloud sur des machines virtuelles Windows Server ou Linux s’exécutant sur Azure, également appelé infrastructure en tant que service (IaaS). SQL Server sur des machines virtuelles Azure est une bonne option pour la migration de bases de données SQL Server et d’applications locales sans aucune modification de la base de données. Toutes les dernières versions et éditions de SQL Server sont disponibles pour l’installation dans une machine virtuelle IaaS. La différence la plus significative par rapport à SQL Database est que les machines virtuelles SQL Server permettent un contrôle total du moteur de base de données. Vous pouvez choisir quand la maintenance/mise à jour corrective démarrera, de passer d’un mode de récupération simple à un mode utilisant les journaux de transactions pour permettre un chargement plus rapide de moins de journaux, d’arrêter ou de démarrer le moteur quand c’est nécessaire, et vous pouvez personnaliser entièrement le moteur de base de données SQL Server. Ce contrôle supplémentaire est assorti accroît la responsabilité de gestion des machines virtuelles.

Le tableau suivant liste les principales différences entre ces options :

| SQL Server sur une machine virtuelle | Instance gérée dans SQL Database | Base de données unique / pool élastique dans SQL Database |
| --- | --- | --- |
|Vous avez le contrôle total du moteur SQL Server.<br/>Jusqu’à 99,95 % de disponibilité.<br/>Parité complète avec la version locale correspondante de SQL Server.<br/>Version du moteur de base de données fixe et connue.<br/>Migration facile à partir d’une version locale de SQL Server.<br/>Adresse IP privée au sein du réseau virtuel Azure.<br/>Vous avez la possibilité de déployer des applications ou des services sur l’hôte où réside SQL Server.| Haute compatibilité avec la version locale de SQL Server.<br/>99,99 % de disponibilité garantie.<br/>Sauvegardes, mise à jour corrective, récupération intégrées.<br/>Dernière version stable du moteur de base de données.<br/>Migration facile à partir de SQL Server.<br/>Adresse IP privée au sein du réseau virtuel Azure.<br/>Intelligence et sécurité avancées intégrées.<br/>Changement en ligne des ressources (processeur/stockage).|Les fonctionnalités SQL Server les plus couramment utilisées sont disponibles.<br/>99,99 % de disponibilité garantie.<br/>Sauvegardes, mise à jour corrective, récupération intégrées.<br/>Dernière version stable du moteur de base de données.<br/>Possibilité d’attribuer les ressources nécessaires (processeur/stockage) à des bases de données individuelles.<br/>Intelligence et sécurité avancées intégrées.<br/>Changement en ligne des ressources (processeur/stockage).|
|Vous devez gérer vos sauvegardes et correctifs.<br>Vous devez implémenter votre propre solution de haute disponibilité.<br/>Vous avez un temps d’arrêt pendant le changement des ressources (processeur/storage)|Un nombre minime de fonctionnalités SQL Server ne sont toujours pas disponibles.<br/>Temps de maintenance exact non garanti (mais presque transparent).<br/>Vous pouvez obtenir la compatibilité avec la version de SQL Server uniquement à l’aide des niveaux de compatibilité de base de données.|La migration à partir de SQL Server peut être difficile.<br/>Certaines fonctionnalités SQL Server ne sont pas disponibles.<br/>Temps de maintenance exact non garanti (mais presque transparent).<br/>Vous pouvez obtenir la compatibilité avec la version de SQL Server uniquement à l’aide des niveaux de compatibilité de base de données.<br/>L’adresse IP privée ne peut pas être attribuée (vous pouvez limiter l’accès avec des règles de pare-feu).|

Découvrez comment chaque option de déploiement s’inscrit dans la plate-forme de données de Microsoft et obtenez de l’aide sur l’option qui répond aux besoins de votre entreprise. Que vous privilégiiez les économies ou une administration minimale, cet article peut vous aider à déterminer l’approche adaptée en fonction des exigences clients essentielles.

## <a name="microsofts-sql-data-platform"></a>Plateforme de données SQL de Microsoft

L'une des premières choses à comprendre dans toute discussion comparant les bases de données Azure et SQL Server locales est que vous pouvez tout utiliser. La plateforme de données de Microsoft utilise la technologie SQL Server et la rend disponible sur les machines physiques locales, les environnements de cloud privé, les environnements de cloud privé hébergé par un tiers et le cloud public. SQL Server sur les machines virtuelles Azure vous permet de répondre à des besoins métier spécifiques et différents par le biais d’une combinaison de déploiements locaux et hébergés dans le cloud, tout en utilisant le même ensemble de produits serveur, d’outils de développement et de savoir-faire dans ces environnements.

   ![Options cloud de SQL Server : SQL Server sur IaaS ou base de données SQL SaaS dans le cloud.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Comme indiqué sur le diagramme, chaque offre peut être caractérisée par le niveau d'administration dont vous disposez sur l'infrastructure (sur l'axe des X) et par le degré d'efficacité opérationnelle obtenue par consolidation au niveau de la base de données et par automatisation (sur l'axe des Y).

Lorsque vous concevez une application, quatre options de base sont disponibles pour l'hébergement de sa partie SQL Server :

- SQL Server sur des machines physiques non virtualisées
- SQL Server sur des machines virtualisées locales (cloud privé)
- SQL Server sur Azure Virtual Machines (cloud public Microsoft)
- Azure SQL Database (cloud public Microsoft)

Dans les sections suivantes, vous allez découvrir SQL Server dans le cloud public Microsoft : Azure SQL Database et SQL Server sur les machines virtuelles Azure. Vous allez également explorer les motivations commerciales courantes permettant de déterminer l’option qui convient le mieux à votre application.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Étude détaillée d’Azure SQL Database et de SQL Server sur les machines virtuelles Azure

En général, ces deux options SQL sont optimisées à différentes fins :

- **Azure SQL Database**

Optimisé pour optimiser les coûts de provisionnement et de gestion de nombreuses bases de données. Elle réduit les coûts d’administration, car vous n’avez plus à gérer les machines virtuelles, le système d’exploitation et le logiciel de base de données. Vous n’avez pas à gérer les mises à niveau, la haute disponibilité, ni les [sauvegardes](sql-database-automated-backups.md). En règle générale, Azure SQL Database augmente considérablement le nombre de bases de données gérées par un informaticien ou un développeur. Les [pools élastiques](sql-database-elastic-pool.md) prennent également en charge les architectures d’applications SaaS multi-locataires grâce à des fonctionnalités incluant l’isolation des locataires et la possibilité de mettre à l’échelle pour réduire les coûts en partageant des ressources entre les bases de données. L’[instance gérée](sql-database-managed-instance.md) prend en charge les fonctionnalités incluses dans l’instance permettant de migrer facilement des applications existantes, ainsi que de partager des ressources entre les bases de données.

- **SQL Server exécuté sur des machines virtuelles Azure**

Optimisé pour la migration d’applications existantes vers Azure ou l’extension d’applications locales existantes au cloud dans des déploiements hybrides. En outre, vous pouvez utiliser SQL Server sur une machine virtuelle pour développer et tester des applications traditionnelles SQL Server. Avec SQL Server sur les machines virtuelles Azure, vous disposez de droits d’administration complets sur une instance SQL Server dédiée et sur une machine virtuelle dans le cloud. C'est le choix idéal lorsqu'une organisation possède déjà les ressources informatiques disponibles pour maintenir les machines virtuelles. Ces fonctionnalités vous permettent de créer un système hautement personnalisé afin de répondre aux exigences de performances de disponibilité de votre application.

Le tableau suivant résume les principales caractéristiques de SQL Database et de SQL Server sur les machines virtuelles Azure :

| | Bases de données uniques SQL Database et pools élastiques | Instances gérées SQL Database |Machines virtuelles Azure avec SQL Server |
| --- | --- | --- |---|
| **Idéal pour :** |Nouvelles applications conçues pour le cloud qui veulent utiliser les dernières fonctionnalités stables de SQL Server et ont des contraintes de délais de développement et de commercialisation. | Nouvelles applications ou applications locales existantes qui utilisent les dernières fonctionnalités stables de SQL Server et sont migrées vers le cloud avec des modifications minimales.  | Applications existantes qui requièrent une migration rapide vers le cloud avec un minimum de modifications, ou sans aucune modification. Scénarios de développement et de test rapides lorsque vous ne souhaitez pas acheter du matériel SQL Server local non destiné à la production. |
|  | Équipes qui nécessitent une haute disponibilité, une récupération d’urgence et une mise à niveau pour la base de données. | Comme les bases de données uniques et mises en pool SQL Database. | Équipes qui peuvent configurer, ajuster, personnaliser et gérer une haute disponibilité, une récupération d’urgence et une mise à jour corrective pour SQL Server. Certaines fonctionnalités automatisées simplifient considérablement cette procédure. |
|  | Équipes qui ne souhaitent pas gérer le système d’exploitation sous-jacent et les paramètres de configuration. | Comme les bases de données uniques et mises en pool SQL Database. | Vous avez besoin d’un environnement personnalisé avec des droits d’administration complets. |
|  | Bases de données comprenant jusqu’à 100 To. | Jusqu’à 8 To. | Instances SQL Server avec 64 To de stockage maximum. L’instance peut prendre en charge autant de bases de données que nécessaire. |
| **Compatibilité** | Prend en charge la plupart des fonctionnalités locales au niveau de la base de données. | Prend en charge presque toutes les fonctionnalités locales au niveau de l’instance et de la base de données. | Prend en charge toutes les fonctionnalités locales. |
| **Ressources :** | Vous ne voulez pas utiliser des ressources informatiques pour la configuration et la gestion de l’infrastructure sous-jacente, mais vous voulez vous concentrer sur la couche Application. | Comme les bases de données uniques et mises en pool SQL Database. | Vous disposez de ressources informatiques pour la configuration et la gestion. Certaines fonctionnalités automatisées simplifient considérablement cette procédure. |
| **Coût total de possession :** | Élimine les coûts matériels et réduit les coûts d’administration. | Comme les bases de données uniques et mises en pool SQL Database. | Élimine les coûts matériels. |
| **Continuité des activités :** |En plus des [fonctionnalités intégrées d’infrastructure de tolérance de panne](sql-database-high-availability.md), Azure SQL Database offre des fonctionnalités comme les [sauvegardes automatisées](sql-database-automated-backups.md), la[limite de restauration dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore), la [géorestauration](sql-database-recovery-using-backups.md#geo-restore), la [géoréplication active](sql-database-active-geo-replication.md) et les [groupes de basculement automatique](sql-database-auto-failover-group.md) afin d’améliorer la continuité de l’activité. Pour plus d’informations, voir [Vue d’ensemble de la continuité des activités de la base de données SQL Azure](sql-database-business-continuity.md). | Comme les bases de données uniques et mises en pool SQL Database, plus les sauvegardes en copie seule lancées par l’utilisateur. | L’option SQL Server sur les machines virtuelles Azure vous permet de configurer une solution de haute disponibilité et de récupération d’urgence pour les besoins spécifiques de votre base de données. Vous pouvez donc disposer d'un système hautement optimisé pour votre application. Vous pouvez tester et exécuter des basculements par vous-même si nécessaire. Pour plus d’informations, voir [Haute disponibilité et récupération d’urgence pour SQL Server dans Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md). |
| **Cloud hybride :** |Votre application locale peut accéder aux données dans Azure SQL Database. | [Implémentation d’un réseau virtuel natif](sql-database-managed-instance-vnet-configuration.md) et sa connexion à votre environnement local à l’aide d’Azure Express Route ou d’une passerelle VPN. | Avec l’option SQL Server sur les machines virtuelles Azure, vous pouvez avoir des applications qui s’exécutent en partie dans le cloud et en partie localement. Par exemple, vous pouvez étendre votre réseau local et votre domaine Active Directory au cloud par le biais d’ [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Pour plus d’informations sur les solutions cloud hybrides, consultez [Extension des solutions de données locales vers le cloud](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |
|  | Prend en charge la [réplication transactionnelle de SQL Server](https://msdn.microsoft.com/library/mt589530.aspx) en tant qu’abonné pour la réplication des données. | La réplication est prise en charge pour une instance gérée en tant que fonctionnalité d'évaluation. | Prend entièrement en charge la [réplication transactionnelle de SQL Server](https://msdn.microsoft.com/library/mt589530.aspx), les [groupes de disponibilité Always On](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), Integration Services et la copie des journaux de transaction pour la réplication des données. En outre, les sauvegardes traditionnelles SQL Server sont entièrement prises en charge |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Critères pour choisir entre Azure SQL Database et SQL Server sur les machines virtuelles Azure

Plusieurs facteurs peuvent influencer votre décision dans le choix de PaaS ou IaaS pour héberger vos bases de données SQL :

- [Coût](#cost) : Les options PaaS et IaaS incluent toutes les deux un prix de base qui couvrent l’infrastructure sous-jacente et les licences. Toutefois, avec l’option IaaS, vous devez consacrer plus de temps et de ressources à la gestion de votre base de données, alors que dans PaaS, ces fonctionnalités d’administration sont incluses dans le prix. L’option IaaS vous permet d’arrêter vos ressources quand vous ne les utilisez pas afin de réduire le coût, alors que la version PaaS est toujours exécutée, sauf si vous supprimez et recréez vos ressources quand vous en avez besoin.
- [Administration](#administration) : Les options PaaS réduisent le temps que vous devez investir pour administrer la base de données. Toutefois, elles limitent également l’éventail des tâches et scripts administratifs personnalisés que vous pouvez effectuer ou exécuter. Par exemple, le CLR n’est pas pris en charge avec les bases de données uniques ou mises en pool, mais il l’est pour une instance gérée. De plus, aucune option de déploiement dans PaaS ne prend en charge l’utilisation des indicateurs de trace.
- [Contrat de niveau de service](#service-level-agreement-sla) : IaaS et PaaS fournissent un contrat SLA élevé répondant au standard du secteur. L’option PaaS garantit un contrat SLA de 99,99 %, alors que IaaS garantit un contrat SLA de 99,95 % pour l’infrastructure, ce qui signifie que vous devez implémenter des mécanismes supplémentaires pour garantir la disponibilité de vos bases de données. Dans les cas extrêmes, si vous voulez implémenter une solution de haute disponibilité correspondant à PaaS, vous devrez créer un serveur SQL supplémentaire dans une machine virtuelle et configurer des groupes de disponibilité AlwaysOn, ce qui peut doubler le coût de votre base de données.
- [Durée de déplacement vers Azure](#market) : SQL Server dans une machine virtuelle Azure est la correspondance exacte de votre environnement. La migration du système local vers une machine virtuelle SQL Azure n’est donc pas différente du déplacement des bases de données d’un serveur local vers un autre. L’instance gérée facilite également beaucoup la migration. Toutefois, vous devez peut-être appliquer certains changements avant de migrer vers une instance gérée.

Ces facteurs sont abordés plus en détail dans les sections suivantes.

### <a name="cost"></a>Coût

Que vous soyez une start-up à court de liquidités ou une équipe dans une société établie qui subit de fortes contraintes budgétaires, le manque de capitaux est souvent le principal moteur pour décider du mode d'hébergement des bases de données. Dans cette section, vous découvrirez d’abord les notions de base de facturation et de licence dans Azure pour ces deux options de base de données relationnelles : SQL Database et SQL Server sur les machines virtuelles Azure. Vous apprendrez également à calculer le coût total de l’application.

#### <a name="billing-and-licensing-basics"></a>Notions de base sur la facturation et les licences

Actuellement, **SQL Database** est vendu en tant que service et est disponible sous la forme de plusieurs options de déploiement. Il propose plusieurs niveaux de service à des prix différents selon les ressources, et qui sont tous facturés à un tarif horaire fixe en fonction du niveau de service et de la taille de calcul choisis. Pour obtenir les dernières informations sur les niveaux de service, les tailles de calcul et les quantités de stockage actuels pris en charge, consultez [Modèle d’achat DTU](sql-database-service-tiers-dtu.md) et [Modèle d’achat vCore](sql-database-service-tiers-vcore.md).

- Avec la base de données unique SQL Database, vous pouvez choisir un niveau de service adapté à vos besoins dans une large gamme de prix à partir de 5 USD par mois pour le niveau de base.
- Vous pouvez créer des [pools élastiques](sql-database-elastic-pool.md) pour partager des ressources entre des instances de base de données pour réduire les coûts et vous adapter aux pics d’utilisation.
- Avec l’instance gérée SQL Database, vous pouvez également utiliser votre propre licence. Pour plus d’informations sur l’utilisation de votre propre licence, consultez [Mobilité de licence à travers la Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/) ou utilisez la [calculatrice Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) pour voir comment **économiser jusqu’à 40 %** .

Par ailleurs, vous êtes facturé pour le trafic internet sortant aux [tarifs de transfert de données](https://azure.microsoft.com/pricing/details/data-transfers/)standard. Vous pouvez ajuster les niveaux de service et les tailles de calcul de manière dynamique pour répondre aux besoins de débit variés de votre application.

Avec **SQL Database**, Microsoft configure, corrige et met à niveau automatiquement le logiciel de base de données, ce qui réduit vos coûts d’administration. En outre, ses fonctionnalités de [sauvegarde intégrée](sql-database-automated-backups.md) vous permettent de réaliser d’importantes économies, notamment si vous avez un grand nombre de bases de données.

Avec **SQL Server sur les machines virtuelles Azure**, vous pouvez utiliser une image SQL Server fournie par la plateforme (qui inclut une licence) ou votre licence SQL Server. Toutes les versions SQL Server (2008 R2, 2012, 2014, 2016) et les éditions (Developer, Express, Web, Standard, Enterprise) prises en charge sont disponibles. Les versions BYOL (apportez votre propre licence) des images sont également disponibles. Lorsque vous utilisez les images fournies par Azure, les coûts opérationnels dépendent de la taille de la machine virtuelle et de l’édition SQL Server choisie. Quelle que soit la taille de la machine virtuelle ou l’édition de SQL Server, vous payez à la minute l’utilisation de la licence de SQL Server et du serveur Windows ou Linux, ainsi que l’utilisation du stockage Azure pour les disques de machine virtuelle. L’option de facturation à la minute vous permet d’utiliser SQL Server aussi longtemps que vous en avez besoin, sans acheter de licences SQL Server supplémentaires. Si vous utilisez votre propre licence SQL Server sur Azure, seuls les coûts du serveur et du stockage vous sont facturés. Pour plus d’informations sur l’utilisation de votre propre licence, consultez [License Mobility via Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/). Par ailleurs, vous êtes facturé pour le trafic internet sortant aux [tarifs de transfert de données](https://azure.microsoft.com/pricing/details/data-transfers/)standard.

#### <a name="calculating-the-total-application-cost"></a>Calcul du coût total de l'application

Lorsque vous utilisez une plateforme cloud, le coût d’exécution de votre application inclut le coût du nouveau développement et les frais d’administration courante, ainsi que les coûts de service pour la plateforme de cloud public.

**Avec Azure SQL Database :**

- Coûts d’administration réduits au strict minimum
- Coûts de développement limités pour les applications migrées (instances gérées)
- Coûts de service pour SQL Database
- Aucun coût d’achat de matériel

**Avec SQL Server sur les machines virtuelles Azure :**

- Coûts d’administration plus élevés
- Coûts de développement limités, voire inexistants, pour les applications migrées
- Coûts de service pour la machine virtuelle
- Aucun coût d’achat de matériel

Pour plus d'informations sur la tarification, consultez les ressources suivantes :

- [Tarification des bases de données SQL](https://azure.microsoft.com/pricing/details/sql-database/)
- [Tarification des machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/) pour [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) et [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Calcul des coûts Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administration

Pour de nombreuses entreprises, la décision de migrer vers un service cloud vise essentiellement à simplifier la complexité d’administration, qui a un coût. Avec IaaS et Paas, Microsoft administre l’infrastructure sous-jacente et réplique automatiquement toutes les données pour assurer la récupération d'urgence, configure et met à niveau le logiciel de base de données, gère l’équilibrage de charge et procède au basculement transparent en cas de défaillance du serveur au sein d’un centre de données.

- Avec **Azure SQL Database**, vous pouvez continuer à administrer votre base de données, mais vous n’avez plus à gérer le moteur de base de données, ni le système d’exploitation ou le matériel. Parmi les éléments que vous pouvez continuer d’administrer, il y a les bases de données et les connexions, l’optimisation des index et des requêtes, ainsi que l’audit et la sécurité. Par ailleurs, la configuration de la haute disponibilité vers un autre centre de données ne requiert qu’une configuration et une administration minimales.
- Avec **SQL Server sur les machines virtuelles Azure**, vous contrôlez entièrement le système d’exploitation et la configuration de l’instance SQL Server. Avec une machine virtuelle, c’est à vous de décider quand mettre à jour ou à niveau le système d’exploitation et le logiciel de base de données, et quand installer d’autres logiciels tels que l’antivirus. Certaines fonctionnalités automatisées simplifient considérablement la gestion des correctifs, la sauvegarde et la haute disponibilité. En outre, vous pouvez contrôler la taille de la machine virtuelle, le nombre de disques et leurs configurations de stockage. Azure vous permet de modifier la taille d’une machine virtuelle en fonction des besoins. Pour plus d’informations, consultez [Tailles de machines virtuelles et de services cloud pour Azure](../virtual-machines/windows/sizes.md).

### <a name="service-level-agreement-sla"></a>Contrat de Niveau de Service (SLA)

Pour bon nombre de services informatiques, répondre aux obligations de temps d’exécution d’un contrat de niveau de service (SLA) est la priorité absolue. Dans cette section, nous allons détailler les implications du contrat SLA pour chaque option d’hébergement de base de données.

Pour **SQL Database**, Microsoft fournit un contrat de niveau de service de 99,99 %. Pour obtenir les dernières informations, consultez [Contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/sql-database/).

Pour **SQL Server sur les machines virtuelles Azure**, Microsoft fournit un SLA avec une disponibilité de 99,95 %, qui ne couvre que la machine virtuelle. Ce contrat SLA ne couvre pas les processus (comme SQL Server) exécutés sur la machine virtuelle et nécessite l’hébergement d’au moins deux instances de machine virtuelle dans un groupe à haute disponibilité. Pour plus d’informations, consultez le [contrat SLA de machine virtuelle](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Pour obtenir une haute disponibilité de la base de données dans les machines virtuelles, vous devez configurer une des options de haute disponibilité prises en charge dans SQL Server, par exemple, [groupes de disponibilité Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). L’utilisation d’une option de haute disponibilité prise en charge ne fournit pas de SLA supplémentaire, mais vous pouvez atteindre une disponibilité de base de données > 99,99 %.

### <a name="market"></a>Il est temps de migrer vers Azure

**Les bases de données unités ou pools élastiques SQL Database** sont la solution idéale pour les applications cloud lorsque la productivité des développeurs et la rapidité de mise sur le marché de nouvelles solutions sont essentielles. Avec les fonctionnalités de programmation comme le DBA, il est parfait pour les architectes et les développeurs du cloud, car il tempère la nécessité de gérer le système d'exploitation et la base de données sous-jacents.

**SQL Database Managed Instance** simplifie considérablement la migration d’applications existantes vers Azure SQL Database, ce qui vous permet de commercialiser rapidement des applications de bases de données migrées dans Azure.

**SQL Server exécuté sur des machines virtuelles Azure** est idéal si vos applications nouvelles ou existantes exigent des bases de données volumineuses ou un accès à toutes les fonctionnalités dans SQL Server ou Windows/Linux, et si vous voulez éviter la perte de temps et les coûts occasionnés par l’acquisition de nouveau matériel local. Elle convient également si vous souhaitez migrer des applications et des bases de données locales en l’état vers Azure dans les cas où l’instance managée Azure SQL Database n’est pas adaptée. Étant donné que vous n’avez pas besoin de changer la présentation, l’application et les couches de données, vous économisez en temps et en budget sur le remaniement de votre solution existante. Ainsi, vous pouvez vous concentrer sur la migration de toutes vos solutions vers Azure et sur l’optimisation des performances requises par la plateforme Azure. Pour plus d’informations, consultez [Meilleures pratiques relatives aux performances de SQL Server dans les machines virtuelles Azure](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour prendre en main SQL Database, consultez la page [Votre première base de données Azure SQL Database](sql-database-single-database-get-started.md).
- Voir [Tarification de Base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).
- Pour plus d’informations sur la prise en main de SQL Server sur Azure Virtual Machines, voir [Approvisionnement d’une machine virtuelle SQL Server dans Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) .
- [Identifiez la bonne référence SKU d’Azure SQL Database/Managed Instance pour votre base de données locale](/sql/dma/dma-sku-recommend-sql-db/).