---
title: Gérer les abonnements
description: Les abonnements sont constitués d’appareils dédiés gérés et peuvent être intégrés ou annulés en fonction des besoins.
ms.date: 3/30/2021
ms.topic: how-to
ms.openlocfilehash: 10098ec66d15b4c894106cc1d37d7ac339d508cd
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387127"
---
# <a name="manage-a-subscription"></a>Gérer un abonnement

Les abonnements sont gérés mensuellement. Quand vous intégrez un abonnement, vous êtes facturé pour celui-ci jusqu’à la fin du mois. De même, si vous annulez un abonnement, vous êtes facturé pour le reste du mois dans lequel vous avez annulé cet abonnement.

## <a name="onboard-a-subscription"></a>Intégrer un abonnement

Pour commencer à utiliser Azure Defender pour IoT, vous devez disposer d’un abonnement Microsoft Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire pour avoir un compte gratuit. Si vous avez déjà accès à un abonnement Azure, mais qu’il ne figure pas dans la liste, vérifiez les détails de votre compte et confirmez vos autorisations avec le propriétaire de l’abonnement.

Pour intégrer un abonnement :

1. Accédez à la page Tarification du portail Azure. 

   :::image type="content" source="media/how-to-manage-subscriptions/no-subscription.png" alt-text="Accédez à la page Tarification du portail Azure.":::

1. Sélectionnez **Intégrer un abonnement**.

1. Dans la fenêtre **Intégrer un abonnement**, sélectionnez votre abonnement et le nombre d’appareils validés dans les menus déroulants. 

   :::image type="content" source="media/how-to-manage-subscriptions/onboard-subscription.png" alt-text="Sélectionnez votre abonnement et le nombre d’appareils validés.":::

1. Sélectionnez **Intégrer**.

## <a name="offboard-a-subscription"></a>Retirer un abonnement

Les abonnements sont gérés mensuellement. Quand vous retirez un abonnement, vous êtes facturé pour celui-ci jusqu’à la fin du mois.

Désinstallez tous les capteurs associés à l’abonnement avant de retirer l’abonnement. Pour plus d’informations sur la suppression d’un capteur, consultez [Supprimer un capteur](how-to-manage-sensors-on-the-cloud.md#delete-a-sensor). 

Pour retirer un abonnement :

1. Accédez à la page **Tarification**.
1. Sélectionnez l’abonnement, puis l’icône **Supprimer** :::image type="icon" source="media/how-to-manage-sensors-on-the-cloud/delete-icon.png" border="false":::.
1. Dans la fenêtre contextuelle de confirmation, activez la case à cocher pour confirmer que vous avez supprimé tous les capteurs associés à l’abonnement.

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/offboard-popup.png" alt-text="Activez la case à cocher et sélectionnez Annuler pour annuler votre capteur.":::

1. Sélectionnez le bouton **Retirer**. 

## <a name="next-steps"></a>Étapes suivantes

[Activer et configurer votre console de gestion locale](how-to-activate-and-set-up-your-on-premises-management-console.md)
