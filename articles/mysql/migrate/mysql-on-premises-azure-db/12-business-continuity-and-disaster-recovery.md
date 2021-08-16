---
title: Guide de migration de MySQL local vers Azure Database pour MySQL - Continuité d’activité et reprise d’activité
description: Comme pour tout système critique, le fait de disposer d’une stratégie de sauvegarde, de restauration et de reprise d’activité est une partie importante de la conception globale de votre système.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/11/2021
ms.openlocfilehash: 4785c49c456b0008f0ec4c67cdee55d8118c76a9
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112082660"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-business-continuity-and-disaster-recovery-bcdr"></a>Guide de migration de MySQL local vers Azure Database pour MySQL - Continuité d’activité et reprise d’activité

## <a name="prerequisites"></a>Prérequis

[Optimisation](11-optimization.md)

## <a name="back-up-and-restore"></a>Sauvegarde et restauration

Comme pour tout système critique, le fait de disposer d’une stratégie de sauvegarde, de restauration et de reprise d’activité est une partie importante de la conception globale de votre système. Si un événement imprévu se produit, vous devez avoir la capacité de restaurer vos données à un point dans le temps (objectif de point de récupération) dans un laps de temps raisonnable (objectif de temps de récupération).

### <a name="backup"></a>Sauvegarde

Azure Database pour MySQL prend en charge par défaut des sauvegardes automatiques pendant 7 jours. Il peut être approprié de modifier cette valeur en la définissant sur le maximum actuel, qui est de 35 jours. Il est important de savoir que si la valeur est remplacée par 35 jours, des frais sont facturés pour le stockage des sauvegardes supplémentaires supérieur à 1x le stockage alloué.

Il existe actuellement plusieurs limitations à la fonctionnalité de sauvegarde de base de données, comme décrit dans l’article [Sauvegarde et restauration dans Azure Database pour MySQL](/azure/mysql/concepts-backup). Il est important de les comprendre pour déterminer quelles stratégies supplémentaires doivent être implémentées.

Vous devez prendre en compte les quelques éléments suivants :

- Pas d’accès direct aux sauvegardes

- Les niveaux qui autorisent jusqu’à 4 To ont une sauvegarde complète une fois par semaine, différentielle deux fois par jour et des journaux toutes les cinq minutes.

- Les niveaux qui autorisent jusqu’à 16 To ont des sauvegardes basées sur des instantanés.

    > [!NOTE]
    > [Certaines régions](/azure/mysql/concepts-pricing-tiers#storage) ne prennent pas encore en charge le stockage jusqu’à 16 To.

### <a name="restore"></a>Restaurer

La redondance (locale ou géoredondance) doit être configurée lors de la création du serveur. Cependant, une géorestauration peut être effectuée et autorise la modification de ces options lors du processus de restauration. Effectuer une opération de restauration peut arrêter temporairement la connectivité ; toutes les applications sont arrêtées pendant le processus de restauration.

Pendant une restauration de base de données, tous les éléments de prise en charge en dehors de la base de données doivent être restaurés. Passez en revue le processus de migration. Pour plus d’informations, consultez [Effectuer des tâches postérieures à la restauration](/azure/mysql/concepts-backup#perform-post-restore-tasks).

## <a name="read-replicas"></a>Réplicas en lecture

Les [réplicas en lecture](/azure/mysql/concepts-read-replicas) peuvent être utilisés pour augmenter le débit en lecture de MySQL, améliorer les performances pour les utilisateurs régionaux et pour implémenter une reprise d’activité. Quand vous créez un ou plusieurs réplicas en lecture, notez que des frais supplémentaires sont appliqués pour le même calcul et le même stockage que le serveur principal.

## <a name="deleted-servers"></a>Serveurs supprimés

Si un administrateur ou une personne malveillante supprime le serveur dans le portail Azure ou via des méthodes automatisées, toutes les sauvegardes et les réplicas en lecture sont supprimés. Il est important que des [verrous de ressource](/azure/azure-resource-manager/management/lock-resources) soient créés sur le groupe de ressources Azure Database pour MySQL afin d’ajouter aux instances une couche supplémentaire de protection contre la suppression.

## <a name="regional-failure"></a>Défaillance au niveau d’une région

Bien que rare, si une défaillance se produit au niveau d’une région, les sauvegardes géoredondantes ou un réplica en lecture peuvent être utilisés pour que les charges de travail de données fonctionnent à nouveau. Il est préférable d’avoir à la fois une géoréplication et un réplica en lecture disponibles pour une meilleure protection contre des défaillances inattendues au niveau d’une région.

> [!NOTE]
> Changer la région du serveur de base de données signifie également que le point de terminaison change et que les configurations des applications doivent être mises à jour en conséquence.

### <a name="load-balancers"></a>Équilibreurs de charge

Si l’application est constituée de nombreuses instances différentes dans le monde, il n’est pas possible de mettre à jour tous les clients. Utilisez un [équilibreur de charge Azure](/azure/load-balancer/load-balancer-overview) ou une [passerelle d’application](/azure/application-gateway/overview) pour implémenter une fonctionnalité de basculement fluide. Bien que pratiques et permettant de gagner du temps, ces outils ne sont pas nécessaires pour la fonctionnalité de basculement entre régions.

## <a name="wwi-scenario"></a>Scénario WWI

WWI voulait tester les fonctionnalités de basculement des réplicas en lecture : ils ont donc effectué les étapes décrites ci-dessous.

### <a name="creating-a-read-replica"></a>Création d’un réplica en lecture

- Ouvrez le portail Azure.

- Accédez à l’instance Azure Database pour MySQL.

- Sous **Paramètres**, sélectionnez **Réplication**.

- Sélectionnez **Ajouter un réplica**.

- Tapez un nom de serveur.

- Sélectionnez la région.

- Sélectionnez **OK** et attendez que l’instance soit déployée. Selon la taille de l’instance principale, la réplication peut prendre un certain temps.

    > [!NOTE]
    > Chaque réplica induit des frais supplémentaires égaux à ceux de l’instance principale.

### <a name="fail-over-to-read-replica"></a>Basculer sur ce réplica en lecture

Une fois qu’un réplica en lecture a été créé et qu’il a terminé le processus de réplication, il peut être utilisé pour un basculement. La réplication s’arrête pendant un basculement et fait du réplica en lecture sa propre instance principale.

Étapes du basculement :

- Ouvrez le portail Azure.

- Accédez à l’instance Azure Database pour MySQL.

- Sous **Paramètres**, sélectionnez **Réplication**.

- Sélectionnez un des réplicas en lecture.

- Sélectionnez **Arrêter la réplication**. Ceci interrompt le réplica en lecture.

- Modifiez toutes les chaînes de connexion des applications pour les faire pointer vers la nouvelle instance principale.

## <a name="bcdr-checklist"></a>Check-list de la continuité d’activité et de la reprise d’activité

- Modifiez la fréquence de sauvegarde de façon répondre aux exigences.

- Configurez des réplicas en lecture pour les charges de travail intensives en lecture et un basculement régional.

- Créer des verrous de ressource sur les groupes de ressources.

- Implémentez une stratégie d’équilibrage de la charge pour les applications de façon à permettre un basculement rapide.  


> [!div class="nextstepaction"]
> [Sécurité](./13-security.md)
