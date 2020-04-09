---
title: Exigences en matière de données SSPR Azure AD - Azure Active Directory
description: Exigences en matière de données pour la réinitialisation du mot de passe en libre-service Azure AD et comment les satisfaire
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 12/09/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42f7e120745357d3bd5735cca568bdd6971ea061
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652355"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Déployer la réinitialisation du mot de passe sans demander l’inscription de l’utilisateur final

Pour déployer la réinitialisation du mot de passe libre-service (SSPR) d’Azure Active Directory (Azure AD), les données d’authentification doivent être présentes. Certaines organisations demandent à leurs utilisateurs d’entrer leurs données d’authentification eux-mêmes, quand d’autres préfèrent synchroniser avec des données qui existent déjà dans Active Directory. Ces données synchronisées sont mises à disposition d’Azure AD et de la SSPR sans nécessiter l’intervention de l’utilisateur si vous remplissez les conditions suivantes :

* Mettez correctement en forme les données dans votre annuaire local.
* Configurez [Azure AD Connect en utilisant les paramètres express](../hybrid/how-to-connect-install-express.md).

Pour que tout fonctionne correctement, les numéros de téléphone doivent être au format *+CodePays NuméroTéléphone*, par exemple : +1 4255551234.

> [!NOTE]
> Il doit y avoir un espace entre l’indicatif du pays et le numéro de téléphone.
>
> La réinitialisation du mot de passe ne prend pas en charge les extensions de téléphone. Même au format +1 4255551234X12345, les extensions sont supprimées avant l’appel.

## <a name="fields-populated"></a>Champs renseignés

Si vous utilisez les paramètres par défaut dans Azure AD Connect, les mappages suivants sont effectués :

| Active Directory local | Azure AD |
| --- | --- |
| telephoneNumber | Téléphone de bureau |
| mobile | Téléphone mobile |

Une fois qu’un utilisateur confirme son numéro de téléphone mobile, le champ *Téléphone* situé sous **Informations de contact d’authentification** dans Azure AD est également renseigné avec ce numéro.

## <a name="authentication-contact-info"></a>Informations de contact d’authentification

Sur la page **Méthodes d’authentification** d’un utilisateur Azure AD dans le Portail Azure, un administrateur général peut définir manuellement les informations de contact d’authentification, comme le montre la capture d’écran suivante :

![Informations de contact d'authentification sur un utilisateur dans Azure AD][Contact]

* Si le champ **Téléphone** est renseigné et si l’option **Téléphone mobile** est activée dans la stratégie SSPR, l’utilisateur voit ce numéro sur la page d’inscription de réinitialisation du mot de passe et lors du workflow de réinitialisation du mot de passe.
* Le champ **Autre téléphone** n’est pas utilisé pour la réinitialisation du mot de passe.
* Si le champ **Adresse e-mail** est renseigné et si l’option **Adresse e-mail** est activée dans la stratégie SSPR, l’utilisateur voit cette adresse e-mail sur la page d’inscription de réinitialisation du mot de passe et lors du workflow de réinitialisation du mot de passe.
* Si le champ **Autre adresse e-mail** est renseigné et si l’option **Adresse e-mail** est activée dans la stratégie SSPR, l’utilisateur **ne voit pas** cette adresse e-mail sur la page d’inscription de réinitialisation du mot de passe. Toutefois, il la voit lors du workflow de réinitialisation du mot de passe.

## <a name="security-questions-and-answers"></a>Questions et réponses de sécurité

Les questions et les réponses de sécurité sont stockées de manière sécurisée dans votre locataire Azure AD et sont uniquement accessibles aux utilisateurs par le biais du [portail d’inscription SSPR](https://aka.ms/ssprsetup). Les administrateurs ne peuvent pas voir, définir ou modifier le contenu des questions et des réponses des autres utilisateurs.

## <a name="what-happens-when-a-user-registers"></a>Ce qu’il se passe lorsqu'un utilisateur s'inscrit

Lorsqu’un utilisateur s'inscrit, la page d’inscription définit les champs suivants :

* **Téléphone d’authentification**
* **E-mail d’authentification**
* **Questions et réponses de sécurité**

Si vous avez fourni une valeur pour **Téléphone mobile** ou **Adresse électronique secondaire**, les utilisateurs peuvent immédiatement l’utiliser pour réinitialiser leur mot de passe, même s’ils ne se sont pas inscrits au service. Les utilisateurs voient ces valeurs quand ils s’inscrivent pour la première fois et ils peuvent les modifier s’ils le souhaitent. Une fois les utilisateurs inscrits, ces valeurs sont conservées respectivement dans les champs **Téléphone d’authentification** et **Adresse e-mail d’authentification**.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Définir et lire les données d’authentification par le biais de PowerShell

Vous pouvez définir les champs suivants par le biais de PowerShell :

* **Adresse électronique secondaire**
* **Téléphone mobile**
* **Téléphone de bureau** : ne peut être défini qu’en l’absence de synchronisation avec un annuaire local.

### <a name="use-powershell-version-1"></a>Utiliser PowerShell version 1

Pour commencer, vous devez [télécharger et installer le module Azure AD PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Une fois le module installé, vous pouvez suivre les étapes suivantes pour configurer chaque champ.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Définir les données d’authentification avec PowerShell version 1

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>Lire les données d’authentification avec PowerShell version 1

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Lire les options Téléphone d’authentification et E-mail d’authentification

Pour lire le **Téléphone d’authentification** et **l’E-mail d’authentification** quand vous utilisez PowerShell version 1, exécutez les commandes suivantes :

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Utiliser PowerShell version 2

Pour commencer, vous devez [télécharger et installer le module Azure AD PowerShell version 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0). Une fois le module installé, vous pouvez suivre les étapes suivantes pour configurer chaque champ.

Pour effectuer une installation rapide à partir de versions récentes de PowerShell qui prennent en charge Install-Module, exécutez les commandes suivantes. (La première ligne vérifie si le module est déjà installé.)

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Définir les données d’authentification avec PowerShell version 2

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Lire les données d’authentification avec PowerShell version 2

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Étapes suivantes

* [Comment réussir le lancement de la réinitialisation de mot de passe en libre-service ?](howto-sspr-deployment.md)
* [Réinitialiser ou modifier votre mot de passe](../user-help/active-directory-passwords-update-your-own-password.md)
* [S’inscrire pour la réinitialisation du mot de passe en libre-service](../user-help/active-directory-passwords-reset-register.md)
* [Vous avez une question relative à la licence ?](concept-sspr-licensing.md)
* [Quelles méthodes d'authentification sont accessibles aux utilisateurs ?](concept-sspr-howitworks.md#authentication-methods)
* [Quelles sont les options de stratégie disponibles avec la réinitialisation de mot de passe en libre-service ?](concept-sspr-policy.md)
* [Quelle est l’écriture différée de mot de passe et pourquoi dois-je m’y intéresser ?](howto-sspr-writeback.md)
* [Comment puis-je générer des rapports sur l’activité dans la réinitialisation de mot de passe en libre-service ?](howto-sspr-reporting.md)
* [Quelles sont toutes les options disponibles dans la réinitialisation de mot de passe en libre-service et que signifient-elles ?](concept-sspr-howitworks.md)
* [Je pense qu’il y a une panne quelque part. Comment puis-je résoudre les problèmes de la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-troubleshoot.md)
* [J’ai une question à laquelle je n’ai pas trouvé de réponse ailleurs](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "Les administrateurs globaux peuvent modifier les informations de contact d’authentification d’un utilisateur"
