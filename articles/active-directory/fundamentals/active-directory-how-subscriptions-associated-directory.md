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
ms.date: 09/13/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a116355c8140d30f8297cde067a82f37f72e02a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56165856"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Associer ou ajouter un abonnement Azure à votre locataire Azure Active Directory
Votre abonnement Azure possède une relation d’approbation avec Azure Active Directory (Azure AD), ce qui signifie que l’abonnement fait confiance à Azure AD pour authentifier les utilisateurs, les services et les périphériques. Plusieurs abonnements peuvent approuver le même répertoire Azure AD, mais chaque abonnement n’approuve qu’un seul répertoire.

En cas d’expiration d’un abonnement, vous perdez l’accès aux autres ressources associées à cet abonnement. Toutefois, le répertoire Azure AD demeure dans Azure, vous permettant d’associer et de gérer le répertoire avec un abonnement Azure différent.

Tous vos utilisateurs ont un seul répertoire « accueil » pour authentification. Toutefois, vos utilisateurs peuvent aussi être des invités d’autres répertoires. Vous pouvez voir les répertoires accueil et invité pour chaque utilisateur dans Azure AD.

>[!Important]
>Tous les utilisateurs [RBAC (contrôle d’accès en fonction du rôle)](../../role-based-access-control/role-assignments-portal.md) disposant d’un accès et tous les administrateurs d’abonnement perdent leur accès si le répertoire d’abonnement est modifié. De plus, si vous disposez de coffres de clés, ils seront également affectés par le déplacement de l’abonnement. Pour y remédier, vous devez [modifier l’ID du locataire du coffre de clés](../../key-vault/key-vault-subscription-move-fix.md) avant la reprise des opérations.


## <a name="before-you-begin"></a>Avant de commencer
Avant de pouvoir associer ou ajouter votre abonnement, vous devez effectuer les tâches suivantes :

- Connectez-vous avec un compte qui :
    - dispose d’un accès **propriétaire RBAC** à l’abonnement.

    - existe dans le répertoire actuellement associé à l’abonnement et dans le nouveau répertoire auquel vous souhaitez que l’abonnement soit associé. Pour plus d’informations sur l’accès à un autre répertoire, consultez [Comment les administrateurs Azure Active Directory ajoutent-ils des utilisateurs B2B Collaboration ?](../b2b/add-users-administrator.md)

- Veillez à ne pas utiliser d’abonnement de fournisseur de services cloud (CSP) Azure (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), Microsoft interne ((MS-AZR-0015P), ou Microsoft Imagine (MS-AZR-0144P).
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Pour associer un abonnement existant à votre annuaire Azure AD
1. Connectez-vous et sélectionnez l’abonnement que vous voulez utiliser dans la [page Abonnements du portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Sélectionnez **Changer de répertoire**.

    ![Page Abonnements, avec l’option Changer de répertoire en surbrillance](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. Examinez les avertissements qui s’affichent, puis sélectionnez **Changer**.

    ![Page Changer de répertoire, montrant le nouveau répertoire](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Le répertoire est modifié pour l’abonnement et un message de réussite s’affiche.

    ![Message de réussite](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)    

4. Utilisez le sélecteur de répertoire pour passer à votre nouveau répertoire. 10 minutes peuvent être nécessaire pour tout afficher correctement.

    ![Page Sélecteur de répertoire](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

La modification du répertoire de l’abonnement est une opération de niveau de service. Elle n’affecte donc pas la propriété de facturation de l’abonnement. L’administrateur du compte peut toujours changer l’administrateur du service depuis le [centre du compte](https://account.azure.com/subscriptions). Pour supprimer le répertoire d’origine, vous devez transférer la propriété de facturation de l’abonnement à un nouvel administrateur du compte. Pour en savoir plus sur le transfert de la propriété de facturation, consultez [Transfert de la propriété d’un abonnement Azure à un autre compte](../../billing/billing-subscription-transfer.md). 

## <a name="next-steps"></a>Étapes suivantes

- Pour créer un locataire Azure AD, voir [Accéder à Azure Active Directory pour créer un locataire](active-directory-access-create-new-tenant.md)

- Pour plus d’informations sur la façon dont l’accès aux ressources est contrôlé dans Microsoft Azure, voir [Présentation de l’accès aux ressources dans Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Pour en savoir plu sur l’affectation de rôles dans Azure AD, voir [Affecter des rôles de répertoire à des utilisateurs avec Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
