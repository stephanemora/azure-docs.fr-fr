---
title: Vue d’ensemble de la haute disponibilité redondante interzone avec le serveur flexible Azure Database pour MySQL
description: Découvrir les concepts de la haute disponibilité redondante interzone avec le serveur flexible Azure Database pour MySQL
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 4a8f7d5bd10c21b14c8935bf0a44040501ebeb90
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123109186"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Concepts de haute disponibilité dans le serveur flexible Azure Database pour MySQL (préversion)

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT] 
> Azure Database pour MySQL - Serveur flexible est actuellement en préversion publique.

Le serveur flexible Azure Database pour MySQL (préversion) permet de configurer la haute disponibilité avec basculement automatique. La solution haute disponibilité est conçue pour éviter toute perte de données en raison de défaillances, et que la base de données devienne un point de défaillance unique dans votre architecture logicielle. Quand la haute disponibilité est configurée, le serveur flexible approvisionne et gère automatiquement un réplica de secours. Deux modèles d'architecture à haute disponibilité sont disponibles :

* **Haute disponibilité redondante interzone** : cette option est recommandée pour une isolation et une redondance complètes de l’infrastructure sur plusieurs zones de disponibilité. Elle offre le niveau de disponibilité le plus élevé, mais vous oblige à configurer la redondance des applications entre zones. La haute disponibilité redondante interzone est préférable quand vous voulez obtenir le niveau de disponibilité le plus élevé en cas de défaillance de l’infrastructure dans la zone de disponibilité et où la latence dans la zone de disponibilité est acceptable. La haute disponibilité redondante interzone ne peut être activée qu’au moment de la création du serveur. La haute disponibilité redondante interzone est disponible dans un [sous-ensemble de régions Azure](./overview.md#azure-regions), où chaque région prend en charge plusieurs [zones de disponibilité](../../availability-zones/az-overview.md) et où des [partages de fichiers Premium redondants interzone](../..//storage/common/storage-redundancy.md#zone-redundant-storage) sont disponibles.

* **Haute disponibilité dans la même zone** : cette option est préférable pour la redondance de l’infrastructure avec une latence réseau inférieure, car le serveur principal et le serveur de secours se trouvent dans la même zone de disponibilité. Elle offre une haute disponibilité sans qu’il soit nécessaire de configurer la redondance des applications entre les zones. La haute disponibilité dans la même zone est préférable quand vous voulez obtenir le niveau de disponibilité le plus élevé au sein d’une même zone de disponibilité avec la latence réseau la plus faible. La haute disponibilité dans la même zone est disponible dans toutes les [régions Azure](./overview.md#azure-regions) où nous pouvons créer un serveur flexible Azure Database pour MySQL.  

## <a name="zone-redundancy-high-availability-architecture"></a>Architecture de haute disponibilité redondante interzone
 
Lorsque vous déployez un serveur avec l’option de haute disponibilité redondante interzone, vous disposez d’un serveur principal créé dans une zone de disponibilité, et d’un serveur réplica de secours présentant la même configuration que le serveur principal (niveau de calcul, taille de calcul, taille de stockage et configuration réseau) dans une autre zone de disponibilité au sein de la même région Azure. Vous pouvez spécifier la zone de disponibilité de votre choix pour le serveur principal et le réplica de secours. La colocalisation des serveurs de base de données de secours et des applications de secours dans la même zone réduit les latences et permet aux utilisateurs de mieux se préparer à des situations de reprise d’activité d’urgence, ainsi qu’à des scénarios de zone défaillante.

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="Haute disponibilité redondante interzone":::

Les fichiers de données et les fichiers journaux sont hébergés dans un [stockage redondant interzone (ZRS)](../../storage/common/storage-redundancy.md#redundancy-in-the-primary-region). Avec la réplication au niveau du stockage disponible avec le stockage redondant interzone, les fichiers de données et les fichiers journaux sont répliqués sur le serveur de secours. En cas de basculement, le réplica de secours est activé et les fichiers journaux binaires du serveur principal continuent à s’appliquer au serveur de secours afin de mettre celui-ci en ligne jusqu’à la dernière transaction validée sur le serveur principal. Les journaux étant dans le stockage redondant interzone, ils restent accessibles quand le serveur principal est indisponible, évitant ainsi toute perte de données. Une fois le réplica de secours activé et les journaux binaires appliqués, le serveur réplica de secours actif prend les rôles du serveur principal. Le DNS est mis à jour afin que les connexions client soient dirigées vers le nouveau serveur principal quand le client se reconnecte. Le basculement est entièrement transparent depuis l’application cliente et ne nécessite aucune action de l’utilisateur. La solution haute disponibilité rétablit ensuite l’ancien serveur principal dès que possible et en fait le serveur de secours.
 
Les applications sont connectées au serveur principal à l’aide du nom du serveur de base de données. Les informations du réplica de secours ne sont pas exposées à un accès direct. Les validations et les écritures sont reconnues après vidage des fichiers journaux sur le stockage redondant interzone (ZRS) du serveur principal. En raison de la technologie de réplication synchrone utilisée pour le stockage redondant interzone, les applications peuvent s’attendre à une latence accrue de 5 à 10 % pour les écritures et les validations.
 
Les sauvegardes automatiques, qu’il s’agisse d’instantanés ou de sauvegardes de fichiers journaux, sont effectuées sur un stockage redondant interzone depuis le serveur de base de données principal.

## <a name="same-zone-high-availability-architecture"></a>Architecture de haute disponibilité dans la même zone
 
Lorsque vous déployez un serveur avec l’option de haute disponibilité dans la même zone, vous disposez d’un serveur principal et d’un serveur réplica de secours créé dans la même zone avec la même configuration que le serveur principal (niveau de calcul, taille de calcul, taille de stockage et configuration réseau). Le serveur de secours offre une redondance de l’infrastructure avec une machine virtuelle distincte (calcul) qui réduit le temps de basculement et la latence du réseau entre l’application utilisateur et le serveur de base de données en raison de la colocalisation.

:::image type="content" source="./media/concepts-high-availability/flexible-server-overview-same-zone-ha.png" alt-text="Haute disponibilité redondante dans la même zone":::

Les fichiers de données et les fichiers journaux sont hébergés dans un [stockage localement redondant (LRS)](../../storage/common/storage-redundancy.md#locally-redundant-storage). Grâce la réplication au niveau du stockage disponible avec le stockage localement redondant, les fichiers de données et les fichiers journaux sont répliqués sur le serveur de secours.

En cas de basculement, le réplica de secours est activé et les fichiers journaux binaires du serveur principal continuent à s’appliquer au serveur de secours afin de mettre celui-ci en ligne jusqu’à la dernière transaction validée sur le serveur principal. Les journaux étant dans le stockage localement redondant, ils restent accessibles quand le serveur principal est indisponible, évitant ainsi toute perte de données. Une fois le réplica de secours activé et les journaux binaires appliqués, le réplica de secours actif prend les rôles du serveur principal. Le DNS est mis à jour pour rediriger les connexions vers le nouveau serveur principal lorsque le client se reconnecte. Le basculement est entièrement transparent depuis l’application cliente et ne nécessite aucune action de l’utilisateur. La solution haute disponibilité rétablit ensuite l’ancien serveur principal dès que possible et en fait le serveur de secours.
 
Les applications sont connectées au serveur principal à l’aide du nom du serveur de base de données. Les informations du réplica de secours ne sont pas exposées à un accès direct. Les validations et les écritures sont reconnues après vidage des fichiers journaux au niveau du stockage localement redondant du serveur principal. Le serveur principal et le réplica de secours opérant sur la même zone, le décalage de réplication est moindre, et les latences plus faibles entre le serveur d’applications et le serveur de base de données si celui-ci est placé dans la même zone de disponibilité Azure. La configuration de haute disponibilité dans la même zone n’offre pas de haute disponibilité pour le scénario dans lequel des infrastructures dépendantes sont défaillantes pour la zone de disponibilité concernée.   Vous êtes confronté à un temps d’arrêt jusqu’à ce que tous les services dépendants reviennent en ligne pour cette zone de disponibilité.
 
Les sauvegardes automatiques, qu’il s’agisse d’instantanés ou de sauvegardes de fichiers journaux, sont effectuées sur un stockage localement redondant à partir du serveur de base de données principal.

>[!Note]
>Tant pour la haute disponibilité redondante interzone et que pour la haute disponibilité dans la même zone,
>* En cas de défaillance, le temps nécessaire pour que le réplica de secours prenne le rôle de serveur principal dépend de l’application de journal binaire sur le serveur de secours. Il est donc recommandé d’utiliser des clés primaires sur toutes les tables pour réduire le temps de basculement. Les temps de basculement sont généralement compris entre 60 et 120 secondes. 
>* Le serveur de secours n’est pas disponible pour les opérations de lecture ou d’écriture, il s’agit en fait d’un serveur passif qui permet de basculer rapidement.
>* Utilisez toujours le nom de domaine complet (FQDN) pour vous connecter à votre serveur principal et évitez d’utiliser l’adresse IP pour vous connecter. En cas de basculement, une fois que le rôle de serveur principal et de secours est basculé, l’enregistrement A DNS peut être modifié, ce qui empêche l’application de se connecter au nouveau serveur principal si l’adresse IP est utilisée dans la chaîne de connexion.

## <a name="failover-process"></a>Processus de basculement 
 
### <a name="planned---forced-failover"></a>Planifié – Basculement forcé
 
Le basculement forcé de serveur flexible Azure Database pour MySQL vous permet de forcer manuellement un basculement afin de tester la fonctionnalité dans vos scénarios d’application, et d’ainsi être prêt en cas de panne. Le basculement forcé permet au serveur de secours de devenir le serveur principal en déclenchant un basculement qui active le réplica de secours pour qu’il devienne le serveur principal avec le même nom de serveur de base de données en mettant à jour l’enregistrement DNS. Le serveur principal d’origine est redémarré et devient le réplica de secours. Les connexions des clients sont déconnectées et doivent être reconnectées pour reprendre leurs opérations. Le temps de basculement global dépendra de la charge de travail actuelle et du dernier point de contrôle. En général, il est compris entre 60 et 120 secondes.
 
### <a name="unplanned--automatic-failover"></a>Non planifié – Basculement automatique 
 
Les interruptions de service non planifiées incluent les bogues logiciels, ou des erreurs d’infrastructure telles que des défaillances de calcul, de réseau, de stockage, voire encore des pannes de courant. Elles ont une incidence sur la disponibilité de la base de données. En cas d’indisponibilité de la base de données, la réplication sur le réplica de secours est interrompue, et le réplica de secours est activé pour devenir la base de données primaire. Le système DNS est mis à jour et les clients se reconnectent ensuite au serveur de base de données pour reprendre leurs opérations. Le temps de basculement global doit prendre entre 60 et 120 s. Toutefois, en fonction de l’activité sur le serveur de base de données primaire au moment du basculement, par exemple si des transactions sont volumineuses et que le temps de récupération est long, le basculement peut prendre plus de temps.

## <a name="high-availability-monitoring"></a>Supervision de la haute disponibilité
L’intégrité de la haute disponibilité est supervisée et signalée en permanence dans la page Vue d’ensemble. Les différents états de réplication sont listés ci-dessous :

| **État** | **Description** |
| :----- | :------ |
| <b>NotEnabled | La haute disponibilité redondante interzone n’est pas activée |
| <b>ReplicatingData | Une fois créé, le réplica de secours rattrape le serveur principal. |
| <b>FailingOver | Le serveur de base de données est en cours de basculement, du serveur principal vers le réplica de secours. |
| <b>Healthy | La haute disponibilité redondante interzone présente un état stable et sain. |
| <b>RemovingStandby | Selon l’action de l’utilisateur, le réplica de secours est en cours de suppression.| 

##  <a name="limitations"></a>Limites 
 
Voici quelques réflexions à prendre en compte lorsque vous utilisez la haute disponibilité :
* La haute disponibilité redondante interzone ne peut être définie que lors de la création du serveur flexible.
* La haute disponibilité n’est pas prise en charge au niveau du calcul expansible.
* Un redémarrage du serveur de base de données primaire pour récupérer les modifications de paramètres statiques a également pour effet de redémarrer le serveur réplica de secours.
* La configuration de réplicas en lecture pour les serveurs à haute disponibilité redondante interzone n’est pas prise en charge.
* La réplication des données entrantes n’est pas prise en charge pour les serveurs haute disponibilité 
* Le mode GTID est activé parce que la solution HA utilise un identificateur de transaction global (Global Transaction Identifier, GTID). Vérifiez si votre charge de travail présente des [restrictions ou des limitations en lien avec la réplication avec GTID](https://dev.mysql.com/doc/refman/5.7/en/replication-gtids-restrictions.html).  
 
## <a name="frequently-asked-questions-faqs"></a>Forum Aux Questions (FAQ)
 
**Puis-je utiliser le réplica de secours pour des opérations de lecture ou d’écriture ?** </br>
Le serveur de secours n’est pas disponible pour les opérations de lecture ou d’écriture, il s’agit en fait d’un serveur passif qui permet de basculer rapidement.</br>
**Le basculement entraîne-t-il une perte de données ?**</br>
Les journaux étant dans le stockage redondant interzone, ils restent accessibles quand le serveur principal est indisponible, ce qui évite toute perte de données. Une fois le réplica de secours activé et les journaux binaires appliqués, le réplica prend les rôles du serveur principal. </br>
**Les utilisateurs doivent-ils faire quelque chose après un basculement ?**</br>
Le basculement est entièrement transparent depuis l’application cliente et ne nécessite aucune action de l’utilisateur. L’application doit simplement implémenter la logique de nouvelle tentative pour ses connexions. </br>
**Que se passe-t-il si je ne sélectionne pas une zone spécifique pour mon réplica de secours ? Puis-je modifier cela par la suite ?**</br>
Si vous ne sélectionnez aucune zone, une zone autre que celle utilisée pour le serveur principal est sélectionnée de manière aléatoire. Pour modifier la zone ultérieurement, vous pouvez désactiver le mode haute disponibilité, puis rétablir la disponibilité redondante interzone en choisissant la zone qui vous convient dans le panneau Haute disponibilité.</br>
**La réplication entre le serveur principal et le serveur réplica de secours est-elle synchrone ?**</br>
 La solution de réplication entre le serveur principal et le serveur de secours peut être considérée comme similaire au [mode semi synchrone](https://dev.mysql.com/doc/refman/5.7/en/replication-semisync.html) dans MySQL. Quand une transaction est validée, il n’est pas indispensable de la valider sur le serveur de secours.  En revanche, quand le serveur principal est indisponible, nous pouvons nous assurer que le serveur de secours est en mesure de répliquer toutes les modifications de données opérées sur le serveur principal, et qu’il n’y a aucune perte de données.</br> 
**Un basculement est-il opéré vers le réplica de secours dans tous les cas de défaillance non planifiée ?**</br>
En cas de plantage d’une base de données ou de défaillance d’un nœud, la machine virtuelle du serveur flexible est redémarrée sur le même nœud. En même temps, un basculement automatique est déclenché. Si le redémarrage de la machine virtuelle du serveur flexible réussit avant la fin du basculement, l’opération de basculement est annulée. L’option qui prend le moins de temps est alors prise en compte pour décider du serveur qui fera office de serveur principal.</br>
**L’utilisation de la solution haute disponibilité a-t-elle une incidence sur les performances ?**</br>
La haute disponibilité redondante interzone peut entraîner une chute de la latence de 5 à 10 % si l’application se connecte au serveur de base de données entre des zones de disponibilité où la latence du réseau est</br> relativement supérieure, de l’ordre de 2 à 4 ms. Pour la haute disponibilité dans la zone, comme le serveur principal et le serveur réplica de secours opèrent sur la même zone, le décalage de réplication est moindre, et les latences plus faibles entre le serveur d’applications et le serveur de base de données si celui-ci est placé dans la même zone de disponibilité Azure.</br>
**Comment la maintenance de mon serveur haute disponibilité se produit-elle ?**</br>
Les événements planifiés, tels que la mise à l’échelle du calcul et les mises à niveau de version mineure, se produisent en même temps sur le serveur principal et le réplica de secours. Vous pouvez définir la [fenêtre de maintenance planifiée](./concepts-maintenance.md) pour un serveur haute disponibilité de la même façon que pour un serveur flexible. Le temps d’arrêt serait le même que pour le serveur flexible Azure Database pour MySQL avec la haute disponibilité désactivée. L’utilisation du mécanisme de basculement afin de réduire le temps d’arrêt pour les serveurs haute disponibilité est à l’étude, et la fonctionnalité sera bientôt disponible. </br>
**Puis-je effectuer une restauration à un instant dans le passé (PITR) de mon serveur haute disponibilité ?**</br>
Vous pouvez effectuer une [PITR](./concepts-backup-restore.md#point-in-time-restore) d’un serveur flexible Azure Database pour MySQL pour lequel la haute disponibilité est activée vers un nouveau serveur flexible Azure Database pour MySQL pour lequel la haute disponibilité est désactivée. Si le serveur source a été créé avec une haute disponibilité redondante interzone, vous pouvez par la suite activer la haute disponibilité redondante interzone ou la haute disponibilité dans la même zone sur le serveur restauré. Si le serveur source a été créé avec une haute disponibilité dans la même zone, vous pouvez uniquement activer la haute disponibilité dans la même zone sur le serveur restauré.</br>
**Puis-je activer la haute disponibilité pour un serveur après sa création ?**</br>
Vous pouvez activer la haute disponibilité dans la même zone après la création du serveur, mais l’option de haute disponibilité redondante interzone ne peut être sélectionnée qu’au moment de la création du serveur.</br> 
**Puis-je désactiver la haute disponibilité après la création du serveur ?** </br>
Après la création du serveur, vous pouvez désactiver la haute disponibilité et la facturation s’arrête immédiatement  </br>
**Comment puis-je réduire le temps d’arrêt ?**</br>
Même si vous n’utilisez pas la fonctionnalité de haute disponibilité, vous devez pouvoir réduire le temps d’arrêt de votre application. La planification d’interruptions de service, par exemple, à des fins de mise à jour corrective, de mise à niveau de version mineure ou d’opérations initiées par l’utilisateur telles qu’une mise à l’échelle du calcul, peut être effectuée dans le cadre de fenêtres de maintenance planifiée, car celles-ci entraînent un temps d’arrêt . Pour limiter l’impact des tâches de maintenance lancées par Azure sur une application, vous pouvez choisir de planifier ces tâches au jour de la semaine et à l’heure qui ont le moins d’impact sur l’application.</br>
**Est-il possible d’avoir un réplica en lecture pour un serveur pour lequel la haute disponibilité est activée ?**</br>
Actuellement, un serveur pour lequel la haute disponibilité est activée ne prend pas en charge le réplica en lecture. Toutefois, un réplica en lecture pour les serveurs haute disponibilité est à l’étude, et nous travaillons pour rendre cette fonctionnalité disponible prochainement.</br>
**Puis-je utiliser la réplication des données entrantes pour des serveurs haute disponibilité ?**</br>
Actuellement, la réplication des données entrantes n’est pas prise en charge pour les serveurs haute disponibilité. En revanche, la réplication des données entrantes pour les serveurs haute disponibilité est à l’étude, et nous travaillons pour rendre cette fonctionnalité disponible prochainement. Pour le moment, si vous souhaitez utiliser la fonctionnalité de réplication des données entrantes pour la migration, vous pouvez suivre les étapes suivantes.
* Créer le serveur avec la haute disponibilité redondante interzone
* Désactiver la haute disponibilité
* Suivez l’article expliquant comment [configurer la réplication des données entrantes](./concepts-data-in-replication.md) (assurez-vous que le paramétrage du mode GTID est identique sur les serveurs source et cible).
* Après le basculement, supprimez les données dans la configuration de la réplication des données entrantes
* Activer la haute disponibilité

**Puis-je basculer vers un serveur de secours pour réduire le temps d’arrêt pendant le redémarrage du serveur ou une mise à l’échelle ?** </br>
Actuellement, lors d’une opération de mise à l’échelle, les serveurs principal et de secours étant mis à l’échelle en même temps, un basculement ne serait d’aucune utilité. En revanche, l’approche d’une mise à l’échelle du serveur de secours en premier, suivie d’un basculement et de la mise à l’échelle du serveur principal, est à l’étude mais pas encore prise en charge.</br>


## <a name="next-steps"></a>Étapes suivantes

- Découvrir la [continuité de l’activité](./concepts-business-continuity.md)
- Découvrir la [haute disponibilité avec redondance interzone](./concepts-high-availability.md)
- Découvrir [la sauvegarde et la récupération](./concepts-backup-restore.md)