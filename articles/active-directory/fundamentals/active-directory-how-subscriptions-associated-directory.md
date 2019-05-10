---
title: Ajouter un abonnement Azure existant à votre locataire - Azure Active Directory | Microsoft Docs
description: Instructions pour l’ajout d’un abonnement Azure existant à votre locataire Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f37c501bb53b3b73c30d02d00d49515f907e4ae
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65463626"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Associer ou ajouter un abonnement Azure à votre locataire Azure Active Directory

Un abonnement Azure a une relation d’approbation avec Azure Active Directory (Azure AD), ce qui signifie que l’abonnement fait confiance à Azure AD pour authentifier les utilisateurs, les services et les périphériques. Plusieurs abonnements peuvent approuver le même répertoire Azure AD, mais chaque abonnement n’approuve qu’un seul répertoire.

En cas d’expiration d’un abonnement, vous perdez l’accès aux autres ressources associées à cet abonnement. Toutefois, le répertoire Azure AD demeure dans Azure, vous permettant d’associer et de gérer le répertoire avec un abonnement Azure différent.

Tous vos utilisateurs ont un seul *domestique* répertoire pour l’authentification. Toutefois, vos utilisateurs peuvent aussi être des invités d’autres répertoires. Vous pouvez voir les répertoires accueil et invité pour chaque utilisateur dans Azure AD.

> [!Important]
> Lorsque vous associez un abonnement à un autre répertoire, les utilisateurs qui ont des rôles attribués à l’aide de [contrôle d’accès en fonction du rôle (RBAC)](../../role-based-access-control/role-assignments-portal.md) perdent leur accès. Les administrateurs d’abonnements Classic (administrateur de Service et aux coadministrateurs) seront également perdre l’accès.
> 
> En outre, le déplacement de votre cluster Azure Kubernetes Service (AKS) vers un autre abonnement ou le déplacement de l’abonnement qui possède le cluster vers un nouveau client, entraîne le cluster perd la fonctionnalité en raison des attributions de rôles perdues et les droits de principaux de service. Pour plus d’informations sur ACS, consultez [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/).

## <a name="before-you-begin"></a>Avant de commencer

Avant de pouvoir associer ou ajouter votre abonnement, vous devez effectuer les tâches suivantes :

1. Passez en revue la liste suivante des modifications et comment vous pouvez être affectées :

    - Les utilisateurs qui ont été affectés des rôles à l’aide de RBAC perdent leur accès
    - Administrateur de service et les Coadministrateurs vont perdre l’accès
    - Si vous avez des coffres de clés, ils seront inaccessibles et vous devrez les corriger après l’association
    - Si vous avez un inscrit Azure Stack, vous devrez réinscrire après l’association

1. Connectez-vous avec un compte qui :
    - A un [propriétaire](../../role-based-access-control/built-in-roles.md#owner) attribution de rôle pour l’abonnement. Pour plus d’informations sur la façon d’attribuer le rôle de propriétaire, consultez [gérer l’accès aux ressources Azure à l’aide de RBAC et le portail Azure](../../role-based-access-control/role-assignments-portal.md).
    - existe dans le répertoire actuellement associé à l’abonnement et dans le nouveau répertoire auquel vous souhaitez que l’abonnement soit associé. Pour plus d’informations sur l’accès à un autre répertoire, consultez [Comment les administrateurs Azure Active Directory ajoutent-ils des utilisateurs B2B Collaboration ?](../b2b/add-users-administrator.md)

1. Veillez à ne pas utiliser d’abonnement de fournisseur de services cloud (CSP) Azure (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), Microsoft interne ((MS-AZR-0015P), ou Microsoft Imagine (MS-AZR-0144P).
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Pour associer un abonnement existant à votre annuaire Azure AD

1. Connectez-vous et sélectionnez l’abonnement que vous voulez utiliser dans la [page Abonnements du portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Sélectionnez **Changer de répertoire**.

    ![Page Abonnements, avec l’option Changer de répertoire en surbrillance](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. Examinez les avertissements qui s’affichent, puis sélectionnez **Changer**.

    ![Page Changer de répertoire, montrant le nouveau répertoire](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Le répertoire est modifié pour l’abonnement et un message de réussite s’affiche.

    ![Message de réussite sur le changement de répertoire](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)
4. Utilisez le **sélecteur de répertoire** pour accéder à votre nouveau répertoire. Il peut prendre plusieurs heures pour tous les éléments s’affichent correctement. Si elle semble trop longtemps, veillez au **filtre d’abonnement Global** pour l’abonnement déplacé, s’assurer qu’il n’est pas simplement masqué.

    ![Page de sélecteur de répertoire, avec les informations de l’exemple](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

La modification du répertoire de l’abonnement est une opération de niveau de service. Elle n’affecte donc pas la propriété de facturation de l’abonnement. L’administrateur du compte peut toujours changer l’administrateur du service depuis le [centre du compte](https://account.azure.com/subscriptions). Pour supprimer le répertoire d’origine, vous devez transférer la propriété de facturation de l’abonnement à un nouvel administrateur du compte. Pour en savoir plus sur le transfert de la propriété de facturation, consultez [Transfert de la propriété d’un abonnement Azure à un autre compte](../../billing/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Valider les étapes de l’association
Une fois que vous associez un abonnement vers un autre répertoire, il peut y avoir des étapes supplémentaires que vous devez effectuer pour reprendre les opérations.

1. Si vous avez des coffres de clés, vous devez modifier l’ID de client de coffre de clés. Pour plus d’informations, consultez [modifier un ID de client de coffre de clés après un déplacement d’abonnement](../../key-vault/key-vault-subscription-move-fix.md).

2. Si vous avez inscrit une pile d’Azure à l’aide de cet abonnement, vous devez réenregistrer. Pour plus d’informations, consultez [inscrire Azure Stack avec Azure](/azure-stack/operator/azure-stack-registration).



## <a name="next-steps"></a>Étapes suivantes

- Pour créer un locataire Azure AD, voir [Accéder à Azure Active Directory pour créer un locataire](active-directory-access-create-new-tenant.md)

- Pour plus d’informations sur la façon dont l’accès aux ressources est contrôlé dans Microsoft Azure, voir [Présentation de l’accès aux ressources dans Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Pour en savoir plu sur l’affectation de rôles dans Azure AD, voir [Affecter des rôles de répertoire à des utilisateurs avec Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
