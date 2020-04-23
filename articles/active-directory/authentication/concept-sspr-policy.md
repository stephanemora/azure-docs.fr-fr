---
title: Stratégies de réinitialisation de mot de passe libre-service - Azure Active Directory
description: En savoir plus sur les différentes options de stratégie de réinitialisation de mot de passe en libre-service Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f5987bee82dc22d3742cb5d87040930e5d2c52d
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393029"
---
# <a name="self-service-password-reset-policies-and-restrictions-in-azure-active-directory"></a>Stratégies et restrictions de réinitialisation de mot de passe en libre-service dans Azure Active Directory

Cet article décrit les stratégies de mot de passe et les exigences en matière de complexité associées aux comptes d’utilisateur sur votre locataire Azure Active Directory (Azure AD).

## <a name="administrator-reset-policy-differences"></a>Différences en matière de stratégie de réinitialisation par l’administrateur

**Microsoft applique par défaut une stratégie de réinitialisation de mot de passe fort à *deux verrous* pour tous les rôles d’administrateur Azure**. Cette stratégie peut être différente de celle que vous avez définie pour vos utilisateurs et ne peut pas être modifiée. Vous devez toujours tester la fonctionnalité de réinitialisation de mot de passe en tant qu’utilisateur, sans qu’un rôle d’administrateur Azure vous soit affecté.

Avec une stratégie à deux verrous, **les administrateurs n’ont pas la possibilité d’utiliser des questions de sécurité**.

La stratégie à deux verrous nécessite deux éléments de données d’authentification, par exemple une *adresse e-mail*, une *application d’authentificateur* ou un *numéro de téléphone*. Une stratégie à deux verrous s’applique dans les conditions suivantes :

* Tous les rôles d’administrateur suivants sont concernés :
  * Administrateur du support technique
  * Administrateur de support de service
  * Administrateur de facturation
  * Prise en charge de niveau 1 de partenaire
  * Prise en charge de niveau 2 de partenaire
  * Administrateur Exchange
  * Administrateur Skype Entreprise
  * Administrateur d’utilisateurs
  * Enregistreurs de répertoire
  * Administrateur général ou administrateur d’entreprise
  * Administrateur SharePoint
  * Administrateur de conformité
  * Administrateur d’application
  * Administrateur de sécurité
  * Administrateur de rôle privilégié
  * Administrateur Intune
  * Administrateur de services de proxy d’application
  * Administrateur Dynamics 365
  * Administrateur de services Power BI
  * Administrateur d’authentification
  * Administrateur d’authentification privilégié

* Si 30 jours se sont écoulés dans un abonnement d’essai ; ou
* Un domaine personnalisé a été configuré pour votre locataire Azure AD, par exemple *contoso.com* ; ou
* Azure AD Connect synchronise les identités à partir de votre répertoire local

### <a name="exceptions"></a>Exceptions

Une stratégie à un verrou nécessite un élément de données d’authentification, par exemple une adresse de messagerie ou un numéro de téléphone. Une stratégie à un verrou s’applique dans les conditions suivantes :

* Pendant les 30 premiers jours d’un abonnement d’essai ; ou
* Un domaine personnalisé n’a pas été configuré pour votre locataire Azure AD. Celui-ci utilise donc le domaine par défaut * *.onmicrosoft.com*. L’utilisation du domaine par défaut * *.onmicrosoft.com* n’est pas recommandée dans un environnement de production ; et
* Azure AD Connect ne synchronise pas les identités

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Stratégies UserPrincipalName s'appliquant à tous les comptes d'utilisateur

Chaque compte d’utilisateur devant se connecter à Azure AD doit être doté d’une valeur d’attribut unique de nom d’utilisateur principal (UPN) associée à son compte. Le tableau suivant décrit les stratégies qui s’appliquent aux comptes d’utilisateur Active Directory Domain Services locaux qui sont synchronisés dans le cloud et aux comptes d’utilisateur uniquement dans le cloud :

