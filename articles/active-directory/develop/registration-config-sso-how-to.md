---
title: Configurer l’authentification unique d’application
description: Comment configurer l’authentification unique pour une application personnalisée que vous développez et inscrivez dans Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: ba1e4305785d0ff4db85f805a780719ce179ab94
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050294"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Comment configurer l’authentification unique pour une application

L’authentification unique (SSO) fédérée dans votre application est automatiquement activée dans le cadre d’une fédération via Azure AD pour OpenID Connect, SAML 2.0 ou WS-Fed. Si vos utilisateurs finaux doivent se connecter malgré une session en cours dans Azure AD, il est probable que votre application soit mal configurée.

* Si vous utilisez la bibliothèque ADAL/MSAL, assurez-vous que le paramètre **PromptBehavior** est défini sur **Automatique** plutôt que sur **Toujours**.

* Si vous créez une application mobile, vous aurez peut-être besoin de configurations supplémentaires pour activer une authentification unique avec ou sans répartiteur.

Pour Android, consultez la page [Activation de l’authentification unique entre applications sur Android à l’aide de la bibliothèque ADAL](../azuread-dev/howto-v1-enable-sso-android.md).<br>

Pour iOS, consultez la page [Activation d’une authentification unique entre applications sur iOS à l’aide de la bibliothèque ADAL](../azuread-dev/howto-v1-enable-sso-ios.md).

## <a name="next-steps"></a>Étapes suivantes

[Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)<br>

[Activation de l’authentification unique entre applications sur Android à l’aide de la bibliothèque ADAL](../azuread-dev/howto-v1-enable-sso-android.md)<br>

[Activation de l’authentification unique entre applications sur iOS à l’aide de la bibliothèque ADAL](../azuread-dev/howto-v1-enable-sso-ios.md)<br>

[Intégration d’applications dans Azure Active Directory](./quickstart-register-app.md)<br>

[Autorisations et consentement dans la plateforme d’identités Microsoft](./v2-permissions-and-consent.md)<br>

[Microsoft Q&A - Azure AD](https://docs.microsoft.com/answers/topics/azure-active-directory.html)