---
title: Attribuer et répertorier les rôles avec une étendue d’unité administrative – Azure Active Directory | Microsoft Docs
description: Utilisation d’unités administratives pour limiter l’étendue des attributions de rôles dans Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ea638124ed81a494231c814962345c028fc12c9
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940852"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Attribuer des rôles dont l’étendue est délimitée à une unité administrative

Pour un contrôle administratif plus précis dans Azure Active Directory (Azure AD), vous pouvez affecter des utilisateurs à un rôle Azure AD dont l’étendue est délimitée à une ou plusieurs unités administratives.

Pour connaître les étapes de préparation à l’utilisation de PowerShell et de Microsoft Graph pour la gestion des unités administratives, consultez [Prise en main](roles-admin-units-manage.md#get-started).

## <a name="roles-available"></a>Rôles disponibles

Role  |  Description
----- |  -----------
Administrateur d’authentification  |  A accès à l’affichage, à la définition et à la réinitialisation des informations de la méthode d’authentification pour tous les utilisateurs non administrateurs à l’intérieur de l’unité administrative affectée uniquement.
Administrateur de groupes  |  Peut gérer tous les aspects des groupes et des paramètres de groupes tels que les stratégies d’affectation de noms et d’expiration à l’intérieur de l’unité administrative affectée uniquement.
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

### <a name="azure-portal"></a>Portail Azure

Dans le portail, accédez à **Azure AD > Unités administratives**. Sélectionnez l’unité administrative sur laquelle vous voulez affecter le rôle à un utilisateur. Dans le volet gauche, sélectionnez Rôles et administrateurs pour répertorier tous les rôles disponibles.

![Sélectionner une unité administrative pour modifier l’étendue du rôle](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

Sélectionnez le rôle à attribuer, puis choisissez **Ajouter des attributions**. Un panneau s’ouvre à droite. Vous pouvez y sélectionner un ou plusieurs utilisateurs auxquels attribuer le rôle.

![Sélectionner le rôle dont délimiter l’étendue, puis sélectionner Ajouter des attributions](./media/roles-admin-units-assign-roles/select-add-assignment.png)

> [!Note]
>
> Pour attribuer un rôle à une unité administrative à l’aide de PIM, suivez les étapes [ici](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-add-role-to-user?tabs=new#assign-a-role-with-restricted-scope).

### <a name="powershell"></a>PowerShell

```powershell
$AdminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$Role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Account Administrator"
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$RoleMember = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo
$RoleMember.ObjectId = $AdminUser.ObjectId
Add-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId -RoleObjectId $Role.ObjectId -RoleMemberInfo $RoleMember
```

Il est possible de modifier la section en surbrillance selon les besoins de l’environnement spécifique.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /directory/administrativeUnits/{id}/scopedRoleMembers
    
Request body
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="list-the-scoped-admins-on-an-au"></a>Répertorier les administrateurs dont l’étendue est délimitée sur une unité administrative

### <a name="azure-portal"></a>Portail Azure

Toutes les attributions de rôles effectuées avec une étendue d’unité administrative sont visibles dans la [section Unités administratives d’Azure AD](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit). Dans le portail, accédez à **Azure AD > Unités administratives**. Sélectionnez l’unité administrative pour les attributions de rôles à répertorier. Sélectionnez **Rôles et administrateurs**, puis ouvrez un rôle pour afficher les affectations dans l’unité administrative.

### <a name="powershell"></a>PowerShell

```powershell
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId | fl *
```

Il est possible de modifier la section en surbrillance selon les besoins de l’environnement spécifique.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
GET /directory/administrativeUnits/{id}/scopedRoleMembers
Request body
{}
```

## <a name="next-steps"></a>Étapes suivantes

- [Utiliser des groupes cloud pour gérer les attributions de rôles](roles-groups-concept.md)
- [Résoudre les problèmes de rôles attribués aux groupes cloud](roles-groups-faq-troubleshooting.md)
