---
title: États des utilisateurs Azure multi-Factor Authentication - Azure Active Directory
description: Découvrez les états utilisateurs dans Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3928a47abf07ab7e6dad0e0a5883162363805df8
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235576"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>Comment exiger la vérification en deux étapes pour un utilisateur

Vous avez le choix entre deux approches pour exiger une vérification en deux étapes. Toutes deux nécessitent l’utilisation d’un compte d’administrateur général. La première option consiste à activer Azure Multi-Factor Authentication (MFA) pour chaque utilisateur. S’il est activé individuellement, l’utilisateur effectue la vérification en deux étapes chaque fois qu’il se connecte (à quelques exceptions près, notamment lorsqu’il se connecte à partir d’adresses IP approuvées ou que la fonctionnalité de _mémorisation des appareils_ est activée). La deuxième option consiste à définir une stratégie d’accès conditionnel qui requiert une vérification en deux étapes sous certaines conditions.

> [!TIP]
> Choisissez une de ces deux méthodes pour exiger la vérification en deux étapes, mais pas les deux. L’activation d’un utilisateur pour l’authentification multifacteur Azure remplace les stratégies d’accès conditionnel.

## <a name="choose-how-to-enable"></a>Choisir comment activer

**Activée en modifiant l’état de l’utilisateur** : il s’agit de la méthode traditionnelle pour exiger une vérification en deux étapes et elle est analysée dans cet article. Elle fonctionne avec Azure MFA dans le cloud et le serveur Azure MFA. À l’aide de cette méthode nécessite que les utilisateurs effectuer la vérification en deux étapes **chaque fois** connectez-vous, puis remplace les stratégies d’accès conditionnel. Il s’agit de la méthode utilisée pour ceux qui ont des licences Office 365 ou Microsoft 365 entreprise, car elles n’incluent pas les fonctionnalités d’accès conditionnel.

Activé par la stratégie d’accès conditionnel - il s’agit la méthode la plus souple pour activer la vérification en deux étapes pour vos utilisateurs. Activation à l’aide de la stratégie d’accès conditionnel fonctionne pour Azure MFA dans le cloud et une fonctionnalité premium d’Azure AD. Vous trouverez plus d’informations sur cette méthode dans [déployer Azure Multi-Factor Authentication basé sur le cloud](howto-mfa-getstarted.md).

Activée par Azure AD Identity Protection : cette méthode utilise la stratégie des risques Azure AD Identity Protection pour imposer la vérification en deux étapes basée uniquement sur le risque de connexion pour toutes les applications cloud. Cette méthode requiert une licence Azure Active Directory P2. Vous pourrez trouver plus d’informations sur cette méthode dans [Azure Active Directory Identity Protection](../identity-protection/howto-sign-in-risk-policy.md)

