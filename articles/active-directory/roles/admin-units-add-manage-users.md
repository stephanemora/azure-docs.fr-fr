---
title: Ajouter, supprimer et répertorier des utilisateurs dans une unité administrative – Azure Active Directory | Microsoft Docs
description: Gérer les utilisateurs et leurs autorisations de rôles dans une unité administrative dans Azure Active Directory
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
ms.openlocfilehash: 2a68295594b0153a7d062ae3dac34e6dcba5b04f
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92373394"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Ajouter et gérer des utilisateurs dans une unité administrative dans Azure Active Directory

Dans Azure Active Directory (Azure AD), vous pouvez ajouter des utilisateurs à une unité administrative pour obtenir une étendue administrative de contrôle plus précise.

Pour connaître les étapes de préparation à l’utilisation de PowerShell et de Microsoft Graph pour la gestion des unités administratives, consultez [Prise en main](admin-units-manage.md#get-started).

## <a name="add-users-to-an-au"></a>Ajouter des utilisateurs à une unité administrative

### <a name="azure-portal"></a>Portail Azure

Vous pouvez affecter des utilisateurs à des unités administratives individuellement ou dans une opération en bloc.

- Affectation individuelle à partir d’un profil utilisateur

   1. Connectez-vous au [centre d’administration Azure AD](https://portal.azure.com) avec les autorisations Administrateur de rôle privilégié.
   1. Sélectionnez **Utilisateurs** et sélectionnez l’utilisateur à affecter à une unité administrative afin d’ouvrir le profil de l’utilisateur.
   1. Sélectionnez **Unités administratives** . Vous pouvez affecter l’utilisateur à une ou plusieurs unités administratives en sélectionnant **Affecter à l’unité administrative** , puis en choisissant les unités administratives auxquelles affecter l’utilisateur.

       ![sélectionner Ajouter, puis entrez un nom pour l’unité administrative](./media/admin-units-add-manage-users/assign-users-individually.png)

- Affectation individuelle d’une unité administrative

   1. Connectez-vous au [centre d’administration Azure AD](https://portal.azure.com) avec les autorisations Administrateur de rôle privilégié.
   1. Sélectionnez **Unités administratives** puis sélectionnez l’unité administrative à laquelle les utilisateurs doivent être affectés.
   1. Sélectionnez **Tous les utilisateurs** puis sélectionnez **Ajouter un membre** pour choisir un ou plusieurs utilisateurs à affecter à l’unité administrative à partir du volet **Ajouter un membre** .

        ![sélectionner une unité administrative, puis choisir Ajouter un membre](./media/admin-units-add-manage-users/assign-to-admin-unit.png)

- Affectation en bloc

   1. Connectez-vous au [centre d’administration Azure AD](https://portal.azure.com) avec les autorisations Administrateur de rôle privilégié.
   1. Sélectionnez **Unités administratives** .
   1. Sélectionnez l’unité administrative à laquelle ajouter les utilisateurs.
   1. Ouvrez **Tous les utilisateurs** > **Ajouter des membres à partir d’un fichier .csv** . Vous pouvez ensuite télécharger le modèle CSV (valeurs séparées par des virgules) et modifier le fichier. Le format est simple et nécessite l’ajout d’un UPN (nom d’utilisateur principal) unique dans chaque ligne. Une fois le fichier prêt, enregistrez-le dans un emplacement approprié, puis chargez-le.

    ![affecter des utilisateurs en bloc à une unité administrative](./media/admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrativeunitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId
```

Dans l’exemple ci-dessus, la cmdlet Add-AzureADAdministrativeUnitMember est utilisée pour ajouter l’utilisateur à l’unité administrative. L’ID d’objet de l’unité administrative à laquelle ajouter l’utilisateur et l’ID d’objet de l’utilisateur à ajouter sont utilisés en tant qu’argument. Il est possible de modifier la section en surbrillance selon les besoins de l’environnement spécifique.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref
Request body
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/{id}"
}
```

Exemple :

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/johndoe@fabidentity.com"
}
```

## <a name="list-administrative-units-for-a-user"></a>Répertorier les unités administratives pour un utilisateur

### <a name="azure-portal"></a>Portail Azure

Dans le Portail Azure, vous pouvez ouvrir le profil d’un utilisateur :

1. En ouvrant **Azure AD** > **Utilisateurs** .

1. Sélectionnez l’utilisateur pour ouvrir son profil.

1. Sélectionnez **Unités administratives** pour afficher la liste des unités administratives auxquelles l’utilisateur a été affecté.

   ![Liste des unités administratives pour un utilisateur](./media/admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```
Remarque : Par défaut, l’option AzureADAdministrativeUnitMember renvoie uniquement 100 membres ; pour récupérer des membres supplémentaires, vous pouvez ajouter « -All $true ».

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/v1.0/users/{id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-au"></a>Supprimer un seul utilisateur d’une unité administrative

### <a name="azure-portal"></a>Portail Azure

Il existe deux façons de supprimer un utilisateur d’une unité administrative. Dans le Portail Azure, vous pouvez ouvrir le profil d’un utilisateur en accédant à **Azure AD** > **Utilisateurs** . Sélectionnez l’utilisateur pour ouvrir son profil. Sélectionnez l’unité administrative dont l’utilisateur doit être supprimé, puis choisissez **Supprimer de l’unité administrative** .

![Supprimer un utilisateur d’une unité administrative à partir du profil utilisateur](./media/admin-units-add-manage-users/user-remove-admin-units.png)

Vous pouvez également supprimer un utilisateur dans **Azure AD** > **Unités administratives** en sélectionnant l’unité administrative dont vous souhaitez le supprimer. Sélectionnez l’utilisateur, puis choisissez **Supprimer un membre** .
  
![Supprimer un utilisateur au niveau de l’unité administrative](./media/admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $auId -MemberId $memberUserObjId
```

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/v1.0/directory/administrativeUnits/{adminunit-id}/members/{user-id}/ $ref

## <a name="bulk-remove-more-than-one-user"></a>Supprimer en bloc plusieurs utilisateurs

Vous pouvez accéder à Azure AD > Unités administratives, puis choisir l’unité administrative dont vous souhaitez supprimer des utilisateurs. Cliquez sur Supprimer un membre en bloc. Téléchargez le modèle CSV pour fournir la liste des utilisateurs à supprimer.

Modifiez le modèle CSV téléchargé avec les entrées utilisateur appropriées. Ne supprimez pas les deux premières lignes du modèle. Ajoutez un UPN d’utilisateur dans chaque ligne.

![Modifier le fichier CSV pour la suppression en bloc d’utilisateurs d’unités administratives](./media/admin-units-add-manage-users/bulk-user-entries.png)

Une fois que vous avez enregistré les entrées dans le fichier, chargez celui-ci, puis sélectionnez **Envoyer** .

![Envoyer le fichier de chargement en bloc](./media/admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>Étapes suivantes

- [Affecter un rôle à une unité administrative](admin-units-assign-roles.md)
- [Ajouter des groupes à une unité administrative](admin-units-add-manage-groups.md)
