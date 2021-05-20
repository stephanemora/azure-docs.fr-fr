---
title: Gérer les locataires dans le compte de facturation de votre Contrat client Microsoft - Azure
description: Cet article vous aide à comprendre et à gérer les locataires associés à votre compte de facturation dans le cadre d’un Contrat client Microsoft.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 05/05/2021
ms.author: banders
ms.reviewer: baolcsva
ms.openlocfilehash: a1d66e1a28717feb66bc223d10ae44e8d5457728
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108747860"
---
# <a name="manage-tenants-in-your-microsoft-customer-agreement-billing-account"></a>Gérer les locataires dans le compte de facturation de votre Contrat client Microsoft

Cet article vous aide à comprendre et à gérer les locataires associés au compte de facturation de votre Contrat client Microsoft. Utilisez les informations pour gérer les locataires, transférer les abonnements et administrer la propriété de facturation tout en garantissant un accès sécurisé à votre environnement de facturation.

## <a name="whats-a-tenant"></a>Qu’est-ce qu’un locataire ?

Un locataire est une représentation numérique de votre organisation. Il est principalement associé à un domaine, par exemple Microsoft.com. Il s’agit d’un environnement managé par le biais d’Azure Active Directory, qui vous permet d’attribuer des autorisations utilisateur pour gérer les ressources et la facturation Azure.

Chaque locataire est distinct et séparé des autres locataires. Cependant, vous pouvez autoriser les utilisateurs invités d’autres locataires à accéder à votre locataire pour suivre vos coûts et gérer la facturation.

## <a name="how-tenants-and-subscriptions-relate-to-billing-account"></a>Relation entre les locataires et abonnements et le compte de facturation

Vous utilisez votre Contrat client Microsoft (compte de facturation) pour suivre les coûts et gérer la facturation. Chaque compte de facturation est associé à au moins un profil de facturation. Le profil de facturation vous permet de gérer votre facture et votre mode de paiement. Chaque profil de facturation contient une section de facture par défaut. Vous pouvez créer plus de sections de facture pour regrouper, suivre et gérer les coûts de façon plus précise si nécessaire.

- Votre compte de facturation est associé à un seul locataire. Cela signifie que seuls les utilisateurs qui font partie du locataire peuvent accéder à votre compte de facturation.
- Quand vous créez un abonnement Azure pour votre compte de facturation, il est toujours créé dans le locataire de votre compte de facturation. Toutefois, vous pouvez déplacer des abonnements vers d’autres locataires. Vous pouvez également lier des abonnements existants d’autres locataires à votre compte de facturation. Ceci vous permet de gérer la facturation de manière centralisée par le biais d’un locataire tout en conservant les ressources et les abonnements dans les autres locataires en fonction de vos besoins.

Le diagramme suivant montre comment le compte de facturation et les abonnements sont liés aux locataires. Le compte de facturation Contoso MCA est associé au locataire 1 alors que le compte Contoso PAYG est associé au locataire 2. Supposons que Contoso souhaite payer son abonnement PAYG par le biais de son compte de facturation MCA. Il peut utiliser un transfert de propriété de facturation pour lier l’abonnement à son compte de facturation MCA. L’abonnement et ses ressources resteront associés au locataire 2, mais l’utilisation du compte de facturation MCA par ces derniers sera payante.

:::image type="content" source="./media/manage-tenants/billing-hierarchy-example.png" alt-text="Diagramme montrant un exemple de hiérarchie de facturation." border="false" lightbox="./media/manage-tenants/billing-hierarchy-example.png":::

## <a name="manage-subscriptions-under-multiple-tenants-in-a-single-microsoft-customer-agreement"></a>Gérer les abonnements sous plusieurs locataires dans un seul Contrat client Microsoft

