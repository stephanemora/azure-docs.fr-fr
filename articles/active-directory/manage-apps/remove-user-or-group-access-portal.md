---
title: Supprimer l’affectation d’un utilisateur ou d’un groupe à une application d’entreprise dans Azure Active Directory | Microsoft Docs
description: Comment supprimer l’affectation de l’accès à un utilisateur ou à un groupe à une application d’entreprise dans Azure Active Directory
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/14/2018
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 709489a1e96691a677261bfb2c7b5c29158e62c9
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55162445"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Supprimer l’affectation d’un utilisateur ou d’un groupe à une application d’entreprise dans Azure Active Directory
Il est facile de supprimer l’affectation de l’accès à un utilisateur ou un groupe à vos applications d’entreprise dans Azure Active Directory (Azure AD). Vous devez disposer des autorisations nécessaires pour gérer l’application d’entreprise, et vous devez être l’administrateur général du répertoire.

> [!NOTE]
> Dans le cas des applications Microsoft (telles que les applications Office 365), utilisez PowerShell pour supprimer des utilisateurs d’une application d’entreprise.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Comment faire pour supprimer l’affectation d’un utilisateur ou d’un groupe à une application d’entreprise dans le portail Azure ?
1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Plus de services**, saisissez **Azure Active Directory** dans la zone de texte, puis sélectionnez **Entrée**.
3. Dans la page **Azure Active Directory - *NomRépertoire*** (autrement dit, la page Azure AD du répertoire que vous gérez), sélectionnez **Applications d’entreprise**.

    ![Ouverture des applications d’entreprise](./media/remove-user-or-group-access-portal/open-enterprise-apps.png)
4. Dans la page **Applications d’entreprise**, sélectionnez **Toutes les applications**. Vous verrez une liste des applications que vous pouvez gérer.
5. Dans la page **Applications d’entreprise - Toutes les applications**, sélectionnez une application.
6. Dans la page ***NomApplication*** (autrement dit, la page intitulée avec le nom de l’application sélectionnée), sélectionnez **Utilisateurs et groupes**.

    ![Sélection d’utilisateurs ou groupes](./media/remove-user-or-group-access-portal/remove-app-users.png)
7. Dans la page ***NomApplication***  **- Affectation d’utilisateur et de groupe**, sélectionnez un ou plusieurs utilisateurs ou groupes, puis sélectionnez la commande **Supprimer**. Confirmez votre choix dans l’invite de commandes.

    ![Sélection de la commande Supprimer](./media/remove-user-or-group-access-portal/remove-users.png)

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Comment faire pour supprimer l’affectation d’un utilisateur ou d’un groupe à une application d’entreprise à l’aide de PowerShell ?
1. Ouvrez une invite de commandes Windows PowerShell avec des privilèges élevés.

    >[!NOTE] 
    > Vous devez installer le module AzureAD (utilisez la commande `Install-Module -Name AzureAD`). Si vous y êtes invité, installez un module NuGet ou le nouveau module Azure Active Directory V2 PowerShell, tapez O et appuyez sur Entrée.

2. Exécutez `Connect-AzureAD` et connectez-vous avec un compte d’utilisateur Administrateur général.
3. Pour affecter un utilisateur et un rôle à une application, utilisez le script suivant :

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ``` 
## <a name="next-steps"></a>Étapes suivantes

- [Voir tous mes groupes](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Affecter un utilisateur ou un groupe à une application d’entreprise](assign-user-or-group-access-portal.md)
- [Désactiver les connexions utilisateur pour une application d’entreprise](disable-user-sign-in-portal.md)
- [Modifier le nom ou le logo d’une application d’entreprise](change-name-or-logo-portal.md)
