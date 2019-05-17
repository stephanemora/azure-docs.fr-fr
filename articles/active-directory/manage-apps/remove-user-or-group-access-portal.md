---
title: Supprimer l’affectation d’un utilisateur ou d’un groupe à une application d’entreprise dans Azure Active Directory | Microsoft Docs
description: Comment supprimer l’affectation de l’accès à un utilisateur ou à un groupe à une application d’entreprise dans Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4ad995db26b67f365cd8ed9b2aeba5e47fe9c01
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826070"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Supprimer l’affectation d’un utilisateur ou d’un groupe à une application d’entreprise dans Azure Active Directory
Il est facile de supprimer un utilisateur ou un groupe d’accès affecté à un de vos applications d’entreprise dans Azure Active Directory (Azure AD). Vous avez besoin des autorisations appropriées pour gérer l’application d’entreprise. Et bien, vous devez être administrateur général pour le répertoire.

> [!NOTE]
> Dans le cas des applications Microsoft (telles que les applications Office 365), utilisez PowerShell pour supprimer des utilisateurs d’une application d’entreprise.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Comment faire pour supprimer l’affectation d’un utilisateur ou d’un groupe à une application d’entreprise dans le portail Azure ?
1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
1. Sélectionnez **Tous les services**, entrez **Azure Active Directory** dans la zone de texte, puis sélectionnez **Entrée**.
1. Sur le **Azure Active Directory - *NomRépertoire***  (autrement dit, la page Azure AD pour le répertoire que vous gérez), sélectionnez **applications d’entreprise**.
1. Sur le **applications d’entreprise - toutes les applications** page, vous verrez une liste des applications que vous pouvez gérer. Sélectionnez une application.
1. Sur le ***appname*** page Vue d’ensemble (autrement dit, la page avec le nom de l’application sélectionnée dans le titre), sélectionnez **Users & Groups**.
1. Dans la page ***NomApplication***  **- Affectation d’utilisateur et de groupe**, sélectionnez un ou plusieurs utilisateurs ou groupes, puis sélectionnez la commande **Supprimer**. Confirmez votre choix dans l’invite de commandes.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Comment faire pour supprimer l’affectation d’un utilisateur ou d’un groupe à une application d’entreprise à l’aide de PowerShell ?
1. Ouvrez une invite de commandes Windows PowerShell avec des privilèges élevés.

    >[!NOTE] 
    > Vous devez installer le module AzureAD (utilisez la commande `Install-Module -Name AzureAD`). Si vous y êtes invité, installez un module NuGet ou le nouveau module Azure Active Directory V2 PowerShell, tapez O et appuyez sur Entrée.

1. Exécutez `Connect-AzureAD` et connectez-vous avec un compte d’utilisateur Administrateur général.
1. Utilisez le script suivant pour supprimer un utilisateur et le rôle d’une application :

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
