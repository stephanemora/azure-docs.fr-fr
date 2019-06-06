---
title: Annulation de votre abonnement Azure | Microsoft Docs
description: Décrit comment annuler votre abonnement Azure, par exemple l’abonnement d’essai gratuit
author: bandersmsft
manager: amberb
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: banders
ms.openlocfilehash: 8f4279d9ac085cdd1ded0dfdda4fad9d3fe12fb8
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480225"
---
# <a name="cancel-your-subscription-for-azure"></a>Annuler votre abonnement Azure

Uniquement un abonnement Azure [administrateur de compte](billing-subscription-transfer.md#whoisaa) peut annuler un abonnement Azure. Un administrateur d’abonnement Azure peut également [affecter un autre utilisateur en tant qu’un administrateur d’abonnement](billing-add-change-azure-subscription-administrator.md#assign-a-user-as-an-administrator-of-a-subscription) afin qu’il peut annuler un abonnement. Après l’annulation de l’abonnement, vous n’avez plus accès aux ressources ni aux services Azure.

Avant d’annuler votre abonnement :

* Sauvegardez vos données. Par exemple, si vous stockez des données dans le stockage Azure ou SQL, téléchargez une copie. Si vous avez une machine virtuelle, enregistrez une image localement.
* Arrêtez les services. Accédez à la [page de ressources du portail de gestion](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) et **arrêtez** les machines virtuelles, les applications ou autres services en cours d’exécution.
* Envisagez de migrer vos données. Consultez la page [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../azure-resource-manager/resource-group-move-resources.md).
* Vous devez supprimer toutes les ressources et tous les groupes de ressources. Il est nécessaire de les supprimer avant de vous pouvez annuler un abonnement. Chaque groupe de ressources doit être supprimé individuellement. Lors de la suppression du groupe de ressources, vous devez confirmer la suppression en tapant le nom de groupe de ressources.
* Si vous avez des rôles personnalisés qui font référence à cet abonnement dans `AssignableScopes`, vous devez mettre à jour ces rôles personnalisés pour supprimer l’abonnement. Si vous essayez de mettre à jour un rôle personnalisé une fois que vous annulez un abonnement, vous pouvez obtenir une erreur. Pour plus d’informations, consultez [résoudre les problèmes avec des rôles personnalisés](../role-based-access-control/troubleshooting.md#problems-with-custom-roles) et [rôles personnalisés pour les ressources Azure](../role-based-access-control/custom-roles.md).

Si vous annulez un plan de support Azure, vous êtes toujours facturé du reliquat de l’abonnement. Pour plus d’informations, consultez les [Plans de support Azure](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-using-the-azure-portal"></a>Annuler l’abonnement à l’aide du portail Azure

1. Sélectionnez votre abonnement dans la [page Abonnements du portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Sélectionnez l'abonnement que vous souhaitez annuler.
3. Sélectionnez **Vue d'ensemble**, puis **Annuler l'abonnement**.

    ![Capture d’écran qui montre le bouton Annuler](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. Suivez les invites et terminez l’annulation.

## <a name="what-happens-after-i-cancel-my-subscription"></a>Que se passe-t-il après l’annulation de mon abonnement ?

Une fois que vous annulez, elle se termine immédiatement. Toutefois, l’annulation peut prendre jusqu’à 10 minutes avant d’être visible sur le portail. Si vous annulez en milieu de période de facturation, nous envoyons la facture finale à la date de facture par défaut à la fin de la période.

Une fois que vous annulez, vos services sont désactivés. Cela signifie que vos machines virtuelles et les adresses IP temporaires sont libérées et que le stockage est en lecture seule.

Nous attendons 90 jours avant de supprimer définitivement vos données au cas où vous devriez y accéder ou changeriez d’avis. Nous ne vous facturons pas la conservation des données. Pour en savoir plus, consultez [Microsoft Trust Center - How we manage your data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409) (Centre de gestion de la confidentialité de Microsoft - Comment nous gérons vos données).

## <a name="reactivate-subscription"></a>Réactivation de l’abonnement

Si vous annulez par inadvertance votre abonnement de paiement à l’utilisation, vous pouvez [le réactiver dans le Centre des comptes](billing-subscription-become-disable.md).

Si votre abonnement n’est pas un abonnement de paiement à l’utilisation, contactez le support dans les 90 jours suivant l’annulation pour réactiver votre abonnement.

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créer une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).
