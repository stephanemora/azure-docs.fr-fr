---
title: Restaurer un serveur flexible Azure Database pour MySQL avec le portail Azure
description: Cet article explique comment effectuer des opérations de restauration dans un serveur flexible Azure Database pour MySQL à l’aide du portail Azure
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 687d9386d330e5b09366e22ace8f7fd8666ee9d9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525164"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>Restauration à un instant dans le passé d’un serveur flexible Azure Database pour MySQL (préversion) à l’aide du portail Azure

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database pour MySQL - Serveur flexible est actuellement en préversion publique.

Cet article indique la procédure pas-à-pas à suivre pour effectuer des restaurations à un instant dans le passé sur un serveur flexible à l’aide de sauvegardes.

## <a name="prerequisites"></a>Prérequis

Pour utiliser ce guide pratique, il vous faut :

- Vous devez disposer d’un serveur flexible Azure Database pour MySQL.

## <a name="restore-to-the-latest-restore-point"></a>Restaurer au dernier point de restauration

Effectuez ces étapes pour restaurer votre serveur flexible à l’aide de la plus ancienne sauvegarde existante.

1. Dans le [portail Azure](https://portal.azure.com/), choisissez le serveur flexible à partir duquel vous voulez restaurer la sauvegarde.

2. Cliquez sur **Vue d’ensemble** dans le panneau gauche.

3. Dans la page de vue d’ensemble, cliquez sur **Restaurer**.

4. La page Restaurer s’affiche et vous permet de choisir entre **Dernier point de restauration** et Point de restauration personnalisé.

5. Sélectionnez **Dernier point de restauration**.

6. Indiquez un nouveau nom de serveur dans le champ **Restaurer sur un nouveau serveur**.

    :::image type="content" source="./media/how-to-restore-server-portal/point-in-time-restore-latest.png" alt-text="Heure de restauration la plus ancienne":::

7. Cliquez sur **OK**.

8. Une notification indique que l’opération de restauration est lancée.

## <a name="using-restore-to-move-a-server-from-public-access-to-private-access"></a>Utilisation de la restauration pour déplacer un serveur d’un accès public à un accès privé

Effectuez ces étapes pour restaurer votre serveur flexible à l’aide de la plus ancienne sauvegarde existante.

1. Dans le [portail Azure](https://portal.azure.com/), choisissez le serveur flexible à partir duquel vous voulez restaurer la sauvegarde.

2. Dans la page de vue d’ensemble, cliquez sur **Restaurer**.

3. La page Restaurer s’affiche et vous permet de choisir entre Point de restauration le plus ancien et Point de restauration personnalisé.

4. Choisissez **Point de restauration le plus ancien** ou un **Point de restauration personnalisé**.

5. Indiquez un nouveau nom de serveur dans le champ **Restaurer sur un nouveau serveur**.

6. Indiquez un nouveau nom de serveur dans le champ **Restaurer sur un nouveau serveur**.

    :::image type="content" source="./media/how-to-restore-server-portal/point-in-time-restore-private-dns-zone.png" alt-text="vue d’ensemble":::

7. Accédez à l’onglet **Réseau** pour configurer vos options réseau.

8. Dans **Méthode de connectivité**, sélectionnez **Accès privé (intégration au réseau virtuel)** . Accédez à la section **Réseau virtuel**. Vous pouvez sélectionner un *réseau virtuel* et un *sous-réseau* déjà existants qui sont délégués à *Microsoft. DBforMySQL/flexibleServers*, ou en créer un en cliquant sur le lien *Créer un réseau virtuel*.
    > [!Note]
    > Seuls les réseaux virtuels et les sous-réseaux de la même région et du même abonnement seront listés dans la liste déroulante. </br>
    > Le sous-réseau choisi sera délégué à *Microsoft.DBforMySQL/flexibleServers*. Cela signifie que seuls les serveurs flexibles Azure Database pour MySQL peuvent utiliser ce sous-réseau.</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/vnet-creation.png" alt-text="Configuration de réseau virtuel":::

9. Créez une **Zone DNS privée** ou sélectionnez-en une qui existe déjà.
    > [!NOTE]
    > Les noms des zones DNS privées doivent se terminer par `mysql.database.azure.com`. </br>
    > Si vous ne voyez pas l’option permettant de créer une zone DNS privée, entrez le nom du serveur sous l’onglet **De base**.</br>
    > Une fois le serveur flexible déployé sur un réseau virtuel et un sous-réseau, vous ne pouvez pas le déplacer vers un accès public (adresses IP autorisées).</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/private-dns-zone.png" alt-text="Configuration de DNS":::
10. Sélectionnez **Vérifier + créer** pour passer en revue la configuration de votre serveur flexible.
11. Sélectionnez **Créer** pour approvisionner le serveur. L'approvisionnement peut prendre quelques minutes.

12. Une notification indique que l’opération de restauration est lancée.

## <a name="perform-post-restore-tasks"></a>Effectuer des tâches de post-restauration

Une fois la restauration terminée, vous devez effectuer les tâches suivantes afin que les utilisateurs et les applications soient de nouveau opérationnels :

- Si le nouveau serveur est censé remplacer le serveur d’origine, redirigez les clients et les applications clientes vers le nouveau serveur.
- Vérifiez que les règles de réseau virtuel appropriées sont en place pour permettre aux utilisateurs de se connecter. Ces règles ne sont pas copiées à partir du serveur d’origine.
- Vérifiez que les connexions et les autorisations appropriées au niveau de la base de données sont en place.
- Configurez les alertes comme il convient pour le serveur nouvellement restauré.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la [continuité de l’activité](concepts-business-continuity.md)
