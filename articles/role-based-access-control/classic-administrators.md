---
title: Administrateurs d'abonnement classique Azure | Microsoft Docs
description: Décrit comment ajouter ou modifier les rôles Coadministrateur et Administrateur de services Azure, et comment voir l’Administrateur de compte.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/26/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 340717242d642475217bbe87fd96be66ec9b2e2d
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74554228"
---
# <a name="azure-classic-subscription-administrators"></a>Administrateurs d'abonnement classique Azure

Microsoft vous recommande de gérer l’accès aux ressources Azure à l’aide du contrôle d’accès en fonction du rôle (RBAC). Toutefois, si vous utilisez toujours le modèle de déploiement classique, vous devrez utiliser un rôle d’administrateur d'abonnement classique : Administrateur et coadministrateur de service. Pour plus d’informations, consultez [Déploiement Azure Resource Manager et déploiement Classic](../azure-resource-manager/resource-manager-deployment-model.md).

Cet article décrit comment ajouter ou modifier les rôles Coadministrateur et Administrateur de services, et comment voir l’Administrateur de compte.

## <a name="add-a-co-administrator"></a>Ajouter un coadministrateur

> [!TIP]
> Vous devez ajouter un coadministrateur uniquement si l’utilisateur a besoin de gérer les déploiements Azure Classic à l’aide du [module PowerShell de gestion des services Azure](https://docs.microsoft.com/powershell/module/servicemanagement/azure). Si l’utilisateur utilise uniquement le portail Azure pour gérer les ressources classiques, vous n’avez pas besoin d’ajouter l’administrateur classique pour l’utilisateur.

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur ou coadministrateur du service.

1. Ouvrez [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) et sélectionnez un abonnement.

    Les coadministrateurs peuvent uniquement être attribués à l’étendue de l’abonnement.

1. Cliquez sur **Contrôle d’accès (IAM)** .

1. Cliquez sur l’onglet **Administrateurs classiques**.

    ![Capture d’écran qui ouvre Administrateurs classiques](./media/classic-administrators/classic-administrators.png)

1. Cliquez sur **Ajouter** > **Ajouter un coadministrateur** pour ouvrir le volet Ajouter des coadministrateurs.

    Si l’option Ajouter un coadministrateur est désactivée, cela signifie que vous n’avez pas les autorisations requises.

1. Sélectionnez l’utilisateur que vous souhaitez ajouter, cliquez sur **Ajouter**.

    ![Capture d’écran d’ajout d’un coadministrateur](./media/classic-administrators/add-coadmin.png)

## <a name="add-a-guest-user-as-a-co-administrator"></a>Ajoutez un utilisateur invité en tant que coadministrateur

Pour ajouter un utilisateur invité en tant que coadministrateur, suivez les mêmes étapes que celles décrites dans la section précédente [Ajouter un coadministrateur](#add-a-co-administrator). L’utilisateur invité doit remplir les critères suivants :

- L’utilisateur invité doit être présent dans votre annuaire. Cela signifie que l’utilisateur a été invité dans votre annuaire et a accepté l’invitation.

Pour plus d’informations sur la manière d’ajouter un utilisateur invité à votre annuaire, consultez [Ajouter des utilisateurs Azure Active Directory B2B Collaboration dans le Portail Azure](../active-directory/b2b/add-users-administrator.md).

### <a name="differences-for-guest-users"></a>Différences pour les utilisateurs invités

Les utilisateurs invités qui ont été affectés au rôle de coadministrateur peuvent constater des différences par rapport aux utilisateurs membres avec le rôle coadministrateur. Examinez le scénario suivant :

- L’utilisateur A avec un compte professionnel ou scolaire Azure AD est administrateur de service pour un abonnement Azure.
- L’utilisateur B dispose d’un compte Microsoft.
- L’utilisateur A attribue le rôle de coadministrateur à l’utilisateur B.
- L’utilisateur B peut presque tout faire, mais il ne peut pas inscrire d’applications ni rechercher des utilisateurs dans l’annuaire Azure AD.

Vous vous attendiez sans doute à ce que l’utilisateur B puisse tout gérer. La raison de cette différence est que le compte Microsoft est ajouté à l’abonnement en tant qu’invité utilisateur et non en tant qu’utilisateur membre. Les utilisateurs invités disposent d’autorisations par défaut différentes dans Azure AD par rapport aux utilisateurs membres. Par exemple, les utilisateurs membres peuvent voir les autres utilisateurs dans Azure AD, ce que ne peuvent pas faire les utilisateurs invités. Les utilisateurs membres peuvent inscrire de nouveaux principaux de service dans Azure AD, ce que ne peuvent pas faire les utilisateurs invités.

Si un utilisateur invité doit pouvoir effectuer ces tâches, une solution possible consiste à lui affecter le rôle administrateur Azure AD spécifique dont l’utilisateur invité a besoin. Par exemple, dans le scénario précédent, vous pouvez attribuer le rôle [lecteur d’annuaire](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) pour pouvoir lire d’autres utilisateurs et affecter le rôle [développeur d’applications](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer) pour pouvoir créer des principaux de service. Pour plus d’informations sur les membres et les utilisateurs invités et leurs autorisations, consultez [Quelles sont les autorisations utilisateur par défaut dans Azure Active Directory ?](../active-directory/fundamentals/users-default-permissions.md). Pour plus d’informations sur l’octroi d’accès pour les utilisateurs invités, consultez [Gérer l’accès aux ressources Azure pour les utilisateurs invités externes à l’aide du contrôle d’accès en fonction du rôle](role-assignments-external-users.md).

Notez que les [rôles intégrés pour les ressources Azure](../role-based-access-control/built-in-roles.md) diffèrent de ceux des [rôles d’administrateur Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md). Les rôles intégrés n’accordent aucun accès à Azure AD. Pour plus d’informations, consultez [Comprendre les différents rôles](../role-based-access-control/rbac-and-directory-admin-roles.md).

Pour plus d’informations comparant les utilisateurs membres et les utilisateurs invités, consultez [Quelles sont les autorisations d’utilisateur par défaut dans Azure Active Directory ?](../active-directory/fundamentals/users-default-permissions.md).

## <a name="remove-a-co-administrator"></a>Supprimer un coadministrateur

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur ou coadministrateur du service.

1. Ouvrez [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) et sélectionnez un abonnement.

1. Cliquez sur **Contrôle d’accès (IAM)** .

1. Cliquez sur l’onglet **Administrateurs classiques**.

1. Ajoutez une coche en regard du Coadministrateur que vous souhaitez supprimer.

1. Cliquez sur **Supprimer**.

1. Dans la boîte de message qui s’affiche, cliquez sur **Oui**.

    ![Capture d’écran de suppression de coadministrateur](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Changement d’administrateur de services

Seul l’administrateur de compte peut modifier l’administrateur de services fédérés d’un abonnement. Par défaut, quand vous vous inscrivez à un abonnement Azure, l’Administrateur de services est le même que l’Administrateur de compte. L’utilisateur avec le rôle Administrateur de compte n’a pas accès au portail Azure. L’utilisateur avec le rôle Administrateur de service a un accès complet au portail Azure. Si l’Administrateur de compte et l’Administrateur de service sont le même utilisateur et que vous remplacez l’Administrateur de service par un autre utilisateur, alors l’Administrateur de compte perd l’accès au portail Azure. Toutefois, l’administrateur de compte peut toujours utiliser le centre des comptes pour se réaffecter le rôle d’administrateur de services fédérés à lui-même.

Il existe deux façons de modifier l’Administrateur de service. Vous pouvez modifier dans le **portail Azure** ou le **Centre des comptes**.

### <a name="azure-portal"></a>Portail Azure

1. Vérifiez que votre scénario est pris en charge en vérifiant les limites de modification des administrateurs de services fédérés.

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur de compte.

1. Ouvrez [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) et sélectionnez un abonnement.

1. Cliquez sur **Propriétés**.

    ![Capture d’écran montrant l’Administrateur de compte](./media/classic-administrators/account-admin.png)

1. En haut, cliquez sur **Admin de service** pour ouvrir le volet Administrateur de service.

    Si le bouton Administrateur de service est désactivé, vous n’avez pas les autorisations requises. Seul l’utilisateur qui est l’Administrateur de compte peut modifier l’Administrateur de service.

1. Sélectionnez un nouvel Administrateur de service, puis cliquez **Enregistrer**.

### <a name="account-center"></a>Centre des comptes

1. Vérifiez que votre scénario est pris en charge en vérifiant les limites de modification des administrateurs de services fédérés.

1. Connectez-vous au [Centre des comptes](https://account.windowsazure.com/subscriptions) en tant qu’administrateur de compte.

1. Cliquez sur un abonnement.

1. Sur le côté droit, cliquez sur **Modifier les détails de l’abonnement**.

    ![Capture d’écran montrant le bouton Modifier l’abonnement dans le Centre des comptes](./media/classic-administrators/editsub.png)

1. Dans la zone **ADMINISTRATEUR DE SERVICES** , entrez l’adresse de messagerie du nouvel administrateur de services fédérés.

    ![Capture d’écran montrant la zone de modification de l’adresse e-mail de l’administrateur de services fédérés](./media/classic-administrators/change-service-admin.png)

1. Cliquez sur la coche pour enregistrer la modification.

### <a name="limitations-for-changing-the-service-administrator"></a>Limites de modification de l’Administrateur de service

Il ne peut y avoir qu’un seul administrateur de service par abonnement Azure. La modification de l’administrateur de service se comporte différemment selon que l’administrateur de compte est un compte Microsoft ou qu’il s’agit d’un compte Azure AD (professionnel ou scolaire).

| Compte d’Administrateur de compte | Est-il possible de modifier l’administrateur de service en un autre compte Microsoft ? | Est-il possible de modifier l’administrateur de service en un compte Azure AD dans le même annuaire ? | Est-il possible de modifier l’administrateur de service en un compte Azure AD dans un annuaire différent ? |
| --- | --- | --- | --- |
| Compte Microsoft | OUI | Non | Non |
| Compte Azure AD | OUI | OUI | Non |

Si l’administrateur de compte est un compte Azure AD, vous pouvez modifier l’administrateur de service en un compte Azure AD dans le même annuaire, mais pas dans un autre. Par exemple, abby@contoso.com peut modifier l’administrateur de service en bob@contoso.com, mais ne pas en john@notcontoso.com, sauf si john@notcontoso.com a une présence dans l’annuaire contoso.com.

Pour plus d’informations sur les comptes Microsoft et Azure AD, voir [Qu’est-ce qu’Azure Active Directory ?](../active-directory/fundamentals/active-directory-whatis.md).

## <a name="view-the-account-administrator"></a>Voir l’administrateur de compte

L’Administrateur de compte est l’utilisateur qui a initialement souscrit l’abonnement Azure et qui est responsable en tant que propriétaire de facturation de l’abonnement. Pour modifier l’administrateur de compte d’un abonnement, consultez [Transférer la propriété d’un abonnement Azure à un autre compte](../billing/billing-subscription-transfer.md).

Pour voir l’Administrateur de compte, procédez comme suit.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Ouvrez [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) et sélectionnez un abonnement.

1. Cliquez sur **Propriétés**.

    L’administrateur de compte de l’abonnement s’affiche dans la zone **Administrateur de compte**.

    ![Capture d’écran montrant l’Administrateur de compte](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Étapes suivantes

* [Comprendre les différents rôles dans Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Gérer l’accès aux ressources Azure à l’aide du contrôle RBAC et du portail Azure](../role-based-access-control/role-assignments-portal.md)
* [Ajouter ou changer des administrateurs d’abonnements Azure](../billing/billing-add-change-azure-subscription-administrator.md)
