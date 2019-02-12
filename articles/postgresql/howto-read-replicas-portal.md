---
title: Gérer les réplicas en lecture dans le portail Azure pour Azure Database pour PostgreSQL
description: Cet article décrit la gestion des réplicas en lecture pour Azure Database pour PostgreSQL dans le portail Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 37150f67e29dae0357c978cfaea9abeebeef428c
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55691403"
---
# <a name="how-to-create-and-manage-read-replicas-in-the-azure-portal"></a>Guide pratique pour créer et gérer des réplicas en lecture dans le portail Azure

> [!IMPORTANT]
> La fonctionnalité de réplica en lecture est en préversion publique.


Dans cet article, vous allez apprendre à créer et à gérer des réplicas en lecture dans le service Azure Database pour PostgreSQL à l’aide du portail Azure. Pour en savoir plus sur les réplicas en lecture, [lisez la documentation relative aux concepts](concepts-read-replicas.md).

## <a name="prerequisites"></a>Prérequis
- Un [serveur Azure Database pour PostgreSQL](quickstart-create-server-database-portal.md) qui fera office de serveur maître.

## <a name="prepare-the-master-server"></a>Préparer le serveur maître
Cette étape de préparation du serveur maître s’applique uniquement aux serveurs à usage général et à mémoire optimisée.

Le paramètre **azure.replication_support** doit être défini sur REPLICA sur le serveur maître. Le changement de ce paramètre nécessite un redémarrage du serveur pour prendre effet.

1. Dans le portail Azure, sélectionnez le serveur Azure Database pour PostgreSQL que vous souhaitez utiliser comme serveur maître.

2. Sélectionnez **Paramètres du serveur** dans le menu de gauche.

3. Recherchez **azure.replication_support**.

   ![Azure Database pour PostgreSQL - azure.replication_support](./media/howto-read-replicas-portal/azure-replication-parameter.png)

4. Définissez **azure.replication_support** sur REPLICA. **Enregistrez** la modification.

   ![Azure Database pour PostgreSQL - Définir sur REPLICA et enregistrer](./media/howto-read-replicas-portal/save-parameter-replica.png)

5. Une fois l’enregistrement terminé, vous recevez une notification.

   ![Azure Database pour PostgreSQL - Notification de l’enregistrement](./media/howto-read-replicas-portal/parameter-save-notification.png)

6. Redémarrez le serveur pour appliquer la modification après l’avoir enregistrée. Consultez la [documentation sur le redémarrage](howto-restart-server-portal.md) pour savoir comment redémarrer un serveur.


## <a name="create-a-read-replica"></a>Créer un réplica en lecture
Vous pouvez créer des réplicas en lecture en effectuant les étapes suivantes :
1.  Sélectionnez le serveur Azure Database pour PostgreSQL que vous souhaitez utiliser comme serveur maître. 

2.  Sélectionnez Réplication dans le menu, sous PARAMÈTRES.

   Si vous n’avez pas défini **azure.replication_support** sur REPLICA sur le serveur maître à usage général ou à mémoire optimisée et redémarré le serveur, un message s’affiche vous invitant à le faire. Faites-le avant de procéder à la création.

3.  Sélectionnez Ajouter un réplica.

   ![Azure Database pour PostgreSQL - Ajouter un réplica](./media/howto-read-replicas-portal/add-replica.png)

4.  Entrez un nom pour le serveur réplica, puis sélectionnez OK pour confirmer la création du réplica.

   ![Azure Database pour PostgreSQL - Nommer le réplica](./media/howto-read-replicas-portal/name-replica.png) 

> [!IMPORTANT]
> Les réplicas en lecture sont créés avec la même configuration de serveur que le serveur maître. Une fois un réplica créé, le niveau tarifaire (sauf vers et à partir du niveau De base), la génération de calcul, les vCores, le stockage et la conservation des sauvegardes peuvent être changés indépendamment du serveur maître.

> [!IMPORTANT]
> Avant de mettre à jour la configuration de serveur d’un maître avec de nouvelles valeurs, la configuration des réplicas doit être mise à jour avec des valeurs égales ou supérieures. Toute autre approche entraîne une erreur. Cela permet de s’assurer que les réplicas sont en mesure de suivre les changements apportés au maître. 


Une fois le serveur réplica créé, il est affiché dans la fenêtre Réplication.

