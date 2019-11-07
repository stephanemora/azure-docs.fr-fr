---
title: Ajouter un abonnement Azure existant à votre locataire - Azure Active Directory | Microsoft Docs
description: Instructions pour l’ajout d’un abonnement Azure existant à votre locataire Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6bce05b99c14fb464cd1a17587d9cf254909d87
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473413"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Associer ou ajouter un abonnement Azure à votre locataire Azure Active Directory

Un abonnement Azure possède une relation d’approbation avec Azure Active Directory (Azure AD) : l’abonnement fait confiance à Azure AD pour authentifier les utilisateurs, les services et les appareils. Plusieurs abonnements peuvent approuver le même répertoire Azure AD, mais chaque abonnement n’approuve qu’un seul répertoire.

En cas d’expiration d’un abonnement, vous perdez l’accès aux autres ressources associées à cet abonnement. Toutefois, le répertoire Azure AD demeure dans Azure, vous permettant d’associer et de gérer le répertoire avec un abonnement Azure différent.

Tous vos utilisateurs ont un seul répertoire *accueil* pour authentification. Toutefois, vos utilisateurs peuvent aussi être des invités d’autres répertoires. Vous pouvez voir les répertoires accueil et invité pour chaque utilisateur dans Azure AD.

> [!Important]
> Lorsque vous associez un abonnement à un autre répertoire, les utilisateurs qui ont des rôles attribués à l’aide du [contrôle d’accès en fonction du rôle (RBAC)](../../role-based-access-control/role-assignments-portal.md) perdent leur accès. Les administrateurs d’abonnements classiques (administrateur de services fédérés et coadministrateurs) perdront également leur accès. 
>
> Les attributions de stratégie sont également supprimées d’un abonnement lorsque celui-ci est associé à un autre répertoire.
> 
> En outre, le déplacement de votre cluster Azure Kubernetes Service (AKS) vers un autre abonnement, ou le déplacement de l’abonnement propriétaire du cluster vers un nouveau tenant, amène le cluster à perdre sa fonctionnalité en raison de la perte des attributions de rôles et des droits de principaux de service. Pour plus d’information sur AKS, consultez [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/).

## <a name="before-you-begin"></a>Avant de commencer

Avant de pouvoir associer ou ajouter votre abonnement, vous devez effectuer les tâches suivantes :

1. Passez en revue la liste suivante de modifications et leurs éventuelles répercussions :

    - Les utilisateurs dont le rôle a été attribué à l’aide de RBAC perdront leur accès
    - L’administrateur de services fédérés et le coadministrateur perdront leur accès
    - Si vous avez des coffres de clés, ces derniers seront inaccessibles et vous devrez les corriger après l’association
    - Si vous disposez d’identités managées pour des ressources telles que Machines Virtuelles ou Logic Apps, vous devrez les réactiver ou les recréer après l’association
    - Si vous disposez d’un Azure Stack inscrit, vous devrez le réinscrire après l’association

1. Connectez-vous avec un compte qui :
    - Dispose d’une attribution de rôle [Propriétaire](../../role-based-access-control/built-in-roles.md#owner) pour l’abonnement. Pour plus d’informations sur l’attribution du rôle Propriétaire, consultez [Gérer l’accès aux ressources Azure à l’aide du contrôle d’accès en fonction du rôle et du portail Azure](../../role-based-access-control/role-assignments-portal.md).
    - existe dans le répertoire actuellement associé à l’abonnement et dans le nouveau répertoire auquel vous souhaitez que l’abonnement soit associé. Pour plus d’informations sur l’accès à un autre répertoire, consultez [Comment les administrateurs Azure Active Directory ajoutent-ils des utilisateurs B2B Collaboration ?](../b2b/add-users-administrator.md)

1. Veillez à ne pas utiliser d’abonnement de fournisseur de services cloud (CSP) Azure (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), Microsoft interne ((MS-AZR-0015P), ou Microsoft Imagine (MS-AZR-0144P).
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Pour associer un abonnement existant à votre annuaire Azure AD

1. Connectez-vous et sélectionnez l’abonnement que vous voulez utiliser dans la [page Abonnements du portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Sélectionnez **Changer de répertoire**.

    ![Page Abonnements, avec l’option Changer de répertoire en surbrillance](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. Examinez les avertissements qui s’affichent, puis sélectionnez **Changer**.

    ![Page Changer de répertoire, montrant le nouveau répertoire](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Le répertoire est modifié pour l’abonnement et un message de réussite s’affiche.

    ![Message de réussite concernant la modification du répertoire](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)
4. Utilisez le **sélecteur de répertoire** pour passer à votre nouveau répertoire. Dans certains cas, l’affichage correct dans son intégralité peut prendre plusieurs heures. Si cette opération semble prendre trop longtemps, vérifiez que le **filtre d’abonnement Global** de l’abonnement déplacé n’est pas masqué. Vous devrez peut-être vous déconnecter du portail Azure et vous reconnecter pour que le nouveau répertoire soit visible. 

    ![Page du sélecteur de répertoire, avec exemples d’informations](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

La modification du répertoire de l’abonnement est une opération de niveau de service. Elle n’affecte donc pas la propriété de facturation de l’abonnement. L’administrateur du compte peut toujours changer l’administrateur du service depuis le [centre du compte](https://account.azure.com/subscriptions). Pour supprimer le répertoire d’origine, vous devez transférer la propriété de facturation de l’abonnement à un nouvel administrateur du compte. Pour en savoir plus sur le transfert de la propriété de facturation, consultez [Transfert de la propriété d’un abonnement Azure à un autre compte](../../billing/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Étapes post-association
Une fois que vous aurez associé un abonnement à un autre répertoire, vous devrez peut-être suivre des étapes supplémentaires pour reprendre les opérations.

1. Si vous avez des coffres de clés, vous devez modifier l’ID de tenant des coffres de clés. Pour plus d’informations, consultez [Modifier l’ID client d’un coffre de clés après un déplacement d’abonnement](../../key-vault/key-vault-subscription-move-fix.md).

2. Si vous utilisiez des identités managées attribuées par le système pour les ressources, vous devez les réactiver. Si vous utilisiez des identités managées attribuées par l’utilisateur, vous devez les recréer. Après la réactivation ou la recréation des identités managées, vous devez rétablir les autorisations attribuées à ces identités. Pour plus d’informations, consultez [Que sont les identités managées pour les ressources Azure ?](../managed-identities-azure-resources/overview.md).

3. Si vous avez inscrit un Azure Stack à l’aide de cet abonnement, vous devez procéder à une réinscription. Pour plus d’informations, consultez [Inscrire Azure Stack auprès d’Azure](/azure-stack/operator/azure-stack-registration).



## <a name="next-steps"></a>Étapes suivantes

- Pour créer un locataire Azure AD, voir [Accéder à Azure Active Directory pour créer un locataire](active-directory-access-create-new-tenant.md)

- Pour plus d’informations sur la façon dont l’accès aux ressources est contrôlé dans Microsoft Azure, voir [Présentation de l’accès aux ressources dans Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Pour en savoir plu sur l’affectation de rôles dans Azure AD, voir [Affecter des rôles de répertoire à des utilisateurs avec Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
