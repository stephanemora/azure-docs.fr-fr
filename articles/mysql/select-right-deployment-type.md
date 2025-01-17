---
title: Sélection du type de déploiement adapté - Azure Database pour MySQL
description: Cet article décrit les facteurs à prendre en compte avant de déployer Azure Database pour MySQL comme infrastructure en tant que service (IaaS) ou plateforme en tant que service (PaaS).
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: 3126eb9a2c3289f6fbc97a14fadf94300d1c2e84
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129728776"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Choisir l’option MySQL Server appropriée dans Azure

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

Avec Azure, vos charges de travail de serveur MySQL peuvent s’exécuter dans une infrastructure en tant que service (IaaS) de machine virtuelle hébergée ou dans une plateforme en tant que service (PaaS) hébergée. PaaS a plusieurs options de déploiement, et il existe des niveaux de service au sein de chaque option de déploiement. Quand vous choisissez entre IaaS et PaaS, vous devez déterminer si vous voulez gérer votre base de données, appliquer des correctifs et effectuer des sauvegardes, ou plutôt déléguer ces opérations à Azure.

Pour prendre votre décision, envisagez les deux options suivantes :

- **Azure Database pour MySQL**. Cette option est un moteur de base de données MySQL entièrement managé, basé sur la version stable de MySQL Community Edition. Cette base de données en tant que service (DBaaS) relationnelle, hébergée sur la plateforme cloud Azure, appartient à la catégorie de secteur PaaS.

  Avec une instance gérée de MySQL sur Azure, vous pouvez utiliser des fonctionnalités intégrées, par exemple, de mise à jour corrective automatisée, de haute disponibilité, de sauvegardes automatisées, de mise à l’échelle élastique, de sécurité de niveau entreprise, de conformité et de gouvernance, de surveillance et d’alertes, qui requièrent une configuration intensive lorsque MySQL Server est local ou dans une machine virtuelle Azure. Quand vous utilisez MySQL en tant que service, vous payez à l’utilisation avec des options de scale-up ou de scale-out pour bénéficier de plus de contrôle sans interruption de service.
  
  Deux modes de déploiement sont disponibles pour [Azure Database pour MySQL](overview.md) optimisé par MySQL Community Edition :

  - Un [Serveur unique](single-server-overview.md) est un service de base de données complètement managé avec des exigences minimales pour les personnalisations de la base de données. La plateforme de serveur unique est conçue pour prendre en charge la plupart des fonctions de gestion de base de données, comme les correctifs, les sauvegardes, la haute disponibilité et la sécurité, avec un minimum de configuration et de contrôle de la part de l’utilisateur. L’architecture est optimisée pour fournir une disponibilité de 99,99 % sur une seule zone de disponibilité. Les serveurs uniques sont particulièrement adaptés aux applications natives Cloud conçues pour gérer les correctifs automatisés sans qu’il soit nécessaire d’exercer un contrôle précis sur le calendrier des correctifs et les paramètres de configuration MySQL personnalisés.

  - Un [serveur flexible (version préliminaire)](flexible-server/overview.md) est un service de base de données entièrement géré conçu pour offrir un contrôle et une flexibilité plus granulaires des fonctions de gestion de base de données et des paramètres de configuration. En général, le service offre davantage de flexibilité et de personnalisations de configuration de serveur par rapport au déploiement de serveur unique en fonction des besoins des utilisateurs. L’architecture de serveur flexible permet aux utilisateurs d’opter pour une haute disponibilité au sein d’une même zone de disponibilité et dans plusieurs zones de disponibilité. Les serveurs flexibles offrent également de meilleurs contrôles d’optimisation des coûts grâce à la possibilité de démarrer/d’arrêter votre serveur et vos références (SKU) expansibles, idéales pour les charges de travail qui n’ont pas besoin en permanence d’une capacité de calcul complète.

Les serveurs flexibles sont adaptés de façon optimale pour ce qui suit :

  - développement d’applications nécessitant un meilleur contrôle et des personnalisations du moteur MySQL ;
  - haute disponibilité redondante interzone ;
  - fenêtres de maintenance managées.
 
  - **MySQL sur des machines virtuelles Azure**. Cette option s’inscrit dans la catégorie de secteur IaaS. Avec ce service, vous pouvez exécuter le serveur MySQL sur une machine virtuelle managée dans le cadre de la plateforme cloud Azure. Toutes les versions et éditions récentes de MySQL peuvent être installées sur la machine virtuelle.

