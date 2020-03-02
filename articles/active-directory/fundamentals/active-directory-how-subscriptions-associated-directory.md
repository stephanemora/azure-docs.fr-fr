---
title: Ajouter un abonnement Azure existant à votre locataire - Azure AD
description: Instructions pour l’ajout d’un abonnement Azure existant à votre locataire Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8f44e9a1e43da2b9ce6c817898c1722fba715c4
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978958"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Associer ou ajouter un abonnement Azure à votre locataire Azure Active Directory

Un abonnement Azure comprend une relation d’approbation avec Azure AD. Un abonnement fait confiance à Azure AD pour authentifier les utilisateurs, les services et les appareils.

Plusieurs abonnements peuvent approuver le même annuaire Azure AD. Chaque abonnement ne peut faire confiance qu’à un seul annuaire.

En cas d’expiration d’un abonnement, vous perdez l’accès aux autres ressources associées à cet abonnement. Toutefois, l’annuaire Azure AD reste dans Azure. Vous pouvez associer et gérer l’annuaire à l’aide d’un autre abonnement Azure.

Tous vos utilisateurs ont un seul répertoire *accueil* pour authentification. Vos utilisateurs peuvent également être les invités d’autres annuaires. Vous pouvez voir les répertoires accueil et invité pour chaque utilisateur dans Azure AD.

> [!Important]
> Lorsque vous associez un abonnement à un autre annuaire, les utilisateurs possédant des rôles attribués à l’aide du [contrôle d’accès en fonction du rôle (RBAC)](../../role-based-access-control/role-assignments-portal.md) perdent leur accès. Les administrateurs d’abonnements classiques, entre autres les administrateurs de services et les coadministrateurs perdent également leur accès.
>
> Les attributions de stratégie sont également supprimées d’un abonnement lorsque celui-ci est associé à un autre répertoire.
>
> Le déplacement de votre cluster Azure Kubernetes Service (AKS) vers un autre abonnement, ou le déplacement de l’abonnement propriétaire du cluster vers un nouveau locataire, amène le cluster à perdre sa fonctionnalité en raison de la perte des attributions de rôles et des droits de principaux de service. Pour plus d’information sur AKS, consultez [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/).


## <a name="before-you-begin"></a>Avant de commencer

Avant de pouvoir associer ou ajouter votre abonnement, effectuez les tâches suivantes :

- Passez en revue la liste suivante de modifications et leurs éventuelles répercussions :

  - Les utilisateurs dont le rôle a été attribué à l’aide de RBAC perdront leur accès
  - L’administrateur de services fédérés et le coadministrateur perdront leur accès
  - Si vous avez des coffres de clés, ces derniers seront inaccessibles et vous devrez les corriger après l’association
  - Si vous disposez d’identités managées pour des ressources telles que Machines Virtuelles ou Logic Apps, vous devez les réactiver ou les recréer après l’association
  - Si vous disposez d’un Azure Stack inscrit, vous devrez le réinscrire après l’association

- Connectez-vous avec un compte qui :

  - Dispose d’une attribution de rôle [Propriétaire](../../role-based-access-control/built-in-roles.md#owner) pour l’abonnement. Pour plus d’informations sur l’attribution du rôle Propriétaire, consultez [Gérer l’accès aux ressources Azure à l’aide du contrôle d’accès en fonction du rôle et du portail Azure](../../role-based-access-control/role-assignments-portal.md).
  - Existe à la fois dans l’annuaire actif et dans le nouvel annuaire. L’annuaire actif est associé à l’abonnement. Vous associerez le nouvel annuaire à l’abonnement. Pour plus d’informations sur l’accès à un autre annuaire, consultez [Ajouter des utilisateurs Azure Active Directory B2B Collaboration dans le portail Azure](../b2b/add-users-administrator.md).

- Veillez à ne pas utiliser d’abonnement de fournisseur de services cloud (CSP) Azure (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), Microsoft interne ((MS-AZR-0015P), ou Microsoft Imagine (MS-AZR-0144P).

## Associer un abonnement à un annuaire<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Pour associer un abonnement existant à votre annuaire Azure AD, suivez ces étapes :

1. Connectez-vous et sélectionnez l’abonnement que vous voulez utiliser dans la [page Abonnements du portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Sélectionnez **Changer de répertoire**.

    ![Page Abonnements, avec l’option Changer de répertoire en surbrillance](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. Examinez les avertissements qui s’affichent, puis sélectionnez **Changer**.

    ![Page Changer de répertoire, montrant le nouveau répertoire](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Le répertoire est modifié pour l’abonnement et un message de réussite s’affiche.

    ![Message de réussite concernant la modification du répertoire](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)

Utilisez **Changer d’annuaire** pour passer à votre nouvel annuaire. Dans certains cas, l’affichage correct dans son intégralité peut prendre plusieurs heures. S’il semble trop long, vérifiez le **Filtre d’abonnement global**. Assurez-vous que l’abonnement déplacé n’est pas masqué. Vous devrez peut-être vous déconnecter du portail Azure et vous reconnecter pour que le nouvel annuaire soit visible.

![Page du sélecteur de répertoire, avec exemples d’informations](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

La modification du répertoire de l’abonnement est une opération de niveau de service. Elle n’affecte donc pas la propriété de facturation de l’abonnement. L’administrateur du compte peut toujours changer l’administrateur du service depuis le [centre du compte](https://account.azure.com/subscriptions). Pour supprimer le répertoire d’origine, vous devez transférer la propriété de facturation de l’abonnement à un nouvel administrateur du compte. Pour en savoir plus sur le transfert de la propriété de facturation, consultez [Transfert de la propriété d’un abonnement Azure à un autre compte](../../cost-management-billing/manage/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Étapes postérieures à l’association

Après avoir associé un abonnement à un autre annuaire, vous pouvez être amené à effectuer les tâches suivantes pour reprendre le cours des opérations :

- Si vous avez des coffres de clés, vous devez modifier l’ID de tenant des coffres de clés. Pour plus d’informations, consultez [Modifier l’ID client d’un coffre de clés après un déplacement d’abonnement](../../key-vault/key-vault-subscription-move-fix.md).

- Si vous utilisiez des identités managées attribuées par le système pour les ressources, vous devez réactiver ces identités. Si vous utilisiez des identités managées affectées par l’utilisateur pour les ressources, vous devez réactiver ces identités. Après la réactivation ou la recréation des identités managées, vous devez rétablir les autorisations attribuées à ces identités. Pour plus d’informations, consultez [Identités managées pour les ressources Azure](../managed-identities-azure-resources/overview.md).

- Si vous avez inscrit une instance Azure Stack à l’aide de cet abonnement, vous devez procéder à une réinscription. Pour plus d’informations, consultez [Inscrire Azure Stack auprès d’Azure](/azure-stack/operator/azure-stack-registration).

## <a name="next-steps"></a>Étapes suivantes

- Pour créer un locataire Azure AD, consultez [Démarrage rapide : Créer un locataire dans Azure Active Directory](active-directory-access-create-new-tenant.md).

- Pour en savoir plus sur la façon dont Microsoft Azure contrôle l’accès aux ressources, consultez [Rôles d’administrateur d’abonnement classique, rôles RBAC Azure et rôles d’administrateur Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Pour en apprendre davantage sur l’attribution de rôles dans Azure AD, consultez [Attribuer des rôles administrateur et non-administrateur aux utilisateurs avec Azure Active Directory](active-directory-users-assign-role-azure-portal.md).
