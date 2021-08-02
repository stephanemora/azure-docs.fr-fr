---
title: Guide de migration de MySQL local vers Azure Database pour MySQL – Continuité d’activité et reprise d’activité
description: Comme pour tout système critique, le fait de disposer d’une stratégie de sauvegarde, de restauration et de reprise d’activité (ou récupération d’urgence) est une partie importante de la conception globale de votre système.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 05/26/2021
ms.openlocfilehash: 6eb944d72f5d4add393079d9fff8e83d37a18e06
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111961538"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-business-continuity-and-disaster-recovery-bcdr"></a>Guide de migration de MySQL local vers Azure Database pour MySQL – Continuité d’activité et reprise d’activité

### <a name="backup-and-restore"></a>Sauvegarde et restauration

Comme pour tout système critique, le fait de disposer d’une stratégie de sauvegarde, de restauration et de reprise d’activité (ou récupération d’urgence) est une partie importante de la conception globale de votre système. Si un événement imprévu se produit, vous devez avoir la capacité de restaurer vos données à un point dans le temps (objectif de point de récupération) dans un laps de temps raisonnable (objectif de temps de récupération).

#### <a name="backup"></a>Sauvegarde

Azure Database pour MySQL prend en charge par défaut des sauvegardes automatiques pour 7 jours. Il peut être approprié de modifier cette valeur en la définissant sur le maximum actuel, qui est de 35 jours. Il est important de savoir que si la valeur est changée en 35 jours, des frais sont facturés pour le stockage de sauvegarde supplémentaire supérieur à 1x le stockage alloué.

Il existe actuellement plusieurs limitations à la fonctionnalité de sauvegarde de base de données, comme décrit dans l’article [Sauvegarde et restauration dans Azure Database pour MySQL](../concepts-backup.md). Il est important de les comprendre pour déterminer quelles stratégies supplémentaires doivent être implémentées.

Vous devez prendre en compte les quelques éléments suivants :

  - Pas d’accès direct aux sauvegardes

  - Les niveaux qui autorisent jusqu’à 4 To ont une sauvegarde complète une fois par semaine, différentielle deux fois par jour et des journaux toutes les cinq minutes.

  - Les niveaux qui autorisent jusqu’à 16 To ont des sauvegardes basées sur des instantanés.

    > [!NOTE] 
    > [Certaines régions](../concepts-pricing-tiers.md#storage) ne prennent pas encore en charge le stockage jusqu’à 16 To.

#### <a name="restore"></a>Restaurer

La redondance (locale ou géo-redondance) doit être configurée lors de la création du serveur. Cependant, une géo-restauration peut être effectuée et autorise la modification de ces options lors du processus de restauration. Une opération de restauration a pour effet d’interrompre temporairement la connectivité, de sorte que toutes les applications sont arrêtées pendant le processus de restauration.

Pendant une restauration de base de données, tous les éléments associés en dehors de la base de données doivent également être restaurés. Passez en revue le processus de migration. Pour plus d’informations, consultez [Effectuer des tâches postérieures à la restauration](../concepts-backup.md#perform-post-restore-tasks).

### <a name="read-replicas"></a>Réplicas en lecture

Les [réplicas en lecture](../concepts-read-replicas.md) peuvent être utilisés pour augmenter le débit en lecture de MySQL, améliorer les performances pour les utilisateurs régionaux et pour implémenter une reprise d’activité. Quand vous créez un ou plusieurs réplicas en lecture, sachez que des frais supplémentaires sont appliqués pour les mêmes capacités de calcul et de stockage que le serveur principal.

### <a name="deleted-servers"></a>Serveurs supprimés

Si un administrateur ou une personne malveillante supprime le serveur dans le portail Azure ou via des méthodes automatisées, l’ensemble des sauvegardes et des réplicas en lecture sont supprimés. Il est important de créer des [verrous de ressource](../../azure-resource-manager/management/lock-resources.md) sur le groupe de ressources Azure Database pour MySQL afin d’ajouter aux instances une couche supplémentaire de protection contre la suppression.

### <a name="regional-failure"></a>Défaillance régionale

Bien qu’une telle situation soit rare, si une défaillance se produit au niveau d’une région, l’utilisation de sauvegardes géo-redondantes ou d’un réplica en lecture permet de faire en sorte que les charges de travail de données fonctionnent à nouveau. L’idéal est d’avoir à la fois une géo-réplication et un réplica en lecture disponibles pour une meilleure protection contre des défaillances inattendues au niveau d’une région.

> [!NOTE]
> Changer la région du serveur de base de données signifie également que le point de terminaison change et que les configurations d’applications doivent être mises à jour en conséquence.

#### <a name="load-balancers"></a>Équilibreurs de charge

Si l’application est constituée de nombreuses instances différentes dans le monde, il n’est pas possible de mettre à jour tous les clients. Utilisez un [équilibreur de charge Azure](../../load-balancer/load-balancer-overview.md) ou une [passerelle d’application](../../application-gateway/overview.md) pour implémenter une fonctionnalité de basculement fluide. Bien que pratiques et permettant de gagner du temps, ces outils ne sont pas indispensables pour la fonctionnalité de basculement entre régions.

### <a name="wwi-scenario"></a>Scénario WWI

WWI voulait tester les fonctionnalités de basculement des réplicas en lecture : ils ont donc effectué les étapes décrites ci-dessous.

#### <a name="creating-a-read-replica"></a>Création d’un réplica en lecture

  - Ouvrez le portail Azure.

  - Accédez à l’instance Azure Database pour MySQL.

  - Sous **Paramètres**, sélectionnez **Réplication**.

  - Sélectionnez **Ajouter un réplica**.

  - Tapez un nom de serveur.

  - Sélectionnez la région.

  - Sélectionnez **OK** et attendez que l’instance soit déployée. Selon la taille de l’instance principale, la réplication peut prendre un certain temps.

    > [!NOTE]
    > Chaque réplica occasionne des frais supplémentaires égaux à ceux de l’instance principale.

#### <a name="failover-to-read-replica"></a>Basculement vers un réplica en lecture

Une fois qu’un réplica en lecture a été créé et qu’il a terminé le processus de réplication, il peut être utilisé pour un basculement. La réplication s’arrête pendant un basculement et fait du réplica en lecture sa propre instance principale.

Étapes du basculement :

  - Ouvrez le portail Azure.

  - Accédez à l’instance Azure Database pour MySQL.

  - Sous **Paramètres**, sélectionnez **Réplication**.

  - Sélectionnez un des réplicas en lecture.

  - Sélectionnez **Arrêter la réplication**. Cela a pour effet d’arrêter le réplica en lecture.

  - Modifiez toutes les chaînes de connexion des applications pour les faire pointer vers la nouvelle instance principale.

### <a name="bcdr-checklist"></a>Liste de vérification de la continuité d’activité et de la reprise d’activité

  - Modifiez la fréquence de sauvegarde de façon répondre aux exigences.

  - Configurez des réplicas en lecture pour les charges de travail intensives en lecture et un basculement régional.

  - Créer des verrous de ressource sur les groupes de ressources.

  - Implémentez une stratégie d’équilibrage de charge pour les applications de façon à permettre un basculement rapide.  


> [!div class="nextstepaction"]
> [Sécurité](./security.md)