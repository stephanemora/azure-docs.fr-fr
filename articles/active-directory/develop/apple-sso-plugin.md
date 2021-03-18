---
title: Plug-in Microsoft Enterprise SSO pour les appareils Apple
titleSuffix: Microsoft identity platform | Azure
description: Découvrez le plug-in d’authentification unique de Microsoft Azure Active Directory pour les appareils iOS, iPadOS et macOS.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 96fbf23128896f23beee70a6b3d32b4b87a454ea
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102427094"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Plug-in Microsoft Enterprise SSO pour les appareils Apple (préversion)

>[!IMPORTANT]
> Cette fonctionnalité [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Le *plug-in Microsoft Enterprise SSO pour les appareils Apple* fournit une authentification unique (SSO) pour les comptes Azure Active Directory (Azure AD) sur macOS, iOS et iPadOS, dans toutes les applications qui prennent en charge la fonctionnalité [Enterprise Single Sign-on](https://developer.apple.com/documentation/authenticationservices) d’Apple. Cela comprend les applications plus anciennes dont votre entreprise peut dépendre, mais qui ne prennent pas encore en charge les protocoles ou les bibliothèques d’identités les plus récents. Microsoft a travaillé en étroite collaboration avec Apple pour développer ce plug-in et ainsi rendre plus facile l’utilisation de votre application tout en offrant la meilleure protection que peuvent offrir Apple et Microsoft.

Le plug-in Enterprise SSO est disponible sous la forme d’une fonctionnalité intégrée dans les applications suivantes :

* [Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) - iOS, iPadOS
* Le [portail d’entreprise](/mem/intune/apps/apps-company-portal-macos) Microsoft Intune - macOS

## <a name="features"></a>Fonctionnalités

Le plug-in Microsoft Enterprise SSO pour les appareils Apple offre les avantages suivants :

- Il fournit l’authentification unique pour les comptes Azure AD dans toutes les applications qui prennent en charge la fonctionnalité Enterprise Single Sign-On d’Apple.
- Il peut être activé par n’importe quelle solution de gestion des appareils mobiles (MDM).
- Il étend l’authentification unique aux applications qui n’utilisent pas encore les bibliothèques de plateforme d’identités Microsoft.
- Il étend l’authentification unique aux applications qui utilisent OAuth2, OpenID Connect et SAML.

## <a name="requirements"></a>Spécifications

Pour utiliser le plug-in Microsoft Enterprise SSO pour les appareils Apple :

- L’appareil doit **prendre en charge** les applications qui comprennent le plug-in Microsoft Enterprise SSO pour appareils Apple. L’une des ces applications doit être **installée** sur l’appareil :
  - iOS 13.0+ : [application Microsoft Authenticator](../user-help/user-help-auth-app-overview.md)
  - iPadOS 13.0+ : [application Microsoft Authenticator](../user-help/user-help-auth-app-overview.md)
  - macOS 10.15+ : [application Portail d’entreprise Intune](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)
- L’appareil doit être **inscrit à MDM** (par exemple, auprès de Microsoft Intune).
- La configuration doit être **envoyée (push) à l’appareil** afin d’y activer le plug-in Microsoft Enterprise SSO pour appareils Apple. Cette contrainte de sécurité est requise par Apple.

### <a name="ios-requirements"></a>Configuration requise pour iOS :
- iOS version 13.0 ou ultérieure doit être installé sur l’appareil.
- Une application Microsoft qui fournit le plug-in Microsoft Enterprise SSO pour les appareils Apple doit être installée sur l’appareil. Dans la préversion publique, ces applications correspondent à l’[application Microsoft Authenticator](/intune/user-help/user-help-auth-app-overview.md).


### <a name="macos-requirements"></a>Configuration requise pour macOS :
- macOS 10.15 ou ultérieur doit être installé sur l’appareil. 
- Une application Microsoft qui fournit le plug-in Microsoft Enterprise SSO pour les appareils Apple doit être installée sur l’appareil. Dans la préversion publique, ces applications comprennent l’[application Portail d’entreprise Intune](/intune/user-help/enroll-your-device-in-intune-macos-cp.md).

## <a name="enable-the-sso-plug-in-with-mobile-device-management-mdm"></a>Activer le plug-in SSO avec la gestion des appareils mobiles (MDM)

### <a name="microsoft-intune-configuration"></a>Configuration de Microsoft Intune

Si vous utilisez Microsoft Intune comme service MDM, vous pouvez utiliser des paramètres de profil de configuration intégrés pour activer le plug-in Microsoft Enterprise SSO.

Tout d’abord, configurez les paramètres de l’[extension d’application d’authentification unique](/mem/intune/configuration/device-features-configure#single-sign-on-app-extension) dans le profil de configuration, puis [attribuez le profil à un utilisateur ou à un groupe d’appareils](/mem/intune/configuration/device-profile-assign) (s’il n’a pas déjà été attribué).

Les paramètres de profil qui activent le plug-in SSO seront automatiquement appliqués aux appareils du groupe la prochaine fois qu’un appareil effectuera un check-in dans Intune.

### <a name="manual-configuration-for-other-mdm-services"></a>Configuration manuelle des autres services MDM

Si vous n’utilisez pas Microsoft Intune pour la gestion des appareils mobiles, utilisez les paramètres suivants pour configurer le plug-in Microsoft Enterprise SSO pour appareils Apple.

#### <a name="ios-settings"></a>Paramètres iOS :

- **ID d’extension** : `com.microsoft.azureauthenticator.ssoextension`
- **ID d’équipe** : (ce champ n’est pas nécessaire pour iOS)

#### <a name="macos-settings"></a>Paramètres macOS :

- **ID d’extension** : `com.microsoft.CompanyPortalMac.ssoextension`
- **ID de l’équipe** : `UBF8T346G9`

#### <a name="common-settings"></a>Paramètres communs :

- **Type** : Rediriger
  - `https://login.microsoftonline.com`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`
  
### <a name="additional-configuration-options"></a>Options de configuration supplémentaires
Des options de configuration supplémentaires peuvent être ajoutées pour étendre la fonctionnalité SSO à d’autres applications.

#### <a name="enable-sso-for-apps-that-dont-use-a-microsoft-identity-platform-library"></a>Activer l’authentification unique pour les applications qui n’utilisent pas de bibliothèque de plateforme d’identités Microsoft

Le plug-in SSO permet à n’importe quelle application de participer à l’authentification unique, même si elle n’a pas été développée à l’aide d’un SDK Microsoft tel que la bibliothèque d’authentification Microsoft (MSAL).

Le plug-in SSO est installé automatiquement par les appareils qui ont téléchargé l’application Microsoft Authenticator sur iOS ou iPadOS, ou l’application Portail d’entreprise Intune sur macOS, et qui sont inscrits auprès de votre organisation. Votre organisation utilise probablement l’application Authenticator pour des scénarios tels que l’authentification multifacteur, l’authentification sans mot de passe et l’accès conditionnel. Elle peut être activée pour vos applications à l’aide de n’importe quel fournisseur MDM, bien que Microsoft facilite sa configuration dans Microsoft Endpoint Manager d’Intune. Une liste verte est utilisée pour configurer ces applications afin qu’elles utilisent le plug-in SSO.

>[!IMPORTANT]
> Seules les applications qui utilisent des technologies réseau Apple natives ou des vues WebView sont prises en charge. Si une application est livrée avec sa propre implémentation de couche réseau, le plug-in plug-in Microsoft Enterprise SSO n’est pas pris en charge.  

Utilisez les paramètres suivants afin de configurer le plug-in Microsoft Enterprise SSO pour les applications qui n’utilisent pas de bibliothèque de plateforme d’identités Microsoft :

Si vous souhaitez spécifier une liste d’applications :

- **Clé** : `AppAllowList`
- **Type** : `String`
- **Valeur** : Liste délimitée par des virgules des ID d’ensemble d’applications pour les applications qui sont autorisées à participer à l’authentification unique
- **Exemple** : `com.contoso.workapp, com.contoso.travelapp`

Ou si vous souhaitez spécifier une liste de préfixes :
- **Clé** : `AppPrefixAllowList`
- **Type** : `String`
- **Valeur** : liste délimitée par des virgules comprenant les préfixes des ID de bundles d’applications pour les applications qui sont autorisées à participer à l’authentification unique. Notez que cela permet à toutes les applications dont le nom commence par un préfixe donné de participer à l’authentification unique.
- **Exemple** : `com.contoso., com.fabrikam.`

[Les applications avec consentement](./application-consent-experience.md) autorisées par l’administrateur MDM à participer à l’authentification unique peuvent obtenir en mode silencieux un jeton pour l’utilisateur final. Par conséquent, il est important d’ajouter uniquement des applications approuvées à la liste d’autorisation. 

>[!NOTE]
> Vous n’avez pas besoin d’ajouter les applications qui utilisent MSAL ou ASWebAuthenticationSession à cette liste. Elles sont activées par défaut. 

##### <a name="how-to-discover-app-bundle-identifiers-on-ios-devices"></a>Comment découvrir des identificateurs de bundles d’applications sur des appareils iOS

Apple ne fournit pas de méthode simple pour découvrir les ID de bundles dans l’App Store. Pour découvrir les ID de bundles des applications que vous souhaitez utiliser pour l’authentification unique, le plus simple est de demander à votre fournisseur ou à votre développeur d’applications. Si ce n’est pas disponible, vous pouvez utiliser votre configuration MDM pour découvrir les ID de bundles. 

Activez temporairement l’indicateur suivant dans votre configuration MDM :

- **Clé** : `admin_debug_mode_enabled`
- **Type** : `Integer`
- **Valeur** : 1 ou 0

Quand cet indicateur est actif, connectez-vous aux applications iOS de l’appareil dont vous souhaitez connaître l’ID de bundle. Ensuite, ouvrez l’application Microsoft Authenticator -> Aide -> Envoyer les journaux -> Afficher les journaux. 

Dans le fichier journal, recherchez la ligne suivante :

`[ADMIN MODE] SSO extension has captured following app bundle identifiers:`

Elle doit contenir tous les identificateurs de bundles d’applications qui sont visibles par l’extension SSO. Vous pouvez ensuite utiliser ces identificateurs afin de configurer l’authentification unique pour ces applications. 

#### <a name="allow-user-to-sign-in-from-unknown-applications-and-the-safari-browser"></a>Autoriser l’utilisateur à se connecter à partir d’applications inconnues et du navigateur Safari

Par défaut, le plug-in Microsoft Enterprise SSO ne fournit l’authentification unique aux applications autorisées que lorsqu’un utilisateur s’est connecté à partir d’une application qui utilise une bibliothèque de plateforme d’identités Microsoft (ADAL ou MSAL, par exemple). Le plug-in Microsoft Enterprise SSO peut également acquérir des informations d’identification partagées lorsqu’il est appelé par une autre application qui utilise une bibliothèque de plateforme d’identités Microsoft, dans le cadre d’une nouvelle acquisition de jetons.

L’activation de l’indicateur `browser_sso_interaction_enabled` permet aux applications qui n’utilisent pas de bibliothèque de plateforme d’identités Microsoft d’effectuer l’amorçage initial et d’obtenir des informations d’identification partagées. Cela permet également au navigateur Safari d’effectuer l’amorçage initial et d’obtenir des informations d’identification partagées. Si le plug-in plug-in Microsoft Enterprise SSO ne dispose pas encore d’informations d’identification partagées, il essaiera d’en obtenir chaque fois qu’une connexion est demandée à partir d’une URL Azure AD dans le navigateur Safari, ASWebAuthenticationSession, SafariViewController ou une autre application native autorisée.  

- **Clé** : `browser_sso_interaction_enabled`
- **Type** : `Integer`
- **Valeur** : 1 ou 0

Sur macOS, ce paramètre est nécessaire pour obtenir une expérience cohérente dans toutes les applications. Sur iOS et iPadOS, ce paramètre n’est pas nécessaire car la plupart des applications utilisent l’application Microsoft Authenticator pour la connexion. Toutefois, si certaines de vos applications n’utilisent pas Microsoft Authenticator sur iOS ou iPadOS, cet indicateur permettra d’améliorer l’expérience. Nous vous recommandons donc d’activer ce paramètre. Il est désactivé par défaut.

#### <a name="disable-asking-for-mfa-on-initial-bootstrapping"></a>Ne plus exiger l’authentification MFA lors de l’amorçage initial

Par défaut, le plug-in Microsoft Enterprise SSO demande toujours à l’utilisateur de s’authentifier avec l’authentification MFA lors de l’amorçage initial et lorsqu’il souhaite obtenir des informations d’identification partagées, même si cela n’est pas obligatoire pour l’application que l’utilisateur vient de lancer. Cela permet d’utiliser facilement les informations d’identification partagées dans toutes les autres applications, sans avoir à demander à l’utilisateur de s’authentifier avec l’authentification MFA si celle-ci devient obligatoire plus tard. Cela réduit le nombre de fois où l’utilisateur est invité à s’authentifier sur l’appareil, ce qui est généralement une bonne chose.

L’activation de `browser_sso_disable_mfa` désactive cette option, et invite l’utilisateur à s’authentifier uniquement lorsque l’authentification MFA est exigée par une application ou une ressource. 

- **Clé** : `browser_sso_disable_mfa`
- **Type** : `Integer`
- **Valeur** : 1 ou 0

Nous vous recommandons de ne pas activer cet indicateur, car cela réduit le nombre de fois où l’utilisateur est invité à s’authentifier sur l’appareil. Si votre organisation utilise rarement l’authentification MFA, vous pouvez alors activer cet indicateur. Cela dit, l’utilisation fréquente de l’authentification MFA est recommandée. C’est la raison pour laquelle elle est désactivée par défaut.

#### <a name="disable-oauth2-application-prompts"></a>Désactiver les invites d’application OAuth2

Le plug-in plug-in Microsoft Enterprise SSO fournit l’authentification unique en ajoutant les informations d’identification partagées aux demandes réseau provenant des applications autorisées. Toutefois, certaines applications OAuth2 peuvent appliquer à tort les invites de l’utilisateur final au niveau de la couche protocole. Si cela se produit, vous verrez que les informations d’identification partagées sont ignorées pour ces applications et que votre utilisateur est invité à se connecter, même si le plug-in Microsoft Enterprise SSO fonctionne pour d’autres applications.  

L’activation de l’indicateur `disable_explicit_app_prompt` restreint la capacité des applications natives et des applications web à forcer une invite de l’utilisateur final sur la couche de protocole et à contourner l’authentification unique.

- **Clé** : `disable_explicit_app_prompt`
- **Type** : `Integer`
- **Valeur** : 1 ou 0

Nous vous recommandons d’activer cet indicateur pour obtenir une expérience plus cohérente sur toutes les applications. Il est désactivé par défaut. 

#### <a name="enable-sso-through-cookies-for-specific-application"></a>Activer l’authentification unique par le biais de cookies pour une application donnée

Certaines applications peuvent être incompatibles avec l’extension SSO, cela dit, elles sont rares. Les applications qui ont des paramètres réseau avancés peuvent rencontrer des problèmes inattendus lorsque l’authentification unique est activée (par exemple, vous pouvez voir une erreur indiquant que la requête réseau a été annulée ou interrompue). 

Si vous rencontrez des problèmes lorsque vous vous authentifiez avec la méthode décrite dans la section `Enable SSO for apps that don't use MSAL`, vous pouvez essayer une autre configuration pour ces applications. 

Utilisez les paramètres suivants afin de configurer le plug-in Microsoft Enterprise SSO pour ces applications :

- **Clé** : `AppCookieSSOAllowList`
- **Type** : `String`
- **Valeur** : liste délimitée par des virgules comprenant les préfixes des ID de bundles d’applications pour les applications qui sont autorisées à participer à l’authentification unique. Notez que cela permet à toutes les applications dont le nom commence par un préfixe donné de participer à l’authentification unique.
- **Exemple** : `com.contoso.myapp1, com.fabrikam.myapp2`

Notez que les applications où est activée l’authentification unique et qui utilisent ce mécanisme doivent être ajoutées à `AppCookieSSOAllowList` et `AppPrefixAllowList`.

Nous vous recommandons d’essayer cette option uniquement pour les applications qui rencontrent des échecs de connexion inattendus. 

#### <a name="use-intune-for-simplified-configuration"></a>Utiliser Intune pour une configuration simplifiée

Comme nous l’avons vu précédemment, vous pouvez utiliser Microsoft Intune en tant que service MDM pour faciliter la configuration du plug-in Microsoft Enterprise SSO, notamment l’activation du plug-in et l’ajout de vos anciennes applications à la liste verte pour que celles-ci puissent bénéficier de l’authentification unique. Pour plus d’informations, consultez la [documentation sur la configuration d’Intune](/intune/configuration/ios-device-features-settings).

## <a name="using-the-sso-plug-in-in-your-application"></a>Utilisation du plug-in SSO dans votre application

La [bibliothèque d’authentification Microsoft (MSAL) pour les appareils Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) de version 1.1.0 ou ultérieure prend en charge le plug-in Microsoft Enterprise SSO pour les appareils Apple. Il s’agit de la méthode recommandée pour ajouter la prise en charge du plug-in Microsoft Enterprise SSO, car elle vous permet de bénéficier de toutes les fonctionnalités de la plateforme d’identités Microsoft.

Si vous créez une application pour des scénarios de travail Frontline, consultez [Mode d’appareil partagé pour les appareils iOS](msal-ios-shared-devices.md) pour une configuration supplémentaire de cette fonctionnalité.

## <a name="how-the-sso-plug-in-works"></a>Fonctionnement du plug-in SSO

Le plug-in plug-in Microsoft Enterprise SSO s’appuie sur l’[infrastructure d’authentification unique d’Apple](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc). Les fournisseurs d’identité intégrés à l’infrastructure peuvent intercepter le trafic réseau pour leurs domaines et améliorer ou modifier le mode de traitement de ces demandes. Par exemple, le plug-in SSO peut afficher une interface utilisateur supplémentaire pour collecter les informations d’identification de l’utilisateur final en toute sécurité, exiger l’authentification MFA ou fournir en mode silencieux des jetons à l’application.

Les applications natives peuvent également implémenter des opérations personnalisées et communiquer directement avec le plug-in SSO.
Vous pouvez en savoir plus sur l’infrastructure d’authentification unique dans cette [vidéo 2019 WWDC d’Apple](https://developer.apple.com/videos/play/tech-talks/301/)

### <a name="applications-that-use-a-microsoft-identity-platform-library"></a>Applications qui utilisent une bibliothèque de plateforme d’identités Microsoft

La [bibliothèque d’authentification Microsoft (MSAL) pour les appareils Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) de version 1.1.0 ou ultérieure prend en charge le plug-in Microsoft Enterprise SSO pour les appareils Apple et de façon native pour les comptes scolaires et professionnels. 

Aucune configuration particulière n’est nécessaire si vous avez suivi [toutes les étapes recommandées](./quickstart-v2-ios.md) et utilisé le [format d’URI de redirection](./redirect-uris-ios.md) par défaut. Lorsqu’elle exécutée sur un appareil sur lequel est présent le plug-in SSO, MSAL l’appelle automatiquement pour toutes les demandes de jeton interactives et silencieuses, ainsi que l’énumération de compte et les opérations de suppression de compte. Étant donné que MSAL implémente le protocole de plug-in SSO natif qui repose sur les opérations personnalisées, cette configuration assure l’expérience native la plus fluide pour l’utilisateur final. 

Si le plug-in SSO n’est pas activé par MDM, mais que l’application Microsoft Authenticator est présente sur l’appareil, MSAL utilise l’application Microsoft Authenticator pour toutes les demandes de jeton interactives. Le plug-in SSO partage l’authentification unique avec l’application Microsoft Authenticator.

### <a name="applications-that-dont-use-a-microsoft-identity-platform-library"></a>Applications qui n’utilisent pas une bibliothèque de plateforme d’identités Microsoft

Les applications qui n’utilisent pas de bibliothèque de plateforme d’identités Microsoft (par exemple, comme MSAL) peuvent toujours bénéficier de l’authentification unique si un administrateur les ajoute explicitement à la liste verte. 

Aucune modification de code n’est nécessaire dans ces applications tant que les conditions suivantes sont satisfaites :

- L’application utilise les infrastructures Apple pour exécuter des requêtes réseau (par exemple, [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview), [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession)) 
- L’application utilise des protocoles standard pour communiquer avec Azure AD (par exemple, OAuth2, SAML, WS-Federation)
- L’application ne collecte pas de nom d’utilisateur et de mot de passe en texte clair dans l’interface utilisateur native

Dans ce cas, l’authentification unique est fournie lorsque l’application crée une requête réseau et ouvre un navigateur web pour connecter l’utilisateur. Lorsque l’utilisateur est redirigé vers une URL de connexion Azure AD, le plug-in SSO valide l’URL et vérifie s’il existe des informations d’identification SSO disponibles pour cette URL. S’il en existe, le plug-in SSO transmet les informations d’identification SSO à Azure AD, ce qui permet à l’application de terminer la requête réseau sans demander à l’utilisateur d’entrer ses informations d’identification. En outre, si l’appareil est connu d’Azure AD, le plug-in SSO transmet également le certificat de l’appareil pour répondre à la vérification de l’accès conditionnel en fonction de l’appareil. 

Pour prendre en charge l’authentification unique pour les applications non-MSAL, le plug-in SSO implémente un protocole similaire au plug-in de navigateur Windows décrit dans [Qu’est-ce qu’un jeton d’actualisation principal](../devices/concept-primary-refresh-token.md#browser-sso-using-prt). 

En comparaison avec les applications basées sur MSAL, le plug-in SSO agit plus en toute transparence pour les applications non-MSAL en s’intégrant à l’expérience de connexion de navigateur existante fournie par les applications. L’utilisateur final voit son expérience habituelle, avec l’avantage de ne pas avoir à effectuer d’autres connexions dans chacune des applications. Par exemple, au lieu d’afficher le sélecteur de compte natif, le plug-in SSO ajoute des sessions SSO à l’expérience du sélecteur de compte basé sur le web. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le mode d’appareil partagé sur iOS, consultez [Mode d’appareil partagé pour les appareils iOS](msal-ios-shared-devices.md).
