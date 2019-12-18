---
title: Masquer une application de l’expérience utilisateur dans Azure AD
description: Guide pratique pour masquer une application depuis l’expérience utilisateur dans les panneaux d’accès Azure Active Directory ou les lanceurs Office 365.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: be29f51771e24c67a8cd99a81e6a69be830dacb8
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970633"
---
# <a name="hide-applications-from-end-users-in-azure-active-directory"></a>Masquer les applications aux utilisateurs finaux dans Azure Active Directory

Instructions pour masquer les applications du panneau MyApps ou du Lanceur Office 365 à l’utilisateur final. Lorsqu’une application est masquée, les utilisateurs ont toujours les autorisations à celle-ci. 

## <a name="prerequisites"></a>Prérequis

Les privilèges d’administrateur d’application sont nécessaires pour masquer une application du panneau MyApps et du Lanceur Office 365.

Les privilèges d’administrateur général sont nécessaires pour masquer toutes les applications Office 365.


## <a name="hide-an-application-from-the-end-user"></a>Masquer une application à l’utilisateur final
Suivez ces étapes pour masquer une application dans le panneau MyApps et dans le lanceur d’applications Office 365.

1.  Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général de votre annuaire.
2.  Sélectionnez **Azure Active Directory**.
3.  Sélectionnez **Applications d’entreprise**. Le panneau **Applications d’entreprise - Toutes les applications** s’ouvre.
4.  Sous **Type d’Application**, sélectionnez **Applications d’entreprise** si cette option n’est pas encore sélectionnée.
5.  Recherchez l’application que vous souhaitez masquer, puis cliquez dessus.  La vue d’ensemble de l’application apparaît.
6.  Cliquez sur **Propriétés**. 
7.  À la question **Visible par les utilisateurs ?** , cliquez sur **Non**.
8.  Cliquez sur **Enregistrer**.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Utiliser Azure AD PowerShell pour masquer une application

Pour masquer une application dans le panneau MyApps, vous pouvez ajouter manuellement la balise HideApp au principal de service pour l’application. Exécutez les commandes [AzureAD PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#service_principals) suivantes pour définir la propriété **Visible par les utilisateurs ?** de l’application sur **Non**. 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags.Add("HideApp")
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-office-365-applications-from-the-myapps-panel"></a>Masquer des applications Office 365 dans le panneau MyApps

Procédez comme suit pour masquer toutes les applications Office 365 dans le panneau MyApps. Les applications sont toujours visibles dans le portail Office 365.

1.  Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général de votre annuaire.
2.  Sélectionnez **Azure Active Directory**.
3.  Sélectionnez **Paramètres utilisateur**.
4.  Sous **Applications d’entreprise**, cliquez sur **Gérer la façon dont les utilisateurs finaux lancent et affichent leurs applications**.
5.  Pour **Les utilisateurs peuvent voir uniquement les applications Office 365 dans le portail Office 365**, cliquez sur **Oui**.
6.  Cliquez sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes
* [Voir tous mes groupes](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Affecter un utilisateur ou un groupe à une application d’entreprise](assign-user-or-group-access-portal.md)
* [Suppression d’une affectation d’utilisateur ou de groupe à partir d’une application d’entreprise](remove-user-or-group-access-portal.md)
* [Modifier le nom ou le logo d’une application d’entreprise dans la version préliminaire d’Azure Active Directory](change-name-or-logo-portal.md)

