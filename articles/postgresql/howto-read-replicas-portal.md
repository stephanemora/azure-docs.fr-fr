---
title: Gérer les réplicas en lecture - Portail Azure - Azure Database pour PostgreSQL - Serveur unique
description: Découvrez comment gérer les réplicas en lecture pour Azure Database pour PostgreSQL (serveur unique) à partir du portail Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 8e148a3dac8435a08c0f1735cd35d06c700e1e84
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86106626"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Créer et gérer les réplicas en lecture dans Azure Database pour PostgreSQL (serveur unique) à partir du portail Azure

Dans cet article, vous allez apprendre à créer et gérer des réplicas en lecture dans Azure Database pour PostgreSQL à partir du portail Azure. Pour en savoir plus sur les réplicas en lecture, consultez [vue d’ensemble](concepts-read-replicas.md).


## <a name="prerequisites"></a>Prérequis
Un [serveur Azure Database pour PostgreSQL](quickstart-create-server-database-portal.md) qui représente le serveur maître.

## <a name="azure-replication-support"></a>Prise en charge de la réplication Azure

Les [réplicas en lecture](concepts-read-replicas.md) et le [décodage logique](concepts-logical.md) dépendent du journal WAL de Postgres pour obtenir des informations. Ces deux fonctionnalités ont besoin de différents niveaux de journalisation à partir de Postgres. Le décodage logique nécessite un niveau de journalisation plus élevé que les réplicas en lecture.

Pour configurer le niveau de journalisation approprié, utilisez le paramètre de prise en charge de la réplication Azure. La prise en charge de la réplication Azure propose trois options de paramétrage :

* **Désactivé** : place le moins d’informations dans le journal WAL. Ce paramètre n’est pas disponible sur la plupart des serveurs Azure Database pour PostgreSQL.  
* **Réplica** : plus de détails que l’option **Désactivé**. Il s’agit du niveau minimal de journalisation nécessaire pour que les [réplicas en lecture](concepts-read-replicas.md) fonctionnent. Il s’agit du paramètre par défaut sur la plupart des serveurs.
* **Logique** : plus de détails que l’option **Réplica**. Il s’agit du niveau minimal de journalisation pour que le décodage logique fonctionne. Les réplicas en lecture fonctionnent également avec ce paramètre.

Le serveur doit être redémarré après une modification de ce paramètre. En interne, ce paramètre définit les paramètres Postgres `wal_level`, `max_replication_slots` et `max_wal_senders`.

## <a name="prepare-the-master-server"></a>Préparer le serveur maître

1. Dans le portail Azure, sélectionnez un serveur Azure Database pour PostgreSQL à utiliser en tant que serveur maître.

2. Sélectionnez **Réplication** depuis le menu du serveur. Si un support de réplication Azure est réglé au moins sur **Réplica**, vous pouvez créer des réplicas en lecture. 

3. Si le support de réplication Azure n’est pas réglé sur au moins **Réplica**, réglez-le. Sélectionnez **Enregistrer**.

   ![Azure Database pour PostgreSQL – Réplication – Définir un réplica et enregistrer](./media/howto-read-replicas-portal/set-replica-save.png)

4. Redémarrez le serveur pour appliquer le changement en sélectionnant **Oui**.

   ![Azure Database pour PostgreSQL - Réplication - Confirmer le redémarrage](./media/howto-read-replicas-portal/confirm-restart.png)

5. Vous recevrez deux notifications du portail Azure une fois que l’opération sera terminée. Une notification concerne la mise à jour du paramètre du serveur. La seconde concerne le redémarrage du serveur qui suit immédiatement.

   ![Notifications de réussite](./media/howto-read-replicas-portal/success-notifications.png)

6. Actualisez la page du portail Azure pour mettre à jour la barre d’outils Réplication. Vous pouvez désormais créer des réplicas en lecture pour ce serveur.
   

## <a name="create-a-read-replica"></a>Créer un réplica en lecture
Pour créer un réplica en lecture, effectuez les étapes suivantes :

1. Sélectionnez un serveur Azure Database pour PostgreSQL à utiliser en tant que serveur maître. 

2. Dans la barre latérale du serveur, sous **PARAMÈTRES**, sélectionnez **Réplication**.

3. Sélectionnez **Ajouter un réplica**.

   ![Ajouter un réplica](./media/howto-read-replicas-portal/add-replica.png)

4. Entrez un nom pour le réplica en lecture. 

    ![Nommer le réplica](./media/howto-read-replicas-portal/name-replica.png)

5. Sélectionnez un emplacement pour le réplica. L’emplacement par défaut est le même que celui du serveur maître.

    ![Sélectionner un emplacement](./media/howto-read-replicas-portal/location-replica.png)

   > [!NOTE]
   > Pour en savoir plus sur les régions dans lesquelles vous pouvez créer un réplica, consultez l’article [Concepts relatifs aux réplicas en lecture](concepts-read-replicas.md). 

6. Sélectionnez **OK** pour confirmer la création du réplica.

Le réplica doit être créé en utilisant les mêmes paramètres de calcul et de stockage que le serveur maître. Une fois le réplica créé, vous pouvez changer plusieurs paramètres indépendamment du serveur maître : génération de calcul, vCores, stockage et période de conservation de la sauvegarde. Le niveau tarifaire peut également être changé indépendamment, sauf vers ou depuis le niveau De base.

