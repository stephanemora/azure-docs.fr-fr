---
title: Restaurer un serveur flexible Azure Database pour MySQL avec le portail Azure
description: Cet article explique comment effectuer des opérations de restauration dans un serveur flexible Azure Database pour MySQL à l’aide du portail Azure
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 962a2cbdbcc238517616c9ade235eed9b8cae6f7
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502043"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>Restauration à un instant dans le passé d’un serveur flexible Azure Database pour MySQL (préversion) à l’aide du portail Azure


> [!IMPORTANT]
> Azure Database pour MySQL - Serveur flexible est actuellement en préversion publique.

Cet article indique la procédure pas-à-pas à suivre pour effectuer des restaurations à un instant dans le passé sur un serveur flexible à l’aide de sauvegardes.

## <a name="prerequisites"></a>Prérequis

Pour utiliser ce guide pratique, il vous faut :

-   Vous devez disposer d’un serveur flexible Azure Database pour MySQL.

## <a name="restore-to-the-latest-restore-point"></a>Restaurer au dernier point de restauration

Effectuez ces étapes pour restaurer votre serveur flexible à l’aide de la plus ancienne sauvegarde existante.

1.  Dans le [portail Azure](https://portal.azure.com/), choisissez le serveur flexible à partir duquel vous voulez restaurer la sauvegarde.

2.  Cliquez sur **Vue d’ensemble** dans le panneau gauche.

3.  Dans la page de vue d’ensemble, cliquez sur **Restaurer**.

4.  La page Restaurer s’affiche et vous permet de choisir entre **Dernier point de restauration** et Point de restauration personnalisé.

5.  Sélectionnez **Dernier point de restauration**.

6.  Indiquez un nouveau nom de serveur dans le champ **Restaurer sur un nouveau serveur**.

    :::image type="content" source="./media/concept-backup-restore/restore-blade-latest.png" alt-text="Heure de restauration la plus ancienne":::

8.  Cliquez sur **OK**.

9.  Une notification indique que l’opération de restauration est lancée.

## <a name="restoring-to-a-custom-restore-point"></a>Restauration à un point de restauration personnalisé

Effectuez ces étapes pour restaurer votre serveur flexible à l’aide de la plus ancienne sauvegarde existante.

1.  Dans le [portail Azure](https://portal.azure.com/), choisissez le serveur flexible à partir duquel vous voulez restaurer la sauvegarde.

2.  Dans la page de vue d’ensemble, cliquez sur **Restaurer**.

3.  La page Restaurer s’affiche et vous permet de choisir entre Point de restauration le plus ancien et Point de restauration personnalisé.

4.  Choisissez **Point de restauration personnalisé**.

5.  Sélectionnez la date et l’heure.

6.  Indiquez un nouveau nom de serveur dans le champ **Restaurer sur un nouveau serveur**.

6.  Indiquez un nouveau nom de serveur dans le champ **Restaurer sur un nouveau serveur**.

    :::image type="content" source="./media/concept-backup-restore/restore-blade-custom.png" alt-text="vue d’ensemble":::

7.  Cliquez sur **OK**.

8.  Une notification indique que l’opération de restauration est lancée.


## <a name="perform-post-restore-tasks"></a>Effectuer des tâches de post-restauration
Une fois la restauration terminée, vous devez effectuer les tâches suivantes afin que les utilisateurs et les applications soient de nouveau opérationnels :

- Si le nouveau serveur est censé remplacer le serveur d’origine, redirigez les clients et les applications clientes vers le nouveau serveur.
- Vérifiez que les règles de réseau virtuel appropriées sont en place pour permettre aux utilisateurs de se connecter. Ces règles ne sont pas copiées à partir du serveur d’origine.
- Vérifiez que les connexions et les autorisations appropriées au niveau de la base de données sont en place.
- Configurez les alertes comme il convient pour le serveur nouvellement restauré.


## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur la [continuité de l’activité](concepts-business-continuity.md)
