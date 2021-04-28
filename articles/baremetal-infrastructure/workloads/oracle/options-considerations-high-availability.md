---
title: Options ou serveurs d’infrastructure Oracle BareMetal
description: En savoir plus sur les options et les points à prendre en compte pour les serveurs d’infrastructure Oracle BareMetal.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: e8a2dece11884e3a659f14b30bce51e7f0244924
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589734"
---
# <a name="options-for-oracle-baremetal-infrastructure-servers"></a>Options pour les serveurs d’infrastructure Oracle BareMetal

Dans cet article, nous envisageons des options et des recommandations pour bénéficier du plus haut niveau de protection et de performances lors de l’exécution d’Oracle sur des serveurs d’infrastructure BareMetal. 

## <a name="data-guard-protection-modes"></a>Modes de protection Data Guard

Data Guard offre une protection indisponible uniquement par le biais du cluster RAC (Oracle Real Applications Cluster), de la réplication logique (par exemple, GoldenGate) et de la réplication basée sur le stockage. 

| Mode de protection | Description |
| --- | --- |
| **Performances maximales** | Le mode de protection par défaut. Il fournit le niveau de protection le plus élevé sans affecter les performances de la base de données primaire. Les données sont considérées comme validées dès qu’elles ont été écrites dans le flux de restauration de la base de données primaire. Il est ensuite répliqué sur la base de données de secours de manière asynchrone. En règle générale, la base de données de secours la reçoit en l’espace de quelques secondes, mais aucune garantie n’est donnée à cet effet. En général, ce mode répond aux besoins de l’entreprise (avec une surveillance du décalage) sans nécessiter une connectivité réseau à faible latence entre les sites principal et secondaire.<br /><br />Il offre la meilleure persistance opérationnelle. Toutefois, il ne garantit aucune perte de données.   |
| **Disponibilité maximale** | Fournit le niveau de protection le plus élevé sans affecter la disponibilité de la base de données primaire. Les données ne sont jamais considérées comme validées dans la base de données principale jusqu’à ce qu’elles aient également été validées dans au moins une base de données de secours. Si la base de données principale ne peut pas écrire les modifications de rétablissement dans au moins une base de données de secours, elle revient au mode Performances maximales au lieu de devenir indisponible. <br /><br />Elle permet au service de continuer de fonctionner si le site de secours n’est pas disponible. Si un seul site fonctionne, une seule copie des données est conservée jusqu’à ce que le deuxième site soit en ligne et que la synchronisation soit rétablie. |
| **Protection maximale** | Offre un niveau de protection similaire à la disponibilité maximale. La base de données primaire s’arrête avec la fonctionnalité ajoutée s’il ne peut pas écrire les modifications de rétablissement dans au moins une base de données de secours. Cela permet de s’assurer que la perte de données ne peut pas se produire, mais au détriment d’une disponibilité plus fragile. |

>[!IMPORTANT]
>Si vous avez besoin d’un objectif de point de récupération (RPO) de zéro, nous vous recommandons d’utiliser la configuration Disponibilité maximale. Le RPO peut alors être garanti même lorsque plusieurs défaillances se produisent. Par exemple, même dans le cas d’une panne réseau de la base de données primaire suivie de la perte de la base de données principale, alors que la panne du réseau est toujours en vigueur.

### <a name="data-guard-deployment-patterns"></a>Modèles de déploiement Data Guard

Oracle vous permet de configurer plusieurs destinations pour la génération de restauration par progression, en autorisant plusieurs bases de données de secours. La configuration la plus courante est illustrée dans la figure suivante, une seule base de données de secours dans une autre région.

:::image type="content" source="media/oracle-high-availability/default-data-guard-deployment.png" alt-text="Diagramme montrant le déploiement de Data Guard par défaut d’Oracle.":::

