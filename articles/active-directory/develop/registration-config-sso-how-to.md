---
title: Guide pratique pour configurer une nouvelle application multilocataire | Microsoft Docs
description: Comment configurer l’authentification unique pour une application personnalisée que vous développez et inscrivez dans Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: be85456c94fbc156827157705bc29169bbae9cdd
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55096757"
---
# <a name="how-to-configure-a-new-multi-tenant-application"></a>Guide pratique pour configurer une nouvelle application multilocataire

L’authentification unique (SSO) fédérée dans votre application est automatiquement activée dans le cadre d’une fédération via Azure AD pour OpenID Connect, SAML 2.0 ou WS-Fed. Si vos utilisateurs finaux doivent se connecter malgré une session en cours dans Azure AD, il est probable que votre application soit mal configurée.

* Si vous utilisez la bibliothèque ADAL/MSAL, assurez-vous que le paramètre **PromptBehavior** est défini sur **Automatique** plutôt que sur **Toujours**.

* Si vous créez une application mobile, vous aurez peut-être besoin de configurations supplémentaires pour activer une authentification unique avec ou sans répartiteur.

Pour Android, consultez la page [Activation de l’authentification unique entre applications sur Android à l’aide de la bibliothèque ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android).<br>

Pour iOS, consultez la page [Activation d’une authentification unique entre applications sur iOS à l’aide de la bibliothèque ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios).

## <a name="next-steps"></a>Étapes suivantes

[Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Activation de l’authentification unique entre applications sur Android à l’aide de la bibliothèque ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[Activation de l’authentification unique entre applications sur iOS à l’aide de la bibliothèque ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Intégration d’applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Étendues, autorisations et consentement dans le point de terminaison Azure Active Directory v2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
