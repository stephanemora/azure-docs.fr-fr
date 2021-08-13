---
title: Ajouter et supprimer des unités administratives – Azure Active Directory | Microsoft Docs
description: Utiliser des unités administratives pour limiter l’étendue des autorisations de rôle dans Azure Active Directory.
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
ms.openlocfilehash: 78468c9528c4502ce691dec183c261b9636325f8
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110094327"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Gérer des unités administratives dans Azure Active Directory

Pour un contrôle administratif plus précis dans Azure Active Directory (Azure AD), vous pouvez assigner des utilisateurs à un rôle Azure AD dont l’étendue est limitée à une ou plusieurs unités administratives.


## <a name="prerequisites"></a>Prérequis

- Licence Azure AD Premium P1 ou P2 pour chaque administrateur d'unité administrative
- Licences Azure AD gratuites pour les membres de l'unité administrative
- Administrateur de rôle privilégié ou Administrateur général
- Module AzureAD (avec PowerShell)
- Consentement administrateur (avec l’Afficheur Graph pour l’API Microsoft Graph)

Pour plus d’informations, consultez [Prérequis pour utiliser PowerShell ou de l’Afficheur Graph](prerequisites.md).

## <a name="add-an-administrative-unit"></a>Ajouter une unité administrative

Vous pouvez ajouter une unité administrative à l’aide du portail Azure ou de PowerShell.

### <a name="azure-portal"></a>Portail Azure

1. Dans le portail Azure, allez à Azure AD. Ensuite, dans le volet gauche, sélectionnez **Unités administratives**.

    ![Capture d’écran du lien « Unités administratives » dans Azure AD.](./media/admin-units-manage/nav-to-admin-units.png)

1. Sélectionnez le bouton **Ajouter** dans la partie supérieure du volet, puis, dans la zone **Nom**, entrez le nom de l’unité administrative. Si vous le souhaitez, ajoutez une description de l’unité administrative.

    ![Capture d’écran montrant le bouton Ajouter et la zone Nom permettant d’entrer le nom de l’unité administrative.](./media/admin-units-manage/add-new-admin-unit.png)

1. Sélectionnez le bouton bleu **Ajouter** pour finaliser l’unité administrative.

### <a name="powershell"></a>PowerShell

```powershell
Connect-AzureAD
New-AzureADMSAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

Vous pouvez modifier les valeurs entre guillemets si nécessaire.

### <a name="microsoft-graph-api"></a>API Microsoft Graph

Requête

```http
POST /administrativeUnits
```

Corps

```http
{
  "displayName": "North America Operations",
  "description": "North America Operations administration"
}
```

## <a name="remove-an-administrative-unit"></a>Supprimer une unité administrative

Dans Azure AD, vous pouvez supprimer une unité administrative dont vous n’avez plus besoin comme une unité d'étendue pour les rôles d'administration.

### <a name="azure-portal"></a>Portail Azure

1. Dans le portail Azure, accédez à **Azure AD** puis, sélectionnez **Unités administratives**. 
1. Sélectionnez l’unité administrative à supprimer, puis **Supprimer**. 
1. Pour confirmer que vous souhaitez supprimer l’unité administrative, sélectionnez **Oui**. L’unité administrative est supprimée.

![Capture d’écran du bouton Supprimer de l’unité administrative et de la fenêtre de confirmation.](./media/admin-units-manage/select-admin-unit-to-delete.png)

### <a name="powershell"></a>PowerShell

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADMSAdministrativeUnit -Id $adminUnitObj.Id
```

Vous pouvez modifier les valeurs entre guillemets si nécessaire, en fonction de l’environnement spécifique.

### <a name="microsoft-graph-api"></a>API Microsoft Graph

Requête

```http
DELETE /administrativeUnits/{admin-unit-id}
```

body

```http
{}
```

## <a name="next-steps"></a>Étapes suivantes

* [Gérer les utilisateurs dans une unité administrative](admin-units-add-manage-users.md)
* [Gérer les groupes dans une unité administrative](admin-units-add-manage-groups.md)
