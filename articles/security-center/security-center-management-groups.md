---
title: Gagner en visibilité au niveau locataire dans Azure Security Center | Microsoft Docs
description: Cet article explique comment gérer votre position de sécurité à grande échelle en appliquant des stratégies à tous les abonnements liés à votre locataire Azure Active Directory.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 13cbc2e9451221fef951eb6fac4c6b2772275122
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99556419"
---
# <a name="organize-management-groups-subscriptions-and-tenant-wide-visibility"></a>Organiser les groupes d’administration, les abonnements et la visibilité à l’échelle du locataire

Cet article explique comment gérer la position de sécurité de votre organisation à grande échelle en appliquant des stratégies de sécurité à tous les abonnements Azure liés à votre locataire Azure Active Directory.

Pour avoir une visibilité de la sécurité de tous les abonnements inscrits dans le locataire Azure AD, un rôle Azure doté des autorisations de lecture suffisantes doit être attribué au groupe d’administration racine.


## <a name="organize-your-subscriptions-into-management-groups"></a>Organiser vos abonnements dans des groupes d’administration

### <a name="introduction-to-management-groups"></a>Introduction aux groupes d’administration

Avec les groupes d’administration Azure, vous avez la possibilité de gérer efficacement non seulement l’accès, les stratégies et les rapports de groupes d’abonnements, mais aussi l’intégralité du domaine Azure en intervenant sur le groupe d’administration racine. Vous pouvez organiser les abonnements en groupes d’administration et y appliquer vos stratégies de gouvernance. Tous les abonnements d’un groupe d’administration héritent automatiquement des stratégies appliquées à ce groupe d’administration. 

Chaque locataire Azure AD reçoit un seul groupe d’administration de niveau supérieur appelé **groupe d’administration racine**. Ce groupe d’administration racine est intégré à la hiérarchie et contient tous les groupes d’administration et abonnements. Il permet d’appliquer des stratégies globales et des attributions de rôles Azure au niveau du répertoire. 

