---
title: Les applications clientes (Microsoft Authentication Library) | Azure
description: En savoir plus sur client public et de client confidentiel applications dans la bibliothèque d’authentification Microsoft (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d09436b9a2ac38e7b07a51f01d65769ed19d08e
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430825"
---
# <a name="public-client-and-confidential-client-applications"></a>Client public et les applications de client confidentiel
Bibliothèque d’authentification Microsoft (MSAL) définit deux types de clients : les clients publics et les clients confidentiels. Les deux types de client sont distinguent par leur capacité à s’authentifier en toute sécurité avec le serveur d’autorisation et de préserver la confidentialité de leurs informations d’identification du client. En revanche, Azure AD Authentication Library (ADAL) utilise ce que l'on appelle *contexte d’authentification* (qui est une connexion à Azure AD).

- **Les applications client confidentiel** sont des applications qui s’exécutent sur des serveurs (applications web, applications d’API Web ou même les applications de service/démon). Ils sont considérés comme difficile pour l’accès et c’est pourquoi capables de maintenir un secret d’application. Les clients confidentiels peuvent contenir des secrets du moment de la configuration. Chaque instance du client a une configuration distincte (y compris l’ID client et clé secrète client). Ces valeurs sont difficiles pour les utilisateurs finaux à extraire. Une application web est la plus courant client confidentiel. L’ID client est exposé via un navigateur web, mais la clé secrète est transmise uniquement dans le canal arrière et jamais directement exposée.

    Applications de client confidentiel : <BR>
    ![Application Web](media/msal-client-applications/web-app.png) ![API Web](media/msal-client-applications/web-api.png) ![service/démon](media/msal-client-applications/daemon-service.png)

- **Les applications de client public** sont des applications qui s’exécutent sur des appareils ou ordinateurs de bureau ou dans un navigateur web. Ils ne sont pas approuvés à conserver en toute sécurité les secrets de l’application, afin qu’ils accéder uniquement aux API Web part de l’utilisateur. (Ils prennent en charge uniquement les flux de client public.) Les clients publics ne peuvent pas contenir les secrets du moment de la configuration, afin qu’ils n’aient des clés secrètes de client.

    Applications de client public : <BR>
    ![Application de bureau](media/msal-client-applications/desktop-app.png) ![API Browserless](media/msal-client-applications/browserless-app.png) ![application Mobile](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> Dans MSAL.js, il n’existe pas de séparation entre les applications clientes publiques et confidentielles.  MSAL.js représente les applications clientes sous la forme d’applications basés sur l’agent utilisateur, les clients publics dans lequel le code client est exécuté dans un agent utilisateur comme un navigateur web. Ces clients ne pas stocker les secrets, car le contexte du navigateur est ouvertement accessible.

## <a name="comparing-the-client-types"></a>Comparaison des types de clients
Voici certaines ressemblances et les différences entre le client public et de client confidentiel applications :

- Les deux types d’application maintenir un cache de jeton d’utilisateur et peuvent acquérir un jeton en mode silencieux (lorsque le jeton est déjà dans le cache de jetons). Les applications de client confidentiel ont également un cache de jeton d’application pour les jetons qui sont utilisés pour l’application elle-même.
- Les deux types d’applications gérer les comptes d’utilisateur et peuvent obtenir un compte à partir du cache de jeton d’utilisateur, obtenir un compte à partir de son identificateur ou supprimer un compte.
- Les applications de client public ont quatre façons d’acquérir un jeton (flux d’authentification quatre). Les applications client confidentiel disposent de trois possibilités pour acquérir un jeton (et un moyen de calculer l’URL du fournisseur d’identité autoriser le point de terminaison). Pour plus d’informations, consultez [l’acquisition des jetons](msal-acquire-cache-tokens.md).

Si vous avez utilisé la bibliothèque ADAL, vous remarquerez peut-être que, contrairement au contexte d’authentification de la bibliothèque ADAL, dans la bibliothèque MSAL l’ID client (également appelé le *ID d’application* ou *ID d’application*) est passé à une seule fois à la construction de l’application. Il n’a pas besoin de passer à nouveau lorsque l’application acquiert un jeton. Cela est vrai à la fois pour les applications clientes publiques et confidentielles. Constructeurs d’applications de client confidentiel sont également passés des informations d’identification du client : le secret, ils partagent avec le fournisseur d’identité.

## <a name="next-steps"></a>Étapes suivantes
Vous en saurez plus sur :
- [Options de configuration d’application client](msal-client-application-configuration.md)
- [Applications clientes lors de l’instanciation à l’aide de MSAL.NET](msal-net-initializing-client-applications.md)
- [Applications clientes lors de l’instanciation en utilisant MSAL.js](msal-js-initializing-client-applications.md)
