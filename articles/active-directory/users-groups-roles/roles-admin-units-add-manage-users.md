---
title: Ajouter, supprimer et répertorier des utilisateurs dans une unité administrative (préversion) – Azure Active Directory | Microsoft Docs
description: Gérer les utilisateurs et leurs autorisations de rôles dans une unité administrative dans Azure Active Directory
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
ms.openlocfilehash: 9c2c5c083115440e1e4da203f39f2b32734458c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81684975"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Ajouter et gérer des utilisateurs dans une unité administrative dans Azure Active Directory

Dans Azure Active Directory (Azure AD), vous pouvez ajouter des utilisateurs à une unité administrative pour obtenir une étendue administrative de contrôle plus précise.

Pour connaître les étapes de préparation à l’utilisation de PowerShell et de Microsoft Graph pour la gestion des unités administratives, consultez [Prise en main](roles-admin-units-manage.md#get-started).

## <a name="add-users-to-an-au"></a>Ajouter des utilisateurs à une unité administrative

### <a name="azure-portal"></a>Portail Azure

Vous pouvez affecter des utilisateurs à des unités administratives de deux manières.

1. Affectation individuelle

    1. Vous pouvez accéder à Azure AD dans le portail, puis sélectionner Utilisateurs et choisir l’utilisateur à affecter à une unité administrative. Vous pouvez ensuite sélectionner Unités administratives dans le volet gauche. Vous pouvez affecter l’utilisateur à une ou plusieurs unités administratives en cliquant sur Affecter à l’unité administrative, puis en choisissant les unités administratives auxquelles affecter l’utilisateur.

       ![sélectionner Ajouter, puis entrez un nom pour l’unité administrative](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

    1. Vous pouvez accéder à Azure AD dans le portail, sélectionner Unités administratives dans le volet gauche, puis choisir l’unité administrative à laquelle affecter les utilisateurs. Sélectionnez Tous les utilisateurs dans le volet gauche, puis choisissez Ajouter un membre. Vous pouvez ensuite sélectionner dans le volet droit un ou plusieurs utilisateurs à affecter à l’unité administrative.

        ![sélectionner une unité administrative, puis choisir Ajouter un membre](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

1. Affectation en bloc

    Accédez à Azure AD dans le portail, puis sélectionnez Unités administratives. Sélectionnez l’unité administrative à laquelle ajouter les utilisateurs. Continuez en cliquant sur Tous les utilisateurs-> Ajouter des membres à partir d’un fichier .csv. Vous pouvez ensuite télécharger le modèle CSV et modifier le fichier. Le format est simple et nécessite l’ajout d’un UPN unique dans chaque ligne. Une fois le fichier prêt, enregistrez-le dans un emplacement approprié, puis chargez-le à l’étape 3 comme dans la capture instantanée.

    ![affecter des utilisateurs en bloc à une unité administrative](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrativeunitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId

Dans l’exemple ci-dessus, la cmdlet Add-AzureADAdministrativeUnitMember est utilisée pour ajouter l’utilisateur à l’unité administrative. L’ID d’objet de l’unité administrative à laquelle ajouter l’utilisateur et l’ID d’objet de l’utilisateur à ajouter sont utilisés en tant qu’argument. Il est possible de modifier la section en surbrillance selon les besoins de l’environnement spécifique.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref
    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/users/{id}"
    }

Exemple :

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/johndoe@fabidentity.com"
    }

## <a name="list-administrative-units-for-a-user"></a>Répertorier les unités administratives pour un utilisateur

### <a name="azure-portal"></a>Portail Azure

Dans le Portail Azure, vous pouvez ouvrir le profil d’un utilisateur en accédant à Azure AD > Utilisateurs. Cliquez sur l’utilisateur pour ouvrir son profil.

![Ouvrir le profil d’un utilisateur dans Azure Active Directory](./media/roles-admin-units-add-manage-users/user-profile-admin-units.png)

Dans le volet gauche, sélectionnez **Unités administratives** pour afficher la liste des unités administratives auxquelles l’utilisateur a été affecté.

![Liste des unités administratives pour un utilisateur](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $userObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/users//memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-single-user-from-an-au"></a>Supprimer un seul utilisateur d’une unité administrative

### <a name="azure-portal"></a>Portail Azure

Il existe deux façons de supprimer un utilisateur d’une unité administrative. Dans le Portail Azure, vous pouvez ouvrir le profil d’un utilisateur en accédant à **Azure AD** > **Utilisateurs**. Sélectionnez l’utilisateur pour ouvrir son profil. Sélectionnez l’unité administrative dont l’utilisateur doit être supprimé, puis choisissez **Supprimer de l’unité administrative**.

![Supprimer un utilisateur d’une unité administrative à partir du profil utilisateur](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

Vous pouvez également supprimer un utilisateur dans **Azure AD** > **Unités administratives** en sélectionnant l’unité administrative dont vous souhaitez le supprimer. Sélectionnez l’utilisateur, puis choisissez **Supprimer un membre**.
  
![Supprimer un utilisateur au niveau de l’unité administrative](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberUserObjId

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<user-id>/ $ref

## <a name="bulk-remove-more-than-one-user"></a>Supprimer en bloc plusieurs utilisateurs

Vous pouvez accéder à Azure AD > Unités administratives, puis choisir l’unité administrative dont vous souhaitez supprimer des utilisateurs. Cliquez sur Supprimer un membre en bloc. Téléchargez le modèle CSV pour fournir la liste des utilisateurs à supprimer.

Modifiez le modèle CSV téléchargé avec les entrées utilisateur appropriées. Ne supprimez pas les deux premières lignes du modèle. Ajoutez un UPN d’utilisateur dans chaque ligne.

![Modifier le fichier CSV pour la suppression en bloc d’utilisateurs d’unités administratives](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

Une fois que vous avez enregistré les entrées dans le fichier, chargez celui-ci, puis sélectionnez **Envoyer**.

![Envoyer le fichier de chargement en bloc](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>Étapes suivantes

- [Affecter un rôle à une unité administrative](roles-admin-units-assign-roles.md)
- [Ajouter des groupes à une unité administrative](roles-admin-units-add-manage-groups.md)