Les propriétaires de facturation peuvent créer des abonnements quand ils disposent des [autorisations appropriées](../manage/understand-mca-roles.md#subscription-billing-roles-and-tasks) sur le compte de facturation. Par défaut, tous les abonnements créés sous le Contrat client Microsoft sont dans le locataire qui lui est associé.

- Vous pouvez lier des abonnements d’autres locataires au compte de facturation de votre Contrat client Microsoft. La prise de propriété de facturation d’un abonnement modifie uniquement la configuration de la facturation. Elle n’affecte pas le locataire du service ou les rôles RBAC Azure.
- Pour modifier le propriétaire de l’abonnement dans le locataire du service, vous devez transférer l’[abonnement vers un autre annuaire Azure Active Directory](../../role-based-access-control/transfer-subscription.md).

Un compte de facturation MCA est géré par un seul locataire/annuaire. Le compte de facturation contrôle uniquement la facturation des abonnements figurant dans son locataire. Toutefois, vous pouvez utiliser un transfert de propriété de facturation pour lier un abonnement à un compte de facturation d’un autre locataire.

### <a name="billing-ownership-transfer"></a>Transfert de propriété de facturation

Un transfert de propriété de facturation modifie uniquement la disposition des factures d’un abonnement individuel. La gestion des utilisateurs et des ressources pour l’abonnement ne change pas.

Un transfert de propriété de facturation réalise deux opérations :

- La propriété de facturation d’origine de l’abonnement est supprimée.
- La propriété de facturation de l’abonnement est *liée* au compte de facturation cible, qui peut se trouver dans un autre locataire/annuaire.

Un transfert de propriété de facturation n’affecte pas les éléments suivants :

- Utilisateurs
- Ressources
- Autorisations Azure RBAC


## <a name="add-guest-users-to-your-microsoft-customer-agreement-tenant"></a>Ajouter des utilisateurs invités au locataire de votre Contrat client Microsoft

Les utilisateurs ajoutés au locataire de facturation de votre Contrat client Microsoft pour gérer les responsabilités de facturation à partir d’un autre locataire doivent être invités.

Pour inviter un utilisateur, celui-ci doit avoir une adresse e-mail existante avec un domaine différent de votre domaine Azure AD (Active Directory). Azure AD envoie à l’utilisateur invité un e-mail contenant un lien pour l’authentification.

:::image type="content" source="./media/manage-tenants/guest-invitation-email.png" alt-text="Capture d’écran montrant un exemple d’invitation par e-mail." lightbox="./media/manage-tenants/guest-invitation-email.png" :::

Quand un utilisateur est ajouté au locataire associé au Contrat client Microsoft, il doit [accepter l’invitation](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation).

Quand il sélectionne le lien **Accepter l’invitation**, il est invité à s’authentifier auprès d’Azure.

:::image type="content" source="./media/manage-tenants/authenticate.png" alt-text="Capture d’écran montrant une invite d’authentification auprès d’Azure." lightbox="./media/manage-tenants/authenticate.png" :::

Puis, il sélectionne **Accepter**.

:::image type="content" source="./media/manage-tenants/accept-invitation.png" alt-text="Capture d’écran montrant une invite permettant d’accepter l’invitation." lightbox="./media/manage-tenants/accept-invitation.png" :::

Après acceptation, il peut [voir le compte de facturation du Contrat client Microsoft sous Cost Management + Billing](../understand/mca-overview.md#check-access-to-a-microsoft-customer-agreement).

:::image type="content" source="./media/manage-tenants/billing-microsoft-customer-agreement-in-list.png" alt-text="Capture d’écran montrant le Contrat client Microsoft dans la liste des comptes de facturation." lightbox="./media/manage-tenants/billing-microsoft-customer-agreement-in-list.png" :::

L’autorisation d’inviter des utilisateurs est contrôlée par vos paramètres Azure AD. La valeur des paramètres est indiquée sous **Paramètres** dans la page **Relations organisationnelles**. Vérifiez que le paramètre est sélectionné. Si ce n’est pas le cas, l’invitation échoue. Pour plus d’informations, consultez [Restriction des autorisations d’accès des utilisateurs invités](../../active-directory/enterprise-users/users-restrict-guest-permissions.md).

:::image type="content" source="./media/manage-tenants/external-collaboration-settings.png" alt-text="Capture d’écran des paramètres de collaboration externe." lightbox="./media/manage-tenants/external-collaboration-settings.png" :::

> [!IMPORTANT]
> Les utilisateurs invités ont accès au locataire associé au Contrat client Microsoft, ce qui peut poser un problème de sécurité. Pour plus d’informations, consultez [Restreindre les autorisations par défaut des utilisateurs invités](../../active-directory/fundamentals/users-default-permissions.md#restrict-member-users-default-permissions).

## <a name="manage-multiple-microsoft-cloud-services-under-an-azure-ad-tenant"></a>Gérer plusieurs services cloud Microsoft sous un locataire Azure AD

Vous pouvez gérer plusieurs services cloud pour votre organisation sous un seul locataire Azure AD. Les comptes d’utilisateur de toutes les offres cloud Microsoft sont stockés dans le locataire Azure AD, qui contient des comptes d’utilisateur et des groupes. Le diagramme suivant illustre une organisation avec plusieurs services qui utilisent un locataire Azure AD commun contenant des comptes. Chaque service a son propre portail, en texte bleu, avec lequel les utilisateurs gèrent leurs services.

:::image type="content" source="./media/manage-tenants/diagram-multiple-services-common-azure-ad-tenant-accounts.png" alt-text="Diagramme illustrant une organisation avec plusieurs services qui utilisent un locataire Azure AD commun contenant des comptes." border="false" lightbox="./media/manage-tenants/diagram-multiple-services-common-azure-ad-tenant-accounts.png":::

## <a name="next-steps"></a>Étapes suivantes

Lisez les articles suivants pour savoir comment administrer la propriété de facturation flexible et garantir un accès sécurisé à votre Contrat client Microsoft.

- [Configurer un locataire](../../active-directory/develop/quickstart-create-new-tenant.md)
- [Rôles intégrés Azure](../../role-based-access-control/built-in-roles.md)
- [Transférer un abonnement Azure vers une autre instance Azure AD Directory](../../role-based-access-control/transfer-subscription.md)
- [Restriction des autorisations d’accès invité (préversion) dans Azure Active Directory](../../active-directory/enterprise-users/users-restrict-guest-permissions.md)
- [Ajoutez des utilisateurs invités à votre annuaire dans le portail Azure](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)
- [Quelles sont les autorisations d’utilisateur par défaut dans Azure Active Directory ?](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)
- [Qu’est-ce qu’Azure Active Directory ?](../../active-directory/fundamentals/active-directory-whatis.md)