![Azure Database pour PostgreSQL - Nouveau réplica](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Arrêter la réplication

> [!IMPORTANT]
> L’arrêt de la réplication vers un serveur est irréversible. Une fois la réplication entre un serveur maître et un serveur réplica arrêtée, elle ne peut pas être annulée. Le serveur réplica devient un serveur autonome et prend désormais en charge la lecture et les écritures. Ce serveur ne peut pas être à nouveau transformé en réplica.

Pour arrêter la réplication entre un serveur maître et un réplica à partir du portail Azure, effectuez les étapes suivantes :
1.  Dans le portail Azure, sélectionnez votre serveur Azure Database pour PostgreSQL maître.

2.  Sélectionnez Réplication dans le menu, sous PARAMÈTRES.

3.  Sélectionnez le serveur réplica pour lequel vous souhaitez arrêter la réplication.

   ![Azure Database pour PostgreSQL - Sélectionner le réplica](./media/howto-read-replicas-portal/select-replica.png)
 
4.  Sélectionnez Arrêter la réplication.

   ![Azure Database pour PostgreSQL - Sélectionner Arrêter la réplication](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5.  Validez que vous voulez arrêter la réplication en cliquant sur OK.

   ![Azure Database pour PostgreSQL - Confirmer l’arrêt de la réplication](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master"></a>Supprimer un serveur maître

> [!IMPORTANT]
> La suppression d’un serveur maître arrête la réplication vers tous les serveurs réplicas. Les serveurs réplicas deviennent des serveurs autonomes qui prennent désormais en charge la lecture et les écritures.
La suppression d’un serveur maître suit les mêmes étapes que celle d’un serveur Azure Database pour PostgreSQL autonome. Pour supprimer un serveur à partir du portail Azure, effectuez les étapes suivantes :

1.  Dans le portail Azure, sélectionnez votre serveur Azure Database pour PostgreSQL maître.

2.  Dans la Vue d’ensemble, sélectionnez Supprimer.

   ![Azure Database pour PostgreSQL - Supprimer un serveur](./media/howto-read-replicas-portal/delete-server.png)
 
3.  Tapez le nom du serveur maître, puis sélectionnez Supprimer pour confirmer la suppression de ce dernier.

   ![Azure Database pour PostgreSQL - Confirmer la suppression](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Supprimer un réplica
Pour supprimer un réplica en lecture, vous pouvez suivre les mêmes étapes que pour la suppression d’un serveur maître ci-dessus. Tout d’abord, ouvrez la page Vue d’ensemble du réplica, puis sélectionnez Supprimer.

   ![Azure Database pour PostgreSQL - Supprimer un réplica](./media/howto-read-replicas-portal/delete-replica.png)
 
Vous pouvez également le supprimer à partir de la fenêtre Réplication.
1.  Dans le portail Azure, sélectionnez votre serveur Azure Database pour PostgreSQL maître.

2.  Sélectionnez Réplication dans le menu, sous PARAMÈTRES.

3.  Sélectionnez le serveur réplica que vous voulez supprimer. 

   ![Azure Database pour PostgreSQL - Sélectionner le réplica](./media/howto-read-replicas-portal/select-replica.png)
 
4.  Sélectionnez Supprimer le réplica.

   ![Azure Database pour PostgreSQL - Sélectionner Supprimer le réplica](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5.  Tapez le nom du réplica, puis sélectionnez Supprimer pour confirmer la suppression de ce dernier.

   ![Azure Database pour PostgreSQL - Confirmer la suppression du réplica](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Superviser un réplica
### <a name="max-lag-across-replicas"></a>Retard maximum entre réplicas
La métrique **Retard maximum entre réplicas** représente le retard en octets entre le master et le réplica le plus en retard. 

1.  Dans le portail Azure, sélectionnez le serveur Azure Database pour PostgreSQL **maître**.

2.  Sélectionnez Métriques. Dans la fenêtre des métriques, sélectionnez **Retard maximum entre réplicas**.

    ![Azure Database pour PostgreSQL - Superviser le retard maximum entre réplicas](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  Sélectionnez **Max** en guise d’agrégation. 

### <a name="replica-lag"></a>Retard du réplica
La métrique **Retard du réplica** indique le temps écoulé depuis la dernière transaction rejouée sur ce réplica. S’il n’y a pas de transactions sur votre maître, la métrique reflète ce retard.

1.  Dans le portail Azure, sélectionnez un serveur Azure Database pour PostgreSQL **réplica**.

2.  Sélectionnez Métriques. Dans la fenêtre des métriques, sélectionnez **Retard du réplica**.

   ![Azure Database pour PostgreSQL - Superviser le retard du réplica](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3.  Sélectionnez **Max** en guise d’agrégation. 
 
## <a name="next-steps"></a>Étapes suivantes
- Découvrez-en plus sur les [réplicas en lecture dans Azure Database pour PostgreSQL](concepts-read-replicas.md).