---
title: Restaurer – Portail Azure – Azure Database pour PostgreSQL – Serveur flexible
description: Cet article explique comment effectuer des opérations de restauration dans Azure Database pour PostgreSQL à l’aide du portail Azure.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 04/22/2021
ms.openlocfilehash: 795cc61f89729da352deb1dc1688bd19e780ca10
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107987711"
---
# <a name="point-in-time-restore-of-a-flexible-server"></a>Restauration à un instant dans le passé d’un serveur flexible

> [!IMPORTANT]
> Azure Database pour PostgreSQL – Serveur flexible est en préversion

Cet article indique la procédure pas-à-pas à suivre pour effectuer des restaurations à un instant dans le passé sur un serveur flexible à l’aide de sauvegardes. Vous pouvez effectuer une restauration au point de restauration le plus récent ou à un point de restauration personnalisé de la période de rétention.

## <a name="pre-requisites"></a>Conditions préalables

Pour utiliser ce guide pratique, il vous faut :

-   Vous devez disposer d’Azure Database pour PostgreSQL – Serveur flexible. La même procédure s’applique également au serveur flexible configuré avec la redondance de zone.

## <a name="restoring-to-the-latest-restore-point"></a>Restaurer au dernier point de restauration

Suivez ces étapes pour restaurer votre serveur flexible à l’aide d’une sauvegarde existante.

1.  Dans le [portail Azure](https://portal.azure.com/), choisissez le serveur flexible à partir duquel vous voulez restaurer la sauvegarde.

2.  Cliquez sur **Vue d’ensemble** dans le volet gauche, puis cliquez sur **Redémarrer**
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="Présentation de la restauration":::

3.  La page Restaurer s’affiche et vous permet de choisir entre Dernier point de restauration et Point de restauration personnalisé.

4.  Sélectionnez **Dernier point de restauration**, puis fournissez un nouveau nom de serveur dans le champ **Restaurer sur un nouveau serveur**. Vous pouvez éventuellement choisir la zone de disponibilité vers laquelle effectuer la restauration.
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-latest.png" alt-text="Dernière heure de restauration":::

5.  Cliquez sur **OK**.

6.  Une notification indique que l’opération de restauration est lancée.

## <a name="restoring-to-a-custom-restore-point"></a>Restauration à un point de restauration personnalisé

Suivez ces étapes pour restaurer votre serveur flexible à l’aide d’une sauvegarde existante.

1.  Dans le [portail Azure](https://portal.azure.com/), choisissez le serveur flexible à partir duquel vous voulez restaurer la sauvegarde.

2.  Dans la page de vue d’ensemble, cliquez sur **Restaurer**.
 :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="Présentation de la restauration":::
    
3.  La page Restaurer s’affiche et vous permet de choisir entre Dernier point de restauration et Point de restauration personnalisé.

4.  Choisissez **Point de restauration personnalisé**.

5.  Sélectionnez la date et l’heure, puis entrez un nouveau nom de serveur dans le champ **Restaurer sur un nouveau serveur**. Fournissez un nouveau nom de serveur. Vous pouvez éventuellement choisir la **zone de disponibilité** vers laquelle effectuer la restauration.
   
:::image type="content" source="./media/how-to-restore-server-portal/restore-custom-2.png" alt-text="Heure de restauration personnalisée":::
 
6.  Cliquez sur **OK**.

7.  Une notification indique que l’opération de restauration est lancée.

## <a name="next-steps"></a>Étapes suivantes

-   Découvrir la [continuité de l’activité](./concepts-business-continuity.md)
-   Découvrir la [haute disponibilité avec redondance interzone](./concepts-high-availability.md)
-   Découvrir [la sauvegarde et la récupération](./concepts-backup-restore.md)
