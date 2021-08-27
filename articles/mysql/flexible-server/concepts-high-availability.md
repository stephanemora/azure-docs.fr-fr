---
title: Vue d’ensemble de la haute disponibilité redondante interzone avec le serveur flexible Azure Database pour MySQL
description: Découvrir les concepts de la haute disponibilité redondante interzone avec le serveur flexible Azure Database pour MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: d78bb5aeb111411641508b38bd49a6f5bcbf5374
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122525580"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Concepts de haute disponibilité dans le serveur flexible Azure Database pour MySQL (préversion)

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT] 
> Azure Database pour MySQL - Serveur flexible est actuellement en préversion publique.

Le serveur flexible Azure Database pour MySQL permet de configurer la haute disponibilité avec le basculement automatique. Quand la haute disponibilité est configurée, le serveur flexible provisionne et gère automatiquement un réplica de secours en utilisant deux options différentes.

* **Haute disponibilité redondante interzone** : cette option est recommandée pour une isolation et une redondance complètes de l’infrastructure sur plusieurs zones de disponibilité. Elle fournit le niveau de disponibilité le plus élevé, mais vous oblige à configurer la redondance des applications interzone. La haute disponibilité redondante interzone est préférable quand vous voulez obtenir le niveau de disponibilité le plus élevé en cas de défaillance de l’infrastructure dans la zone de disponibilité et où la latence dans la zone de disponibilité est acceptable. La haute disponibilité redondante interzone est disponible dans un [sous-ensemble des régions Azure](https://docs.microsoft.com/azure/mysql/flexible-server/overview#azure-regions), où la région prend en charge plusieurs zones de disponibilité et où la haute disponibilité redondante interzone est disponible.

* **Haute disponibilité dans la même zone** : Cette option est préférable pour la redondance de l’infrastructure avec une latence réseau inférieure, car le serveur principal et le serveur de secours se trouveront dans la même zone de disponibilité. Elle offre une haute disponibilité sans qu’il soit nécessaire de configurer la redondance des applications entre les zones. La haute disponibilité dans la même zone est préférable quand vous voulez obtenir le niveau de disponibilité le plus élevé au sein d’une même zone de disponibilité avec la latence réseau la plus faible. La haute disponibilité dans la même zone est disponible dans toutes les [régions Azure où Serveur flexible est disponible](https://docs.microsoft.com/azure/mysql/flexible-server/overview#azure-regions).  

## <a name="zone-redundant-high-availability"></a>Haute disponibilité redondante interzone

Quand le serveur flexible est créé avec la haute disponibilité redondante interzone activée, les fichiers de données et les fichiers journaux sont hébergés dans un [stockage redondant interzone (ZRS)](../../storage/common/storage-redundancy.md#redundancy-in-the-primary-region). Avec la réplication au niveau du stockage disponible avec le stockage redondant interzone, les fichiers de données et les fichiers journaux sont répliqués de façon synchrone sur le serveur de secours pour garantir l’absence de perte de données. Le basculement est entièrement transparent depuis l’application cliente et ne nécessite aucune action de l’utilisateur. La récupération du serveur de secours pour permettre la mise en ligne pendant le basculement dépend de l’application de journaux binaires sur le serveur de secours. Il est donc recommandé d’utiliser des clés primaires sur toutes les tables pour réduire le temps de basculement. Le serveur de secours n’est pas disponible pour les opérations de lecture ou d’écriture, il s’agit en fait d’un serveur passif qui permet de basculer rapidement. Les temps de basculement sont généralement compris entre 60 et 120 secondes.

> [!Note]
> La haute disponibilité redondante interzone peut entraîner une chute de la latence de 5 à 10 % si l’application se connecte au serveur de base de données entre des zones de disponibilité où la latence du réseau est relativement supérieure, de l’ordre de 2 à 4 ms. 

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="Haute disponibilité redondante interzone":::

### <a name="zone-redundancy-architecture"></a>Architecture de la redondance de zone

Le serveur principal est déployé dans la région et dans une zone de disponibilité spécifique. Quand la haute disponibilité est choisie, un serveur réplica de secours avec la même configuration que celle du serveur principal est déployé automatiquement dans la « zone de disponibilité spécifiée », avec le niveau de calcul, la taille de calcul, la taille de stockage et la configuration réseau. Les données des journaux sont répliquées de façon synchrone sur le réplica de secours afin de garantir aucune perte de données en cas de panne. Les sauvegardes automatiques, qu’il s’agisse d’instantanés ou de sauvegardes de fichiers journaux, sont effectuées sur un stockage redondant interzone depuis le serveur de base de données principal.

### <a name="standby-zone-selection"></a>Sélection de la zone de secours
Dans un scénario de haute disponibilité redondante interzone, vous pouvez choisir l’emplacement de la zone du serveur de secours de votre choix. La colocalisation des serveurs de base de données de secours et des applications de secours dans la même zone réduit les latences et permet aux utilisateurs de mieux se préparer à des situations de reprise d’activité d’urgence et à des scénarios de « zone à l’arrêt ».

## <a name="same-zone-high-availability"></a>Haute disponibilité dans la même zone

Quand le serveur flexible est créé avec la haute disponibilité dans la même zone activée, les fichiers de données et les fichiers journaux sont hébergés dans un [stockage localement redondant (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy#locally-redundant-storage). Avec la réplication au niveau du stockage disponible avec le stockage localement redondant, les fichiers de données et les fichiers journaux sont répliqués de façon synchrone sur le serveur de secours pour garantir l’absence de perte de données. Le serveur de secours offre une redondance de l’infrastructure avec une machine virtuelle distincte (calcul) qui réduit le temps de basculement et la latence du réseau entre l’application utilisateur et le serveur de base de données en raison de la colocalisation. Le basculement est entièrement transparent depuis l’application cliente et ne nécessite aucune action de l’utilisateur. La récupération du serveur de secours pour permettre la mise en ligne pendant le basculement dépend de l’application de journaux binaires sur le serveur de secours. Il est donc recommandé d’utiliser des clés primaires sur toutes les tables pour réduire le temps de basculement. Le serveur de secours n’est pas disponible pour les opérations de lecture ou d’écriture, il s’agit en fait d’un serveur passif qui permet de basculer rapidement. Les temps de basculement sont généralement compris entre 60 et 120 secondes.

La haute disponibilité dans la même zone permet aux utilisateurs de placer un serveur de secours dans la même zone que le serveur principal, ce qui réduit le décalage de la réplication entre le serveur principal et les serveurs secondaires. Ceci permet également de réduire les latences entre le serveur d’applications et le serveur de base de données s’il est placé dans la même zone de disponibilité Azure.

:::image type="content" source="./media/concepts-high-availability/flexible-server-overview-same-zone-ha.png" alt-text="Haute disponibilité redondante dans la même zone":::

## <a name="high-availability-monitoring"></a>Supervision de la haute disponibilité
L’intégrité de la haute disponibilité est supervisée et signalée en permanence dans la page Vue d’ensemble. Les différents états de réplication sont listés ci-dessous :

| **État** | **Description** |
| :----- | :------ |
| <b>NotEnabled | La haute disponibilité redondante interzone n’est pas activée |
| <b>ReplicatingData | Une fois créé, le réplica de secours rattrape le serveur principal. |
| <b>FailingOver | Le serveur de base de données est en cours de basculement, du serveur principal vers le réplica de secours. |
| <b>Healthy | La haute disponibilité redondante interzone présente un état stable et sain. |
| <b>RemovingStandby | Selon l’action de l’utilisateur, le réplica de secours est en cours de suppression.| 

## <a name="advantages"></a>Avantages

Voici quelques avantages à utiliser la fonctionnalité de haute disponibilité avec redondance interzone : 

- Le réplica de secours est déployé dans une configuration de machine virtuelle à l’identique de celle du serveur principal, par exemple les vCores, le stockage, les paramètres réseau (réseau virtuel, pare-feu), etc.
- Possibilité de supprimer le réplica de secours en désactivant la haute disponibilité.
- Les sauvegardes automatiques sont basées sur un instantané, sont effectuées à partir du serveur de base de données principal et sont stockées dans un stockage redondant interzone ou dans un stockage localement redondant, selon l’option de haute disponibilité.
- En cas de basculement, le serveur flexible Azure Database pour MySQL bascule automatiquement vers le réplica de secours si la haute disponibilité est activée. La configuration de la haute disponibilité surveille le serveur principal et le remet en ligne.
- Les clients se connectent toujours au serveur de base de données primaire.
- En cas de plantage d’une base de données ou de défaillance d’un nœud, la machine virtuelle du serveur flexible est redémarrée sur le même nœud. En même temps, un basculement automatique est déclenché. Si le redémarrage de la machine virtuelle du serveur flexible réussit avant la fin du basculement, l’opération de basculement est annulée.
- Possibilité de redémarrer le serveur pour intégrer toute modification de paramètre du serveur statique.

## <a name="steady-state-operations"></a>Opérations à l’état stable

Les applications sont connectées au serveur principal à l’aide du nom du serveur de base de données. Les informations du réplica de secours ne sont pas exposées à un accès direct. Les validations et les écritures sont reconnues après vidage des fichiers journaux sur le stockage redondant interzone (ZRS) du serveur principal. En raison de la technologie de réplication synchrone utilisée dans le stockage ZRS, les applications peuvent s’attendre à une latence mineure pour les écritures et les validations.

## <a name="failover-process"></a>Processus de basculement 
Afin de préserver la continuité des activités, vous devez disposer d’un processus de basculement pour les événements planifiés et non planifiés. 

>[!NOTE]
> Utilisez toujours le nom de domaine complet (FQDN) pour vous connecter à votre serveur principal et évitez d’utiliser l’adresse IP pour vous connecter. En cas de basculement, une fois que le rôle de serveur principal et de secours est basculé, l’enregistrement A DNS peut être modifié, ce qui empêche l’application de se connecter au nouveau serveur principal si l’adresse IP est utilisée dans la chaîne de connexion. 

### <a name="planned-events---forced-failover"></a>Événements planifiés - Basculement forcé

Le basculement forcé d’Azure Database pour MySQL vous permet de forcer manuellement un basculement, ce qui vous permet de tester la fonctionnalité dans vos scénarios d’application et vous aide à être prêt en cas de panne. Le basculement forcé permet au serveur de secours de devenir le serveur principal en déclenchant un basculement qui active le réplica de secours pour qu’il devienne le serveur principal avec le même nom de serveur de base de données en mettant à jour l’enregistrement DNS. Le serveur principal d’origine est redémarré et devient le réplica de secours. Les connexions des clients sont déconnectées et doivent être reconnectées pour reprendre leurs opérations. Le temps de basculement global dépendra de la charge de travail actuelle et du dernier point de contrôle. En général, il doit être compris entre 60 et 120 secondes. 

### <a name="failover-process---unplanned-events"></a>Processus de basculement – événements non planifiés
Les interruptions de service non planifiées comprennent les bogues logiciels, ou les erreurs d’infrastructure telles que les défaillances de calcul, de réseau, de stockage, ou encore les pannes de courant ; elles ont un impact sur la disponibilité de la base de données. En cas d’indisponibilité de la base de données, la réplication sur le réplica de secours est interrompue, et le réplica de secours est activé pour devenir la base de données primaire. Le système DNS est mis à jour et les clients se reconnectent ensuite au serveur de base de données pour reprendre leurs opérations. Le temps de basculement global doit prendre entre 60 et 120 s. Toutefois, en fonction de l’activité sur le serveur de base de données primaire au moment du basculement, par exemple si des transactions sont volumineuses et que le temps de récupération est long, le basculement peut prendre plus de temps.

## <a name="schedule-maintenance-window"></a>Fenêtre Planifier la maintenance 

Les serveurs flexibles offrent une fonctionnalité de planification de la maintenance, où vous pouvez choisir un créneau de 30 minutes sur un jour de votre choix, au cours duquel la maintenance Azure s’effectue, comme une mise à jour de correctifs ou une mise à niveau de versions mineures se produirait. Pour vos serveurs flexibles configurés avec une haute disponibilité, ces activités de maintenance sont d’abord effectuées sur le réplica de secours. 

## <a name="point-in-time-restore"></a>Restauration dans le temps 
Comme le serveur flexible est configuré en haute disponibilité et qu’il réplique les données de façon synchrone, le serveur de secours est à jour par rapport au serveur principal. Toute erreur utilisateur sur le serveur principal (par exemple, la suppression accidentelle d’une table ou des mises à jour incorrectes) est répliquée fidèlement sur le serveur de secours. Par conséquent, vous ne pouvez pas utiliser le serveur de secours pour récupérer à partir de ces erreurs logiques. Pour récupérer à partir de ces erreurs logiques, vous devez procéder à une restauration à un instant dans le passé, juste avant que l’erreur ne se produise. Lorsque, à l’aide de la fonctionnalité de restauration à un instant dans le passé du serveur flexible, vous restaurez la base de données configurée avec une haute disponibilité, un nouveau serveur de base de données est restauré en tant que nouveau serveur flexible, avec le nom fourni par l’utilisateur. Vous pouvez ensuite exporter l’objet à partir du serveur de base de données, puis l’importer sur votre serveur de base de données de production. De même, si vous souhaitez cloner votre serveur de base de données à des fins de test et de développement, ou que vous voulez effectuer une restauration à d’autres fins, vous pouvez choisir le point de restauration le plus récent ou un point de restauration personnalisé. L’opération de restauration crée un serveur flexible dans une seule zone.

## <a name="mitigate-downtime"></a>Réduire les temps d’arrêt 
Lorsque vous n’utilisez pas la fonctionnalité de haute disponibilité de redondance de zone, vous devez malgré tout encore être en mesure de réduire les temps d’arrêt de votre application. La planification d’interruptions de service (par exemple, les mises à jour correctives prévues, les mises à niveau de version mineure ou les opérations initiées par l’utilisateur) peut être effectuée dans le cadre de fenêtres de maintenance planifiée. Les interruptions de service planifiées, telles que les mises à jour correctives et mises à niveau de version mineure prévues, ou les opérations lancées par l’utilisateur comme la mise à l’échelle du calcul, subissent un temps d’arrêt. Pour limiter l’impact des tâches de maintenance lancées par Azure sur une application, vous pouvez choisir de planifier ces tâches au jour de la semaine et à l’heure qui ont le moins d’impact sur l’application. 

Au cours d’événements d’interruption non planifiés, tels qu’un plantage de base de données ou une panne du serveur, le serveur concerné est redémarré dans la même zone. Bien que le cas soit rare, si la zone entière est affectée, vous pouvez restaurer la base de données sur une autre zone de la région. 

## <a name="things-to-know-with-zone-redundancy"></a>Informations à connaître sur la redondance de zone 

Voici quelques réflexions à prendre en compte lorsque vous utilisez la haute disponibilité redondante interzone : 

- La haute disponibilité peut **uniquement** être définie lors de la création du serveur flexible.
- La haute disponibilité n’est pas prise en charge au niveau du calcul expansible.
- En raison de la réplication synchrone vers une autre zone de disponibilité, le serveur de base de données primaire peut rencontrer une latence d’écriture et de validation élevée.
- Un réplica de secours ne peut pas être utilisé pour des requêtes en lecture seule.
- En fonction de l’activité sur le serveur principal au moment du basculement, l’exécution du basculement peut prendre entre 60 et 120 secondes, voire plus.
- Un redémarrage du serveur de base de données primaire pour récupérer les modifications de paramètres statiques a également pour effet de redémarrer le serveur réplica de secours.
- La configuration de réplicas en lecture pour les serveurs à haute disponibilité redondante interzone n’est pas prise en charge.
- La configuration des tâches de gestion lancées par le client ne peut pas être automatisée pendant la fenêtre de maintenance gérée.
- Les événements planifiés, tels que la mise à l’échelle du calcul et les mises à niveau de version mineure, se produisent en même temps sur le serveur principal et le réplica de secours. 


## <a name="next-steps"></a>Étapes suivantes

- Découvrir la [continuité de l’activité](./concepts-business-continuity.md)
- Découvrir la [haute disponibilité avec redondance interzone](./concepts-high-availability.md)
- Découvrir [la sauvegarde et la récupération](./concepts-backup-restore.md)