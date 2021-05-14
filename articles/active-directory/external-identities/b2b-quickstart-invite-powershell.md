---
title: 'Démarrage rapide : Ajouter un utilisateur invité avec PowerShell - Azure AD'
description: Dans ce guide de démarrage rapide, vous allez apprendre à utiliser PowerShell pour envoyer une invitation à un utilisateur externe d’Azure AD B2B Collaboration.
services: active-directory
author: msmimart
ms.author: mimart
manager: celestedg
ms.reviewer: mal
ms.date: 08/28/2018
ms.topic: quickstart
ms.service: active-directory
ms.subservice: B2B
ms.custom:
- it-pro
- seo-update-azuread-jan
- mode-api
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3541698e4a02f82f54d84eabf1bf7bb285cda10
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107987575"
---
# <a name="quickstart-add-a-guest-user-with-powershell"></a>Démarrage rapide : Ajouter un utilisateur invité avec PowerShell

Avec Azure Active Directory B2B Collaboration, vous disposez de plusieurs méthodes pour inviter des partenaires externes à utiliser vos applications et vos services. Dans le guide de démarrage rapide précédent, vous avez vu comment ajouter des utilisateurs invités directement dans le portail d’administration Azure Active Directory. Vous pouvez également utiliser PowerShell pour ajouter des utilisateurs invités, un par un, ou en bloc. Dans ce guide de démarrage rapide, vous allez utiliser la commande New-AzureADMSInvitation pour ajouter un utilisateur invité à votre locataire Azure.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 

## <a name="prerequisites"></a>Prérequis

### <a name="powershell-module"></a>Module PowerShell
Installez le [module Azure AD V2 PowerShell pour Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) ou la [préversion du module Azure AD V2 PowerShell pour Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

### <a name="get-a-test-email-account"></a>Obtenir un compte e-mail de test

Vous avez besoin d’un compte e-mail de test auquel vous pouvez envoyer l’invitation. Le compte doit être externe à votre organisation. Vous pouvez utiliser n’importe quel type de compte e-mail, y compris un compte de réseaux sociaux, comme gmail.com ou outlook.com.

## <a name="sign-in-to-your-tenant"></a>Connectez-vous à votre locataire

Exécutez la commande suivante pour vous connecter au domaine du locataire :

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```
Par exemple : `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`.

À l’invite, entrez vos informations d’identification.

## <a name="send-an-invitation"></a>Envoyer une invitation

1. Pour envoyer une invitation à votre compte e-mail de test, exécutez la commande PowerShell suivante (remplacez **"Sanda"** et **sanda\@fabrikam.com** par le nom de votre compte e-mail de test et par votre adresse e-mail) : 

   ```powershell
   New-AzureADMSInvitation -InvitedUserDisplayName "Sanda" -InvitedUserEmailAddress sanda@fabrikam.com -InviteRedirectURL https://myapps.microsoft.com -SendInvitationMessage $true
   ```
2. La commande envoie une invitation à l’adresse e-mail spécifiée. Regardez la sortie, qui doit être semblable à l’exemple qui suit :

   ![Sortie de PowerShell montrant l’acceptation d’un utilisateur en attente](media/quickstart-invite-powershell/powershell-azureadmsinvitation-result.png)

## <a name="verify-the-user-exists-in-the-directory"></a>Vérifier que l’utilisateur se trouve dans l’annuaire

1. Pour vérifier que l’utilisateur invité a bien été ajouté à Azure AD, exécutez la commande suivante :
 
   ```powershell
   Get-AzureADUser -Filter "UserType eq 'Guest'"
   ```
3. Examinez la sortie pour voir si l’utilisateur que vous avez invité y figure, avec un nom d’utilisateur principal (UPN) au format suivant : *adresse_e-mail*#EXT#\@*domaine*. Par exemple, *sanda_fabrikam.com#EXT#\@contoso.onmicrosoft.com*, où contoso.onmicrosoft.com est l’organisation à partir de laquelle vous avez envoyé les invitations.

   ![Sortie PowerShell montrant l’ajout d’un utilisateur invité](media/quickstart-invite-powershell/powershell-guest-user-added.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’en avez plus besoin, vous pouvez supprimer le compte d’utilisateur de test de l’annuaire. Exécutez la commande suivante pour supprimer un compte d’utilisateur :

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```
Par exemple : `Remove-AzureADUser -ObjectId "sanda_fabrikam.com#EXT#@contoso.onmicrosoft.com"`


## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez invité, puis ajouté un utilisateur invité à votre annuaire à l’aide de PowerShell. Ensuite, vous avez vu comment inviter des utilisateurs en bloc à l’aide de PowerShell.

> [!div class="nextstepaction"]
> [Tutoriel : Inviter en bloc des utilisateurs Azure AD B2B Collaboration](tutorial-bulk-invite.md)
