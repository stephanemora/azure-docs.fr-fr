---
title: Sélection du type de déploiement adapté à votre instance Azure Database for MariaDB
description: Cet article décrit les points que vous devez prendre en compte avant de choisir entre IaaS (Infrastructure-as-a-service) ou PaaS (Platform-as-a-service) pour votre instance Azure Database for MariaDB.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: fa87748719e2d3775034e5a2850281cf8f1a0e8a
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817380"
---
# <a name="choose-the-right-mariadb-server-option-in-azure"></a>Choisir l’option MariaDB Server appropriée dans Azure

Avec Azure, vos charges de travail de serveur MariaDB peuvent s’exécuter dans une machine virtuelle d’infrastructure hébergée (IaaS) ou en tant que service hébergé (PaaS). PaaS a plusieurs options de déploiement, et il existe des niveaux de service au sein de chaque option de déploiement. Quand vous choisissez entre PaaS ou IaaS, vous devez déterminer si vous voulez gérer votre base de données, appliquer des correctifs et effectuer des sauvegardes, ou plutôt déléguer ces opérations à Azure.</br>

En fonction de votre réponse à cette question, envisagez les options suivantes : <br/>

**Azure Database for MariaDB** est un moteur de base de données MariaDB complètement managé basé sur la version stable de l’édition Community. Cette base de données en tant que service (DBaaS) relationnelle, hébergée dans le cloud Azure, appartient à la catégorie de secteur PaaS (Platform-as-a-Service). Avec une instance managée de MariaDB sur Azure, vous pouvez utiliser les fonctionnalités intégrées ainsi que celles qui nécessitent une configuration étendue quand vous utilisez MariaDB Server (soit en local, soit dans une machine virtuelle Azure). Quand vous utilisez MariaDB en tant que service, vous payez à l’utilisation, avec des possibilités de mise à l’échelle pour obtenir plus de puissance sans interruption du service. De plus, contrairement au serveur MariaDB autonome, Azure Database for MariaDB présente des fonctionnalités supplémentaires telles que la haute disponibilité, l’intelligence et la gestion intégrées. <br/><br/>
**Machines virtuelles MariaDB sur Azure** appartient à la catégorie de secteur IaaS (Infrastructure-as-a-Service ) et vous permet d’exécuter MariaDB Server dans une machine virtuelle complètement managée dans le cloud Azure. Toutes les versions et éditions récentes de MariaDB peuvent être installées sur une machine virtuelle IaaS. La différence la plus significative par rapport à Azure Database for MariaDB est que Machines virtuelles MariaDB sur Azure permet un contrôle du moteur de base de données. Toutefois, en contrepartie de ce contrôle, il vous appartient de gérer les machines virtuelles et de nombreuses tâches DBA, telles que la maintenance et la mise à jour corrective des serveurs de base de données ou la conception de la récupération et de la haute disponibilité.

Le tableau suivant liste les principales différences entre ces options :

