---
title: Ajout ou modification de rôles d’abonnement administrateur Azure | Microsoft Docs
description: Décrit comment ajouter ou modifier un coadministrateur, administrateur de services et administrateur de compte Azure
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/14/2018
ms.author: cwatson
ms.openlocfilehash: 821d263856f21897915ba7954487b4d029cc4ed0
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47395243"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Ajout ou modification des administrateurs d’abonnements Azure

Pour gérer l’accès aux ressources Azure, vous devez disposer du rôle d’administrateur approprié. Cet article décrit comment ajouter ou modifier le rôle d’administrateur pour un utilisateur au niveau de l’abonnement.

> [!div class="nextstepaction"]
> [Aidez-nous à améliorer les documents de facturation Azure](https://go.microsoft.com/fwlink/p/?linkid=2010091)

## <a name="what-administrator-role-do-i-use"></a>Quel rôle d’administrateur utiliser ?

Azure dispose de plusieurs rôles différents. Pour gérer l’accès aux ressources, vous pouvez utiliser les rôles d’administrateur d’abonnement classiques, par exemple administrateur de services fédérés et coadministrateur, ou un système d’autorisation plus récent appelé Contrôle d’accès en fonction du rôle (RBAC). Pour assurer un meilleur contrôle et simplifier la gestion des accès, nous vous recommandons d’utiliser RBAC pour tous vos besoins de gestion des accès. Si cela est possible, nous vous recommandons de reconfigurer les stratégies d’accès existantes à l’aide de RBAC. Pour plus d’informations, consultez [Qu’est-ce que le contrôle d’accès en fonction du rôle (RBAC) ?](../role-based-access-control/overview.md) et [Comprendre les différents rôles dans Azure](../role-based-access-control/rbac-and-directory-admin-roles.md).

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-for-a-subscription-in-azure-portal"></a>Ajouter un propriétaire RBAC pour un abonnement dans le portail Azure 

Pour ajouter un utilisateur en tant qu’administrateur pour un abonnement Azure, assignez-leur le rôle de [propriétaire](../role-based-access-control/built-in-roles.md#owner) (rôle RBAC) au niveau de l’abonnement. Le rôle Propriétaire a la possibilité de gérer les ressources de l’abonnement que vous avez affecté sans avoir de privilèges d’accès à d’autres abonnements.

1. Visitez [**Abonnements** dans le portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Sélectionnez l’abonnement auquel octroyer un accès.
3. Sélectionnez **Ajouter**.
   (Si le bouton Ajouter manque, vous n’êtes pas autorisé à ajouter des autorisations.)
4. Sélectionnez **Contrôle d’accès (IAM)** dans la liste.
5. Dans la zone **Rôle**, sélectionnez **Propriétaire**. 
6. Dans la zone **Attribuer l’accès à**, sélectionnez **Utilisateur, groupe ou application Azure AD**. 
7. Dans la zone **Sélectionner**, tapez l’adresse e-mail de l’utilisateur à ajouter comme propriétaire. Sélectionnez l’utilisateur, puis **Enregistrer**.

    ![Capture d’écran montrant le rôle Propriétaire sélectionné](./media/billing-add-change-azure-subscription-administrator/add-role.png)

L’utilisateur obtient ainsi un accès total à toutes les ressources, ainsi que le droit de déléguer l’accès à d’autres personnes. Pour accorder l’accès à une portée différente, comme un groupe de ressources, ouvrez le panneau **Contrôle d’accès (IAM)** pour cette portée.

## <a name="add-or-change-co-administrator"></a>Ajouter ou modifier un coadministrateur

Seul un [propriétaire](../role-based-access-control/built-in-roles.md#owner) peut être ajouté en tant que coadministrateur. Les autres utilisateurs dont les rôles sont [Contributeur](../role-based-access-control/built-in-roles.md#contributor) et [Lecteur](../role-based-access-control/built-in-roles.md#reader) ne peuvent pas être ajoutés en tant que coadministrateurs.

> [!TIP]
> Vous devez ajouter le Propriétaire comme coadministrateur uniquement si l’utilisateur a besoin de gérer les déploiements Azure Classic. Nous vous recommandons d’utiliser RBAC pour toutes les autres opérations.

1. Si vous ne l’avez pas déjà fait, ajoutez un utilisateur en tant que propriétaire en suivant les instructions ci-dessus.
2. **Cliquez avec le bouton droit** sur l’utilisateur Propriétaire que vous venez d’ajouter, puis sélectionnez **Ajouter comme coadministrateur**. Si vous ne voyez pas l’option **Ajouter comme coadministrateur**, actualisez la page ou essayez avec un autre navigateur Internet. 

    ![Capture d’écran d’ajout d’un coadministrateur](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    Pour supprimer le privilège de coadministrateur, **cliquez avec le bouton droit** sur l’utilisateur Coadministrateur, puis sélectionnez **Supprimer le coadministrateur**.

    ![Capture d’écran de suppression de coadministrateur](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>Modifier l’Administrateur de services fédérés d’un abonnement Azure

Seul l’Administrateur de compte peut modifier l’Administrateur de services fédérés d’un abonnement. Par défaut, au moment de l’inscription, l’Administrateur de services est le même que l’Administrateur de compte. Si le rôle d’Administrateur de services fédérés est affecté à un autre utilisateur, l’Administrateur de compte perd l’accès au portail Azure. Toutefois, l’Administrateur de compte peut toujours utiliser le centre des comptes pour se réaffecter le rôle d’Administrateur de services fédérés à lui-même.

1. Vérifiez que votre scénario est pris en charge en vérifiant les [limites de modification des Administrateurs de services fédérés](#limits).
1. Connectez-vous au [Centre des comptes](https://account.windowsazure.com/subscriptions) en tant qu’Administrateur de compte.
1. Sélectionnez un abonnement.
1. Sur le côté droit, sélectionnez **Modifier les détails de l’abonnement**.

    ![Capture d’écran montrant le bouton Modifier l’abonnement dans le Centre des comptes](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. Dans la zone **ADMINISTRATEUR DE SERVICES**, entrez l’adresse de messagerie du nouvel Administrateur de services fédérés.

    ![Capture d’écran montrant la zone de modification de l’adresse e-mail de l’administrateur de services fédérés](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>Limites de modification des Administrateurs de services fédérés

* Chaque abonnement est associé à un annuaire Azure AD. Pour identifier l’annuaire auquel est associé l’abonnement, accédez à [**Abonnements**](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), puis sélectionnez un abonnement pour voir l’annuaire.
* Si vous êtes connecté avec un compte professionnel ou scolaire, vous pouvez ajouter d’autres comptes à votre organisation en tant qu’Administrateur de services fédérés. Par exemple, abby@contoso.com peut ajouter bob@contoso.com en tant qu’Administrateur de services fédérés, mais ne peut pas ajouter john@notcontoso.com, sauf si john@notcontoso.com est présent dans l’annuaire de contoso.com. Les utilisateurs connectés avec des comptes professionnels ou scolaires peuvent continuer à ajouter des utilisateurs de comptes Microsoft en tant qu’Administrateur de services fédérés.

  | Méthode de connexion | Ajouter l’utilisateur de Microsoft Account en tant qu’administrateur de Service ? | Ajouter le compte professionnel ou scolaire de la même organisation comme Administrateur de services fédérés ? | Ajouter le compte professionnel ou scolaire de la même organisation comme Administrateur de services fédérés ? |
  | --- | --- | --- | --- |
  |  Compte Microsoft |Oui |Non  |Non  |
  |  Compte professionnel ou scolaire |Oui |Oui |Non  |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>Modifier l’Administrateur de compte d’un abonnement Azure

L’Administrateur de compte est l’utilisateur qui a initialement souscrit l’abonnement Azure et qui est responsable en tant que propriétaire de facturation de l’abonnement. Pour modifier l’Administrateur de compte d’un abonnement, consultez [Transférer la propriété d’un abonnement Azure à un autre compte](billing-subscription-transfer.md).

<a name="check-the-account-administrator-of-the-subscription"></a>

**Besoin d’identifier l’administrateur de compte ?** Procédez comme suit :

1. Visitez [**Abonnements** dans le portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Sélectionnez l’abonnement que vous souhaitez vérifier, puis regardez sous **Paramètres**.
1. Sélectionner **Propriétés**. L’administrateur de compte de l’abonnement s’affiche dans la zone **Administrateur de compte**.  

## <a name="learn-more-about-resource-access-control-and-active-directory"></a>En savoir plus sur le contrôle d’accès aux ressources et Active Directory

* Pour en savoir plus sur RBAC, consultez [Qu’est-ce que le contrôle d’accès en fonction du rôle (RBAC) ?](../role-based-access-control/overview.md)
* Pour en savoir plus sur tous les rôles dans Azure, consultez [Comprendre les différents rôles dans Azure](../role-based-access-control/rbac-and-directory-admin-roles.md).
* Pour plus d’informations sur Azure Active Directory, consultez les pages [Association des abonnements Azure avec Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md) et [Affecter des rôles Administrateur dans Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.

Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
