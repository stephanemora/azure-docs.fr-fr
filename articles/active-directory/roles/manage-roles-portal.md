---
title: Attribuer des rôles Azure AD aux utilisateurs – Azure Active Directory
description: Découvrez comment accorder l’accès à des utilisateurs dans Azure Active Directory en attribuant des rôles Azure AD.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 07/15/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 311478f52de547a99354a6effad376145166704b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531486"
---
# <a name="assign-azure-ad-roles-to-users"></a>Attribuer des rôles Azure AD aux utilisateurs

Pour accorder l’accès à des utilisateurs dans Azure Active Directory (Azure AD), vous attribuez des rôles Azure AD. Un rôle est une collection d’autorisations. Cet article explique comment attribuer des rôles Azure AD à l’aide du Portail Azure et de PowerShell.

## <a name="prerequisites"></a>Prérequis

- Administrateur de rôle privilégié ou Administrateur général
- Licence Azure AD Premium P2 avec Privileged Identity Management (PIM)
- Module AzureADPreview avec PowerShell
- Consentement administrateur (avec l’Afficheur Graph pour l’API Microsoft Graph)

Pour plus d’informations, consultez [Prérequis pour utiliser PowerShell ou de l’Afficheur Graph](prerequisites.md).

## <a name="azure-portal"></a>Portail Azure

Effectuez les étapes indiquant comment attribuer des rôles Azure AD à l’aide du Portail Azure. Votre expérience sera différente selon que vous avez activé ou non [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md).

### <a name="assign-a-role"></a>Attribuer un rôle

