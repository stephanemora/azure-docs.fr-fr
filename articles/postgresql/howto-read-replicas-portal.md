---
title: Gérer les réplicas en lecture pour Azure Database pour PostgreSQL à partir du portail Azure
description: Découvrez comment gérer les réplicas en lecture pour Azure Database pour PostgreSQL à partir du portail Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: b34b103d3b710b90fd7b396f2c8d0e7adc27aaca
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56454665"
---
# <a name="create-and-manage-read-replicas-from-the-azure-portal"></a>Créer et gérer des réplicas en lecture à partir du portail Azure

Dans cet article, vous allez apprendre à créer et gérer des réplicas en lecture dans Azure Database pour PostgreSQL à partir du portail Azure. Pour en savoir plus sur les réplicas en lecture, consultez [vue d’ensemble](concepts-read-replicas.md).

> [!IMPORTANT]
> La fonctionnalité de réplica en lecture est en préversion publique.

## <a name="prerequisites"></a>Prérequis
Un [serveur Azure Database pour PostgreSQL](quickstart-create-server-database-portal.md) qui représente le serveur maître.

## <a name="prepare-the-master-server"></a>Préparer le serveur maître
Ces étapes permettent de préparer un serveur maître pour les niveaux à usage général ou à mémoire optimisée.

Le paramètre `azure.replication_support` doit avoir la valeur **REPLICA** sur le serveur maître. Quand vous changez ce paramètre, un redémarrage du serveur est nécessaire pour que la modification soit prise en compte.

1. Dans le portail Azure, sélectionnez le serveur Azure Database pour PostgreSQL existant à utiliser en tant que serveur maître.

2. Dans le menu de gauche, sélectionnez **Paramètres du serveur**.

3. Recherchez le paramètre `azure.replication_support`.

   ![Recherchez le paramètre azure.replication_support](./media/howto-read-replicas-portal/azure-replication-parameter.png)

4. Affectez au paramètre `azure.replication_support` la valeur **REPLICA**. Sélectionnez **Enregistrer** pour conserver vos changements.

   ![Affectez la valeur REPLICA au paramètre, puis enregistrez vos changements](./media/howto-read-replicas-portal/save-parameter-replica.png)

5. Après avoir enregistré vos changements, vous recevez une notification :

   ![Enregistrer la notification](./media/howto-read-replicas-portal/parameter-save-notification.png)

6. Redémarrez le serveur pour appliquer les changements apportés. Pour apprendre à redémarrer un serveur, consultez [Redémarrer un serveur Azure Database pour PostgreSQL](howto-restart-server-portal.md).


## <a name="create-a-read-replica"></a>Créer un réplica en lecture
Pour créer un réplica en lecture, effectuez les étapes suivantes :

1.  Sélectionnez le serveur Azure Database pour PostgreSQL à utiliser en tant que serveur maître. 

2.  Dans le menu du serveur, sous **PARAMÈTRES**, sélectionnez **Réplication**.

   Si vous n’avez pas affecté au paramètre `azure.replication_support` la valeur **REPLICA** sur un serveur maître à usage général ou à mémoire optimisée, et si vous n’avez pas redémarré le serveur, vous recevez une notification. Effectuez ces étapes avant de créer le réplica.

3.  Sélectionnez **Ajouter un réplica**.

   ![Ajouter un réplica](./media/howto-read-replicas-portal/add-replica.png)

4.  Entrez un nom pour le réplica en lecture. Sélectionnez **OK** pour confirmer la création du réplica.

   ![Nommer le réplica](./media/howto-read-replicas-portal/name-replica.png) 

Un réplica est créé à partir de la même configuration que celle du serveur maître. Une fois le réplica créé, vous pouvez changer plusieurs paramètres indépendamment du serveur maître : génération de calcul, vCores, stockage et période de conservation de la sauvegarde. Le niveau tarifaire peut également être changé indépendamment, sauf vers ou depuis le niveau De base.

> [!IMPORTANT]
> Avant de mettre à jour une configuration de serveur maître avec de nouvelles valeurs, mettez à jour la configuration du réplica avec des valeurs égales ou supérieures. Ainsi, vous avez la garantie que le réplica peut suivre les changements apportés au maître.

