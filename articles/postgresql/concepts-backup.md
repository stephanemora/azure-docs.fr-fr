---
title: Sauvegarde et restauration - Azure Database pour PostgreSQL - Serveur unique
description: Apprenez-en davantage sur la restauration et les sauvegardes automatiques de votre serveur Azure Database pour PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 3e6dfd5882e49ad903e8cff6f0ec7f3d6bd4a8b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77619623"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---single-server"></a>Sauvegarde et restauration dans Azure Database pour PostgreSQL - Single Server

Azure Database pour PostgreSQL crée automatiquement des sauvegardes de serveur et les conserve dans un stockage géoredondant ou redondant localement configuré par l’utilisateur. Les sauvegardes peuvent être utilisées pour restaurer votre serveur à un point dans le temps. La sauvegarde et la restauration sont une partie essentielle de toute stratégie de continuité d’activité, dans la mesure où elles protègent vos données des corruptions et des suppressions accidentelles.

## <a name="backups"></a>Sauvegardes

Azure Database pour PostgreSQL effectue des sauvegardes des fichiers de données et du journal des transactions. Selon la taille de stockage maximale prise en charge, nous prenons en charge des sauvegardes complètes et différentielles (serveurs de stockage de 4 To maximum) ou des sauvegardes d’instantanés (serveurs de stockage jusqu’à 16 To maximum). Celles-ci vous permettent de restaurer un serveur à n’importe quel point dans le temps au sein de votre période de rétention de sauvegarde configurée. La période de rétention de sauvegarde par défaut est de sept jours. Vous pouvez éventuellement la configurer sur 35 jours maximum. Toutes les sauvegardes sont chiffrées à l’aide du chiffrement AES de 256 bits.

Ces fichiers de sauvegarde ne peuvent pas être exportés. Les sauvegardes sont utilisables uniquement pour les opérations de restauration dans Azure Database pour PostgreSQL. Vous pouvez utiliser [pg_dump](howto-migrate-using-dump-and-restore.md) pour copier une base de données.

### <a name="backup-frequency"></a>Fréquence de sauvegarde

En général, les sauvegardes complètes sont effectuées toutes les semaines et les sauvegardes différentielles sont effectuées deux fois par jour pour les serveurs avec un stockage pris en charge maximal de 4 To. Les sauvegardes d’instantanés ont lieu au moins une fois par jour pour les serveurs prenant en charge jusqu’à 16 To de stockage. Dans les deux cas, les sauvegardes des journaux des transactions se produisent toutes les cinq minutes. Le premier instantané de la sauvegarde complète est planifié immédiatement après la création d’un serveur. La sauvegarde complète initiale peut prendre plus de temps sur un serveur restauré volumineux. Le point dans le temps le plus ancien vers lequel un nouveau serveur peut être restauré est le moment où la sauvegarde complète initiale est terminée. Du fait que les instantanés sont immédiats, les serveurs prenant en charge jusqu’à 16 To de stockage peuvent être restaurés jusqu’au moment de la création.

### <a name="backup-redundancy-options"></a>Options de redondance de sauvegarde

