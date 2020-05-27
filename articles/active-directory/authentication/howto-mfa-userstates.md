---
title: Activer Multi-Factor Authentication - Azure Active Directory
description: Découvrez comment activer Multi-Factor Authentication par utilisateur en modifiant l’état utilisateur
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0db72e30fbced17665c112ad56510d7c2ca23d12
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83639619"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>Activer Multi-Factor Authentication par utilisateur pour sécuriser les événements de connexion

Il existe deux façons de sécuriser les événements de connexion de l’utilisateur en exigeant Multi-Factor Authentication dans Azure AD. La première option (recommandée) consiste à définir une stratégie d’accès conditionnel qui requiert une authentification multifacteur sous certaines conditions. La seconde option consiste à activer Azure Multi-Factor Authentication (MFA) pour chaque utilisateur. S’il est activé individuellement, l’utilisateur effectue l’authentification multifacteur chaque fois qu’il se connecte (à quelques exceptions près, notamment lorsqu’il se connecte à partir d’adresses IP approuvées ou que la fonctionnalité de _mémorisation des appareils_ est activée).

> [!NOTE]
> Il est recommandé d’activer Microsoft Azure Multi-Factor Authentication à l’aide de stratégies d’accès conditionnel. Il n’est plus conseillé de modifier des états utilisateur, sauf si vos licences n’incluent pas l’accès conditionnel, car cela oblige les utilisateurs à effectuer une authentification multifacteur chaque fois qu’ils se connectent.
>
> Pour commencer à utiliser l’accès conditionnel, consultez [Tutoriel : Événements de connexion utilisateur sécurisée avec Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md).

## <a name="azure-multi-factor-authentication-user-states"></a>Azure Multi-Factor Authentication - État utilisateur

Les comptes d'utilisateur dans Azure Multi-Factor Authentication peuvent présenter les trois états suivants :

> [!IMPORTANT]
> L’activation d’Azure Multi-Factor Authentication via une stratégie d’accès conditionnel ne modifie pas l’état de l’utilisateur. Ne soyez pas surpris si les utilisateurs sont signalés comme étant désactivés. L’accès conditionnel ne change pas l’état.
>
> **Vous ne devriez pas activer ou appliquer les utilisateurs si vous utilisez des stratégies d’accès conditionnel.**

| Statut | Description | Applications affectées (autres que des navigateurs) | Applications du navigateur affectées | Authentification moderne affectée |
|:---:| --- |:---:|:--:|:--:|
| Désactivé | État par défaut d’un nouvel utilisateur non inscrit dans Azure Multi-Factor Authentication. | Non | Non | Non |
| activé | L'utilisateur ne s’est pas inscrit à Azure Multi-Factor Authentication. Il sera invité à s’inscrire la prochaine fois qu’il se connectera. | Non.  Ils continuent de fonctionner jusqu’à ce que le processus d’inscription soit terminé. | Oui. Après expiration de la session, l’inscription à Azure Multi-Factor Authentication est nécessaire.| Oui. Après expiration du jeton d’accès, l’inscription à Azure Multi-Factor Authentication est nécessaire. |
| Appliquée | L'utilisateur a été inscrit et a terminé le processus d'inscription pour Azure Multi-Factor Authentication. | Oui. Les applications requièrent des mots de passe d'application. | Oui. Azure Multi-Factor Authentication est requise à la connexion. | Oui. Azure Multi-Factor Authentication est requise à la connexion. |

L’état d’un utilisateur indique si un administrateur l’a inscrit dans Azure Multi-Factor Authentication et s’il a terminé le processus d’inscription.

Tous les utilisateurs commencent avec l’état *Désactivé*. Dès lors qu’ils sont inscrits à Azure Multi-Factor Authentication, leur état devient *Activé*. Lorsque les utilisateurs activés se connectent et suivent le processus d’inscription, leur état passe à *Appliqué*.

> [!NOTE]
> Si l’authentification multifacteur (MFA) est réactivée sur un objet utilisateur qui a déjà les détails de l’inscription, comme le téléphone ou l’e-mail, les administrateurs doivent demander à cet utilisateur de se réinscrire avec MFA par le biais du portail Azure ou de PowerShell. Si l’utilisateur ne se réinscrit pas, son état MFA ne passe pas de *Activé* à *Appliqué* dans l’interface utilisateur de la gestion MFA.

## <a name="view-the-status-for-a-user"></a>Afficher l’état d’un utilisateur