> [!Note]
> Vous trouverez plus d’informations sur les licences et la tarification sur les pages de tarification [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) et [Authentification multifacteur](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="enable-azure-mfa-by-changing-user-state"></a>Activer Azure MFA en changeant l’état de l’utilisateur

Les comptes d'utilisateur dans Azure Multi-Factor Authentication peuvent présenter les trois états suivants :

| Statut | Description | Applications affectées (autres que des navigateurs) | Applications du navigateur affectées | Authentification moderne affectée |
|:---:|:---:|:---:|:--:|:--:|
| Désactivé |État par défaut d’un nouvel utilisateur non inscrit à Azure MFA. |Non  |Non  |Non |
| activé |L’utilisateur a été inscrit dans l’authentification multifacteur Azure, mais n’a pas été enregistré. Il sera invité à s’inscrire la prochaine fois qu’il se connectera. |Non.  Ils continuent de fonctionner jusqu’à ce que le processus d’inscription soit terminé. | Oui. Après expiration de la session, l’inscription à Azure MFA est nécessaire.| Oui. Après expiration du jeton d’accès, l’inscription à Azure MFA est nécessaire. |
| Appliquée |L’utilisateur a été inscrit et a terminé le processus d’inscription pour utiliser l’authentification multifacteur Azure. |Oui. Les applications requièrent des mots de passe d'application. |Oui. Azure MFA est requis lors de la connexion. | Oui. Azure MFA est requis lors de la connexion. |

L’état d’un utilisateur indique si un administrateur l’a inscrit dans l’authentification multifacteur Azure et s’il a terminé le processus d’inscription.

Tous les utilisateurs commencent avec l’état *Désactivé*. Dès lors qu’ils sont inscrits à Azure MFA, leur état devient *Activé*. Lorsque les utilisateurs activés se connectent et suivent le processus d’inscription, leur état passe à *Appliqué*.  

### <a name="view-the-status-for-a-user"></a>Afficher l’état d’un utilisateur

Pour accéder à la page où vous pouvez afficher et gérer les états des utilisateurs, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.
2. Accédez à **Azure Active Directory** > **Utilisateurs et groupes** > **Tous les utilisateurs**.
3. Sélectionnez **Multi-Factor Authentication**.
   ![Sélectionnez Multi-Factor Authentication](./media/howto-mfa-userstates/selectmfa.png)
4. Une nouvelle page, qui affiche les états utilisateurs, s’ouvre.
   ![État utilisateur pour l’authentification multifacteur - capture d’écran](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Modifier l’état d’un utilisateur

1. Suivez les étapes précédentes pour accéder à la page **utilisateurs** d’Azure Multi-Factor Authentication.
2. Recherchez l’utilisateur pour lequel vous souhaitez activer Azure MFA. Vous devrez peut-être modifier l’affichage en haut de la page.
   ![Sélectionnez l’utilisateur pour modifier l’état d’à partir de l’onglet utilisateurs](./media/howto-mfa-userstates/enable1.png)
3. Cochez la case en regard du nom de l’utilisateur.
4. À droite, sous **étapes rapides**, cliquez sur **Activer** ou **Désactiver**.
   ![Activer l’utilisateur sélectionné en cliquant sur Activer dans le menu étapes rapides](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > Les utilisateurs *activés* basculent automatiquement vers l’état *Appliqué* quand ils s’inscrivent à Azure MFA. Ne définissez pas manuellement l’état utilisateur *Appliqué*.

5. Confirmez votre sélection dans la fenêtre contextuelle qui s’ouvre.

Dès que vous avez activé les utilisateurs, informez-les-en par e-mail. Informez-les qu’ils seront invités à s’inscrire la prochaine fois qu’ils se connectent. Par ailleurs, si votre organisation utilise des applications sans navigateur qui ne prennent pas en charge l’authentification moderne, vos utilisateurs devront créer des mots de passe d’application. Vous pouvez également inclure un lien vers le [guide de l’utilisateur final d’Azure MFA](../user-help/multi-factor-authentication-end-user.md) pour les aider à commencer.

### <a name="use-powershell"></a>Utiliser PowerShell

Pour modifier l’état utilisateur avec [Azure AD PowerShell](/powershell/azure/overview), modifiez `$st.State`. Il existe trois états possibles :

* activé
* Appliquée
* Désactivé  

Ne basculez pas les utilisateurs directement vers l’état *Appliquée*. Sinon, les applications sans navigateur cesseront de fonctionner, car l’utilisateur n’a pas effectué l’enregistrement Azure MFA et obtenu un [mot de passe d’application](howto-mfa-mfasettings.md#app-passwords).

Commencez par installer le module au moyen de la commande suivante :

   ```PowerShell
   Install-Module MSOnline
   ```

> [!TIP]
> N’oubliez pas de vous connecter d’abord au moyen de la commande **Connect-MsolService**.

Cet exemple de script PowerShell permet l’authentification multifacteur pour un utilisateur individuel :

   ```PowerShell
   Import-Module MSOnline
   $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
   $st.RelyingParty = "*"
   $st.State = "Enabled"
   $sta = @($st)
   Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
   ```

PowerShell est une bonne option si vous devez activer de nombreux utilisateurs à la fois. À titre d’exemple, le script suivant parcourt une liste d’utilisateurs et active l’authentification multifacteur sur leurs comptes :

   ```PowerShell
   $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
   foreach ($user in $users)
   {
       $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
       $st.RelyingParty = "*"
       $st.State = "Enabled"
       $sta = @($st)
       Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
   }
   ```

Pour désactiver l’authentification multifacteur, utilisez ce script :

   ```PowerShell
   Get-MsolUser -UserPrincipalName user@domain.com | Set-MsolUser -StrongAuthenticationRequirements @()
   ```

qui peut également être raccourci ainsi :

   ```PowerShell
   Set-MsolUser -UserPrincipalName user@domain.com -StrongAuthenticationRequirements @()
   ```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Les utilisateurs de convertir à partir de l’authentification Multifacteur par utilisateur à l’accès conditionnel en fonction de MFA

La commande PowerShell suivante peut vous aider à effectuer la conversion en fonction de l’accès conditionnel Azure multi-Factor Authentication.

```PowerShell
# Disable MFA for all users, keeping their MFA methods intact
Get-MsolUser -All | Disable-MFA -KeepMethods

# Enforce MFA for all users
Get-MsolUser -All | Set-MfaState -State Enforced

# Wrapper to disable MFA with the option to keep the MFA
# methods (to avoid having to proof-up again later)
function Disable-Mfa {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipeline=$True)]
        $User,
        [switch] $KeepMethods
    )

    Process {

        Write-Verbose ("Disabling MFA for user '{0}'" -f $User.UserPrincipalName)
        $User | Set-MfaState -State Disabled

        if ($KeepMethods) {
            # Restore the MFA methods which got cleared when disabling MFA
            Set-MsolUser -ObjectId $User.ObjectId `
                         -StrongAuthenticationMethods $User.StrongAuthenticationMethods
        }
    }
}

# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

```

## <a name="next-steps"></a>Étapes suivantes

* Qu’est-ce qui détermine qu’un utilisateur est ou n’est pas invité à effectuer une MFA ? Consultez la section [Rapport de connexion Azure AD du document Rapports dans Azure Multi-Factor Authentication](howto-mfa-reporting.md#azure-ad-sign-ins-report).
* Pour configurer des paramètres supplémentaires tels que les adresses IP approuvées, les messages vocaux personnalisés et les alertes de fraude, consultez l’article [Configurer les paramètres d’Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)
* Vous pouvez trouver des informations sur la gestion des paramètres utilisateur pour Azure Multi-Factor Authentication dans l’article [Gestion des paramètres utilisateur avec Azure Multi-Factor Authentication dans le cloud](howto-mfa-userdevicesettings.md)