| Propriété | Conditions requises pour UserPrincipalName |
| --- | --- |
| Caractères autorisés |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Caractères non autorisés |<ul> <li>Tout caractère « \@ \"» qui ne sépare pas le nom d’utilisateur du domaine.</li> <li>Ne peut pas contenir un point « . » précédant immédiatement le symbole « \@\" ».</li></ul> |
| Contraintes de longueur |<ul> <li>La longueur totale ne doit pas dépasser 113 caractères</li><li>Jusqu’à 64 caractères avant le symbole « \@\" ».</li><li>Jusqu’à 48 caractères après le symbole « \@\" ».</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Stratégies de mot de passe s’appliquant uniquement aux comptes d'utilisateur dans le cloud

Le tableau suivant décrit les paramètres de stratégie de mot de passe appliqués aux comptes d'utilisateurs créés et gérés dans Azure AD :

| Propriété | Spécifications |
| --- | --- |
| Caractères autorisés |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ' , . ? / \` ~ " ( ) ;</li> <li>espace vide</li></ul> |
| Caractères non autorisés | Caractères Unicode. |
| Restrictions de mot de passe |<ul><li>8 caractères minimum et 256 caractères maximum.</li><li>trois des quatre éléments suivants sont requis :<ul><li>Caractères minuscules.</li><li>Caractères majuscules.</li><li>Nombres (0-9).</li><li>Symboles (voir les restrictions de mot de passe précédentes).</li></ul></li></ul> |
| Durée d’expiration du mot de passe (âge maximum du mot de passe) |<ul><li>Valeur par défaut : **90** jours.</li><li>La valeur peut être configurée à l’aide de l’applet de commande `Set-MsolPasswordPolicy` à partir du module Azure Active Directory pour Windows PowerShell.</li></ul> |
| Notification d’expiration du mot de passe (lorsque les utilisateurs sont avertis de l’expiration du mot de passe) |<ul><li>Valeur par défaut : **14** jours (avant l’expiration du mot de passe).</li><li>La valeur peut être configurée à l’aide de l’applet de commande `Set-MsolPasswordPolicy`.</li></ul> |
| Expiration du mot de passe (empêche le mot de passe d’expirer) |<ul><li>Valeur par défaut : **false** (indique que le mot de passe a une date d’expiration).</li><li>La valeur peut être configurée pour des comptes d’utilisateur individuels à l’aide de l’applet de commande `Set-MsolUser`.</li></ul> |
| Historique de modification du mot de passe | Le dernier mot de passe *ne peut pas* être réutilisé lorsque l’utilisateur modifie un mot de passe. |
| Historique de réinitialisation du mot de passe | Le dernier mot de passe *peut* être réutilisé lorsque l’utilisateur réinitialise un mot de passe oublié. |
| Verrouillage de compte | Au bout de 10 tentatives de connexion infructueuses avec un mot de passe incorrect, l’utilisateur est bloqué pendant une minute. La durée de blocage de l’utilisateur augmente au fil des nouvelles tentatives de connexion incorrectes. Le [verrouillage intelligent](howto-password-smart-lockout.md) suit les trois derniers hachages de mots de passe incorrects afin d'éviter d'incrémenter le compteur de verrouillages pour le même mot de passe. Si un utilisateur entre plusieurs fois le même mot de passe incorrect, le compte n'est pas verrouillé. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Définir des stratégies d’expiration de mot de passe dans Azure AD

Un *administrateur global* ou un *administrateur d’utilisateurs* d’un service cloud Microsoft peut utiliser le *module Microsoft Azure AD pour Windows PowerShell* afin de définir des mots de passe utilisateur qui n’expirent pas. Vous pouvez également utiliser des applets de commande Windows PowerShell pour supprimer la configuration de non-expiration ou pour voir quels mots de passe utilisateur sont définis pour ne jamais expirer.

