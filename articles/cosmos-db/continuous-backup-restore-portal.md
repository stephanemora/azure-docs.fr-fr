---
title: Utiliser Portail Azure pour configurer la sauvegarde continue et la restauration à un instant dans le passé dans Azure Cosmos DB
description: Découvrez comment identifier le point de restauration et configurer la sauvegarde continue à l’aide de Portail Azure. Cet article montre comment restaurer un compte actif et supprimé.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: ee6eedbc078e1b9c07ed00922ce1c37b38410128
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381866"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-portal"></a>Configurer et gérer la sauvegarde en continu et la restauration à un instant dans le passé (préversion) – Portail Azure
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> La fonctionnalité de restauration à un instant dans le passé (mode de sauvegarde continue) pour Azure Cosmos DB est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La fonctionnalité de restauration à un instant dans le passé d’Azure Cosmos DB (préversion) vous permet de récupérer vos données après une modification accidentelle au sein d’un conteneur, de restaurer un compte, une base de données ou un conteneur supprimé, ou de restaurer dans n’importe quelle région (où des sauvegardes existent). Le mode de sauvegarde continue vous permet d’effectuer une restauration à n’importe quel instant dans le passé au cours des 30 derniers jours.

Cet article explique comment identifier le point de restauration et configurer la sauvegarde continue à l’aide de Portail Azure.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>Approvisionner un compte avec sauvegarde continue

Lorsque vous créez un compte Azure Cosmos DB, pour l’option **Stratégie de sauvegarde**, choisissez le mode **continu** pour activer la fonctionnalité de restauration à un instant dans le passé pour le nouveau compte. Une fois cette fonctionnalité activée pour le compte, toutes les bases de données et tous les conteneurs sont disponibles pour la sauvegarde continue. Avec la restauration à un instant dans le passé, les données sont toujours restaurées sur un nouveau compte. Actuellement, vous ne pouvez pas effectuer de restauration vers un compte existant.

:::image type="content" source="./media/continuous-backup-restore-portal/configure-continuous-backup-portal.png" alt-text="Approvisionner un compte Azure Cosmos DB avec une configuration de sauvegarde continue." border="true":::

## <a name="restore-a-live-account-from-accidental-modification"></a><a id="restore-live-account"></a>Restaurer un compte actif à la suite d’une modification accidentelle

Vous pouvez utiliser Portail Azure pour restaurer un compte actif ou certaines bases de données et certains conteneurs sous celui-ci. Pour restaurer vos données, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Accédez à votre compte Azure Cosmos DB et ouvrez le volet **Restauration à un instant dans le passé**.

   > [!NOTE]
   > Le volet de restauration de Portail Azure n’est rempli que si vous disposez de l’autorisation `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`. Pour en savoir plus sur la définition de cette autorisation, consultez l’article [Autorisations de sauvegarde et de restauration](continuous-backup-restore-permissions.md).

