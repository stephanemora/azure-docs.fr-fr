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
ms.date: 04/14/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: e24bf5df84015ada6b62c35fdd29571c66e06ebd
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505256"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Attribuer des rôles dont l’étendue est délimitée à une unité administrative

Dans Azure Active Directory (Azure AD), pour un contrôle administratif plus précis, vous pouvez assigner des utilisateurs à un rôle Azure AD dont l’étendue est limitée à une ou plusieurs unités administratives.

Pour vous préparer à utiliser PowerShell et Microsoft Graph pour la gestion des unités administratives, consultez [Prise en main](admin-units-manage.md#get-started).

## <a name="available-roles"></a>Rôles disponibles

Role  |  Description
----- |  -----------
Administrateur d’authentification  |  A accès à l’affichage, à la définition et à la réinitialisation des informations de la méthode d’authentification pour tous les utilisateurs non administrateurs à l’intérieur de l’unité administrative affectée uniquement.
Administrateur de groupes  |  Peut gérer tous les aspects des groupes et des paramètres de groupes, tels que les stratégies d’affectation de noms et d’expiration, dans l’unité administrative attribuée uniquement.
Administrateur du support technique  |  Peut réinitialiser les mots de passe des non-administrateurs et des administrateurs du support technique à l’intérieur de l’unité administrative affectée uniquement.
Administrateur de licence  |  Peut attribuer, supprimer et mettre à jour des affectations de licence à l’intérieur de l’unité administrative uniquement.
Administrateur de mots de passe  |  Peut réinitialiser les mots de passe des non-administrateurs et des administrateurs de mots de passe à l’intérieur de l’unité administrative affectée uniquement.
Administrateur d'utilisateurs  |  Peut gérer tous les aspects des utilisateurs et des groupes, notamment la réinitialisation des mots de passe pour les administrateurs limités à l’intérieur de l’unité administrative affectée uniquement.

## <a name="security-principals-that-can-be-assigned-to-a-scoped-role"></a>Principaux de sécurité qui peuvent être affectés à un rôle dont l’étendue est délimitée

Les principaux de sécurité suivants peuvent être affectés à un rôle avec une étendue d’unité administrative :

* Utilisateurs
* Groupes cloud attribuables à un rôle (préversion)
* Nom de principal du service

## <a name="assign-a-scoped-role"></a>Attribuer un rôle dont l’étendue est délimitée

Vous pouvez attribuer un rôle étendu à l’aide du portail Azure, de PowerShell ou de Microsoft Graph.

### <a name="use-the-azure-portal"></a>Utilisation du portail Azure

1. Dans le portail Azure, accédez à **Azure AD**.

1. Sélectionnez **Unités administratives**, puis sélectionnez l’unité administrative à laquelle vous souhaitez attribuer une étendue de rôle utilisateur. 

1. Dans le volet gauche, sélectionnez **Rôles et administrateurs** pour répertorier tous les rôles disponibles.

   ![Capture d’écran du volet « Rôles et administrateurs » pour la sélection d’une unité administrative dont l’étendue de rôle doit être attribuée.](./media/admin-units-assign-roles/select-role-to-scope.png)

1. Sélectionnez le rôle à attribuer, puis choisissez **Ajouter des attributions**. 

1. Dans le volet **Ajouter des attributions**, sélectionnez un ou plusieurs utilisateurs à affecter au rôle.

   ![Sélectionner le rôle dont délimiter l’étendue, puis sélectionner Ajouter des attributions](./media/admin-units-assign-roles/select-add-assignment.png)

> [!Note]
> Pour attribuer un rôle à une unité administrative à l’aide d’Azure AD Privileged Identity Management (PIM), consultez [Attribuer des rôles Azure AD dans PIM](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope).

### <a name="use-powershell"></a>Utiliser PowerShell

```powershell
$adminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Administrator"
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$roleMember = New-Object -TypeName Microsoft.Open.MSGraph.Model.MsRoleMemberInfo
$roleMember.Id = $adminUser.ObjectId
Add-AzureADMSScopedRoleMembership -Id $adminUnitObj.Id -RoleId $role.ObjectId -RoleMemberInfo $roleMember
```

Vous pouvez modifier la section en surbrillance selon les besoins de l’environnement spécifique.

### <a name="use-microsoft-graph"></a>Utiliser Microsoft Graph

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

### <a name="use-the-azure-portal"></a>Utilisation du portail Azure

Vous pouvez afficher toutes les attributions de rôles créées avec une étendue d’unité administrative dans la [section Unités administratives d’Azure AD](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit). 

1. Dans le portail Azure, accédez à **Azure AD**.

1. Dans le volet gauche, sélectionnez **Unités administratives**, puis sélectionnez l’unité administrative pour la liste des attributions de rôles que vous souhaitez afficher. 

1. Sélectionnez **Rôles et administrateurs**, puis ouvrez un rôle pour afficher les affectations dans l’unité administrative.

### <a name="use-powershell"></a>Utiliser PowerShell

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -Id $adminUnitObj.Id | fl *
```

Vous pouvez modifier la section en surbrillance selon les besoins de votre environnement spécifique.

### <a name="use-microsoft-graph"></a>Utiliser Microsoft Graph

Requête

```http
GET /directory/administrativeUnits/{admin-unit-id}/scopedRoleMembers
```

body

```http
{}
```

## <a name="next-steps"></a>Étapes suivantes

- [Utiliser des groupes cloud pour gérer les attributions de rôles](groups-concept.md)
- [Résoudre les problèmes de rôles attribués aux groupes cloud](groups-faq-troubleshooting.md)