> [!IMPORTANT]
> Avant de modifier un paramètre du serveur maître, remplacez la valeur du paramètre du réplica par une valeur supérieure ou égale à celle du serveur maître. Vous garantissez ainsi l’alignement du réplica sur les changements apportés au serveur maître.

Une fois le réplica en lecture créé, vous pouvez le voir dans la fenêtre **Réplication** :

![Voir le nouveau réplica dans la fenêtre Réplication](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Arrêter la réplication
Vous pouvez arrêter la réplication entre un serveur maître et un réplica en lecture.

> [!IMPORTANT]
> Une fois que vous avez arrêté la réplication entre un serveur maître et un réplica en lecture, vous ne pouvez plus l’annuler. Le réplica en lecture devient un serveur autonome qui prend en charge les lectures et les écritures. Le serveur autonome ne peut pas être retransformé en réplica.

Pour arrêter la réplication entre un serveur maître et un réplica en lecture à partir du portail Azure, effectuez les étapes suivantes :

1. Dans le portail Azure, sélectionnez votre serveur Azure Database pour PostgreSQL maître.

2. Dans le menu du serveur, sous **PARAMÈTRES**, sélectionnez **Réplication**.

3. Sélectionnez le serveur réplica dont vous souhaitez arrêter la réplication.

   ![Sélectionner le réplica](./media/howto-read-replicas-portal/select-replica.png)
 
4. Sélectionnez **Arrêter la réplication**.

   ![Sélectionner Arrêter la réplication](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5. Sélectionnez **OK** pour arrêter la réplication.

   ![Confirmer l’arrêt de la réplication](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>Supprimer un serveur maître
Pour supprimer un serveur maître, suivez les mêmes étapes que celles qui permettent de supprimer un serveur Azure Database pour PostgreSQL autonome. 

> [!IMPORTANT]
> Quand vous supprimez un serveur maître, la réplication est arrêtée sur tous les réplicas en lecture. Les réplicas en lecture deviennent des serveurs autonomes qui prennent désormais en charge les lectures et les écritures.

Pour supprimer un serveur du portail Azure, effectuez les étapes suivantes :

1. Dans le portail Azure, sélectionnez votre serveur Azure Database pour PostgreSQL maître.

2. Ouvrez la page **Vue d’ensemble** du serveur. Sélectionnez **Supprimer**.

   ![Dans la page de vue d’ensemble du serveur, sélectionner Supprimer pour supprimer le serveur maître](./media/howto-read-replicas-portal/delete-server.png)
 
3. Entrez le nom du serveur maître à supprimer. Sélectionnez **Supprimer** pour confirmer la suppression du serveur maître.

   ![Confirmer la suppression du serveur maître](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Supprimer un réplica
Vous pouvez supprimer un réplica en lecture de la même façon que vous supprimez un serveur maître.

- Dans le portail Azure, ouvrez la page **Vue d’ensemble** du réplica en lecture. Sélectionnez **Supprimer**.

   ![Dans la page de vue d’ensemble du réplica, sélectionner Supprimer pour supprimer le réplica](./media/howto-read-replicas-portal/delete-replica.png)
 
Vous pouvez également supprimer le réplica en lecture de la fenêtre **Réplication** en effectuant les étapes suivantes :

1. Dans le portail Azure, sélectionnez votre serveur Azure Database pour PostgreSQL maître.

2. Dans le menu du serveur, sous **PARAMÈTRES**, sélectionnez **Réplication**.

3. Sélectionnez le réplica en lecture à supprimer.

   ![Sélectionner le réplica à supprimer](./media/howto-read-replicas-portal/select-replica.png)
 
4. Sélectionnez **Supprimer le réplica**.

   ![Sélectionnez Supprimer le réplica](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5. Entrez le nom du réplica à supprimer. Sélectionnez **Supprimer** pour confirmer la suppression du réplica.

   ![Confirmer la suppression du réplica](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Superviser un réplica
Deux métriques sont disponibles pour superviser les réplicas en lecture.

### <a name="max-lag-across-replicas-metric"></a>Métrique Retard maximum entre réplicas
La métrique **Retard maximum entre réplicas** indique le retard en octets entre le serveur maître et le réplica le plus en retard. 

1.  Dans le portail Azure, sélectionnez le serveur Azure Database pour PostgreSQL maître.

2.  Sélectionnez **Métriques**. Dans la fenêtre **Métriques**, sélectionnez **Retard maximum entre réplicas**.

    ![Superviser le retard maximum entre réplicas](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  Pour votre **Agrégation**, sélectionnez **Max**.


### <a name="replica-lag-metric"></a>Métrique Retard du réplica
La métrique **Retard du réplica** indique le temps écoulé depuis la dernière transaction réexécutée sur un réplica. S’il n’y a pas de transactions sur votre maître, la métrique reflète ce retard.

1. Dans le portail Azure, sélectionnez le réplica en lecture Azure Database pour PostgreSQL.

2. Sélectionnez **Métriques**. Dans la fenêtre **Métriques**, sélectionnez **Retard du réplica**.

   ![Superviser le retard du réplica](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3. Pour votre **Agrégation**, sélectionnez **Max**. 
 
## <a name="next-steps"></a>Étapes suivantes
* Découvrez-en plus sur les [réplicas en lecture dans Azure Database pour PostgreSQL](concepts-read-replicas.md).
* Découvrez comment [créer et gérer des réplicas en lecture avec l’interface de ligne de commande Azure et l’API REST](howto-read-replicas-cli.md).