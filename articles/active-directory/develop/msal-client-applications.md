---
title: Les applications clientes (Microsoft Authentication Library) | Azure
description: En savoir plus sur client public et de client confidentiel applications dans la bibliothèque d’authentification Microsoft (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
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
ms.openlocfilehash: f21aa62bae7599cf586ccf6f885ad9f58c148d1f
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077139"
---
# <a name="public-client-and-confidential-client-applications"></a>Client public et les applications de client confidentiel
Bibliothèque d’authentification Microsoft (MSAL) définit deux types de clients : les clients publics et les clients confidentiels. Les deux types de client sont distinguent par leur capacité à s’authentifier en toute sécurité avec le serveur d’autorisation et de préserver la confidentialité de leurs informations d’identification du client.  En revanche, Azure AD Authentication Library (ADAL) propose le concept de contexte d’authentification (qui est une connexion à Azure AD).

- **Les applications client confidentiel** sont des applications qui s’exécutent sur des serveurs (Web Apps, API Web ou même les applications de service/démon). Ils sont considérés comme difficiles d’accès et par conséquent capable de maintenir un secret d’application. Les clients confidentiels sont en mesure de stocker des clés secrètes de temps de configuration. Chaque instance du client a une configuration distincte (y compris l’ID de client et le secret). Ces valeurs sont difficiles pour les utilisateurs finaux à extraire. Une application web est la plus courant client confidentiel. L’ID client est exposé via un navigateur web, mais la clé secrète est transmise uniquement dans le canal arrière et jamais directement exposée.

    Applications de client confidentiel : <BR>
    ![Application Web](media/msal-client-applications/web-app.png) ![API Web](media/msal-client-applications/web-api.png) ![service/démon](media/msal-client-applications/daemon-service.png)

- **Les applications de client public** sont des applications qui s’exécutent sur des appareils ou ordinateurs de bureau ou dans un navigateur web. Ils ne sont pas approuvés pour les conserver en toute sécurité les secrets de l’application et par conséquent uniquement accéder aux API Web part de l’utilisateur (ils seulement prennent en charge les flux de client public). Les clients publics ne peuvent pas stocker des clés secrètes de temps de configuration et par conséquent n’avoir aucune clé secrète client.

    Applications de client public : <BR>
    ![Application de bureau](media/msal-client-applications/desktop-app.png) ![API Browserless](media/msal-client-applications/browserless-app.png) ![application Mobile](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> Dans MSAL.js, il n’existe pas de séparation entre les applications clientes publiques et confidentielles.  MSAL.js représente les applications clientes sous la forme d’applications basé sur l’agent utilisateur, un client public dans lequel le code client est exécuté dans un agent utilisateur comme un navigateur web.  Ces clients ne stockent pas de secrets, étant donné que le contexte de navigateur est directement accessible.

## <a name="comparing-the-client-types"></a>Comparaison des types de clients
Il existe certains points communs et les différences entre le client public et de client confidentiel applications :

- Les deux types d’applications maintenir un cache de jeton d’utilisateur et peuvent acquérir un jeton en mode silencieux (dans les cas où le jeton est déjà dans le cache de jetons). Les applications de client confidentiel ont également un cache de jeton d’application pour les jetons qui sont utilisés pour l’application elle-même.
- Les deux gérer les comptes d’utilisateur et peuvent obtenir les comptes à partir du cache de jeton d’utilisateur, obtenir un compte à partir de son identificateur ou supprimer un compte.
- Les applications de client public ont quatre façons d’acquérir un jeton (quatre flux d’authentification), tandis que les applications client confidentiel comportent trois (et une méthode pour calculer l’URL du fournisseur d’identité autoriser le point de terminaison). Pour plus d’informations, consultez les scénarios et l’acquisition de jetons.

Si vous avez utilisé la bibliothèque ADAL dans le passé, vous pouvez remarquer que, contrairement à contexte d’authentification de la bibliothèque ADAL, dans la bibliothèque MSAL l’ID client (également appelé ID d’application ou ID d’application) est transmis qu’une seule fois à la construction de l’application et pas plus longtemps doit être répétée lors de l’acquisition d’un jeton. C’est le cas pour les applications clientes publiques et confidentielles. Constructeurs d’applications de client confidentiel sont également passés des informations d’identification du client : le secret, ils partagent avec le fournisseur d’identité.

## <a name="next-steps"></a>Étapes suivantes
Vous en saurez plus sur :
- [Options de configuration d’application client](msal-client-application-configuration.md)
- [Instanciation d’applications clientes à l’aide de MSAL.NET](msal-net-initializing-client-applications.md).
- [Instanciation des applications clientes utilisant MSAL.js](msal-js-initializing-client-applications.md).
