---
title: 'Azure AD Connect : Authentification unique transparente - Questions fréquentes | Microsoft Docs'
description: Réponse à des questions fréquentes sur l’authentification unique transparente Azure Active Directory.
services: active-directory
keywords: Qu’est-ce qu’Azure AD Connect, Installation d’Active Directory, Composants requis pour Azure AD, SSO, Authentification unique
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8de47aab231c66f3539c2d2f0f0e4c535a04038a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58085369"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Authentification unique transparente Azure Active Directory : Questions fréquentes (FAQ)

Dans cet article, nous répondons au forum aux questions sur l’authentification unique et transparente Azure Active Directory. N’hésitez pas à le consulter régulièrement, du contenu nouveau y est fréquemment ajouté.

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>Avec quelles méthodes de connexion l’authentification unique transparente est-elle compatible ?

L’authentification unique transparente peut être combinée avec la [synchronisation de hachage de mot de passe](how-to-connect-password-hash-synchronization.md) et [l’authentification directe](how-to-connect-pta.md). Toutefois cette fonctionnalité ne peut pas être utilisée avec les services de fédération Active Directory (ADFS).

## <a name="is-seamless-sso-a-free-feature"></a>La fonctionnalité d’authentification unique transparente est-elle gratuite ?

L’authentification unique transparente est une fonctionnalité gratuite et il n’est pas utile de disposer des éditions payantes d’Azure AD pour l’utiliser.

## <a name="is-seamless-sso-available-in-the-microsoft-azure-germany-cloudhttpswwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>L’authentification unique transparente est-elle disponible dans le [cloud Microsoft Azure Allemagne](https://www.microsoft.de/cloud-deutschland) et le [cloud Microsoft Azure Government](https://azure.microsoft.com/features/gov/) ?

Non. L’authentification unique transparente est uniquement disponible dans l’instance à l’échelle mondiale d’Azure AD.

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>Quelles sont les applications qui tirent parti des paramètres `domain_hint` et `login_hint` de l’authentification unique transparente ?

Vous trouverez ci-dessous une liste non exhaustive des applications qui envoient ces paramètres à Azure AD et permettent donc aux utilisateurs de se connecter de manière silencieuse à l’aide de l’authentification unique transparente (par ex. les utilisateurs n’ont pas besoin d’entrer leur nom d’utilisateur ou mot de passe) :

| Nom de l’application | URL d’application à utiliser |
| -- | -- |
| Panneau d’accès | https://myapps.microsoft.com/contoso.com |
| Outlook sur le Web | https://outlook.office365.com/contoso.com |
| Portails Office 365 | <https://portal.office.com?domain_hint=contoso.com>, <https://www.office.com?domain_hint=contoso.com> |

En outre, les utilisateurs obtiennent une expérience d’authentification en mode silencieux si une application envoie des demandes de connexion aux points de terminaison d’Azure AD configurés en tant que clients - autrement dit, https://login.microsoftonline.com/contoso.com/<..> ou https://login.microsoftonline.com/<tenant_ID>/<..> : au lieu point de terminaison commun d’Azure AD - autrement dit, https://login.microsoftonline.com/common/<...>. Vous trouverez ci-dessous une liste non exhaustive d’applications qui rendent ces types de requêtes de connexion.

| Nom de l’application | URL d’application à utiliser |
| -- | -- |
| SharePoint Online | https://contoso.sharepoint.com |
| Portail Azure | https://portal.azure.com/contoso.com |

Dans les tables ci-dessus, remplacez « contoso.com » par votre nom de domaine pour obtenir les URL d’application de votre abonné.

Si vous souhaitez que d’autres applications utilisent notre expérience d’authentification en mode silencieux, faites-le nous savoir dans la section des commentaires.

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>L’authentification unique transparente prend-elle en charge `Alternate ID` comme nom d’utilisateur à la place de `userPrincipalName` ?

Oui. L’authentification unique transparente prend en charge `Alternate ID` comme nom d’utilisateur dans Azure AD Connect comme indiqué [ici](how-to-connect-install-custom.md). Toutes les applications Office 365 ne prennent pas en charge `Alternate ID`. Reportez-vous à la documentation de l’application qui vous intéresse pour avoir des précisions sur sa prise en charge.