Ces conseils s’appliquent à d’autres fournisseurs, tels que Intune et Office 365, qui s’appuient également sur Azure AD pour les services d’identité et d’annuaire. L’expiration du mot de passe est la seule partie de la stratégie qui peut être modifiée.

> [!NOTE]
> Seuls les mots de passe de comptes d’utilisateurs non synchronisés via une synchronisation d’annuaires peuvent être configurés pour ne pas expirer. Pour plus d’informations sur la synchronisation d’annuaires, consultez [Connecter AD à Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Définir ou vérifier les stratégies de mot de passe à l’aide de PowerShell

Pour commencer, [téléchargez et installez le module Azure AD PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Une fois le module installé, suivez les étapes suivantes pour configurer chaque champ.

### <a name="check-the-expiration-policy-for-a-password"></a>Vérifier la stratégie d’expiration d’un mot de passe

1. Connectez-vous à Windows PowerShell à l’aide de vos informations d’identification d’administrateur d’entreprise ou d’administrateur d’utilisateurs.
1. Exécutez l’une des commandes suivantes :

   * Pour voir si le mot de passe d’un utilisateur donné est défini pour ne jamais expirer, exécutez la cmdlet suivante en utilisant l’UPN (par exemple, *aprilr\@contoso.onmicrosoft.com*) ou l’identifiant utilisateur de l’utilisateur à vérifier :

   ```powershell
   Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

   * Pour afficher le paramètre **Le mot de passe n’expire jamais** pour tous les utilisateurs, exécutez la cmdlet suivante :

   ```powershell
   Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

### <a name="set-a-password-to-expire"></a>Définir un mot de passe pour qu’il expire

1. Connectez-vous à Windows PowerShell à l’aide de vos informations d’identification d’administrateur d’entreprise ou d’administrateur d’utilisateurs.
1. Exécutez l’une des commandes suivantes :

   * Pour définir le mot de passe d’un utilisateur afin qu’il expire, exécutez la cmdlet suivante en utilisant l’UPN ou l’identifiant utilisateur de l’utilisateur :

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
   ```

   * Pour définir les mots de passe de tous les utilisateurs de l’organisation afin qu’ils expirent, utilisez la cmdlet suivante :

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
   ```

### <a name="set-a-password-to-never-expire"></a>Définir un mot de passe pour qu’il n’expire jamais

1. Connectez-vous à Windows PowerShell à l’aide de vos informations d’identification d’administrateur d’entreprise ou d’administrateur d’utilisateurs.
1. Exécutez l’une des commandes suivantes :

   * Pour définir le mot de passe d’un utilisateur afin qu’il n’expire jamais, exécutez la cmdlet suivante en utilisant l’UPN ou l’identifiant utilisateur de l’utilisateur :

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
   ```

   * Pour définir les mots de passe de tous les utilisateurs de l’organisation afin qu’ils n’expirent jamais, utilisez la cmdlet suivante :

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
   ```

   > [!WARNING]
   > Les mots de passe définis sur `-PasswordPolicies DisablePasswordExpiration` le restent en fonction de l’attribut `pwdLastSet`. En fonction de l’attribut `pwdLastSet`, si vous définissez l’expiration sur `-PasswordPolicies None`, tous les mots dont `pwdLastSet` est supérieur à 90 jours doivent être modifiés par l’utilisateur lors de sa connexion suivante. Cette modification peut affecter un grand nombre d’utilisateurs.

## <a name="next-steps"></a>Étapes suivantes

Pour bien démarrer avec SSPR, consultez [Tutoriel : Permettre aux utilisateurs de déverrouiller leur compte ou de réinitialiser des mots de passe à l’aide de la réinitialisation de mot de passe en libre-service Azure Active Directory](tutorial-enable-sspr.md).

Si vos utilisateurs ou vous-même rencontrez des problèmes liés à SSPR, consultez [Résoudre les problèmes de réinitialisation de mot de passe en libre-service](active-directory-passwords-troubleshoot.md).