## <a name="comparing-the-mysql-deployment-options-in-azure"></a>Comparaison des options de déploiement de MySQL dans Azure

Le tableau suivant liste les principales différences entre ces options :

| Attribut          | Azure Database pour MySQL<br/>Serveur unique |Azure Database pour MySQL<br/>Serveur flexible  |MySQL sur des machines virtuelles Azure |
|:-------------------|:-------------------------------------------|:---------------------------------------------|:------------------|
| [**Généralités**](flexible-server/overview.md)  | | | |
| Disponibilité générale | GA depuis 2018 | Version préliminaire publique | GA |
| Contrat de niveau de service (SLA) | Contrat SLA de disponibilité de 99,99 % |Aucun contrat de niveau de service en préversion| 99,99 % utilisant des zones de disponibilité|
| SE sous-jacent | Windows | Linux  | Géré par l’utilisateur |
| Édition MySQL | Community Edition | Community Edition | Édition Community ou Entreprise |
| Prise en charge de la version de MySQL | 5.6 (mise hors service), 5.7 et 8.0| 5.7 et 8.0 | Toutes les versions|
| Sélection de la zone de disponibilité pour la colocation d’applications | Non | Oui | Oui |
| Nom d’utilisateur dans la chaîne de connexion | `<user_name>@server_name`. Par exemple, `mysqlusr@mypgServer` | Nom d’utilisateur uniquement. Par exemple, `mysqlusr` | Nom d’utilisateur uniquement. Par exemple, `mysqlusr` | 
| [**Calcul et mise à l’échelle du stockage**](flexible-server/concepts-compute-storage.md) | | | |
| Niveaux de calcul | De base, Usage général, À mémoire optimisée | Burstable, Usage général, À mémoire optimisée | Burstable, Usage général, À mémoire optimisée |
| Mise à l’échelle du calcul | Pris en charge (la mise à l’échelle de et vers le niveau De base n’est pas prise en charge)| Prise en charge | Prise en charge|
| Taille de stockage | 5 Gio à 16Tio| De 20 Gio à 16 Tio | De 32 Gio à 32 767 Gio|
| Mise à l’échelle du stockage en ligne | Prise en charge| Prise en charge| Non pris en charge|
| Mise à l’échelle du stockage automatique | Prise en charge| Prise en charge| Non pris en charge|
| Mise à l’échelle des E/S par seconde | Non pris en charge| Prise en charge| Non pris en charge|
| [**Optimisation des coûts**](https://azure.microsoft.com/pricing/details/mysql/flexible-server/) | | | |
| Prix ​​des instances réservées | Prise en charge | Pris en charge | Prise en charge |
| Arrêter/démarrer le serveur pour le développement | Le serveur peut être arrêté jusqu’à 7 jours | Le serveur peut être arrêté jusqu’à 30 jours | Prise en charge |
| Références SKU Burstable à faible coût | Non pris en charge | Prise en charge | Prise en charge |
| [**Mise en réseau et sécurité**](concepts-security.md) | | | |
| Connectivité réseau | - Points de terminaison publics avec pare-feu de serveur.<br/> - Accès privé avec prise en charge de Liaison privée.|- Points de terminaison publics avec pare-feu de serveur.<br/> - Accès privé avec intégration de réseau virtuel.| - Points de terminaison publics avec pare-feu de serveur.<br/> - Accès privé avec prise en charge de Liaison privée.|
| SSL/TLS | Activé par défaut avec prise en charge de TLS v1.2, 1.1 et 1.0 | Activé par défaut avec prise en charge de TLS v1.2, 1.1 et 1.0| Pris en charge avec TLS v1.2, 1.1 et 1.0 |
| Chiffrement des données au repos | Pris en charge avec clés gérées par le client (BYOK) | Pris en charge avec clés gérées par le service | Non pris en charge|
| Azure AD Authentication | Prise en charge | Non pris en charge | Non pris en charge|
| Prise en charge Azure Defender | Oui | Non | Non |
| Audit du serveur | Prise en charge | Prise en charge | Géré par l’utilisateur |
| [**Mise à jour corrective et maintenance**](flexible-server/concepts-maintenance.md) | | |
| Mise à jour corrective du système d’exploitation| Automatique  | Automatique  | Géré par l’utilisateur |
| Mise à niveau d’une version mineure de MySQL  | Automatique  | Automatique | Géré par l’utilisateur |
| Mise à niveau d’une version majeure sur place de MySQL | Pris en charge de 5.6 à 5.7 | Non pris en charge | Géré par l’utilisateur |
| Contrôle de la maintenance | Géré par le système | Managée par le client | Géré par l’utilisateur |
| Fenêtre de maintenance | À tout moment dans la fenêtre de 15 heures | Fenêtre de 1 h | Géré par l’utilisateur |
| Notification de maintenance planifiée | 3 jours | 5 jours | Géré par l’utilisateur |
| [**Haute disponibilité**](flexible-server/concepts-high-availability.md) | | | |
| Haute disponibilité | Haute disponibilité intégrée (sans serveur de secours)| Haute disponibilité intégrée (sans serveur de secours), haute disponibilité dans la même zone et redondante interzone avec serveur de secours | Géré par l’utilisateur |
| Redondance de zone | Non pris en charge | Prise en charge | Prise en charge|
| Positionnement de la zone de secours | Non pris en charge | Prise en charge | Prise en charge|
| Basculement automatique | Oui (tourne un autre serveur)| Oui | Géré par l’utilisateur|
| basculement forcé | Non | Oui | Géré par l’utilisateur |
| Basculement d'application transparent | Oui | Oui | Géré par l’utilisateur|
| [**Réplication**](flexible-server/concepts-read-replicas.md) | | | |
| Prise en charge des réplicas en lecture | Oui | Oui | Géré par l’utilisateur |
| Nombre de réplicas en lecture pris en charge | 5 | 10 | Géré par l’utilisateur |
| Mode de réplication | Asynchrone | Asynchrone | Géré par l’utilisateur |
| Prise en charge GTID des réplicas en lecture | Prise en charge | Prise en charge | Géré par l’utilisateur |
| Prise en charge entre régions (géoréplication) | Oui | Non pris en charge | Géré par l’utilisateur |
| Scénarios hybrides | Pris en charge avec [Réplication des données entrantes](./concepts-data-in-replication.md)| Pris en charge avec [Réplication des données entrantes](./flexible-server/concepts-data-in-replication.md) | Géré par l’utilisateur |
| Prise en charge GTID pour la réplication de données entrantes | Prise en charge | Non pris en charge | Géré par l’utilisateur |
| Réplication de données sortantes | Non pris en charge | En préversion | Prise en charge |
| [**Sauvegarde et récupération**](flexible-server/concepts-backup-restore.md) | | | |
| Sauvegardes automatisées | Oui | Oui | Non |
| Rétention des sauvegardes | 7 à 35 jours | 1 à 35 jours | Géré par l’utilisateur |
| Rétention à long terme des sauvegardes | Géré par l’utilisateur | Géré par l’utilisateur | Géré par l’utilisateur |
| Exportation des sauvegardes | Prise en charge à l’aide de sauvegardes logiques | Prise en charge à l’aide de sauvegardes logiques | Prise en charge |
| Limite de restauration dans le temps à tout moment pendant la période de rétention | Oui | Oui | Géré par l’utilisateur |
| Possibilité de restauration sur une autre zone | Non pris en charge | Oui | Oui |
| Possibilité de restauration sur un autre réseau virtuel | Non | Oui | Oui |
| Possibilité de restauration dans une autre région | Oui (Géoredondant) | No | Géré par l’utilisateur |
| Possibilité de restaurer un serveur supprimé | Oui | Non | Non |
| [**Récupération d’urgence**](flexible-server/concepts-business-continuity.md) | | | | 
| DR dans les régions Azure | Utilisation de réplicas en lecture entre les régions, sauvegarde géoredondante | Non pris en charge | Géré par l’utilisateur |
| Basculement automatique | Non | Non pris en charge | Non |
| Possibilité d’utiliser le même point de terminaison lecture/écriture | Non | Non pris en charge | Non |
| [**Surveillance**](flexible-server/concepts-monitoring.md) | | | |
| Intégration Azure Monitor et alertes | Prise en charge | Prise en charge | Géré par l’utilisateur |
| Supervision des opérations de base de données | Prise en charge | Prise en charge | Géré par l’utilisateur |
| Query Performance Insight | Prise en charge | Prise en charge (à l’aide de Workbooks)| Géré par l’utilisateur |
| Journaux d’activité du serveur | Prise en charge | Prise en charge (à l’aide des journaux de diagnostic) | Géré par l’utilisateur |
| Journaux d’audit | Prise en charge | Pris en charge | Prise en charge | 
| Journaux d’activité d’erreurs | Non pris en charge | Prise en charge | Prise en charge |
| Prise en charge Azure Advisor | Prise en charge | Non pris en charge | Non pris en charge |
| **Plug-ins** | | | |
| validate_password | Non pris en charge | En préversion | Prise en charge |
| caching_sha2_password | Non pris en charge | En préversion | Prise en charge |
| [**Productivité des développeurs**](flexible-server/quickstart-create-server-cli.md) | | | |
| Gestion de flotte | Prise en charge avec Azure CLI, PowerShell, REST et Azure Resource Manager | Prise en charge avec Azure CLI, PowerShell, REST et Azure Resource Manager  | Pris en charge pour les machines virtuelles avec Azure CLI, PowerShell, REST et Azure Resource Manager |
| Prise en charge Terraform | Prise en charge | Non pris en charge | Prise en charge |
| GitHub Actions | Prise en charge | Prise en charge | Géré par l’utilisateur |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Motivations métier pour choisir PaaS ou IaaS

Plusieurs facteurs peuvent influencer votre décision quant au choix de PaaS ou IaaS pour héberger vos bases de données MySQL.

### <a name="cost"></a>Coût

La réduction des coûts est souvent le facteur principal qui détermine la meilleure solution d’hébergement de vos bases de données. C’est vrai que vous soyez une start-up à court de liquidités ou une équipe dans une société établie qui subit de fortes contraintes budgétaires. Cette section décrit les principes de base de facturation et de licence dans Azure qui s’appliquent à Azure Database pour MySQL et à MySQL sur des machines virtuelles Azure.

#### <a name="billing"></a>Facturation

Azure Database pour MySQL est disponible en tant que service dans plusieurs niveaux avec différents prix pour les ressources. Toutes les ressources sont facturées à un tarif horaire fixe. Pour obtenir les dernières informations sur les niveaux de service, les tailles de calcul et les quantités de stockage actuellement pris en charge, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/mysql/). Vous pouvez ajuster les niveaux de service et les tailles de calcul de manière dynamique pour répondre aux besoins de débit variés de votre application. Vous êtes facturé pour le trafic internet sortant aux [tarifs de transfert de données](https://azure.microsoft.com/pricing/details/data-transfers/) standard.

Avec Azure Database pour MySQL, Microsoft configure, corrige et met à niveau automatiquement le logiciel de base de données. Ces actions automatisées réduisent vos coûts d’administration. En outre, Azure Database pour MySQL offre des fonctionnalités de [sauvegardes automatisées](./concepts-backup.md). Ces fonctionnalités vous permettent de réaliser d’importantes économies, notamment si vous avez un grand nombre de bases de données. Au contraire, avec MySQL sur des machines virtuelles Azure, vous pouvez choisir et exécuter n’importe quelle version de MySQL. Quelle que soit la version de MySQL que vous utilisez, vous payez pour la machine virtuelle approvisionnée, le coût de stockage associé aux données, la sauvegarde, la surveillance des données et le stockage des journaux, ainsi que les coûts associés au type de licence MySQL spécifique utilisé (le cas échéant).

Azure Database pour MySQL offre une haute disponibilité intégrée pour tout type d’interruption de niveau nœud tout en conservant le niveau SLA garanti de 99,99 % pour le service. Toutefois, pour la haute disponibilité de base de données au sein de machines virtuelles, vous devez utiliser des options de haute disponibilité telles que la [réplication MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication.html) disponibles sur une base de données MySQL. L’utilisation d’une option de haute disponibilité prise en charge ne fournit pas de SLA supplémentaire. Toutefois, pour des coûts supplémentaires et une plus grande charge administrative, elle vous permet d’atteindre une disponibilité de base de données supérieure à 99,99 %.

Pour plus d’informations sur les tarifs, consultez les articles suivants :

- [Tarifs Azure Database pour MySQL](https://azure.microsoft.com/pricing/details/mysql/)
- [Tarifs des machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administration

Pour de nombreuses entreprises, la décision de migrer vers un service cloud vise autant à simplifier l’administration qu’à réduire son coût.

Avec IaaS, Microsoft :

- Administre l’infrastructure sous-jacente.
- Fournit une mise à jour corrective automatisée pour le matériel et le système d’exploitation sous-jacents.
  
Avec PaaS, Microsoft :

- Administre l’infrastructure sous-jacente.
- Fournit une mise à jour corrective automatisée pour le matériel, le système d’exploitation et le moteur de base de données sous-jacents.
- Gère la haute disponibilité de la base de données.
- Effectue automatiquement des sauvegardes et réplique toutes les données pour assurer la récupération d’urgence.
- Chiffre les données au repos et en mouvement par défaut.
- Analyse votre serveur et fournit des fonctionnalités pour obtenir des informations sur les performances des requêtes et des recommandations en matière de performances

La liste suivante décrit les considérations administratives pour chaque option :

- Avec Azure Database pour MySQL, vous pouvez continuer à administrer votre base de données. Mais vous n’avez plus besoin de gérer le moteur de base de données, le système d’exploitation ni le matériel. Voici quelques exemples d’éléments que vous pouvez continuer à administrer :

  - Bases de données
  - Connexion
  - Réglage des index
  - Paramétrage des requêtes
  - Audit
  - Sécurité

  Par ailleurs, la configuration de la haute disponibilité vers un autre centre de données requiert une configuration ou une administration minimale ou nulle.

- Avec MySQL sur des machines virtuelles Azure, vous disposez d’un contrôle total sur le système d’exploitation et la configuration des instances de serveur MySQL. Avec une machine virtuelle, vous décidez quand mettre à jour ou à niveau le système d’exploitation et le logiciel de base de données, ainsi que les correctifs à appliquer. Vous décidez également quand installer des logiciels supplémentaires tels qu’une application antivirus. Certaines fonctionnalités automatisées simplifient considérablement la gestion des correctifs, la sauvegarde et la haute disponibilité. Vous pouvez contrôler la taille de la machine virtuelle, le nombre de disques et leurs configurations de stockage. Pour plus d’informations, consultez [Tailles des machines virtuelles et des services cloud pour Azure](../virtual-machines/sizes.md).

### <a name="time-to-move-to-azure"></a>Il est temps de migrer vers Azure

- Azure Database pour MySQL est la solution idéale pour des applications cloud quand la productivité des développeurs et la rapidité de mise sur le marché de nouvelles solutions sont essentielles. Avec des fonctionnalités de programmation similaires à DBA, le service est adapté pour les architectes et les développeurs cloud, car il tempère la nécessité de gérer le système d’exploitation et la base de données sous-jacents.

- Lorsque vous souhaitez éviter le temps et les frais liés à l’acquisition d’un nouveau matériel local, MySQL sur machines virtuelles Azure est la solution adaptée aux applications qui nécessitent un contrôle granulaire et une personnalisation du moteur MySQL, non pris en charge par le service ou nécessitant un accès du système d’exploitation sous-jacent. Cette solution permet également d’effectuer une migration d’applications et de bases de données locales vers Azure en l’état, au cas où Azure Database pour MySQL ne conviendrait pas.

Comme il n’est pas nécessaire de changer la présentation, l’application ni les couches de données, vous économisez en temps et en budget sur le remaniement de votre solution existante. À la place, vous pouvez vous concentrer sur la migration de toutes vos solutions vers Azure et sur l’optimisation des performances requise par la plateforme Azure.

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Tarifs Azure Database pour MySQL](https://azure.microsoft.com/pricing/details/MySQL/).
- Commencer par [créer votre premier serveur](./quickstart-create-mysql-server-database-using-azure-portal.md).