## <a name="what-is-the-difference-between-the-single-sign-on-experience-provided-by-azure-ad-joinactive-directory-azureadjoin-overviewmd-and-seamless-sso"></a>Quelle est la différence entre l’expérience d’authentification unique fournie par [Azure AD Join](../active-directory-azureadjoin-overview.md) et l’authentification unique transparente ?

[Azure AD Join](../active-directory-azureadjoin-overview.md) fournit l’authentification unique aux utilisateurs si leurs appareils sont inscrits auprès d’Azure AD. Ces appareils ne doivent pas nécessairement être joints au domaine. L’authentification unique est fournie à l’aide de *jetons d’actualisation principaux* ou *PRTs*, et non Kerberos. L’expérience utilisateur est optimale sur des appareils Windows 10. L’authentification unique s’effectue automatiquement sur le navigateur Microsoft Edge. Elle fonctionne également sur Chrome avec une extension de navigateur.

Vous pouvez utiliser Azure AD Join et l’authentification unique transparente sur votre client. Ces deux fonctionnalités sont complémentaires. Si les deux fonctionnalités sont activées, l’authentification unique à partir d’Azure AD Join est prioritaire sur l’authentification unique transparente.

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>Je souhaite inscrire des appareils non Windows 10 auprès d’Azure AD sans utiliser les services AD FS. Puis-je à la place utiliser l’authentification unique transparente ?

