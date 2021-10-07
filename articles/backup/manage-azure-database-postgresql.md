---
title: Gérer un serveur Azure Database pour PostgreSQL
description: En savoir plus sur la gestion d’un serveur Azure Database pour PostgreSQL.
ms.topic: conceptual
ms.date: 09/21/2021
ms.openlocfilehash: 2546279f22df9a39f454b926d9960f38cf42f2fc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631238"
---
# <a name="manage-azure-database-for-postgresql-server"></a>Gérer un serveur Azure Database pour PostgreSQL

Cet article explique comment gérer des serveurs Azure Database pour PostgreSQL qui sont sauvegardés avec le service Sauvegarde Azure.

## <a name="change-policy"></a>Changer la stratégie

Vous pouvez changer la stratégie associée à une instance de sauvegarde.

1. Sélectionnez l’**instance de sauvegarde** -> **Changer la stratégie**.


   :::image type="content" source="./media/manage-azure-database-postgresql/change-policy.png" alt-text="Capture d’écran montrant l’option de modification de la stratégie.":::
   
1. Sélectionnez la nouvelle stratégie que vous souhaitez appliquer à la base de données.

   :::image type="content" source="./media/manage-azure-database-postgresql/reassign-policy.png" alt-text="Capture d’écran montrant l’option de réattribution de la stratégie.":::

## <a name="stop-protection-preview"></a>Arrêter la protection (préversion)

Il existe trois façons d’arrêter la protection d’un serveur Azure Database pour PostgreSQL.

- **Arrêter la protection et conserver les données (conserver indéfiniment)**  : Cette option vous permet d’empêcher tous les futurs travaux de sauvegarde de protéger votre serveur Azure Database pour PostgreSQL. Cependant, le service Sauvegarde Azure conservera les points de récupération qui sont sauvegardés indéfiniment. Vous devrez payer pour conserver ces points de récupération dans le coffre (consultez l’article [Tarification de Sauvegarde Azure](https://azure.microsoft.com/pricing/details/backup/) pour en savoir plus). Vous pourrez effectuer une restauration à partir de ces points de récupération, le cas échéant. Pour reprendre la protection, utilisez l’option **Reprendre la sauvegarde**.

- **Arrêter la protection et conserver les données (conserver conformément à la stratégie)**  : Cette option vous permet d’empêcher tous les futurs travaux de sauvegarde de protéger votre serveur Azure Database pour PostgreSQL. Les points de récupération sont conservés conformément à une stratégie et sont facturables en fonction de la [tarification de la sauvegarde Azure](https://azure.microsoft.com/pricing/details/backup/). Toutefois, le dernier point de récupération est conservé définitivement.

- **Arrêter la protection et supprimer les données** : Cette option vous permet d’empêcher tous les futurs travaux de sauvegarde de protéger votre serveur Azure Database pour PostgreSQL et supprime tous les points de récupération. Vous ne pourrez pas restaurer la base de données ni utiliser l’option **Reprendre la sauvegarde**.

### <a name="stop-protection-and-retain-data"></a>Arrêter la protection et conserver les données

1. Accédez à **Centre de sauvegarde** et sélectionnez **Serveur Azure Database pour PostgreSQL (préversion)** .

1. Dans la liste d’instances de sauvegarde du serveur, sélectionnez l’instance que vous souhaitez conserver.

1. Sélectionnez **Arrêter la sauvegarde (préversion)** .

   :::image type="content" source="./media/manage-azure-database-postgresql/select-postgresql-server-backup-instance-to-delete-inline.png" alt-text="Capture d’écran montrant la sélection de l’instance de sauvegarde du serveur Azure Database pour PostgreSQL à arrêter." lightbox="./media/manage-azure-database-postgresql/select-postgresql-server-backup-instance-to-delete-expanded.png":::

1. Sélectionnez l’une des options de conservation des données suivantes :

   1. Conserver indéfiniment
   1. Conserver conformément à une stratégie
   
   :::image type="content" source="./media/manage-azure-database-postgresql/data-retention-options-inline.png" alt-text="Capture d’écran montrant les options de conservation des données à sélectionner." lightbox="./media/manage-azure-database-postgresql/data-retention-options-expanded.png":::

   Vous pouvez également sélectionner la raison de l’arrêt des sauvegardes dans la liste déroulante.

1. Cliquez sur **Arrêter la sauvegarde**.

1. Sélectionnez **Confirmer** pour arrêter la sauvegarde.

   :::image type="content" source="./media/manage-azure-database-postgresql/confirmation-to-stop-backup-inline.png" alt-text="Capture d’écran de la confirmation de l’arrêt de la sauvegarde." lightbox="./media/manage-azure-database-postgresql/confirmation-to-stop-backup-expanded.png":::

### <a name="stop-protection-and-delete-data"></a>Arrêter la protection et supprimer les données

1. Accédez à **Centre de sauvegarde** et sélectionnez **Serveur Azure Database pour PostgreSQL (préversion)** .

1.  Dans la liste d’instances de sauvegarde du serveur, sélectionnez l’instance que vous souhaitez supprimer.

1. Cliquez sur **Arrêter la sauvegarde (préversion)** .

1. Sélectionnez **Supprimer les données de sauvegarde**.

   Indiquez le nom de l’instance de sauvegarde, la raison de la suppression et tout autre commentaire.

   :::image type="content" source="./media/manage-azure-database-postgresql/delete-backup-data-and-provide-details-inline.png" alt-text="Capture d’écran montrant l’option permettant de supprimer les données de sauvegarde et les détails nécessaires à ajouter." lightbox="./media/manage-azure-database-postgresql/delete-backup-data-and-provide-details-expanded.png":::

1. Sélectionnez **Arrêter la sauvegarde**.

1. Sélectionnez **Confirmer** pour arrêter la sauvegarde.

   :::image type="content" source="./media/manage-azure-database-postgresql/confirmation-to-stop-backup-inline.png" alt-text="Capture d’écran de la confirmation de l’arrêt de la sauvegarde." lightbox="./media/manage-azure-database-postgresql/confirmation-to-stop-backup-expanded.png":::

## <a name="resume-protection"></a>Reprendre la protection

Si vous avez sélectionné l’option **Arrêter la protection et conserver les données** pendant l’arrêt de la sauvegarde des données, vous pouvez reprendre la protection de votre serveur Azure Database pour PostgreSQL.

>[!Note]
>Lorsque vous commencez à protéger une base de données, la stratégie de sauvegarde est également appliquée aux données conservées. Les points de récupération qui ont expiré conformément à la stratégie seront nettoyés.

Utiliser les étapes suivantes :

1. Accédez à **Centre de sauvegarde** et sélectionnez **Serveur Azure Database pour PostgreSQL (préversion)** .

1. Dans la liste d’instances de sauvegarde du serveur, sélectionnez l’instance que vous souhaitez reprendre.

1. Sélectionnez **Reprendre la sauvegarde (préversion)** .

   :::image type="content" source="./media/manage-azure-database-postgresql/resume-data-protection-inline.png" alt-text="Capture d’écran montrant l’option permettant de reprendre la protection des données." lightbox="./media/manage-azure-database-postgresql/resume-data-protection-expanded.png":::

1. Sélectionnez **Reprendre la sauvegarde**.

   :::image type="content" source="./media/manage-azure-database-postgresql/resume-data-backup-inline.png" alt-text="Capture d’écran montrant l’option permettant de reprendre la sauvegarde de données." lightbox="./media/manage-azure-database-postgresql/resume-data-backup-expanded.png":::

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble des coffres de sauvegarde](backup-vault-overview.md)
