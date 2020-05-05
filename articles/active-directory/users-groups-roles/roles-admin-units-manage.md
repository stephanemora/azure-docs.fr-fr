---
title: Ajouter et supprimer des unités administratives (préversion) - Azure Active Directory | Microsoft Docs
description: Utiliser des unités administratives pour limiter l’étendue des autorisations de rôle dans Azure Active Directory.
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
ms.openlocfilehash: 7a4a0dfaeda18b3f68ddc3c7cc7333b8c994d174
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81684910"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Gérer des unités administratives dans Azure Active Directory

Pour un contrôle administratif plus précis dans Azure Active Directory (Azure AD), vous pouvez assigner des utilisateurs à un rôle Azure AD dont l’étendue est limitée à une ou plusieurs unités administratives (UA).

## <a name="get-started"></a>Bien démarrer

1. Pour exécuter des requêtes à partir des instructions suivantes via l'[Afficheur Graph](https://aka.ms/ge), procédez comme suit :

    a. Dans le portail Azure, allez à Azure AD. Dans la liste des applications, sélectionnez **Afficheur Graph**, puis **Accorder un consentement d’administrateur à l’Afficheur Graph**.

    ![Capture d’écran montrant le lien vers « Accorder un consentement d’administrateur »](./media/roles-admin-units-manage/select-graph-explorer.png)

    b. Dans l’Afficheur Graph, sélectionnez la version **bêta**.

    ![Capture d’écran montrant la version bêta sélectionnée](./media/roles-admin-units-manage/select-beta-version.png)

1. Utilisez la préversion d'Azure AD PowerShell.

## <a name="add-an-administrative-unit"></a>Ajouter une unité administrative

### <a name="use-the-azure-portal"></a>Utilisation du portail Azure

1. Dans le portail Azure, accédez à Azure AD puis, dans le volet gauche, sélectionnez **Unités administratives**.

    ![Capture d’écran du lien (préversion) des unités administratives dans Azure AD](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. Sélectionnez **Ajouter**, puis entrez le nom de l’unité administrative. Si vous le souhaitez, ajoutez une description de l’unité administrative.

    ![Capture d’écran du bouton Ajouter et de la zone de texte permettant d’entrer le nom de l’unité administrative](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. Sélectionnez **Ajouter** pour finaliser l’unité administrative.

### <a name="use-powershell"></a>Utiliser PowerShell

Installez Azure AD PowerShell (préversion) avant d’essayer d’exécuter les commandes suivantes :

    Connect-AzureAD
    New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"

Vous pouvez modifier les valeurs entre guillemets si nécessaire.

### <a name="use-microsoft-graph"></a>Utiliser Microsoft Graph

    Http Request
    POST /administrativeUnits
    Request body
    {
        "displayName": "North America Operations",
        "description": "North America Operations administration"
    }

## <a name="remove-an-administrative-unit"></a>Supprimer une unité administrative

Dans Azure AD, vous pouvez supprimer une unité administrative dont vous n’avez plus besoin comme une unité d'étendue pour les rôles d'administration.

### <a name="use-the-azure-portal"></a>Utilisation du portail Azure

1. Dans le portail Azure, accédez à **Azure AD** > **Unités administratives**. 
1. Sélectionnez l’unité administrative à supprimer, puis **Supprimer**. 
1. Pour confirmer que vous souhaitez supprimer l’unité administrative, sélectionnez **Oui**. L’unité administrative est supprimée.

![Capture d’écran du bouton Supprimer de l’unité administrative et fenêtre de confirmation](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>Utiliser PowerShell

    $delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
    Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId

Vous pouvez modifier les valeurs entre guillemets si nécessaire, en fonction de l’environnement spécifique.

### <a name="use-the-graph-api"></a>Utiliser l’API Graph

    HTTP request
    DELETE /administrativeUnits/{Admin id}
    Request body
    {}

## <a name="next-steps"></a>Étapes suivantes

* [Gérer les utilisateurs dans une unité administrative](roles-admin-units-add-manage-users.md)
* [Gérer les groupes dans une unité administrative](roles-admin-units-add-manage-groups.md)