|            | **Azure Database for MariaDB** | **Machines virtuelles MariaDB sur Azure**    |
|:-------------------|:-----------------------------|:--------------------|
| **CONTRAT SLA**                | Offre un contrat SLA garantissant une disponibilité de 99,99 %| Disponibilité jusqu’à 99,95 % avec 2 instances ou plus dans le même groupe à haute disponibilité. <br/>Machine virtuelle à instance unique avec disponibilité de 99,9 % utilisant le stockage Premium <br/> Disponibilité de 99,99 % avec une zone de disponibilité comprenant 2 instances ou plus dans au moins 2 groupes à haute disponibilité.<br/> Remarque : [SLA pour Machines virtuelles](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) |
| **Application de correctifs au système d’exploitation**        | Automatique  | Géré par les clients |
|**Mise à jour corrective de MariaDB**     | Automatique  | Géré par les clients |
| **Haute disponibilité** | Le modèle de haute disponibilité (HA) repose sur des mécanismes de basculement intégrés lorsqu'une interruption se produit au niveau du nœud. Dans ce cas, le service crée automatiquement l’instance et attache le stockage à cette nouvelle instance. | La haute disponibilité est conçue, implémentée, testée et gérée par le client. Cela peut inclure Always-On (clustering de basculement ou réplication de groupe), la copie des journaux de transaction et la réplication transactionnelle, en fonction de la version du moteur MariaDB en cours d’utilisation.|
| **Redondance de zone** | Non prise en charge. | Les machines virtuelles Azure peuvent être configurées pour s’exécuter dans différentes zones de disponibilité. Pour une solution locale, les clients sont censés créer et gérer leur propre centre de données secondaire.|
| **Scénarios hybrides** | La [Réplication des données entrantes](https://docs.microsoft.com/azure/MariaDB/concepts-data-in-replication) vous permet de synchroniser les données d’un serveur MariaDB externe avec le service Azure Database for MariaDB. Le serveur externe peut être hébergé localement, dans des machines virtuelles, ou il peut s'agir d'un service de base de données hébergé par d'autres fournisseurs de services cloud.  <br/> <br/> La fonctionnalité de [réplica en lecture](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) vous permet de répliquer des données d’un serveur Azure Database for MariaDB (maître) sur jusqu’à cinq serveurs en lecture seule (réplicas) dans la même région Azure ou entre plusieurs régions. Les réplicas en lecture seule sont mis à jour de manière asynchrone à l’aide de la technologie de réplication binlog.   <br/> <br/> Remarque : La réplication en lecture entre plusieurs régions est disponible en préversion publique.| Géré par les clients <br/>
| **Sauvegarde et restauration** | Crée automatiquement des [sauvegardes de serveur](https://docs.microsoft.com/azure/MariaDB/concepts-backup#backups) et les conserve dans un stockage géoredondant ou redondant localement configuré par l’utilisateur. Le service accepte les sauvegardes complètes, différentielles et de fichier journal. | Géré par les clients |
| **Supervision des opérations de base de données** | Permet aux clients de [définir des alertes](https://docs.microsoft.com/azure/MariaDB/concepts-monitoring) sur l’opération de base de données et d’agir dès qu’un seuil est atteint. | Géré par les clients |
| **Protection avancée contre les menaces** | Fournit le service [Advanced Threat Protection](https://docs.microsoft.com/azure/MariaDB/howto-database-threat-protection-portal), qui détecte les activités anormales indiquant des tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des bases de données. <br/> <br/> Remarque : La protection avancée contre les menaces est disponible en préversion publique.| Les clients doivent se prémunir eux-mêmes.
| **Sauvegardes (reprise d’activité)** | Stocke les sauvegardes automatisées dans un [stockage géoredondant ou redondant localement](https://docs.microsoft.com/azure/MariaDB/howto-restore-server-portal) configuré par l’utilisateur. Les sauvegardes peuvent également être utilisées pour restaurer un serveur à un point dans le temps. La période de conservation peut être définie entre 7 et 35 jours. La restauration peut être effectuée à l’aide du portail Azure. <br/> | Entièrement gérées par le client, notamment la planification, le test, l’archivage, le stockage et la conservation, entre autres. Une autre option consiste à utiliser le coffre Azure Recovery Services pour sauvegarder les machines virtuelles et les bases de données Azure sur des machines virtuelles (fonctionnalité disponible en préversion). |
| **Recommandation sur les performances** | Fournit aux clients une [recommandation sur les performances](https://techcommunity.microsoft.com/t5/Azure-Database-for-MariaDB/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) proactive basée sur la télémétrie d’utilisation pour faciliter l’optimisation des charges de travail. <br/> <br/> Remarque : la recommandation sur les performances est disponible en préversion publique. | Géré par les clients |


## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Motivations métier pour choisir PaaS ou IaaS

Plusieurs facteurs peuvent influencer votre décision dans le choix de PaaS ou IaaS pour héberger vos bases de données MariaDB :

### <a name="cost"></a>Coût

Que vous soyez une start-up à court de liquidités ou une équipe dans une société établie qui subit de fortes contraintes budgétaires, le manque de capitaux est souvent la principale considération pour déterminer la meilleure solution d’hébergement des bases de données. Cette section décrit les principes de base de facturation et de licence dans Azure en ce qui concerne Azure Database for MariaDB et Machines virtuelles MariaDB sur Azure :

#### <a name="billing"></a>Facturation

Azure Database for MariaDB est disponible en tant que service dans plusieurs niveaux avec différents prix pour les ressources, qui sont toutes facturées à l’heure à un taux fixe. Pour obtenir les dernières informations sur les niveaux de service, les tailles de calcul et les quantités de stockage actuels pris en charge, consultez [Modèle d’achat vCore](https://docs.microsoft.com/azure/MariaDB/concepts-pricing-tiers). Vous pouvez ajuster les niveaux de service et les tailles de calcul de manière dynamique pour répondre aux besoins de débit variés de votre application. Vous êtes facturé pour le trafic internet sortant aux [tarifs de transfert de données](https://azure.microsoft.com/pricing/details/data-transfers/) standard.

Avec Azure Database for MariaDB, Microsoft configure, corrige et met à niveau automatiquement le logiciel de base de données, ce qui réduit vos coûts d’administration. En outre, ses fonctionnalités de [sauvegarde intégrée](https://docs.microsoft.com/azure/MariaDB/concepts-backup) vous permettent de réaliser d’importantes économies, notamment si vous avez un grand nombre de bases de données. Avec MariaDB sur machines virtuelles Azure, vous pouvez choisir et exécuter n’importe quelle version de MariaDB. Quelle que soit la version de MariaDB que vous utilisez, vous payez pour la machine virtuelle provisionnée et les coûts liés au type de licence spécifique utilisé pour MariaDB.

Azure Database for MariaDB offre une haute disponibilité intégrée pour tout type d’interruption de niveau nœud tout en conservant un niveau SLA de 99,99 % pour le service. Toutefois, pour la haute disponibilité de base de données au sein des machines virtuelles, le client doit passer par les options de haute disponibilité disponibles sur la base de données MariaDB, comme la [réplication MariaDB](https://mariadb.com/kb/en/library/setting-up-replication/). L’utilisation d’une option de haute disponibilité prise en charge ne fournit pas de SLA supplémentaire, mais vous pouvez atteindre une disponibilité de base de données supérieure à 99,99 % pour un coût et des tâches administratives supplémentaires.

Pour plus d'informations sur la tarification, consultez les ressources suivantes :
* [Tarification Azure Database for MariaDB](https://azure.microsoft.com/pricing/details/MariaDB/)
* [Tarifs des machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Calcul des coûts Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administration

Pour de nombreuses entreprises, la décision de migrer vers un service cloud vise essentiellement à simplifier la complexité d’administration. Avec IaaS et PaaS, Microsoft administre l’infrastructure sous-jacente et réplique automatiquement toutes les données pour assurer la reprise d’activité, configure et met à niveau le logiciel de base de données, gère l’équilibrage de charge et procède au basculement transparent en cas de défaillance du serveur.

* **Avec Azure Database for MariaDB**, vous pouvez continuer à administrer votre base de données, mais vous n’avez plus à gérer le moteur de base de données, ni le système d’exploitation ou le matériel. Parmi les éléments que vous pouvez continuer d’administrer, il y a les bases de données et les connexions, l’optimisation des index et des requêtes, ainsi que l’audit et la sécurité. Par ailleurs, la configuration de la haute disponibilité vers un autre centre de données requiert une configuration ou une administration minimale ou nulle.<br/><br/>
* **Avec MariaDB sur machines virtuelles Azure**, vous contrôlez entièrement le système d’exploitation et la configuration de l’instance MariaDB Server. Avec une machine virtuelle, vous décidez quand mettre à jour ou à niveau le système d’exploitation et le logiciel de base de données, et quand installer d’autres logiciels tels qu’une application antivirus. Certaines fonctionnalités automatisées simplifient considérablement la gestion des correctifs, la sauvegarde et la haute disponibilité. En outre, vous pouvez contrôler la taille de la machine virtuelle, le nombre de disques et leurs configurations de stockage. Pour plus d’informations, consultez Tailles de machines virtuelles et de services cloud pour Azure.

### <a name="time-to-move-to-azure-br"></a>Il est temps de migrer vers Azure <br/>
* **Azure Database for MariaDB** est la solution idéale pour des applications cloud quand la productivité des développeurs et la rapidité de mise sur le marché de nouvelles solutions sont essentielles. Avec les fonctionnalités de programmation comme le DBA, le service est parfait pour les architectes et les développeurs du cloud, car il tempère la nécessité de gérer le système d’exploitation et la base de données sous-jacents.<br/><br/>
* **MariaDB sur machines virtuelles Azure** est idéal pour les applications nouvelles ou existantes qui exigent une base de données MariaDB ou un accès aux fonctionnalités de la base de données MariaDB sur Windows/Linux, et si vous voulez éviter la perte de temps et les coûts occasionnés par l’acquisition de nouveau matériel local. Cette option convient également pour effectuer une migration d’applications et de bases de données locales en l’état vers Azure si l’instance Azure Database for MariaDB n’est pas adaptée. Étant donné qu’il n’est pas nécessaire de changer la présentation, l’application et les couches de données, vous économisez en temps et en budget sur le remaniement de votre solution existante. Ainsi, vous pouvez vous concentrer sur la migration de toutes vos solutions vers Azure et sur l’optimisation des performances requises par la plateforme Azure.

## <a name="next-steps"></a>Étapes suivantes

* Consultez [Tarification Azure Database for MariaDB](https://azure.microsoft.com/pricing/details/MariaDB/).
* Commencer par [créer votre premier serveur](https://review.docs.microsoft.com/azure/MariaDB/quickstart-create-MariaDB-server-database-using-azure-portal).

