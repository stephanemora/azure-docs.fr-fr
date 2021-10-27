---
title: Réinitialisation de l’état d’acceptation d’un utilisateur invité – Azure AD
description: Découvrez comment réinitialiser l’état d’acceptation d’invitation d’un utilisateur invité Azure Active Directory B2B dans Azure AD External Identities.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 10/12/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e19c4e6e13f4abcb8143f789dc1a7aa8de47e82
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129998957"
---
# <a name="reset-redemption-status-for-a-guest-user-preview"></a>Réinitialiser l’état d’acceptation d’un utilisateur invité (préversion)

Il peut arriver, une fois qu’un utilisateur invité a accepté votre invitation à B2B Collaboration, que vous deviez mettre à jour ses informations de connexion, par exemple dans les cas suivants :

- L’utilisateur souhaite se connecter avec une autre adresse e-mail et un autre fournisseur d’identité.
- Le compte de l’utilisateur a été supprimé et recréé dans son locataire.
- L’utilisateur se trouve maintenant dans une autre société, mais il a toujours besoin du même accès à vos ressources.
- Les responsabilités de l’utilisateur ont été transmises à un autre utilisateur.

Pour gérer ces scénarios, il fallait auparavant supprimer manuellement du répertoire le compte de l’utilisateur invité et réinviter ce dernier. Vous pouvez maintenant utiliser PowerShell ou l’API d’invitation Microsoft Graph pour réinitialiser l’état d’acceptation de l’utilisateur et le réinviter tout en conservant son ID d’objet, ses appartenances aux groupes et ses affectations d’applications. Lorsque l’utilisateur accepte la nouvelle invitation, l’UPN de l’utilisateur ne change pas, mais le nom de connexion de l’utilisateur change pour devenir la nouvelle adresse e-mail. L’utilisateur peut ensuite se connecter avec la nouvelle adresse e-mail ou une adresse e-mail que vous avez ajoutée à la propriété `otherMails` de l’objet utilisateur.

## <a name="use-the-azure-portal-to-reset-redemption-status"></a>Utiliser le portail Azure pour réinitialiser l’état d’acceptation

1. Connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide d’un compte Administrateur général ou d’un compte Administrateur d’utilisateurs pour le répertoire.
1. Recherchez et sélectionnez **Azure Active Directory**.
1. Sélectionnez **Utilisateurs**.
1. Dans la liste, sélectionnez le nom d’utilisateur pour ouvrir le profil de l’utilisateur.
1. Si l’utilisateur souhaite se connecter avec un autre e-mail :
   - Sélectionnez l’icône **Modifier** en haut de la page.
   - Dans la section **Informations de contact**, sous **E-mail**, tapez le nouvel e-mail.
   - À côté de la section **Adresse e-mail alternative**, sélectionnez **Modifier**. Mettez à jour l’adresse de messagerie alternative dans la liste avec le nouvel e-mail, puis sélectionnez **Mise à jour**.
   - Sélectionnez l’icône **Enregistrer** en haut de la page.
1. Dans la section **Identité**, sous **Invitation acceptée**, sélectionnez **(gérer)** .
1. Sous **État d’acceptation**, à côté de  **Réinitialiser le statut de l’invitation ? (préversion)** , sélectionnez **Oui**.
1. Sélectionnez **Oui** pour confirmer.


## <a name="use-powershell-or-microsoft-graph-api-to-reset-redemption-status"></a>Utilisez PowerShell ou l’API Microsoft Graph pour réinitialiser l’état d’acceptation

### <a name="reset-the-email-address-used-for-sign-in"></a>Réinitialiser l’adresse e-mail utilisée pour la connexion

Si un utilisateur souhaite se connecter avec un autre e-mail :

1. Assurez-vous que la nouvelle adresse e-mail est ajoutée à la propriété `mail` ou `otherMails` de l’objet utilisateur. 
1.  Remplacez l’adresse e-mail dans la propriété `InvitedUserEmailAddress` par la nouvelle adresse e-mail.
1. Utilisez l’une des méthodes ci-dessous pour réinitialiser l’état d’acceptation de l’utilisateur.

> [!NOTE]
>Pendant la préversion publique, nous avons deux recommandations :
>- Lorsque vous redéfinissez l’adresse de messagerie de l’utilisateur sur une nouvelle adresse, nous vous recommandons de définir la propriété `mail`. Ainsi, l’utilisateur peut accepter l’invitation en se connectant à votre annuaire en plus de l’utilisation du lien d’échange dans l’invitation.
>- Lorsque vous réaffectez l’état d’un utilisateur invité B2B, veillez à le faire sous le contexte de l’utilisateur. Les appels d’application uniquement ne sont actuellement pas pris en charge.
>
### <a name="use-powershell-to-reset-redemption-status"></a>Réinitialisation de l’état d’acceptation avec PowerShell

Installez le dernier module PowerShell AzureADPreview et créez une invitation avec `InvitedUserEmailAddress` défini sur la nouvelle adresse e-mail et `ResetRedemption` sur `true`.

```powershell  
Uninstall-Module AzureADPreview 
Install-Module AzureADPreview 
Connect-AzureAD 
$ADGraphUser = Get-AzureADUser -objectID "UPN of User to Reset"  
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId 
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser -ResetRedemption $True 
```

### <a name="use-microsoft-graph-api-to-reset-redemption-status"></a>Réinitialisation de l’état d’acceptation avec l’API Microsoft Graph

Avec [l’API d’invitation Microsoft Graph](/graph/api/resources/invitation?view=graph-rest-beta&preserve-view=true), définissez la propriété `resetRedemption` sur `true` et spécifiez la nouvelle adresse e-mail dans la propriété `invitedUserEmailAddress`.

```json
POST https://graph.microsoft.com/beta/invitations  
Authorization: Bearer eyJ0eX...  
ContentType: application/json  
{  
   "invitedUserEmailAddress": "<<external email>>",  
   "sendInvitationMessage": true,  
   "invitedUserMessageInfo": {  
      "messageLanguage": "en-US",  
      "ccRecipients": [  
         {  
            "emailAddress": {  
               "name": null,  
               "address": "<<optional additional notification email>>"  
            }  
         } 
      ],  
      "customizedMessageBody": "<<custom message>>"  
},  
"inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",  
"invitedUser": {  
   "id": "<<ID for the user you want to reset>>"  
}, 
"resetRedemption": true 
}
```

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter des utilisateurs Azure Active Directory B2B Collaboration à l’aide de PowerShell](customize-invitation-api.md#powershell)
- [Propriétés d’un utilisateur invité Azure AD B2B](user-properties.md)