Pour accéder à la page du portail Azure où vous pouvez afficher et gérer les états des utilisateurs, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.
1. Recherchez et sélectionnez *Azure Active Directory*, puis choisissez **Utilisateurs** > **Tous les utilisateurs**.
1. Sélectionnez **Multi-Factor Authentication**. Il se peut que vous deviez faire défiler vers la droite pour afficher cette option de menu. Sélectionnez l’exemple de capture d’écran ci-dessous pour afficher entièrement la fenêtre du portail Azure et l’emplacement du menu : [![](media/howto-mfa-userstates/selectmfa-cropped.png "Sélectionnez Authentification multifacteur dans la fenêtre Utilisateurs d’Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Une nouvelle page s’ouvre et affiche l’état de l’utilisateur, comme illustré dans l’exemple suivant.
   ![Capture d’écran montrant des exemples d’informations d’état utilisateur pour Azure Multi-Factor Authentication](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Modifier l’état d’un utilisateur

Pour modifier l’état Azure Multi-Factor Authentication pour un utilisateur, procédez comme suit :

1. Suivez les étapes précédentes pour accéder à la page **utilisateurs** d’Azure Multi-Factor Authentication.
1. Recherchez l'utilisateur que vous souhaitez activer pour Azure Multi-Factor Authentication. Vous devrez peut-être modifier l’affichage en haut de la page afin d’afficher les **utilisateurs**.
   ![Sélectionner l’utilisateur dont l’état doit être modifié dans l’onglet Utilisateurs](./media/howto-mfa-userstates/enable1.png)
1. Activez la case à cocher en regard des noms des utilisateurs dont vous souhaitez modifier l’état.
1. Sur la partie droite, sous **étapes rapides**, cliquez sur **Activer** ou **Désactiver**. Dans l’exemple suivant, l’utilisateur *John Smith* affiche une coche en regard de son nom et il est autorisé à utiliser : ![Activer l’utilisateur sélectionné en cliquant sur Activer dans le menu étapes rapides](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > Les utilisateurs *activés* basculent automatiquement vers l’état *Appliqué* quand ils s’inscrivent à Azure Multi-Factor Authentication. Ne définissez pas manuellement l’état utilisateur *Appliqué*.

1. Confirmez votre sélection dans la fenêtre contextuelle qui s’ouvre.

Dès que vous avez activé les utilisateurs, informez-les-en par e-mail. Informez les utilisateurs qu’une invite s’affiche et leur demande de s’inscrire la prochaine fois qu’ils se connectent. Par ailleurs, si votre organisation utilise des applications sans navigateur qui ne prennent pas en charge l’authentification moderne, vos utilisateurs devront créer des mots de passe d’application. Pour plus d’informations, consultez le [guide de l’utilisateur final sur Azure Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user.md) pour les aider à commencer.

## <a name="change-state-using-powershell"></a>Modifier l’état à l’aide de PowerShell

Pour modifier l’état utilisateur avec [Azure AD PowerShell](/powershell/azure/overview), modifiez le paramètre `$st.State` pour un compte d’utilisateur. Il existe trois états possibles pour un compte d'utilisateur :

* *Activé*
* *Appliqué*
* *Désactivé*  

Ne basculez pas les utilisateurs directement vers l’état *Appliquée*. Sinon, les applications sans navigateur cesseront de fonctionner, car l’utilisateur n’a pas effectué l’enregistrement Azure Multi-Factor Authentication et obtenu un [mot de passe d’application](howto-mfa-mfasettings.md#app-passwords).

Pour commencer, installez le module *MSOnline* à l’aide de [Install-Module](/powershell/module/powershellget/install-module), comme suit :

```PowerShell
Install-Module MSOnline
```

Puis connectez-vous à l’aide de [Connect-MsolService](/powershell/module/msonline/connect-msolservice) :

```PowerShell
Connect-MsolService
```

L’exemple de script PowerShell suivant active l’authentification multifacteur pour un utilisateur individuel nommé *bsimon@contoso.com* :

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

PowerShell est une bonne option si vous devez activer de nombreux utilisateurs à la fois. Le script suivant parcourt une liste d’utilisateurs et active l’authentification multifacteur sur leurs comptes. Définissez les comptes d’utilisateur sur la première ligne de `$users`, comme suit :

   ```PowerShell
   # Define your list of users to update state in bulk
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

Pour désactiver l’authentification multifacteur, l’exemple suivant obtient un utilisateur avec [Get-MsolUser](/powershell/module/msonline/get-msoluser), puis supprime toute valeur *StrongAuthenticationRequirements* définie pour l’utilisateur à l’aide de [Set-MsolUser](/powershell/module/msonline/set-msoluser) :

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

Vous pouvez également désactiver directement l’authentification multifacteur pour un utilisateur à l’aide de [Set-MsolUser](/powershell/module/msonline/set-msoluser), comme suit :

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Convertir des utilisateurs de l’authentification multifacteur par utilisateur à l’authentification multifacteur basée sur l’accès conditionnel

La commande PowerShell suivante peut vous aider à effectuer cette conversion.

```PowerShell
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

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Nous avons récemment modifié le comportement et ce script PowerShell. Auparavant, le script enregistrait les méthodes MFA, désactivait l’authentification MFA et restaurait les méthodes. Ce n’est plus nécessaire maintenant que le comportement par défaut de désactivation n’efface plus les méthodes.
>
> Si l’authentification multifacteur (MFA) est réactivée sur un objet utilisateur qui a déjà les détails de l’inscription, comme le téléphone ou l’e-mail, les administrateurs doivent demander à cet utilisateur de se réinscrire avec MFA par le biais du portail Azure ou de PowerShell. Si l’utilisateur ne se réinscrit pas, son état MFA ne passe pas de *Activé* à *Appliqué* dans l’interface utilisateur de la gestion MFA.

## <a name="next-steps"></a>Étapes suivantes

Pour configurer des paramètres supplémentaires Azure Multi-Factor Authentication tels que les adresses IP approuvées, les messages vocaux personnalisés et les alertes de fraude, consultez [Configurer les paramètres d’Azure Multi-Factor Authentication](howto-mfa-mfasettings.md). Pour gérer les paramètres utilisateur pour Azure Multi-Factor Authentication, consultez [Gestion des paramètres utilisateur avec Azure Multi-Factor Authentication](howto-mfa-userdevicesettings.md).

Afin de comprendre la raison pour laquelle un utilisateur est invité ou non à effectuer une authentification multifacteur, consultez [Rapports Azure Multi-Factor Authentication](howto-mfa-reporting.md).