Une fois le réplica en lecture créé, vous pouvez le voir dans la fenêtre **Réplication** :

![Voir le nouveau réplica dans la fenêtre Réplication](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Arrêter la réplication
Vous pouvez arrêter la réplication entre un serveur maître et un réplica en lecture.

> [!IMPORTANT]
> Une fois que vous avez arrêté la réplication entre un serveur maître et un réplica en lecture, vous ne pouvez plus l’annuler. Le réplica en lecture devient un serveur autonome qui prend en charge les lectures et les écritures. Le serveur autonome ne peut pas être retransformé en réplica.

Pour arrêter la réplication entre un serveur maître et un réplica en lecture à partir du portail Azure, effectuez les étapes suivantes :

1.  Dans le portail Azure, sélectionnez votre serveur Azure Database pour PostgreSQL maître.

2.  Dans le menu du serveur, sous **PARAMÈTRES**, sélectionnez **Réplication**.

3.  Sélectionnez le serveur réplica dont vous souhaitez arrêter la réplication.

   ![Sélectionner le réplica](./media/howto-read-replicas-portal/select-replica.png)
 
4.  Sélectionnez **Arrêter la réplication**.

   ![Sélectionner Arrêter la réplication](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5.  Sélectionnez **OK** pour arrêter la réplication.

   ![Confirmer l’arrêt de la réplication](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>Supprimer un serveur maître
Pour supprimer un serveur maître, suivez les mêmes étapes que celles qui permettent de supprimer un serveur Azure Database pour PostgreSQL autonome. 

> [!IMPORTANT]
> Quand vous supprimez un serveur maître, la réplication est arrêtée sur tous les réplicas en lecture. Les réplicas en lecture deviennent des serveurs autonomes qui prennent désormais en charge les lectures et les écritures.

Pour supprimer un serveur du portail Azure, effectuez les étapes suivantes :

1.  Dans le portail Azure, sélectionnez votre serveur Azure Database pour PostgreSQL maître.

2.  Ouvrez la page **Vue d’ensemble** du serveur. Sélectionnez **Supprimer**.

   ![Dans la page de vue d’ensemble du serveur, sélectionner Supprimer pour supprimer le serveur maître](./media/howto-read-replicas-portal/delete-server.png)
 
3.  Entrez le nom du serveur maître à supprimer. Sélectionnez **Supprimer** pour confirmer la suppression du serveur maître.

   ![Confirmer la suppression du serveur maître](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Supprimer un réplica
Vous pouvez supprimer un réplica en lecture de la même façon que vous supprimez un serveur maître.

- Dans le portail Azure, ouvrez la page **Vue d’ensemble** du réplica en lecture. Sélectionnez **Supprimer**.

   ![Dans la page de vue d’ensemble du réplica, sélectionner Supprimer pour supprimer le réplica](./media/howto-read-replicas-portal/delete-replica.png)
 
Vous pouvez également supprimer le réplica en lecture de la fenêtre **Réplication** en effectuant les étapes suivantes :

1.  Dans le portail Azure, sélectionnez votre serveur Azure Database pour PostgreSQL maître.

2.  Dans le menu du serveur, sous **PARAMÈTRES**, sélectionnez **Réplication**.

3.  Sélectionnez le réplica en lecture à supprimer.

   ![Sélectionner le réplica à supprimer](./media/howto-read-replicas-portal/select-replica.png)
 
4.  Sélectionnez **Supprimer le réplica**.

   ![Sélectionnez Supprimer le réplica](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5.  Entrez le nom du réplica à supprimer. Sélectionnez **Supprimer** pour confirmer la suppression du réplica.

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

1.  Dans le portail Azure, sélectionnez le réplica en lecture Azure Database pour PostgreSQL.

2.  Sélectionnez **Métriques**. Dans la fenêtre **Métriques**, sélectionnez **Retard du réplica**.

   ![Superviser le retard du réplica](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3.  Pour votre **Agrégation**, sélectionnez **Max**. 
 
## <a name="next-steps"></a>Étapes suivantes
Découvrez-en plus sur les [réplicas en lecture dans Azure Database pour PostgreSQL](concepts-read-replicas.md).