Oui, ce scénario nécessite la version 2.1 ou ultérieure du [client workplace-join](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account"></a>Comment puis-je substituer la clé de déchiffrement Kerberos du `AZUREADSSOACC` compte d’ordinateur ?

Il est important de fréquemment substituer la clé de déchiffrement Kerberos du `AZUREADSSOACC` compte d’ordinateur (c’est-à-dire Azure AD) créé dans votre forêt AD locale.

>[!IMPORTANT]
>Nous vous recommandons vivement de substituer la clé de déchiffrement Kerberos au moins tous les 30 jours.

Procédez comme suit sur le serveur local où vous exécutez Azure AD Connect :

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Étape 1. Obtenez la liste des forêts AD dans lesquelles l’authentification unique transparente a été activée.

1. Commencez par télécharger et installer [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Accédez au dossier `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importez le module PowerShell Authentification unique (SSO) transparente à l’aide de la commande suivante : `Import-Module .\AzureADSSO.psd1`.
4. Exécutez PowerShell ISE en tant qu’administrateur. Dans PowerShell, appelez `New-AzureADSSOAuthenticationContext`. Cette commande doit afficher une fenêtre contextuelle dans laquelle vous devez entrer vos informations d’identification d’administrateur général de locataire.
5. Appelez `Get-AzureADSSOStatus`. Cette commande vous fournit la liste des forêts AD (examinez la liste « Domaines ») dans lesquelles cette fonctionnalité a été activée.

### <a name="step-2-update-the-kerberos-decryption-key-on-each-ad-forest-that-it-was-set-it-up-on"></a>Étape 2. Mettre à jour la clé de déchiffrement Kerberos sur chaque forêt AD à laquelle elle a été attribuée.

1. Appelez `$creds = Get-Credential`. Quand vous y êtes invité, entrez les informations d’identification d’administrateur de domaine pour la forêt AD souhaitée.

   > [!NOTE]
   > Nous utilisons le nom d’utilisateur de l’administrateur de domaine, fourni dans le format des noms d’utilisateurs principaux (UPN) (johndoe@contoso.com), ou dans celui du nom de compte SAM de domaine complet (contoso\johndoe ou contoso.com\johndoe), pour rechercher la forêt AD souhaitée. Si vous utilisez le nom de compte SAM de domaine complet, nous utilisons la partie domaine du nom d’utilisateur pour [localiser le contrôleur de domaine de l’administrateur de domaine à l’aide de DNS](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Si vous utilisez un UPN à la place, nous [le convertissons en nom de compte SAM de domaine complet](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) avant de trouver le contrôleur de domaine approprié.

2. Appelez `Update-AzureADSSOForest -OnPremCredentials $creds`. Cette commande met à jour la clé de déchiffrement de Kerberos pour le `AZUREADSSOACC` compte de l’ordinateur et la forêt AD spécifique et dans Azure AD.
3. Répétez les étapes précédentes pour chaque forêt AD dans laquelle vous avez configuré la fonctionnalité.

>[!IMPORTANT]
>Assurez-vous de _ne pas_ exécuter la `Update-AzureADSSOForest` commande plusieurs fois. Dans le cas contraire, la fonctionnalité cesse de fonctionner jusqu’à ce que les tickets Kerberos de vos utilisateurs expirent et soient régénérés par votre annuaire Active Directory local.

## <a name="how-can-i-disable-seamless-sso"></a>Comment désactiver l’authentification unique transparente ?

### <a name="step-1-disable-the-feature-on-your-tenant"></a>Étape 1. Désactiver la fonctionnalité pour votre locataire

#### <a name="option-a-disable-using-azure-ad-connect"></a>Option A : Désactiver à l’aide d’Azure AD Connect

1. Exécutez Azure AD Connect, cliquez sur la **page Modifier la connexion utilisateur** puis sur **Suivant**.
2. Désactivez l’option **Activer l’authentification unique**. Suivez les instructions de l’Assistant.

À la fin de l’Assistant, l’authentification unique fluide sera désactivée pour votre locataire. Toutefois, le message suivant s’affichera :

"L’authentification unique est désormais désactivée, mais des étapes manuelles supplémentaires restent à effectuer pour terminer le nettoyage. En savoir plus."

Pour terminer le processus de nettoyage, suivez les étapes 2 et 3 sur le serveur local où vous exécutez Azure AD Connect.

#### <a name="option-b-disable-using-powershell"></a>Option B : Désactiver à l’aide de PowerShell

Exécutez les étapes suivantes sur le serveur local où vous exécutez Azure AD Connect :

1. Commencez par télécharger et installer [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Accédez au dossier `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importez le module PowerShell Authentification unique (SSO) transparente à l’aide de la commande suivante : `Import-Module .\AzureADSSO.psd1`.
4. Exécutez PowerShell ISE en tant qu’administrateur. Dans PowerShell, appelez `New-AzureADSSOAuthenticationContext`. Cette commande doit afficher une fenêtre contextuelle dans laquelle vous devez entrer vos informations d’identification d’administrateur général de locataire.
5. Appelez `Enable-AzureADSSO -Enable $false`.

>[!IMPORTANT]
>La désactivation de l’authentification unique fluide à l’aide de PowerShell ne modifiera pas l’état dans Azure AD Connect. L’authentification unique fluide apparaîtra comme étant activée dans la page **Modifier la connexion utilisateur**.

### <a name="step-2-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Étape 2. Obtenez la liste des forêts AD dans lesquelles l’authentification unique transparente a été activée.

Suivez les tâches 1 à 4 ci-dessous si vous avez désactivé l’authentification unique fluide à l’aide d’Azure AD Connect. Si vous avez désactivé l’authentification unique fluide à l’aide de PowerShell, passez directement à la tâche 5 ci-dessous.

1. Commencez par télécharger et installer [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Accédez au dossier `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importez le module PowerShell Authentification unique (SSO) transparente à l’aide de la commande suivante : `Import-Module .\AzureADSSO.psd1`.
4. Exécutez PowerShell ISE en tant qu’administrateur. Dans PowerShell, appelez `New-AzureADSSOAuthenticationContext`. Cette commande doit afficher une fenêtre contextuelle dans laquelle vous devez entrer vos informations d’identification d’administrateur général de locataire.
5. Appelez `Get-AzureADSSOStatus`. Cette commande vous fournit la liste des forêts AD (examinez la liste « Domaines ») dans lesquelles cette fonctionnalité a été activée.

### <a name="step-3-manually-delete-the-azureadssoacct-computer-account-from-each-ad-forest-that-you-see-listed"></a>Étape 3. Supprimez manuellement le compte d’ordinateur `AZUREADSSOACCT` de chaque forêt AD répertoriée.

## <a name="next-steps"></a>Étapes suivantes

- [**Démarrage rapide**](how-to-connect-sso-quick-start.md) : découvrez l’authentification unique transparente Azure AD.
- [**Immersion technique**](how-to-connect-sso-how-it-works.md) : découvrez comment fonctionne cette fonctionnalité.
- [**Résolution des problèmes**](tshoot-connect-sso.md) : découvrez comment résoudre les problèmes courants rencontrés avec cette fonctionnalité.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) : pour le dépôt de nouvelles demandes de fonctionnalités.
