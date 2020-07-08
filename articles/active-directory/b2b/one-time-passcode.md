---
title: Authentification par code secret à usage unique pour les utilisateurs invités B2B - Azure AD
description: Comment utiliser le code secret à usage unique d’e-mail pour authentifier les utilisateurs invités B2B sans avoir besoin d’un compte Microsoft.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 714e4484c71b995bee186a2d94dc45c7ff82c50d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85551305"
---
# <a name="email-one-time-passcode-authentication-preview"></a>Authentification par code secret à usage unique d’e-mail (préversion)

> [!NOTE]
> Le code secret à usage unique d’e-mail est une fonctionnalité d’évaluation publique d’Azure Active Directory. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article décrit comment activer l’authentification par code secret à usage unique par e-mail pour les utilisateurs invités B2B. La fonctionnalité de code secret à usage unique d’e-mail authentifie les utilisateurs invités B2B lorsqu’ils ne peuvent pas être authentifiés via d’autres moyens, comme Azure AD, un compte Microsoft (MSA) ou la fédération Google. Avec l’authentification par code secret à usage unique, il est inutile de créer un compte Microsoft. Lorsque l’utilisateur invité accepte une invitation ou accède à une ressource partagée, il peut demander un code temporaire, qui est envoyé à son adresse e-mail. Puis, il entre ce code pour se connecter.

