---
title: Prise en charge des applications pour l’authentification par SMS dans Azure Active Directory
description: Découvrez les applications prises en charge pour que les utilisateurs se connectent à Azure Active Directory à l’aide de SMS
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: justinha
author: aanjusingh
manager: daveba
ms.reviewer: anjusingh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f5b0ff9c70c84a1f3adda2e93c74ce388ceb322
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122534735"
---
# <a name="app-support-for-sms-based-authentication"></a>Prise en charge des applications pour l’authentification par SMS

L’authentification par SMS est disponible pour les applications Microsoft intégrées à la plateforme Microsoft Identity (Azure AD). Le tableau répertorie certaines des applications Web et mobiles qui prennent en charge l’authentification par SMS. Si vous souhaitez ajouter ou valider une application, [contactez-nous](https://feedback.azure.com/forums/169401-azure-active-directory). 

| Application | Application Web/navigateur | Application mobile native |
| --- |:---:|:---:|
| Office 365 - Microsoft Online Services* | ● |   |
| Microsoft OneNote | ● |   |
| Microsoft Teams | ● | ● |
| Microsoft Intune/ portail d’entreprise | ● | ● |
| Portail Mes applications | ● |Non disponible|
| Microsoft Forms | ● |Non disponible|
| Microsoft Edge | ● |   |
| Microsoft Power BI | ● |   |
| Microsoft Stream | ● |   |
| Microsoft Power Apps | ● |   |
| Microsoft Azure | ● | ● |
| Azure Virtual Desktop | ● |  | 

*_La connexion par SMS n’est pas disponible pour les applications Office, telles que Word, Excel, etc., en cas d’accès direct sur le web, mais est disponible lors d’un accès via l’[application web Office 365](https://www.office.com)_

Les applications Microsoft mentionnées ci-dessus prennent en charge la connexion par SMS, car elles utilisent la connexion Microsoft Identity (`https://login.microsoftonline.com/`), qui permet à l’utilisateur d’entrer un numéro de téléphone et un code SMS.

## <a name="unsupported-microsoft-apps"></a>Applications Microsoft non prises en charge

Les applications de bureau Microsoft 365 (Windows ou Mac) et les applications web Microsoft 365 (à l’exception de MS OneNote) accessibles directement sur le web ne prennent pas en charge la connexion par SMS. Ces applications utilisent la connexion Microsoft Office (`https://office.live.com/start/*`) qui requiert un mot de passe pour se connecter.
Pour la même raison, les applications mobiles Microsoft Office (à l’exception de Microsoft Teams, du Portail d’entreprise Intune et de Microsoft Azure) ne prennent pas en charge la connexion par SMS.

| Applications Microsoft non prises en charge| Exemples |
| --- | --- |
| Applications Microsoft natives de bureau | Microsoft Teams, applications O365, Word, Excel, etc.|
| Applications Microsoft mobiles natives (à l’exception de Microsoft Teams, du Portail d’entreprise Intune et de Microsoft Azure) | Outlook, Edge, Power BI, Stream, SharePoint, Power Apps, Word, etc.|
| Applications web Microsoft 365 (accessibles directement sur le web) | [Outlook](https://outlook.live.com/owa/), [Word](https://office.live.com/start/Word.aspx), [Excel](https://office.live.com/start/Excel.aspx), [PowerPoint](https://office.live.com/start/PowerPoint.aspx), [OneDrive](https://onedrive.live.com/about/signin)|  

## <a name="support-for-non-microsoft-apps"></a>Prise en charge des applications non-Microsoft 

Pour rendre les applications non-Microsoft compatibles avec la fonctionnalité de connexion par SMS : 
- Intégrez des applications web non-Microsoft avec Azure AD et utilisez l’authentification Azure AD. Utilisez Security Assertion Markup Language [SAML](../manage-apps/add-application-portal-setup-sso.md) ou Open ID Connect [OIDC](../manage-apps/add-application-portal-setup-oidc-sso.md) pour l’intégration à Azure AD SSO. 
- Intégrer des applications non-Microsoft locales avec Azure AD à l’aide du [proxy d’application Azure AD](../app-proxy/application-proxy-add-on-premises-application.md)
- Intégrer des applications clientes non-Microsoft avec [Microsoft Identity Platform](../develop/v2-overview.md) à des fins d’authentification 
    - [Exemple d’application iOS](../develop/tutorial-v2-ios.md)
    - [Exemple d’application Android](../develop/tutorial-v2-android.md)

## <a name="next-steps"></a>Étapes suivantes

- [Comment activer la connexion par SMS pour les utilisateurs](howto-authentication-sms-signin.md)
- Pour activer la connexion par SMS pour les applications mobiles natives à l’aide des bibliothèques MSAL, consultez les liens suivants : 
  - [iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)
  - [Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [Intégrer une application SAAS avec Azure Active Directory](../saas-apps/tutorial-list.md)

## <a name="recommended-content"></a>Contenu recommandé

- [Ajouter une application à votre répertoire Azure Active Directory](../manage-apps/add-application-portal.md)
- [Vue d’ensemble des bibliothèques MSAL pour acquérir un jeton auprès de la plateforme Microsoft Identity en vue d’authentifier les utilisateurs](../develop/msal-overview.md)
- [Configurer l’écran d’accueil managé par Microsoft avec Azure AD](/mem/intune/apps/app-configuration-managed-home-screen-app)
