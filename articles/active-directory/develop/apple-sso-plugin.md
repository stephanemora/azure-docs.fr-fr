---
title: Plug-in Microsoft Enterprise SSO pour les appareils Apple
titleSuffix: Microsoft identity platform | Azure
description: Découvrez le plug-in d’authentification unique de Microsoft Azure Active Directory pour les appareils iOS et macOS.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: a6af1a78582ebf62aed82bad8fa06cac37f511ea
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982580"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Plug-in Microsoft Enterprise SSO pour les appareils Apple (préversion)

> [!NOTE]
> Cette fonctionnalité est en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le *plug-in Microsoft Enterprise SSO pour les appareils Apple* fournit une authentification unique (SSO) pour les comptes Azure Active Directory (Azure AD) dans toutes les applications qui prennent en charge la fonctionnalité [Enterprise Single Sign-on](https://developer.apple.com/documentation/authenticationservices) d’Apple. Microsoft a travaillé en étroite collaboration avec Apple pour développer ce plug-in et ainsi rendre plus facile l’utilisation de votre application tout en offrant la meilleure protection que peuvent offrir Apple et Microsoft.

Dans cette préversion publique, le plug-in Enterprise SSO est disponible uniquement pour les appareils iOS et est distribué dans certaines applications Microsoft.

Notre première utilisation du plug-in Enterprise SSO s’effectue dans le cadre de notre nouvelle fonctionnalité de [mode d’appareil partagé](msal-ios-shared-devices.md).

## <a name="features"></a>Fonctionnalités

Le plug-in Microsoft Enterprise SSO pour les appareils Apple offre les avantages suivants :

- Il fournit l’authentification unique pour les comptes Azure AD dans toutes les applications qui prennent en charge la fonctionnalité Enterprise Single Sign-On d’Apple.
- Il est fourni automatiquement dans Microsoft Authenticator et peut être activé par toute solution de gestion des appareils mobiles (MDM).

## <a name="requirements"></a>Spécifications

Pour utiliser le plug-in Microsoft Enterprise SSO pour les appareils Apple :

- iOS version 13.0 ou ultérieure doit être installé sur l’appareil.
- Une application Microsoft qui fournit le plug-in Microsoft Enterprise SSO pour les appareils Apple doit être installée sur l’appareil. Pour la préversion publique, ces applications incluent l’[application Microsoft Authenticator](../user-help/user-help-auth-app-overview.md).
- L’appareil doit être inscrit à MDM (par exemple, avec Microsoft Intune).
- La configuration doit être envoyée (push) à l’appareil pour activer le plug-in Microsoft Enterprise SSO pour les appareils Apple sur l’appareil. Cette contrainte de sécurité est requise par Apple.

## <a name="enable-the-sso-extension-with-mobile-device-management-mdm"></a>Activer l’extension SSO avec la gestion des appareils mobiles (MDM)

Pour activer le plug-in Microsoft Enterprise SSO pour les appareils Apple, vos appareils doivent recevoir un signal via un service MDM. Comme Microsoft inclut le plug-in Enterprise SSO dans l’[application Microsoft Authenticator](..//user-help/user-help-auth-app-overview.md), utilisez votre MDM pour configurer l’application afin d’activer le plug-in Microsoft Enterprise SSO.

Utilisez les paramètres suivants pour configurer le plug-in Microsoft Enterprise SSO pour les appareils Apple :

- **Type** : Redirect
- **ID d’extension** : `com.microsoft.azureauthenticator.ssoextension`
- **ID de l’équipe** : `SGGM6D27TK`
- **URL** :
  - `https://login.microsoftonline.com`
  - `https://login.windows.net`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`

Vous pouvez utiliser Microsoft Intune comme service MDM pour faciliter la configuration du plug-in Microsoft Enterprise SSO. Pour plus d’informations, consultez la [documentation sur la configuration d’Intune](https://docs.microsoft.com/intune/configuration/ios-device-features-settings).

## <a name="using-the-sso-extension-in-your-application"></a>Utilisation de l’extension SSO dans votre application

La [bibliothèque d’authentification Microsoft (MSAL) pour les appareils Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) de version 1.1.0 ou ultérieure prend en charge le plug-in Microsoft Enterprise SSO pour les appareils Apple.

Si vous souhaitez prendre en charge le mode d’appareil partagé fourni par le plug-in Microsoft Enterprise SSO pour les appareils Apple, vérifiez que vos applications utilisent la version minimale requise spécifiée de MSAL.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le mode d’appareil partagé sur iOS, consultez [Mode d’appareil partagé pour les appareils iOS](msal-ios-shared-devices.md).
