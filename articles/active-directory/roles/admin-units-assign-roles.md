---
title: Attribuer et répertorier les rôles avec une étendue d’unité administrative – Azure Active Directory | Microsoft Docs
description: Utilisez des unités administratives pour limiter l’étendue des attributions de rôles dans Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 05/14/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b34eafac248bc0fd06076550e784a061573a712
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531951"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Attribuer des rôles dont l’étendue est délimitée à une unité administrative

Dans Azure Active Directory (Azure AD), pour un contrôle administratif plus précis, vous pouvez assigner des utilisateurs à un rôle Azure AD dont l’étendue est limitée à une ou plusieurs unités administratives.

## <a name="prerequisites"></a>Prérequis

- Licence Azure AD Premium P1 ou P2 pour chaque administrateur d'unité administrative
- Licences Azure AD gratuites pour les membres de l'unité administrative
- Administrateur de rôle privilégié ou Administrateur général
- Module AzureAD (avec PowerShell)
- Consentement administrateur (avec l'Afficheur Graph pour l'API Microsoft Graph)

Pour plus d'informations, consultez [Prérequis pour utiliser PowerShell ou de l'Afficheur Graph](prerequisites.md).


## <a name="available-roles"></a>Rôles disponibles

Role  |  Description
----- |  -----------
Administrateur d’authentification  |  A accès à l’affichage, à la définition et à la réinitialisation des informations de la méthode d’authentification pour tous les utilisateurs non administrateurs à l’intérieur de l’unité administrative affectée uniquement.
Administrateur de groupes  |  Peut gérer tous les aspects des groupes et des paramètres de groupes, tels que les stratégies d’affectation de noms et d’expiration, dans l’unité administrative attribuée uniquement.
Administrateur du support technique  |  Peut réinitialiser les mots de passe des non-administrateurs et des Administrateurs du support technique au sein de l'unité administrative affectée uniquement.
Administrateur de licence  |  Peut attribuer, supprimer et mettre à jour des affectations de licence à l’intérieur de l’unité administrative uniquement.
Administrateur de mots de passe  |  Peut réinitialiser les mots de passe des non-administrateurs et des administrateurs de mots de passe à l’intérieur de l’unité administrative affectée uniquement.
Administrateur d'utilisateurs  |  Peut gérer tous les aspects des utilisateurs et des groupes, notamment la réinitialisation des mots de passe pour les administrateurs limités à l’intérieur de l’unité administrative affectée uniquement.

## <a name="security-principals-that-can-be-assigned-to-a-scoped-role"></a>Principaux de sécurité qui peuvent être affectés à un rôle dont l’étendue est délimitée

Les principaux de sécurité suivants peuvent être affectés à un rôle avec une étendue d’unité administrative :

* Utilisateurs
* Groupes Azure AD assignables par rôle
* Nom de principal du service

## <a name="assign-a-scoped-role"></a>Attribuer un rôle dont l’étendue est délimitée

Vous pouvez attribuer un rôle étendu à l’aide du portail Azure, de PowerShell ou de Microsoft Graph.

### <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com) ou au [Centre d’administration Azure AD](https://aad.portal.azure.com).

1. Sélectionnez **Azure Active Directory** > **Unités administratives**, puis sélectionnez l’unité administrative à laquelle vous souhaitez attribuer une étendue de rôle utilisateur. 

1. Dans le volet gauche, sélectionnez **Rôles et administrateurs** pour répertorier tous les rôles disponibles.

   ![Capture d’écran du volet « Rôles et administrateurs » pour la sélection d’une unité administrative dont l’étendue de rôle doit être attribuée.](./media/admin-units-assign-roles/select-role-to-scope.png)

1. Sélectionnez le rôle à attribuer, puis choisissez **Ajouter des attributions**. 

1. Dans le volet **Ajouter des attributions**, sélectionnez un ou plusieurs utilisateurs à affecter au rôle.

   ![Sélectionner le rôle dont délimiter l’étendue, puis sélectionner Ajouter des attributions](./media/admin-units-assign-roles/select-add-assignment.png)

> [!Note]
> Pour attribuer un rôle à une unité administrative à l’aide d’Azure AD Privileged Identity Management (PIM), consultez [Attribuer des rôles Azure AD dans PIM](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope).

### <a name="powershell"></a>PowerShell

```powershell
$adminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Administrator"
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$roleMember = New-Object -TypeName Microsoft.Open.MSGraph.Model.MsRoleMemberInfo
$roleMember.Id = $adminUser.ObjectId
Add-AzureADMSScopedRoleMembership -Id $adminUnitObj.Id -RoleId $role.ObjectId -RoleMemberInfo $roleMember
```

Vous pouvez modifier la section en surbrillance selon les besoins de l’environnement spécifique.

### <a name="microsoft-graph-api"></a>API Microsoft Graph

Requête

```http
POST /directory/administrativeUnits/{admin-unit-id}/scopedRoleMembers
```
    
Corps

```http
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="view-a-list-of-the-scoped-admins-in-an-administrative-unit"></a>Afficher une liste des administrateurs étendus dans une unité administrative

Vous pouvez afficher la liste des administrateurs étendus à l’aide du portail Azure, de PowerShell ou de Microsoft Graph.

### <a name="azure-portal"></a>Portail Azure

Vous pouvez afficher toutes les attributions de rôles créées avec une étendue d’unité administrative dans la [section Unités administratives d’Azure AD](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit). 

1. Connectez-vous au [Portail Azure](https://portal.azure.com) ou au [Centre d’administration Azure AD](https://aad.portal.azure.com).

1. Sélectionnez **Azure Active Directory** > **Unités administratives**, puis sélectionnez l’unité administrative pour la liste des attributions de rôles que vous souhaitez afficher. 

1. Sélectionnez **Rôles et administrateurs**, puis ouvrez un rôle pour afficher les affectations dans l’unité administrative.

### <a name="powershell"></a>PowerShell

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -Id $adminUnitObj.Id | fl *
```

Vous pouvez modifier la section en surbrillance selon les besoins de votre environnement spécifique.

### <a name="microsoft-graph-api"></a>API Microsoft Graph

Requête

```http
GET /directory/administrativeUnits/{admin-unit-id}/scopedRoleMembers
```

body

```http
{}
```

## <a name="next-steps"></a>Étapes suivantes

- [Utiliser des groupes Azure AD pour gérer les attributions de rôles](groups-concept.md)
- [Résoudre les problèmes de rôles Azure AD attribués aux groupes](groups-faq-troubleshooting.yml)
