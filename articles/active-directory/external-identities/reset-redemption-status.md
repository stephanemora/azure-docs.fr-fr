---
title: Réinitialisation de l’état d’acceptation d’un utilisateur invité – Azure AD
description: Découvrez comment réinitialiser l’état d’acceptation d’invitation d’un utilisateur invité Azure Active Directory B2B dans Azure AD External Identities.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc51a8af85b85db28265d1fca420a5b9b368b078
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576851"
---
# <a name="reset-redemption-status-for-a-guest-user"></a>Réinitialisation de l’état d’acceptation d’un utilisateur invité

Il peut arriver, une fois qu’un utilisateur invité a accepté votre invitation à B2B Collaboration, que vous deviez mettre à jour ses informations de connexion, par exemple dans les cas suivants :

- L’utilisateur souhaite se connecter avec une autre adresse e-mail et un autre fournisseur d’identité.
- Le compte de l’utilisateur a été supprimé et recréé dans son locataire.
- L’utilisateur se trouve maintenant dans une autre société, mais il a toujours besoin du même accès à vos ressources.
- Les responsabilités de l’utilisateur ont été transmises à un autre utilisateur.

Pour gérer ces scénarios, il fallait auparavant supprimer manuellement du répertoire le compte de l’utilisateur invité et réinviter ce dernier. Vous pouvez maintenant utiliser PowerShell ou l’API d’invitation Microsoft Graph pour réinitialiser l’état d’acceptation de l’utilisateur et le réinviter tout en conservant son ID objet, ses appartenances aux groupes et ses affectations d’applications. Lorsque l’utilisateur accepte la nouvelle invitation, la nouvelle adresse e-mail devient son nom UPN. L’utilisateur peut ensuite se connecter avec la nouvelle adresse e-mail ou une adresse e-mail que vous avez ajoutée à la propriété `otherMails` de l’objet utilisateur.

## <a name="use-powershell-to-reset-redemption-status"></a>Réinitialisation de l’état d’acceptation avec PowerShell

Installez le dernier module PowerShell AzureADPreview et créez une invitation avec `InvitedUserEMailAddress` défini sur la nouvelle adresse e-mail et `ResetRedemption` sur `true`.

```powershell  
Uninstall-Module AzureADPreview 
Install-Module AzureADPreview 
Connect-AzureAD 
$ADGraphUser = Get-AzureADUser -objectID "UPN of User to Reset"  
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId 
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser -ResetRedemption $True 
```

## <a name="use-microsoft-graph-api-to-reset-redemption-status"></a>Réinitialisation de l’état d’acceptation avec l’API Microsoft Graph

Avec [l’API d’invitation Microsoft Graph](/graph/api/resources/invitation?view=graph-rest-1.0), définissez la propriété `resetRedemption` sur `true` et spécifiez la nouvelle adresse e-mail dans la propriété `invitedUserEmailAddress`.

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
