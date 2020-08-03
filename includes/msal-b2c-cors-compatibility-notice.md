---
author: mmacy
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.date: 07/27/2020
ms.author: marsma
ms.openlocfilehash: 99ca8e45da27f5bce5258d55f46bcfa25b358239
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87311537"
---
> [!IMPORTANT]
> Actuellement, MSAL.js 2.0 ne prend pas en charge l’utilisation d’Azure AD B2C avec le flux de code d’autorisation PKCE. Pour l’heure, Azure AD B2C recommande d’utiliser le flux implicite, tel qu’il est décrit dans [Tutoriel : Inscrire une application][implicit-flow]. Pour suivre la progression de ce problème, consultez la [page wiki MSAL.js][msal-wiki].

[github-issue]: https://github.com/AzureAD/microsoft-authentication-library-for-js/issues/1795
[implicit-flow]: ../articles/active-directory-b2c/tutorial-register-applications.md
[msal-wiki]: https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-browser-B2C-CORS-issue
