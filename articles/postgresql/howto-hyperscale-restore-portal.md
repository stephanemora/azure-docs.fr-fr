---
title: Restauration - Hyperscale (Citus) - Azure Database pour PostgreSQL - Portail Azure
description: Cet article explique comment effectuer des opérations de restauration dans Azure Database pour PostgreSQL - Hyperscale (Citus) à l’aide du portail Azure.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/14/2021
ms.openlocfilehash: 99e507fed35e5b74aa44bc2713550f7cf9d7b2e5
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518879"
---
# <a name="point-in-time-restore-of-a-hyperscale-citus-server-group"></a>Restauration à un point dans le temps d’un groupe de serveurs Hyperscale (Citus)

Cet article fournit des procédures pas à pas pour effectuer des [récupérations jusqu’à une date et heure](concepts-hyperscale-backup.md#restore) pour un groupe de serveurs Hyperscale (Citus) à l’aide de sauvegardes. Vous pouvez restaurer à la sauvegarde la plus récente ou à un point de restauration personnalisé de la période de rétention.

## <a name="restoring-to-the-earliest-restore-point"></a>Restauration au point de restauration le plus ancien

Procédez comme suit pour restaurer votre groupe de serveurs Hyperscale (Citus) à sa sauvegarde existante la plus ancienne.

1.  Dans le [portail Azure](https://portal.azure.com/), choisissez le groupe de serveurs à arrêter.

2.  Cliquez sur **Vue d’ensemble** dans le volet gauche, puis cliquez sur **Redémarrer**.

    > [!IMPORTANT]
    > Si le bouton **Restaurer** n’est pas encore présent pour votre groupe de serveurs, ouvrez une demande de support Azure.

3.  La page Restaurer vous demande de choisir entre le point de restauration **Le plus ancien** et le point de restauration **Personnalisé**, et affiche la date la plus ancienne.

4.  Sélectionnez **Point de restauration le plus ancien**.

5.  Indiquez un nouveau nom de groupe de serveurs dans le champ **Restaurer sur un nouveau serveur**. Les autres champs (abonnement, groupe de ressources et emplacement) sont affichés, mais ils ne peuvent pas être modifiés.

6.  Cliquez sur **OK**.

7.  Une notification indique que l’opération de restauration est lancée.

Enfin, suivez les [tâches post-restauration](#post-restore-tasks).

## <a name="restoring-to-a-custom-restore-point"></a>Restauration à un point de restauration personnalisé

Procédez comme suit pour restaurer votre groupe de serveurs Hyperscale (Citus) à une date et une heure de votre choix.

1.  Dans le [portail Azure](https://portal.azure.com/), choisissez le groupe de serveurs à arrêter.

2.  Cliquez sur **Vue d’ensemble** dans le volet gauche, puis cliquez sur **Redémarrer**

    > [!IMPORTANT]
    > Si le bouton **Restaurer** n’est pas encore présent pour votre groupe de serveurs, ouvrez une demande de support Azure.

3.  La page Restaurer vous demande de choisir entre le point de restauration **Le plus ancien** et le point de restauration **Personnalisé**, et affiche la date la plus ancienne.

4.  Choisissez **Point de restauration personnalisé**.

5.  Sélectionnez la date et l’heure pour le **Point de restauration (UTC)** , puis entrez un nouveau nom de groupe de serveurs dans le champ **Restaurer sur un nouveau serveur**. Les autres champs (abonnement, groupe de ressources et emplacement) sont affichés, mais ils ne peuvent pas être modifiés.
 
6.  Cliquez sur **OK**.

7.  Une notification indique que l’opération de restauration est lancée.

Enfin, suivez les [tâches post-restauration](#post-restore-tasks).

## <a name="post-restore-tasks"></a>Tâches post-restauration

Après une restauration, vous devez effectuer les opérations suivantes afin que les utilisateurs et les applications soient de nouveau opérationnels :

* Si le nouveau serveur est destiné à remplacer le serveur d’origine, redirigez les clients et les applications clientes vers le nouveau serveur
* Vérifiez qu’un pare-feu approprié au niveau du serveur est en place pour permettre aux utilisateurs de se connecter. Ces règles ne sont pas copiées à partir du groupe de serveurs d’origine.
* Ajustez les paramètres du serveur PostgreSQL, si nécessaire. Les paramètres ne sont pas copiés à partir du groupe de serveurs d’origine.
* Vérifiez que les connexions et les autorisations appropriées au niveau de la base de données sont en place.
* Configurer les alertes, selon les besoins.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [la sauvegarde et la restauration](concepts-hyperscale-backup.md) dans Hyperscale (Citus).
* Définissez des  [alertes suggérées](./howto-hyperscale-alert-on-metric.md#suggested-alerts) sur des groupes de serveurs Hyperscale (Citus).