Data Guard est configuré en mode de Performances maximales pour un déploiement par défaut. Cette configuration fournit une réplication des données en temps quasi réel via le transport de restauration asynchrone. La base de données de secours n’a pas besoin d’être exécutée à l’intérieur d’un déploiement RAC, mais nous vous recommandons de répondre aux demandes en matière de performances du site principal.

Nous recommandons un déploiement comme celui illustré dans la figure suivante pour les environnements qui nécessitent un temps d’activité strict ou un RPO de zéro. La configuration Disponibilité maximale est constituée d’une base de données de secours locale appliquant la restauration en mode synchrone et une deuxième base de données de secours s’exécutant dans une région distante.

:::image type="content" source="media/oracle-high-availability/max-availability-data-guard-deployment.png" alt-text="Diagramme montrant le déploiement de Data Guard en disponibilité maximale.":::

Vous pouvez créer une base de données de secours locale lorsque les performances de l’application sont affectées en exécutant la base de données et les serveurs d’applications dans des régions distinctes. Dans cette configuration, une base de données de secours locale est utilisée lorsque la maintenance planifiée ou non planifiée est nécessaire sur le cluster principal. Vous pouvez exécuter ces bases de données avec la réplication synchrone, car elles se trouvent dans la même région, ce qui garantit qu’aucune donnée n’est perdue entre elles.

### <a name="data-guard-configuration-considerations"></a>Considérations relatives à la configuration Data Guard

Le répartiteur Data Guard doit être implémenté, car il simplifie l’implémentation d’une configuration Data Guard et s’assure que les meilleures pratiques sont respectées. Il fournit des fonctionnalités d’analyse des performances et simplifie grandement les procédures de basculemen et de reinstantiation.

Data Guard vous permet d’exécuter un processus d’observateur, qui surveille toutes les bases de données dans une configuration Data Guard pour déterminer la disponibilité de la base de données. En cas de défaillance d’une base de données principale, l’observateur Data Guard peut démarrer automatiquement un basculement vers une base de données de secours dans la configuration. Vous pouvez implémenter l’observateur Data Guard avec plusieurs observateurs en fonction du nombre de sites physiques (jusqu’à trois). 

Cet observateur doit se trouver sur l’infrastructure qui prendra en charge la couche Application. L’observateur principal doit toujours exister sur le site physique où la base de données principale n’est pas localisée. Nous vous recommandons de faire attention à l’automatisation des opérations de basculement déclenchées par un observateur Data Guard. Tout d’abord, assurez-vous que vos applications sont conçues et testées pour fournir un service acceptable lorsque la base de données s’exécute dans un emplacement distinct.

Si l’application peut uniquement fonctionner en local, le basculement vers le site secondaire doit être manuel. Les environnements qui requièrent des niveaux de haute disponibilité (99,99 % ou 99,999 % de temps d’activité) doivent utiliser une base de données de secours locale et distante, comme illustré dans la figure précédente. Dans ce cas, le paramètre FastStartFailoverTarget est défini uniquement sur la base de données de secours locale.

Pour toutes les applications qui prennent en charge l’accès aux applications/bases de données entre sites, FastStartFailoverTarget est défini sur toutes les bases de données de secours dans la configuration de Data Guard.

### <a name="active-data-guard"></a>Data Guard actif

Oracle Active Data Guard (ADG) est un sur-ensemble de fonctionnalités de base de Data Guard incluses dans Oracle Database Enterprise Edition. Il fournit les fonctionnalités supplémentaires suivantes, qui seront utilisées dans le déploiement Oracle Exadata :

- Détection de corruption unique et réparation automatique.
- Basculement rapide vers le réplica synchronisé de production – manuel ou automatique.
- Décharger la charge de travail de production vers une mise en veille synchronisée ouverte en lecture seule.
- Mises à niveau et mise en veille de la base de données. Premier correctif à l’aide de la mise en veille physique.
- Déchargez les sauvegardes incrémentielles vers le mode veille.
- Protection de la récupération des données sans aucune perte sur n’importe quelle distance sans incidence sur les performances.