1. Renseignez les détails suivants pour la restauration :

   * **Point de restauration (UTC)**  : Un timestamp au cours des 30 derniers jours. Le compte doit exister à cette date. Vous pouvez spécifier le point de restauration en heure UTC. Il peut être aussi proche de la seconde quand vous souhaitez le restaurer. Sélectionnez le lien **Cliquez ici** pour obtenir de l’aide sur l’[identification du point de restauration](#event-feed).

   * **Emplacement** : Région de destination où le compte est restauré. Le compte doit exister dans cette région à l’heure donnée (par exemple, USA Ouest ou USA Est). Un compte ne peut être restauré que dans les régions où le compte source existait.

   * **Restaurer une ressource** : Vous pouvez choisir soit **Intégralité du compte**, soit **une base de données ou un conteneur** à restaurer. Les bases de données et les conteneurs doivent exister à l’heure donnée. En fonction du point de restauration et de l’emplacement sélectionnés, les ressources de restauration sont renseignées, ce qui permet à l’utilisateur de sélectionner des bases de données ou des conteneurs spécifiques qui doivent être restaurés.

   * **Groupe de ressources** : Groupe de ressources sous lequel le compte cible sera créé et restauré. Le groupe de ressources doit déjà exister.

   * **Restaurer le compte cible** : Nom du compte cible. Le nom du compte cible doit respecter les mêmes instructions que lorsque vous créez un nouveau compte. Ce compte sera créé par le processus de restauration dans la même région que celle où se trouve votre compte source.
 
   :::image type="content" source="./media/continuous-backup-restore-portal/restore-live-account-portal.png" alt-text="Restaurer un compte actif à la suite d’une modification accidentelle sur le portail Azure." border="true":::

1. Après avoir sélectionné les paramètres ci-dessus, sélectionnez le bouton **Envoyer** pour lancer une restauration. Le coût de restauration représente des frais ponctuels, qui sont basés sur la quantité de données et les frais de stockage dans la région donnée. Pour plus d’informations, consultez la section [Tarification](continuous-backup-restore-introduction.md#continuous-backup-pricing).

## <a name="use-event-feed-to-identify-the-restore-time"></a><a id="event-feed"></a>Utiliser un flux d’événements pour identifier l’heure de la restauration

Lorsque vous renseignez l’heure de l’instant dans le passé dans le portail Azure, si vous avez besoin d’aide pour identifier le point de restauration, sélectionnez le lien **Cliquez ici** pour accéder au panneau du flux d’événements. Le flux d’événements fournit une liste complète des événements de création, de remplacement et de suppression sur les bases de données et les conteneurs du compte source. 

Par exemple, si vous souhaitez effectuer une restauration au point précédant la suppression ou la mise à jour d’un conteneur donné, consultez ce flux d’événements. Les événements sont affichés dans l’ordre chronologique décroissant du moment où ils se sont produits, les événements récents se trouvant en haut de la liste. Vous pouvez parcourir les résultats et sélectionner l’heure avant ou après l’événement pour affiner encore davantage l’heure de votre choix.

:::image type="content" source="./media/continuous-backup-restore-portal/event-feed-portal.png" alt-text="Utiliser un flux d’événements pour identifier l’heure du point de restauration." border="true":::

> [!NOTE]
> Le flux d’événements n’affiche pas les modifications apportées aux ressources de l’élément. Vous pouvez toujours spécifier manuellement un timestamp au cours des 30 derniers jours (à condition que le compte existe à ce moment-là) pour la restauration.

## <a name="restore-a-deleted-account"></a><a id="restore-deleted-account"></a>Restaurer un compte supprimé

Vous pouvez utiliser Portail Azure pour restaurer complètement un compte supprimé dans les 30 jours suivant sa suppression. Pour restaurer un compte supprimé, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Recherchez les ressources *Azure Cosmos DB* dans la barre de recherche globale. Elle répertorie tous vos comptes existants.
1. Sélectionnez ensuite le bouton **Restaurer**. Le volet Restaurer affiche la liste des comptes supprimés qui peuvent être restaurés pendant la période de rétention, qui est de 30 jours à partir de la date de suppression.
1. Choisissez le compte que vous souhaitez restaurer.

   :::image type="content" source="./media/continuous-backup-restore-portal/restore-deleted-account-portal.png" alt-text="Restaurer un compte supprimé à partir de Portail Azure." border="true":::

   > [!NOTE]
   > Remarque : Le volet de restauration de Portail Azure n’est rempli que si vous disposez de l’autorisation `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`. Pour en savoir plus sur la définition de cette autorisation, consultez l’article [Autorisations de sauvegarde et de restauration](continuous-backup-restore-permissions.md).

1. Pour restaurer un compte supprimé, sélectionnez un compte à restaurer et entrez les informations suivantes :

   * **Point de restauration (UTC)**  : Un timestamp au cours des 30 derniers jours. Le compte doit exister à cette date. Spécifiez le point de restauration en heure UTC. Il peut être aussi proche de la seconde quand vous souhaitez le restaurer.

   * **Emplacement** : Région de destination où le compte doit être restauré. Le compte source doit exister dans cette région à l’heure donnée. Par exemple : USA Ouest ou USA Est.  

   * **Groupe de ressources** : Groupe de ressources sous lequel le compte cible sera créé et restauré. Le groupe de ressources doit déjà exister.

   * **Restaurer le compte cible** : Le nom du compte cible doit respecter les mêmes instructions que lorsque vous créez un nouveau compte. Ce compte sera créé par le processus de restauration dans la même région que celle où se trouve votre compte source.

## <a name="track-the-status-of-restore-operation"></a><a id="track-restore-status"></a>Suivre l’état de l’opération de restauration

Après avoir lancé une opération de restauration, sélectionnez l’icône **Notification** en forme de cloche dans le coin supérieur droit du portail. Elle donne un lien qui affiche l’état du compte en cours de restauration. Pendant que la restauration est en cours, l’état du compte est *En cours de création* ; une fois l’opération de restauration terminée, l’état du compte devient *En ligne*.

:::image type="content" source="./media/continuous-backup-restore-portal/track-restore-operation-status.png" alt-text="L’état du compte restauré passe d’En cours de création à En ligne lorsque l’opération est terminée." border="true":::

## <a name="next-steps"></a>Étapes suivantes

* Configurez et gérez la sauvegarde continue en utilisant [Azure CLI](continuous-backup-restore-command-line.md), [PowerShell](continuous-backup-restore-powershell.md) ou [Azure Resource Manager](continuous-backup-restore-template.md).
* [Modèle de ressource du mode de sauvegarde continue](continuous-backup-restore-resource-model.md).
* [Gérez les autorisations](continuous-backup-restore-permissions.md) requises pour restaurer les données en mode de sauvegarde continue.
