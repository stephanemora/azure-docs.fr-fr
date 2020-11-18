---
title: Annulation de votre abonnement Azure
description: Décrit comment annuler votre abonnement Azure, par exemple l’abonnement d’essai gratuit
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 11/09/2020
ms.author: banders
ms.openlocfilehash: f90d1fe22ae2f46fdc6d764ce98db8e0f48b8b35
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408020"
---
# <a name="cancel-your-azure-subscription"></a>Annulation de votre abonnement Azure

Vous pouvez annuler votre abonnement Azure dans le portail Azure si vous n’en avez plus besoin.

Bien que cela ne soit pas obligatoire, Microsoft *recommande* d’effectuer les actions suivantes avant d’annuler votre abonnement :

* Sauvegardez vos données. Par exemple, si vous stockez des données dans le stockage Azure ou SQL, téléchargez une copie. Si vous avez une machine virtuelle, enregistrez une image localement.
* Arrêtez les services. Accédez à la [page de ressources du portail de gestion](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) et **arrêtez** les machines virtuelles, les applications ou autres services en cours d’exécution.
* Envisagez de migrer vos données. Consultez la page [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
* Supprimez l’ensemble des ressources et des groupes de ressources.
* Si vous disposez de rôles personnalisés associés à cet abonnement dans `AssignableScopes`, vous devez mettre à jour ces rôles pour supprimer l’abonnement. Si vous essayez de mettre à jour un rôle personnalisé après annulation d'un abonnement, une erreur peut s'afficher. Pour plus d’informations, consultez [Résoudre les problèmes liés aux rôles personnalisés](../../role-based-access-control/troubleshooting.md#problems-with-custom-roles) et [Rôles personnalisés Azure](../../role-based-access-control/custom-roles.md).

Si vous annulez un plan de support Azure, vous êtes toujours facturé du reliquat de l’abonnement. Pour plus d’informations, consultez les [Plans de support Azure](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-in-the-azure-portal"></a>Annuler un abonnement dans le portail Azure

1. Sélectionnez votre abonnement dans la [page Abonnements du portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Sélectionnez l'abonnement que vous souhaitez annuler.
1. Sélectionnez **Vue d'ensemble**, puis **Annuler l'abonnement**.
    ![Capture d’écran montrant le bouton Annuler](./media/cancel-azure-subscription/cancel_ibiza.png)
1. Suivez les invites et terminez l’annulation.

## <a name="who-can-cancel-a-subscription"></a>Qui peut annuler un abonnement ?

Le tableau ci-dessous présente l’autorisation requise pour annuler un abonnement.

|Type d’abonnement     |Qui peut annuler  |
|---------|---------|
|Abonnements créés lorsque vous vous inscrivez à Azure via le site web Azure. Par exemple, quand vous souscrivez à un [compte gratuit Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), un [compte avec tarifs de paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/) ou en tant qu’[abonné Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  Administrateur de compte et propriétaires de l’abonnement  |
|[Contrat Entreprise Microsoft](https://azure.microsoft.com/pricing/enterprise-agreement/) et [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  Propriétaire du compte et propriétaires de l’abonnement       |
|[Offre Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) et [Offre Azure pour DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Propriétaires de l’abonnement      |

## <a name="what-happens-after-i-cancel-my-subscription"></a>Que se passe-t-il après l’annulation de mon abonnement ?

Une fois que vous l’annulez, la facturation s’arrête immédiatement. Toutefois, l’annulation peut prendre jusqu’à 10 minutes avant d’être visible sur le portail. Si vous annulez en milieu de période de facturation, nous envoyons la facture finale à la date de facture par défaut à la fin de la période.

Après l'annulation, vos services sont désactivés. Cela signifie que vos machines virtuelles et les adresses IP temporaires sont libérées et que le stockage est en lecture seule.

Une fois votre abonnement annulé, Microsoft attend 30 à 90 jours avant de supprimer définitivement vos données au cas où vous deviez y accéder ou changiez d’avis. Nous ne vous facturons pas la conservation des données. Pour en savoir plus, consultez [Microsoft Trust Center - How we manage your data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409) (Centre de gestion de la confidentialité de Microsoft - Comment nous gérons vos données).

## <a name="delete-free-trial-subscription"></a>Supprimer un abonnement d’essai gratuit

Si vous disposez d’un abonnement d’essai gratuit, vous n’êtes pas tenu d’attendre 30 jours pour que l’abonnement soit automatiquement supprimé. Vous pouvez supprimer votre abonnement *trois jours* après l’avoir annulé. L’option **Supprimer l’abonnement** n’est disponible qu’au bout de trois jours après son annulation.

1. Attendez trois jours après la date d’annulation de l’abonnement.
1. Sélectionnez votre abonnement dans la page [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) du portail Azure.
1. Sélectionnez l'abonnement que vous souhaitez supprimer.
1. Sélectionnez **Vue d'ensemble**, puis **Supprimer l’abonnement**.

## <a name="reactivate-subscription"></a>Réactivation de l’abonnement

Si vous annulez par inadvertance votre abonnement assorti d’un paiement à l’utilisation, vous pouvez [le réactiver dans le Centre des comptes](subscription-disabled.md).

Si votre abonnement n’est pas un abonnement assorti d’un paiement à l’utilisation, contactez le support dans les 90 jours suivant l’annulation pour le réactiver.

## <a name="why-dont-i-see-the-cancel-subscription-option-on-the-azure-portal"></a>Pourquoi ne puis-je pas voir l’option Annuler l’abonnement sur le portail Azure ? 

Il est possible que vous ne soyez pas autorisé à annuler un abonnement. Consultez [Qui peut annuler un abonnement ?](https://docs.microsoft.com/azure/cost-management-billing/manage/cancel-azure-subscription#who-can-cancel-a-subscription) pour obtenir une description des personnes en mesure d’annuler différents types d’abonnements.

## <a name="how-do-i-delete-my-azure-account"></a>Comment supprimer mon compte Azure ?

*Je dois supprimer mon compte, y compris toutes mes informations personnelles. J’ai déjà annulé mes abonnements actifs (essai gratuit). Je n’ai pas d’abonnement actif et je souhaiterais supprimer mon compte dans son intégralité*.

* Si vous disposez d’un compte Azure Active Directory par l’intermédiaire de votre organisation, l’administrateur Azure AD peut supprimer le compte. Une fois ce délai passé, vos services sont désactivés. Cela signifie que vos machines virtuelles et les adresses IP temporaires sont libérées et que le stockage est en lecture seule. En résumé, une fois que vous l’annulez, la facturation s’arrête immédiatement.

* Si vous ne possédez pas de compte Azure AD par l’intermédiaire de votre organisation, vous pouvez annuler et supprimer vos abonnements Azure, puis supprimer votre carte de crédit du compte. Si l’action ne supprime pas le compte, elle le rend inutilisable. Vous pouvez aller plus loin et supprimer également le compte Microsoft associé s’il n’est pas utilisé à d’autres fins.

## <a name="how-do-i-cancel-a-visual-studio-professional-account"></a>Comment annuler un compte Visual Studio Professional ?

Consultez l’article [Renouvellement et annulation](https://docs.microsoft.com/visualstudio/subscriptions/faq/admin/renewal-cancellation). Si vous avez des abonnements Azure pour Visual Studio, ceux-ci doivent également être annulés et supprimés.

## <a name="next-steps"></a>Étapes suivantes

- Si nécessaire, vous pouvez réactiver un abonnement avec paiement à l’utilisation dans le [Centre des comptes](subscription-disabled.md).