Azure Database pour PostgreSQL offre la possibilité de choisir entre le stockage de sauvegarde géoredondant ou localement redondant dans les niveaux Usage général et À mémoire optimisée. Lorsque les sauvegardes sont conservées dans le stockage de sauvegarde géoredondant, elles ne sont pas uniquement conservées dans la région d’hébergement de votre serveur, mais sont également répliquées dans un [centre de données jumelé](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Cela permet de bénéficier d’une meilleure protection et de la possibilité de restaurer votre serveur dans une région différente en cas de sinistre. Le niveau De base propose uniquement un stockage de sauvegarde redondant localement.

> [!IMPORTANT]
> La configuration du stockage géoredondant ou redondant localement pour la sauvegarde est uniquement possible lors de la création du serveur. Une fois que le serveur est approvisionné, vous ne pouvez pas modifier l’option de redondance du stockage de sauvegarde.

### <a name="backup-storage-cost"></a>Coût du stockage de sauvegarde

Azure Database pour PostgreSQL fournit jusqu’à 100 % du stockage de serveur approvisionné pour le stockage de sauvegarde sans coût supplémentaire. En règle générale, cela est adapté pour une rétention de sauvegarde de sept jours. Tous les stockages de sauvegarde supplémentaires utilisés sont facturés en Go par mois.

Par exemple, si vous avez approvisionné un serveur avec 250 Go, vous bénéficiez de 250 Go d’espace de stockage de sauvegarde sans coût supplémentaire. Tout stockage au-dessus de 250 Go est facturé.

## <a name="restore"></a>Restaurer

Dans Azure Database pour PostgreSQL, l’exécution d’une restauration crée un serveur à partir de sauvegardes de serveur d’origine.

Deux types de restauration sont disponibles :

- La **restauration à un point dans le temps** est disponible avec l’option de redondance de sauvegarde et crée un serveur dans la même région que votre serveur d’origine.
- La **géorestauration** est disponible uniquement si vous avez configuré votre serveur pour le stockage géoredondant ; elle vous permet de restaurer votre serveur dans une autre région.

Le délai estimé de récupération dépend de plusieurs facteurs, notamment du nombre total de bases de données à récupérer dans la même région au même moment, de la taille des bases de données, de la taille du journal des transactions et de la bande passante réseau. Le délai de récupération est généralement inférieur à 12 heures.

> [!IMPORTANT]
> Il n’est **pas** possible de restaurer des serveurs supprimés. Si vous supprimez le serveur, toutes les bases de données qui appartiennent au serveur sont également supprimées, sans pouvoir être restaurées. À l'issue du déploiement, pour protéger les ressources du serveur d'une suppression accidentelle ou de changements inattendus, les administrateurs peuvent utiliser des [verrous de gestion](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

### <a name="point-in-time-restore"></a>Restauration dans le temps

Quelle que soit l’option de redondance de sauvegarde, vous pouvez effectuer une restauration à n’importe quel point dans le temps au sein de votre période de rétention de sauvegarde. Un serveur est créé dans la même région Azure que le serveur d’origine. Il est créé avec la configuration du serveur d’origine pour le niveau de tarification, la génération de calcul, le nombre de vCores, la taille de stockage, la période de rétention de sauvegarde et l’option de redondance de sauvegarde.

La restauration à un point dans le temps est utile dans plusieurs scénarios. Par exemple, lorsqu’un utilisateur supprime accidentellement des données, perd une base de données ou une table importante ou si une application remplace accidentellement des données correctes par des données erronées en raison d’un défaut de l’application.

Vous devez peut-être attendre la prochaine sauvegarde du journal des transactions avant de pouvoir restaurer à un point dans le temps dans les cinq dernières minutes.

### <a name="geo-restore"></a>La géorestauration

Vous pouvez restaurer un serveur dans une autre région Azure où le service est disponible si vous avez configuré votre serveur pour les sauvegardes géoredondantes. Les serveurs qui prennent en charge jusqu’à 4 To de stockage peuvent être restaurés dans la région géographiquement associée ou dans n’importe quelle région qui prend en charge jusqu’à 16 To de stockage. Pour les serveurs prenant en charge jusqu’à 16 To de stockage, les géo-sauvegardes peuvent être restaurées dans n’importe quelle région qui prend également en charge les serveurs de 16 To. Passez en revue [Niveaux tarifaires d’Azure Database pour PostgeSQL](concepts-pricing-tiers.md) pour obtenir la liste des régions prises en charge.

La géorestauration constitue l’option de récupération par défaut lorsque votre serveur est indisponible en raison d’un incident dans la région où il est hébergé. Si un incident à grande échelle dans une région entraîne l’indisponibilité de votre application de base de données, vous pouvez restaurer un serveur à partir des sauvegardes géoredondantes sur un serveur situé dans n’importe quelle autre région. Il peut y avoir un délai entre le moment où une sauvegarde est effectuée et celui où elle est répliquée dans une autre région. Ce délai peut atteindre une heure. En cas d’incident, il peut donc y avoir jusqu’à une heure de pertes de données.

Pendant la géorestauration, les configurations de serveur qui peuvent être changées incluent la génération de calcul, les vCores, la période de conservation des sauvegardes et les options de redondance de sauvegarde. La modification du niveau tarifaire (De base, Usage général ou À mémoire optimisée) ou de la taille du stockage n’est pas prise en charge.

### <a name="perform-post-restore-tasks"></a>Effectuer des tâches de post-restauration

Après une restauration à l’aide d’un de ces mécanismes de récupération, vous devez effectuer les tâches suivantes afin que les utilisateurs et les applications soient de nouveau opérationnels :

- Si le nouveau serveur est destiné à remplacer le serveur d’origine, redirigez les clients et les applications clientes vers le nouveau serveur
- Vérifiez que les règles de pare-feu et de réseau virtuel appropriées sont en place au niveau du serveur pour permettre aux utilisateurs de se connecter. Ces règles ne sont pas copiées à partir du serveur d’origine.
- Assurez-vous que les connexions et les autorisations appropriées au niveau de la base de données sont en place
- Configurer les alertes, selon les besoins

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment effectuer une restauration à l’aide du  [portail Azure](howto-restore-server-portal.md).
- Découvrez comment effectuer une restauration à l’aide d’ [Azure CLI](howto-restore-server-cli.md).
- Pour en savoir plus sur la continuité d’activité, consultez la  [vue d’ensemble de la continuité d’activité](concepts-business-continuity.md).
