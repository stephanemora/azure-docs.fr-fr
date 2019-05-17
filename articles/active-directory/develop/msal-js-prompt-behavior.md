---
title: Invite de comportement dans des requêtes interactives (bibliothèque d’authentification Microsoft pour JavaScript) | Azure
description: En savoir plus sur la personnalisation du comportement de l’invite dans les appels interactifs à l’aide de la bibliothèque d’authentification Microsoft pour JavaScript (MSAL.js).
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd0d736345f312f1a1d6f8f029b41429a3e5f0a7
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544268"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Comportement de l’invite dans des requêtes interactives MSAL.js

Lorsqu’un utilisateur a établi un Azure active session Active Directory avec plusieurs comptes d’utilisateur, la page de connexion Azure AD par défaut invite l’utilisateur à sélectionner un compte avant de commencer à se connecter. Utilisateurs ne verront une sélection de compte expérience s’il existe uniquement une session authentifiée auprès d’Azure AD.

La bibliothèque MSAL.js (à partir de v0.2.4) n’envoie pas un paramètre de demande pendant les requêtes interactives (`loginRedirect`, `loginPopup`, `acquireTokenRedirect` et `acquireTokenPopup`) et ainsi ne pas appliquer de tout comportement de l’invite. Pour les demandes de jeton en mode silencieux à l’aide de la `acquireTokenSilent` méthode, MSAL.js transmet un paramètre de demande la valeur `none`.

Selon votre scénario d’application, vous pouvez contrôler le comportement de l’invite pour les demandes interactives en définissant le paramètre d’invite dans les paramètres de requête passés aux méthodes. Par exemple, si vous souhaitez appeler l’expérience de sélection de compte :

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Les valeurs d’invites suivantes peuvent être transmis lors de l’authentification auprès d’Azure AD :

**connectez-vous :** Cette valeur force l’utilisateur à entrer les informations d’identification sur la demande d’authentification.

**select_account:** Cette valeur fournira l’utilisateur une expérience de sélection de compte répertoriant tous les comptes dans la session.

**consentement :** Cette valeur appelle la boîte de dialogue de consentement OAuth qui permet aux utilisateurs d’accorder des autorisations à l’application.

**None :** Cette valeur garantit que l’utilisateur ne voit pas aucune invite interactive. Il est recommandé de ne pas passer cette valeur à des méthodes interactives dans MSAL.js comme il peut avoir des comportements inattendus. Au lieu de cela, utilisez le `acquireTokenSilent` pour obtenir des appels en mode silencieux.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la `prompt` paramètre dans le [octroi implicite OAuth 2.0](v2-oauth2-implicit-grant-flow.md) protocole utilise la bibliothèque MSAL.js.