1. Connectez-vous au [Portail Azure](https://portal.azure.com) ou au [Centre d’administration Azure AD](https://aad.portal.azure.com).

1. Sélectionnez **Azure Active Directory** > **Rôles et administrateurs** pour afficher la liste de tous les rôles disponibles.

    ![Page Rôles et administrateurs dans Azure Active Directory.](./media/manage-roles-portal/roles-and-administrators.png)

1. Sélectionnez un rôle pour afficher ses attributions.

    Pour identifier plus facilement le rôle dont vous avez besoin, utilisez **Ajouter des filtres** pour filtrer les rôles.

1. Sélectionnez **Ajouter des attributions**, puis sélectionnez les utilisateurs que vous souhaitez affecter à ce rôle.

    Si vous voyez un nom différent de celui de l’image suivante, vous pouvez activer PIM. Voir la section suivante.

    ![Volet Ajouter des attributions pour le rôle sélectionné.](./media/manage-roles-portal/add-assignments.png)

1. Sélectionnez **Ajouter** pour attribuer le rôle.

### <a name="assign-a-role-using-pim"></a>Affecter un rôle à l’aide de PIM

Si [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) est activé, vous disposez de fonctionnalités d’attribution de rôle supplémentaires. Par exemple, vous pouvez rendre un utilisateur éligible à un rôle ou définir la durée. Lorsque PIM est activé, vous disposez de deux méthodes pour attribuer des rôles à l’aide du Portail Azure. Vous pouvez utiliser la page Rôles et administrateurs ou l’expérience PIM. Les deux utilisent le même service PIM.

Pour affecter des rôles à l’aide de la page [Rôles et administrateurs](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators), procédez comme suit. Si vous souhaitez attribuer des rôles à partir de la page [Privileged Identity Management](https://portal.azure.com/#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart), consultez [Attribuer des rôles Azure AD dans Privileged Identity Management](../privileged-identity-management/pim-how-to-add-role-to-user.md).

1. Connectez-vous au [Portail Azure](https://portal.azure.com) ou au [Centre d’administration Azure AD](https://aad.portal.azure.com).

1. Sélectionnez **Azure Active Directory** > **Rôles et administrateurs** pour afficher la liste de tous les rôles disponibles.

    ![Page Rôles et administrateurs dans Azure Active Directory lorsque PIM est activé.](./media/manage-roles-portal/roles-and-administrators.png)

1. Sélectionnez un rôle pour afficher ses attributions de rôles éligibles, actives et expirées.

    Pour identifier plus facilement le rôle dont vous avez besoin, utilisez **Ajouter des filtres** pour filtrer les rôles.

1. Sélectionnez **Ajouter des affectations**.

1. Sélectionnez **Aucun membre sélectionné**, puis sélectionnez les utilisateurs que vous souhaitez attribuer à ce rôle.

    ![Ajoutez la page Affectations et sélectionnez un volet de membre avec PIM activé.](./media/manage-roles-portal/add-assignments-pim.png)

1. Sélectionnez **Suivant**.

1. Dans l’onglet **Paramètre**, indiquez si vous souhaitez que cette attribution de rôle soit **Éligible** ou **Active**.

    Une attribution de rôle éligible signifie que l’utilisateur doit effectuer une ou plusieurs actions pour utiliser le rôle. Une attribution de rôle active signifie que l’utilisateur n’a pas à effectuer d’action pour utiliser le rôle. Pour plus d’informations sur la signification de ces paramètres, consultez [Terminologie PIM](../privileged-identity-management/pim-configure.md#terminology).

    ![Page Ajouter des affectations et onglet Paramètre alors que PIM est activé.](./media/manage-roles-portal/add-assignments-pim-setting.png)

1. Utilisez les options restantes pour définir la durée de l’affectation.

1. Sélectionnez **Attribuer** pour attribuer le rôle.

## <a name="powershell"></a>PowerShell

Effectuez les étapes suivantes pour attribuer des rôles Azure AD à l’aide de PowerShell.

### <a name="setup"></a>Installation

1. Ouvrez une fenêtre PowerShell et utilisez [Import-Module](/powershell/module/microsoft.powershell.core/import-module) pour importer le module AzureADPreview. Pour plus d'informations, consultez [Prérequis pour utiliser PowerShell ou de l'Afficheur Graph](prerequisites.md).

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. Dans une fenêtre PowerShell, utilisez [Connect-AzureAD](/powershell/module/azuread/connect-azuread) pour vous connecter à votre locataire.

    ```powershell
    Connect-AzureAD
    ```

1. Utilisez [Get-AzureADUser](/powershell/module/azuread/get-azureaduser) pour obtenir l’utilisateur auquel vous souhaitez attribuer un rôle.

    ```powershell
    $user = Get-AzureADUser -Filter "userPrincipalName eq 'user@contoso.com'"
    ```

### <a name="assign-a-role"></a>Attribuer un rôle

1. Utilisez [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition) pour obtenir le rôle que vous souhaitez attribuer.

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Billing Administrator'"
    ```

1. Utilisez [New-AzureADMSRoleAssignment](/powershell/module/azuread/new-azureadmsroleassignment) pour attribuer le rôle.

    ```powershell
    $roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
    ```

### <a name="assign-a-role-as-eligible-using-pim"></a>Attribuer un rôle comme éligible à l’aide de PIM

Si PIM est activé, vous disposez de fonctionnalités supplémentaires, telles que la création d’un utilisateur éligible pour une attribution de rôle ou la définition de l’heure de début et l’heure de fin d’une attribution de rôle. Ces fonctionnalités utilisent un autre ensemble de commandes PowerShell. Pour plus d’informations sur l’utilisation de PowerShell et PIM, consultez [PowerShell pour les rôles Azure AD dans Privileged Identity Management](../privileged-identity-management/powershell-for-azure-ad-roles.md).


1. Utilisez [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition) pour obtenir le rôle que vous souhaitez attribuer.

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Billing Administrator'"
    ```

1. Utilisez [Get-AzureADMSPrivilegedResource](/powershell/module/azuread/get-azureadmsprivilegedresource) pour accéder à la ressource privilégiée. Dans ce cas, votre locataire.

    ```powershell
    $aadTenant = Get-AzureADMSPrivilegedResource -ProviderId aadRoles
    ```

1. Utilisez [New-Object](/powershell/module/microsoft.powershell.utility/new-object) pour créer un objet `AzureADMSPrivilegedSchedule` afin de définir l’heure de début et l’heure de fin de l’attribution de rôle.

    ```powershell
    $schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
    $schedule.Type = "Once"
    $schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
    $schedule.EndDateTime = "2021-07-25T20:00:00.000Z"
    ```

1. Utilisez [Open-AzureADMSPrivilegedRoleAssignmentRequest](/powershell/module/azuread/open-azureadmsprivilegedroleassignmentrequest) pour attribuer le rôle comme éligible.

    ```powershell
    $roleAssignmentEligible = Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId $aadTenant.Id -RoleDefinitionId $roleDefinition.Id -SubjectId $user.objectId -Type 'AdminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "Review billing info"
    ```

## <a name="microsoft-graph-api"></a>API Microsoft Graph

Suivez ces instructions pour attribuer un rôle à l’aide de l’API Microsoft Graph dans [Afficheur Graph](https://aka.ms/ge).

### <a name="assign-a-role"></a>Attribuer un rôle

Dans cet exemple, un principal de sécurité avec objectID `f8ca5a85-489a-49a0-b555-0a6d81e56f0d` se voit recevoir le rôle d’administrateur de facturation (ID de définition de rôle `b0f54661-2d74-4c50-afa3-1ec803f12efe`) au niveau de l’étendue du locataire. Si vous souhaitez afficher la liste des ID de modèle de rôle immuables de tous les rôles intégrés, consultez [Rôles intégrés Azure AD](permissions-reference.md).

1. Connectez-vous à l’[Afficheur Graph](https://aka.ms/ge).
2. Sélectionnez **POST** en tant que méthode HTTP dans la liste déroulante. 
3. Sélectionnez la version de l’API sur **bêta**.
4. Utilisez l’API [roleAssignments](/graph/api/rbacapplication-post-roleassignments) pour attribuer des rôles. Ajoutez les détails suivants à l’URL et au corps de la requête, puis sélectionnez **Exécuter la requête**.

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
Content-type: application/json

{ 
    "@odata.type": "#microsoft.graph.unifiedRoleAssignment",
    "roleDefinitionId": "b0f54661-2d74-4c50-afa3-1ec803f12efe",
    "principalId": "f8ca5a85-489a-49a0-b555-0a6d81e56f0d",
    "directoryScopeId": "/"
}
```

### <a name="assign-a-role-using-pim"></a>Affecter un rôle à l’aide de PIM

Dans cet exemple, un principal de sécurité avec objectID `f8ca5a85-489a-49a0-b555-0a6d81e56f0d` se voit recevoir une attribution de rôle éligible en fonction du temps pour l’administrateur de facturation (ID `b0f54661-2d74-4c50-afa3-1ec803f12efe` de définition de rôle) pendant 180 jours.

1. Connectez-vous à l’[Afficheur Graph](https://aka.ms/ge).
2. Sélectionnez **POST** en tant que méthode HTTP dans la liste déroulante. 
3. Sélectionnez la version de l’API sur **bêta**.
4. Ajoutez les détails suivants à l’URL et au corps de la requête, puis sélectionnez **Exécuter la requête**.

```HTTP
POST https://graph.microsoft.com/beta/rolemanagement/directory/roleEligibilityScheduleRequests

Content-type: application/json

{
    "action": "AdminAssign",
    "justification": "for managing admin tasks",
    "directoryScopeId": "/",
    "principalId": "f8ca5a85-489a-49a0-b555-0a6d81e56f0d",
    "roleDefinitionId": "b0f54661-2d74-4c50-afa3-1ec803f12efe",
    "scheduleInfo": {
        "startDateTime": "2021-07-15T19:15:08.941Z",
        "expiration": {
            "type": "AfterDuration",
            "duration": "PT180D"
        }
    }
}

```

Dans l’exemple suivant, un principal de sécurité reçoit une attribution de rôle permanente éligible à l’administrateur de facturation.

```HTTP
POST https://graph.microsoft.com/beta/rolemanagement/directory/roleEligibilityScheduleRequests

Content-type: application/json

{
    "action": "AdminAssign",
    "justification": "for managing admin tasks",
    "directoryScopeId": "/",
    "principalId": "f8ca5a85-489a-49a0-b555-0a6d81e56f0d",
    "roleDefinitionId": "b0f54661-2d74-4c50-afa3-1ec803f12efe",
    "scheduleInfo": {
        "startDateTime": "2021-07-15T19:15:08.941Z",
        "expiration": {
            "type": "NoExpiration"
        }
    }
}

```

Pour activer l’attribution de rôle, utilisez l’API suivante.

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentScheduleRequests

Content-type: application/json

{
    "action": "SelfActivate",
    "justification": "activating role assignment for admin privileges",
    "roleDefinitionId": "b0f54661-2d74-4c50-afa3-1ec803f12efe",
    "directoryScopeId": "/",
    "principalId": "f8ca5a85-489a-49a0-b555-0a6d81e56f0d"
}

```

## <a name="next-steps"></a>Étapes suivantes

- [Répertorier les attributions de rôle Azure AD](view-assignments.md)
- [Assigner des rôles personnalisés avec une étendue de ressources à l’aide de PowerShell](custom-assign-powershell.md)
- [Rôles intégrés Azure AD](permissions-reference.md)