Cette fonctionnalité est actuellement disponible en préversion (consultez [Choix de la préversion](#opting-in-to-the-preview) ci-dessous). Après la préversion, cette fonctionnalité sera activée par défaut pour tous les locataires.

> [!NOTE]
> Les utilisateurs d'un code secret à usage unique doivent se connecter à l'aide d'un lien incluant le contexte du locataire (par exemple, `https://myapps.microsoft.com/?tenantid=<tenant id>` ou `https://portal.azure.com/<tenant id>`, ou dans le cas d'un domaine vérifié, `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`). Liens directs aux applications et ressources fonctionnent également tant qu’ils incluent le contexte client. Les utilisateurs invités ne peuvent actuellement pas se connecter à l’aide de points de terminaison qui n’ont aucun contexte locataire. Par exemple, l’utilisation de `https://myapps.microsoft.com`, de `https://portal.azure.com` ou du point de terminaison commun des équipes entraînera une erreur. 

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Expérience utilisateur pour les utilisateurs invités avec code secret à usage unique
Avec l’authentification par code secret à usage unique, l’utilisateur invité peut accepter votre invitation en cliquant sur un lien direct ou à l’aide de l’e-mail d’invitation. Dans les deux cas, un message dans le navigateur indique qu’un code sera envoyé à l’adresse e-mail de l’utilisateur invité. L’utilisateur invité sélectionne **Envoyer le code** :
 
   ![Capture d’écran montrant le bouton Envoyer le code](media/one-time-passcode/otp-send-code.png)
 
Un code secret est envoyé à l’adresse e-mail de l’utilisateur. L’utilisateur récupère le code secret à partir de l’e-mail et le saisit dans la fenêtre du navigateur :
 
   ![Capture d’écran montrant le bouton Entrer le code](media/one-time-passcode/otp-enter-code.png)
 
L’utilisateur invité est maintenant authentifié, et il peut voir la ressource partagée ou continuer à se connecter. 

> [!NOTE]
> Les codes secrets à usage unique sont valides pendant 30 minutes. Après 30 minutes, ce code secret à usage unique spécifique n’est plus valide, et l’utilisateur doit en demander un nouveau. Les sessions utilisateur expirent après 24 heures. Passée cette durée, l’utilisateur invité reçoit un nouveau code secret quand ils accèdent à la ressource. L’expiration de la session garantit plus de sécurité, particulièrement quand un utilisateur invité quitte l’entreprise ou n’a plus besoin d’accéder à la ressource.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Quand un utilisateur invité obtient-il un code secret à usage unique ?

Quand un utilisateur invité accepte une invitation ou utilise un lien vers une ressource qui a été partagée avec lui, il va recevoir un code secret à usage unique dans les cas suivants :
- Il n’a pas de compte Azure AD 
- Il n’a pas de compte Microsoft 
- Le locataire à l’origine de l’invitation n’a pas configuré la fédération Google pour les utilisateurs @gmail.com et @googlemail.com 

Au moment de l’invitation, rien n’indique que l’utilisateur que vous invitez devra utiliser l’authentification par code secret à usage unique. Mais lorsque l’utilisateur invité se connecte, l’authentification par code secret à usage unique est la méthode de secours si aucune autre méthode d’authentification ne peut être utilisée. 

Vous pouvez afficher les utilisateurs invités qui s’authentifient à l’aide de codes secrets à usage unique dans le portail Azure en accédant à **Azure Active Directory** > **Utilisateurs**.

![Capture d’écran montrant un utilisateur de code secret à usage unique avec la valeur Source définie sur Code secret à usage unique](media/one-time-passcode/otp-users.png)

> [!NOTE]
> Lorsqu’un utilisateur accepte un code secret à usage unique, puis obtient un compte Azure AD, MSA, ou un autre compte fédéré, il continue d’être authentifié à l’aide d’un code secret à usage unique. Si vous souhaitez mettre à jour leur méthode d’authentification, vous pouvez supprimer le compte d’utilisateur invité et les inviter à nouveau.

### <a name="example"></a>Exemple
L’utilisateur invité alexdoe@gmail.com est invité sur Fabrikam, qui n’a pas de fédération Google configurée. Alex n’a pas de compte Microsoft. Il va recevoir un code secret à usage unique pour s’authentifier.

## <a name="opting-in-to-the-preview"></a>Choix de la préversion 
Quelques minutes peuvent être nécessaires à la validation du choix. Après cela, seuls les utilisateurs nouvellement invités qui respectent les conditions mentionnées ci-dessus utiliseront l’authentification par code secret à usage unique. Les utilisateurs invités qui ont accepté une invitation par le passé continueront d’utiliser la même méthode d’authentification.

### <a name="to-opt-in-using-the-azure-ad-portal"></a>Pour choisir à l’aide du portail Azure AD
1.  Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général Azure AD.
2.  Dans le volet de navigation, sélectionnez **Azure Active Directory**.
3.  Sélectionnez **Identités externes** > **Paramètres de collaboration externe**.
5.  Sous **Activer le mot de passe e-mail à usage unique pour les invités (préversion)** , sélectionnez **Oui**.
 
### <a name="to-opt-in-using-powershell"></a>Pour choisir à l’aide de PowerShell

Vous allez devoir commencer par installer la dernière version du module Azure AD PowerShell for Graph (AzureADPreview). Puis vous allez déterminer si les stratégies B2B existent déjà et exécuter les commandes appropriées.

#### <a name="prerequisite-install-the-latest-azureadpreview-module"></a>Condition préalable : Installez la dernière version du module AzureADPreview.
Vérifiez d’abord les modules que vous avez installés. Ouvrez Windows PowerShell en tant qu’utilisateur avec privilèges élevés (Exécuter en tant qu’administrateur) et exécutez la commande suivante :
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

Si le module AzureADPreview s’affiche sans un message indiquant qu’une version plus récente existe, tout est prêt. Sinon, en fonction de la sortie, effectuez l’une des actions suivantes :

- Si aucun résultat n’est retourné, exécutez la commande suivante pour installer le module AzureADPreview :
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Si seul le module AzureAD apparaît dans les résultats, exécutez les commandes suivantes pour installer le module AzureADPreview : 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Si seul le module AzureADPreview apparaît dans les résultats, mais que vous recevez un message indiquant qu’il existe une version plus récente, exécutez les commandes suivantes pour mettre à jour le module : 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
  ```

Vous pouvez recevoir une invite indiquant que vous installez le module à partir d’un dépôt non approuvé. Ce cas se produit si vous n’aviez pas configuré le dépôt PSGallery comme dépôt fiable. Appuyez sur **O** pour installer le module.

#### <a name="check-for-existing-policies-and-opt-in"></a>Rechercher les stratégies existantes et choisir

Ensuite, vérifiez si une stratégie B2BManagementPolicy existe actuellement en exécutant la commande suivante :

```powershell 
$currentpolicy =  Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
$currentpolicy -ne $null
```
- Si le résultat est False, la stratégie n’existe pas. Créer une stratégie B2BManagementPolicy et choisissez la version préliminaire en exécutant la commande suivante :

   ```powershell 
   $policyValue=@("{`"B2BManagementPolicy`":{`"PreviewPolicy`":{`"Features`":[`"OneTimePasscode`"]}}}")
   New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true
   ```

- Si le résultat est True, la stratégie B2BManagementPolicy existe. Pour mettre à jour la stratégie et choisir la version préliminaire, exécutez la commande suivante :
  
   ```powershell 
   $policy = $currentpolicy.Definition | ConvertFrom-Json
   $features=[PSCustomObject]@{'Features'=@('OneTimePasscode')}; $policy.B2BManagementPolicy | Add-Member 'PreviewPolicy' $features -Force; $policy.B2BManagementPolicy
   $updatedPolicy = $policy | ConvertTo-Json -Depth 3
   Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
   ```

## <a name="opting-out-of-the-preview-after-opting-in"></a>Refuser la préversion après l’avoir choisie
Quelques minutes peuvent être nécessaires à la validation du refus. Si vous désactivez la préversion, les utilisateurs invités qui ont accepté un code secret à usage unique ne pourront plus se connecter. Vous pouvez supprimer l’utilisateur invité et réinvitez l’utilisateur pour lui permettre de se connecter à l’aide d’une autre méthode d’authentification.

### <a name="to-turn-off-the-preview-using-the-azure-ad-portal"></a>Pour désactiver la préversion à l’aide du portail Azure AD
1.  Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général Azure AD.
2.  Dans le volet de navigation, sélectionnez **Azure Active Directory**.
3.  Sélectionnez **Identités externes** > **Paramètres de collaboration externe**.
5.  Sous **Activer le mot de passe e-mail à usage unique pour les invités (préversion)** , sélectionnez **Non**.

### <a name="to-turn-off-the-preview-using-powershell"></a>Pour désactiver la préversion à l’aide de PowerShell
Installez le dernier module AzureADPreview si vous ne l’avez pas déjà fait (consultez [Configuration requise : Installez la dernière version du module AzureADPreview](#prerequisite-install-the-latest-azureadpreview-module) ci-dessus). Ensuite, vérifiez que la stratégie de préversion de code secret à usage unique existe en exécutant la commande suivante :

```powershell 
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
($currentPolicy -ne $null) -and ($currentPolicy.Definition -like "*OneTimePasscode*")
```

Si le résultat est True, refusez la préversion en exécutant la commande suivante :

```powershell 
$policy = $currentpolicy.Definition | ConvertFrom-Json
$policy.B2BManagementPolicy.PreviewPolicy.Features = $policy.B2BManagementPolicy.PreviewPolicy.Features.Where({$_ -ne "OneTimePasscode"})
$updatedPolicy = $policy | ConvertTo-Json -Depth 3
Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
```

