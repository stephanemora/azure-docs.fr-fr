---
title: Organiser des abonnements en groupes d’administration et attribuer des rôles à des utilisateurs pour Azure Security Center
description: Découvrez comment organiser vos abonnements Azure en groupes d’administration dans Azure Security Center, ainsi qu’attribuer des rôles à des utilisateurs au sein de votre organisation
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: 3508d508a19d6ce7fba4f3ef3a4fa545a58a167d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102099384"
---
# <a name="organize-subscriptions-into-management-groups-and-assign-roles-to-users"></a>Organiser des abonnements en groupes d’administration et attribuer des rôles à des utilisateurs

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
Cet article vous a montré comment organiser des abonnements en groupes d’administration et attribuer des rôles à des utilisateurs. Pour plus d’informations, consultez :

- [Autorisations dans Azure Security Center](security-center-permissions.md)