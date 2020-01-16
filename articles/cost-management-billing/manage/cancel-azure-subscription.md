---
title: Annulation de votre abonnement Azure | Microsoft Docs
description: Décrit comment annuler votre abonnement Azure, par exemple l’abonnement d’essai gratuit
author: bandersmsft
manager: amberb
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: e4ce97b69250d8af4c94e8eed8df9c9497e0bb46
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75985989"
---
# <a name="cancel-your-azure-subscription"></a>Annulation de votre abonnement Azure

Vous pouvez annuler votre abonnement Azure dans le portail Azure si vous n’avez plus besoin de l’abonnement.

Avant d’annuler votre abonnement :
* Sauvegardez vos données. Par exemple, si vous stockez des données dans le stockage Azure ou SQL, téléchargez une copie. Si vous avez une machine virtuelle, enregistrez une image localement.
* Arrêtez les services. Accédez à la [page de ressources du portail de gestion](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) et **arrêtez** les machines virtuelles, les applications ou autres services en cours d’exécution.
* Envisagez de migrer vos données. Consultez la page [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
* Supprimez l’ensemble des ressources et des groupes de ressources. Leur suppression est impérative pour vous permettre d'annuler un abonnement. Chaque groupe de ressources doit être supprimé individuellement. Lors de la suppression du groupe de ressources, vous devez confirmer cette suppression en entrant le nom du groupe de ressources.
* Si vous disposez de rôles personnalisés associés à cet abonnement dans `AssignableScopes`, vous devez mettre à jour ces rôles pour supprimer l’abonnement. Si vous essayez de mettre à jour un rôle personnalisé après annulation d'un abonnement, une erreur peut s'afficher. Pour plus d’informations, consultez [Résoudre les problèmes liés aux rôles personnalisés](../../role-based-access-control/troubleshooting.md#problems-with-custom-roles) et [Rôles personnalisés pour les ressources Azure](../../role-based-access-control/custom-roles.md).

Si vous annulez un plan de support Azure, vous êtes toujours facturé du reliquat de l’abonnement. Pour plus d’informations, consultez les [Plans de support Azure](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-in-the-azure-portal"></a>Annuler un abonnement dans le portail Azure

1. Sélectionnez votre abonnement dans la [page Abonnements du portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Sélectionnez l'abonnement que vous souhaitez annuler.
3. Sélectionnez **Vue d'ensemble**, puis **Annuler l'abonnement**.
    ![Capture d’écran montrant le bouton Annuler](./media/cancel-azure-subscription/cancel_ibiza.png)
3. Suivez les invites et terminez l’annulation.


## <a name="who-can-cancel-a-subscription"></a>Qui peut annuler un abonnement ?

Le tableau ci-dessous présente l’autorisation requise pour annuler un abonnement.

|Type d’abonnement     |Qui peut annuler  |
|---------|---------|
|Abonnements créés lorsque vous vous inscrivez à Azure via le site web Azure. Par exemple, quand vous souscrivez à un [compte gratuit Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), un [compte avec tarifs de paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/) ou en tant qu’[abonné Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  Administrateur de compte, propriétaires et contributeurs pour l’abonnement  |
|[Contrat Entreprise Microsoft](https://azure.microsoft.com/pricing/enterprise-agreement/) et [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  Propriétaire du compte, propriétaires et contributeurs pour l’abonnement       |
|[Offre Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) et [Offre Azure pour DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Propriétaires et contributeurs pour l’abonnement      |


## <a name="what-happens-after-i-cancel-my-subscription"></a>Que se passe-t-il après l’annulation de mon abonnement ?

Une fois que vous l’annulez, la facturation s’arrête immédiatement. Toutefois, l’annulation peut prendre jusqu’à 10 minutes avant d’être visible sur le portail. Si vous annulez en milieu de période de facturation, nous envoyons la facture finale à la date de facture par défaut à la fin de la période.

Après l'annulation, vos services sont désactivés. Cela signifie que vos machines virtuelles et les adresses IP temporaires sont libérées et que le stockage est en lecture seule.

Nous attendons 90 jours avant de supprimer définitivement vos données au cas où vous devriez y accéder ou changeriez d’avis. Nous ne vous facturons pas la conservation des données. Pour en savoir plus, consultez [Microsoft Trust Center - How we manage your data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409) (Centre de gestion de la confidentialité de Microsoft - Comment nous gérons vos données).

## <a name="reactivate-subscription"></a>Réactivation de l’abonnement

Si vous annulez par inadvertance votre abonnement assorti d’un paiement à l’utilisation, vous pouvez [le réactiver dans le Centre des comptes](subscription-disabled.md).

Si votre abonnement n’est pas un abonnement assorti d’un paiement à l’utilisation, contactez le support dans les 90 jours suivant l’annulation pour le réactiver.

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).