Le livre blanc disponible dans [https://www.oracle.com/technetwork/database/availability/dg-adg-technical-overview-wp-5347548.pdf](https://www.oracle.com/technetwork/database/availability/dg-adg-technical-overview-wp-5347548.pdf) fournit une bonne présentation des fonctionnalités précédentes, comme illustré dans la figure suivante.

:::image type="content" source="media/oracle-high-availability/active-data-guard-features.png" alt-text="Diagramme montrant une vue d’ensemble des fonctionnalités Active Data Guard d’Oracle.":::

## <a name="backup-recommendations"></a>Recommandations de sauvegarde

Veillez à sauvegarder vos bases de données. Utilisez les fonctionnalités de restauration et de récupération pour restaurer une base de données sur le même système ou un autre système, ou pour récupérer des fichiers de la base de données.

Il est important de créer une stratégie de récupération de sauvegarde pour protéger les bases de données Oracle Database Appliance contre la perte de données. Une telle perte peut résulter d’un problème physique avec un disque provoquant un échec de lecture ou d’écriture sur un fichier disque nécessaire à l’exécution de la base de données. Une erreur d’utilisateur peut également entraîner une perte de données. La fonctionnalité de sauvegarde permet de **restaurer dans le temps, restaurer la base de données, récupérer le numéro de changement de système et d’accéder à la dernière récupération**. Vous pouvez créer une stratégie de sauvegarde dans l’interface utilisateur du navigateur ou à partir de l’interface de ligne de commande.

Les options de sauvegarde suivantes sont disponibles :

- Sauvegarde sur le volume de stockage NFS (zone de récupération rapide-FRA- /u98).
- À l’aide d’Azure NetApp Files SnapCenter – instantané.

Processus à prendre en compte :

- Sauvegardes manuelles ou automatiques.
- Les sauvegardes automatiques sont écrites dans des volumes de stockage NFS (par exemple, /u98).
- Les sauvegardes ont lieu entre minuit et 6 h dans le fuseau horaire du système de la base de données.
- Périodes de rétention actuelles : 7, 15, 30, 45 et 60 jours.

- Récupérer la base de données à partir d’une sauvegarde stockée dans le stockage d’objets :
  - Vers le dernier état correct connu avec une perte de données réduite au minimum.
  - À l’aide du timestamp spécifié.
  - À l’aide du SCN spécifié.
  - BackupReport : _utilise SCN à partir du rapport de sauvegarde au lieu du SCN spécifié_.

:::image type="content" source="media/oracle-high-availability/customer-db-backup-to-fra.png" alt-text="Diagramme montrant la sauvegarde de la base de données client jusqu’à FRA (/u98) et non-FRA (/u95).":::

### <a name="backup-policy"></a>Stratégie de sauvegarde

La stratégie de sauvegarde définit les détails de la sauvegarde. Lorsque vous créez une stratégie de sauvegarde, vous définissez la destination pour les sauvegardes de base de données FRA (emplacement NFS) et vous définissez la fenêtre de récupération.

Par défaut, l’algorithme de compression BASIC est utilisé. Lorsque vous utilisez les algorithmes de compression LOW, MEDIUM ou HIGH pour une stratégie de sauvegarde sur disque ou NFS, des considérations relatives aux licences sont d’application.

### <a name="backup-levels"></a>Niveaux de sauvegarde

Spécifiez le niveau de sauvegarde lorsque vous effectuez une sauvegarde.

- Niveau 0 - Complet
- Niveau 1 – Incrémentiel
- LongTerm/Archievelog-Except pour la stratégie de rétention de sauvegarde, utilisez un emplacement autre que FRA (par exemple,/U95).

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment récupérer votre base de données Oracle en cas de défaillance :

> [!div class="nextstepaction"]
> [Récupération de votre base de données Oracle sur l’infrastructure Azure BareMetal](oracle-high-availability-recovery.md)
