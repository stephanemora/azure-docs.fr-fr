---
title: Attribuer et répertorier les rôles avec une étendue d’unité administrative (préversion) – Azure Active Directory | Microsoft Docs
description: Utilisation d’unités administratives pour limiter l’étendue des attributions de rôles dans Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3af281846e2bd1a39e691d84e964d8a8f780a6f1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81870413"
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

## <a name="assign-a-scoped-role"></a>Attribuer un rôle dont l’étendue est délimitée

### <a name="azure-portal"></a>Portail Azure

Dans le portail, accédez à **Azure AD > Unités administratives**. Sélectionnez l’unité administrative sur laquelle vous voulez affecter le rôle à un utilisateur. Dans le volet gauche, sélectionnez Rôles et administrateurs pour répertorier tous les rôles disponibles.

![Sélectionner une unité administrative pour modifier l’étendue du rôle](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

Sélectionnez le rôle à attribuer, puis choisissez **Ajouter des attributions**. Cela a pour effet d’ouvrir un panneau à droite, dans lequel vous pouvez sélectionner un ou plusieurs utilisateurs à assigner au rôle.

![Sélectionner le rôle dont délimiter l’étendue, puis sélectionner Ajouter des attributions](./media/roles-admin-units-assign-roles/select-add-assignment.png)

### <a name="powershell"></a>PowerShell

    $administrative = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $AdminUser = Get-AzureADUser -ObjectId 'janedoe@fabidentity.onmicrosoft.com'
    $uaRoleMemberInfo = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo -Property @{ObjectId = $AdminUser.ObjectId}
    Add-AzureADScopedRoleMembership -RoleObjectId $UserAdminRole.ObjectId -ObjectId $administrative unitObj.ObjectId -RoleMemberInfo  $uaRoleMemberInfo

Il est possible de modifier la section en surbrillance selon les besoins de l’environnement spécifique.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{id}/scopedRoleMembers
    
    Request body
    {
      "roleId": "roleId-value",
      "roleMemberInfo": {
        "id": "id-value"
      }
    }

## <a name="list-the-scoped-admins-on-an-au"></a>Répertorier les administrateurs dont l’étendue est délimitée sur une unité administrative

### <a name="azure-portal"></a>Portail Azure

Toutes les attributions de rôles effectuées avec une étendue d’unité administrative sont visibles dans la [section Unités administratives d’Azure AD](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit). Dans le portail, accédez à **Azure AD > Unités administratives**. Sélectionnez l’unité administrative pour les attributions de rôles à répertorier. Sélectionnez **Rôles et administrateurs**, puis ouvrez un rôle pour afficher les affectations dans l’unité administrative.

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADScopedRoleMembership -ObjectId $administrative unitObj.ObjectId | fl *

Il est possible de modifier la section en surbrillance selon les besoins de l’environnement spécifique.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    GET /administrativeUnits/{id}/scopedRoleMembers
    Request body
    {}

## <a name="next-steps"></a>Étapes suivantes

- [Résolution des problèmes d’unités administratives et FAQ](roles-admin-units-faq-troubleshoot.md)