Le groupe d’administration racine est créé automatiquement quand vous effectuez l’une des actions suivantes : 
- Ouvrez **Groupes d’administration** dans le [portail Azure](https://portal.azure.com).
- Créer un groupe d’administration avec un appel d’API.
- Créer un groupe d’administration avec PowerShell. Pour des instructions sur PowerShell, consultez [Créer des groupes d’administration pour gérer les ressources et l’organisation](../governance/management-groups/create-management-group-portal.md).

Les groupes d’administration ne sont pas obligatoires pour intégrer Security Center, mais nous vous recommandons vivement d’en créer au moins un pour que le groupe d’administration racine soit créé. Une fois que le groupe est créé, tous les abonnements sous votre locataire Azure AD y sont liés. 


Pour une présentation détaillée des groupes d’administration, consultez l’article [Organiser vos ressources avec des groupes d’administration Azure](../governance/management-groups/overview.md).

### <a name="view-and-create-management-groups-in-the-azure-portal"></a>Afficher et créer des groupes d’administration dans le portail Azure

1. À partir du [portail Azure](https://portal.azure.com), utilisez la zone de recherche dans la barre supérieure pour rechercher et ouvrir **Groupes d'administration**.

    :::image type="content" source="./media/security-center-management-groups/open-management-groups-service.png" alt-text="Accès à vos groupes d'administration":::

    La liste de vos groupes d’administration s’affiche.

1. Pour créer un groupe d’administration, sélectionnez **Ajouter un groupe d’administration**, entrez les informations appropriées, puis sélectionnez **Enregistrer**.

    :::image type="content" source="media/security-center-management-groups/add-management-group.png" alt-text="Ajout d’un groupe d’administration à Azure":::

    - L’**ID du groupe d’administration** est l’identificateur unique de l’annuaire utilisé pour envoyer des commandes sur ce groupe d’administration. Cet identificateur n’est pas modifiable après sa création car il est utilisé dans tout le système Azure pour identifier ce groupe. 
    - Le champ du nom d’affichage correspond au nom qui s’affiche dans le portail Azure. Un nom d’affichage distinct est un champ facultatif lors de la création du groupe d’administration. Il peut être modifié à tout moment.  


### <a name="add-subscriptions-to-a-management-group"></a>Ajouter des abonnements à un groupe d’administration
Vous pouvez ajouter des abonnements au groupe d’administration que vous avez créé.

1. Sous **Groupes d’administration**, sélectionnez le groupe d’administration pour votre abonnement.

    :::image type="content" source="./media/security-center-management-groups/management-group-subscriptions.png" alt-text="Sélectionner un groupe d’administration pour votre abonnement":::

1. Lorsque la page du groupe s’ouvre, sélectionnez **Détails**

    :::image type="content" source="./media/security-center-management-groups/management-group-details-page.png" alt-text="Ouverture de la page des détails d’un groupe d’administration":::

1. Dans la page des détails du groupe, sélectionnez **Ajouter un abonnement**, choisissez vos abonnements, puis sélectionnez **Enregistrer**. Répétez les étapes pour tous les abonnements concernés.

    :::image type="content" source="./media/security-center-management-groups/management-group-add-subscriptions.png" alt-text="Ajout d’un abonnement à un groupe d’administration":::
   > [!IMPORTANT]
   > Les groupes d’administration peuvent contenir à la fois des abonnements et des groupes d’administration enfants. Lorsque vous attribuez un rôle Azure à un utilisateur dans le groupe d’administration parent, l’accès est hérité par les abonnements du groupe d’administration enfant. Les stratégies définies au niveau du groupe d’administration parent sont aussi héritées par les enfants. 


## <a name="grant-tenant-wide-permissions-to-yourself"></a>Accorder à vous-même des autorisations à l’échelle du locataire

Si un utilisateur ayant le rôle Azure Active Directory (AD) d’**administrateur général** peut avoir des responsabilités à l’échelle du locataire, il peut ne pas disposer des autorisations Azure lui permettant de consulter les informations à l’échelle de l’organisation dans Azure Security Center. L’élévation d’autorisations est requise, car les attributions de rôles Azure AD n’accordent pas l’accès aux ressources Azure. 

> [!TIP]
> En savoir plus sur l’élévation d’autorisations pour le rôle d’administrateur général dans [Élever l’accès pour gérer tous les abonnements et groupes d’administration Azure](../role-based-access-control/elevate-access-global-admin.md).

Pour vous attribuer des autorisations au niveau du locataire :

1. Si votre organisation gère l’accès aux ressources avec [Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md) ou tout autre outil PIM, le rôle d’administrateur général doit être actif pour l’utilisateur qui effectue la procédure ci-dessous.

1. En tant qu’utilisateur Administrateur général sans affectation au groupe d’administration racine du locataire, ouvrez la page **Vue d’ensemble** de Security Center, puis sélectionnez le lien **Visibilité à l’échelle du locataire** dans la bannière. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-banner.png" alt-text="Activer des autorisations au niveau du locataire dans Azure Security Center":::

1. Sélectionnez le nouveau rôle Azure à attribuer. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-form.png" alt-text="Formulaire de définition des autorisations au niveau du locataire à attribuer à votre utilisateur":::

    > [!TIP]
    > En règle générale, le rôle Administrateur de sécurité est nécessaire pour appliquer des stratégies au niveau racine, tandis que Lecteur de Sécurité est suffisant pour fournir une visibilité au niveau locataire. Pour plus d’informations sur les autorisations accordées par ces rôles, consultez la [description du rôle intégré Administrateur de sécurité](../role-based-access-control/built-in-roles.md#security-admin) ou la [description du rôle intégré de Lecteur Sécurité](../role-based-access-control/built-in-roles.md#security-reader).
    >
    > Pour connaître les différences entre ces rôles spécifiques à Security Center, consultez le tableau présenté dans [Rôles et actions autorisées](security-center-permissions.md#roles-and-allowed-actions).

    La vue à l’échelle de l’organisation est obtenue en accordant des rôles au niveau du groupe d’administration racine du locataire.  

1. Déconnectez-vous du portail Azure, puis reconnectez-vous.

1. Une fois que vous disposez d’un accès élevé, ouvrez ou actualisez Azure Security Center pour vérifier que vous voyez tous les abonnements sous votre locataire Azure AD. 


## <a name="request-tenant-wide-permissions-when-yours-are-insufficient"></a>Demander des autorisations à l’échelle du locataire quand les vôtres sont insuffisantes

Si vous vous connectez à Security Center et que vous voyez une bannière indiquant que votre affichage est limité, vous pouvez cliquer dessus pour envoyer une demande à l’administrateur général de votre organisation. Dans la demande, vous pouvez inclure le rôle que vous souhaitez obtenir, et l’administrateur général prendra une décision sur le rôle à accorder. 

Il appartient à l’administrateur général d’accepter ou de rejeter ces demandes. 

> [!IMPORTANT]
> Vous ne pouvez envoyer qu’une demande tous les sept jours.

Pour demander des autorisations à privilège élevé à votre administrateur général :

1. Dans le portail Azure, ouvrez Azure Security Center.

1. Si la bannière « Vous voyez des informations limitées. » s’affiche, Sélectionnez-le.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Bannière informant l’utilisateur qu’il peut demander des autorisations à l’échelle du locataire.":::

1. Dans le formulaire détaillé de la demande, sélectionnez le rôle souhaité et la raison pour laquelle vous avez besoin de ces autorisations.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-details.png" alt-text="Page de détails pour la demande d’autorisations à l’échelle du locataire auprès de votre administrateur général Azure":::

1. Sélectionnez **Demander l’accès**.

    Un e-mail est envoyé à l’administrateur général. L’e-mail contient un lien vers Security Center où il peut approuver ou rejeter la demande.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-email.png" alt-text="E-mail envoyé à l’administrateur général pour de nouvelles autorisations":::

    Une fois que l’administrateur général a sélectionné **Examiner la demande** et a terminé le processus, la décision est envoyée par e-mail à l’utilisateur qui effectue la demande. 

## <a name="assign-azure-roles-to-other-users"></a>Attribuer des rôles Azure à d’autres utilisateurs

### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>Attribuer des rôles Azure aux utilisateurs via le portail Azure : 
1. Connectez-vous au [portail Azure](https://portal.azure.com). 
1. Pour voir les groupes d’administration, sélectionnez **Tous les services** sous le menu principal d’Azure puis sélectionnez **Groupes d'administration**.
1.  Sélectionnez un groupe d’administration, puis **Détails**.

    :::image type="content" source="./media/security-center-management-groups/management-group-details.PNG" alt-text="Capture d’écran Détails des groupes d'administration":::

1. Sélectionnez **Contrôle d’accès (IAM)** , puis **Attributions de rôles**.
1. Sélectionnez **Ajouter une attribution de rôle**.
1. Sélectionnez le rôle à affecter et l’utilisateur, puis **Enregistrer**.  
   
   ![Capture d’écran de l’ajout du rôle Lecteur Sécurité](./media/security-center-management-groups/asc-security-reader.png)

### <a name="assign-azure-roles-to-users-with-powershell"></a>Attribuer des rôles Azure aux utilisateurs à l’aide de PowerShell : 
1. Installez [Azure PowerShell](/powershell/azure/install-az-ps).
2. Exécutez les commandes suivantes : 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. Quand vous y êtes invité, connectez-vous avec des informations d’identification d’administrateur général. 

    ![Capture d’écran de l’invite de connexion](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Accordez les autorisations du rôle de lecteur en exécutant la commande suivante :

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Pour supprimer le rôle, utilisez la commande suivante : 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

## <a name="remove-elevated-access"></a>Supprimer l’accès élevé 

Une fois que les rôles Azure ont été attribués aux utilisateurs, l’administrateur client doit se supprimer du rôle d’administrateur des accès utilisateur.

1. Connectez-vous au [portail Azure](https://portal.azure.com) ou au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com).

2. Dans la liste de navigation, sélectionnez **Azure Active Directory**, puis **Propriétés**.

3. Sous **Gestion des accès aux ressources Azure**, définissez le commutateur sur **Non**.

4. Pour enregistrer votre paramètre, sélectionnez **Enregistrer**.



## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à gagner en visibilité au niveau locataire dans Azure Security Center. Pour plus d’informations, consultez :

- [Autorisations dans Azure Security Center](security-center-permissions.md)