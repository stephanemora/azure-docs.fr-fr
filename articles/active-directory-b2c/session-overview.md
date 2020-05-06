---
title: Sessions d’authentification unique dans Azure Active Directory B2C | Microsoft Docs
description: Configurez le comportement de session dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1f7be4d01dd930e9ff421b2a163f1648f1793da9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82230791"
---
# <a name="azure-ad-b2c-session"></a>Session Azure AD B2C

Lorsque les utilisateurs s’authentifient auprès d’applications dans Azure Active Directory B2C (Azure AD B2C), l’authentification unique (SSO) constitue la méthode la plus sécurisée et la plus pratique. Cet article décrit les différentes méthodes d’authentification unique utilisées dans Azure AD B2C et vous aide à choisir la méthode SSO la plus appropriée lors de la configuration de votre stratégie.

Avec l’authentification unique, les utilisateurs se connectent une seule fois avec un seul compte et accèdent à plusieurs applications. L’application peut être une application web, mobile ou monopage, indépendamment du nom de la plateforme ou du domaine.

Lorsque l’utilisateur se connecte pour la première fois à une application, Azure AD B2C maintient une session basée sur un cookie. Lors des demandes d’authentification suivantes, Azure AD B2C lit et valide la session basée sur un cookie et émet un jeton d’accès sans inviter l’utilisateur à se reconnecter. Si la session basée sur un cookie expire ou devient non valide, l’utilisateur est invité à se connecter à nouveau.  

## <a name="sso-session-types"></a>Types de sessions SSO

L’intégration avec Azure AD B2C implique trois types de sessions SSO :

- **Azure AD B2C** : session gérée par Azure AD B2C
- **Fournisseur d’identité fédéré** : session gérée par le fournisseur d’identité, par exemple Facebook, Salesforce ou compte Microsoft
- **Application** : session gérée par l’application web, mobile ou monopage

![Session SSO](media/session-overview/sso-session-types.png)

### <a name="azure-ad-b2c-sso"></a>SSO Azure AD B2C 

Quand un utilisateur réussit à s’authentifier avec un compte local ou social, Azure AD B2C stocke une session basée sur un cookie sur le navigateur de l’utilisateur. Le cookie est stocké sous le nom de domaine du locataire Azure AD B2C, par exemple, `https://contoso.b2clogin.com`.

Si un utilisateur se connecte initialement avec un compte fédéré, puis, pendant la fenêtre de temps de la session (durée de vie ou TTL), se connecte à la même application ou à une autre application, Azure AD B2C essaie d’acquérir un nouveau jeton d’accès auprès du fournisseur d’identité fédéré. Si la session du fournisseur d’identité fédéré a expiré ou n’est pas valide, le fournisseur d’identité fédéré invite l’utilisateur à entrer ses informations d’identification. Si la session est toujours active (ou si l’utilisateur s’est connecté avec un compte local au lieu d’un compte fédéré), Azure AD B2C autorise l’utilisateur et élimine les invites supplémentaires.

Vous pouvez configurer le comportement de la session, dont sa durée de vie et la façon dont Azure AD B2C partage la session entre les stratégies et les applications.

### <a name="federated-identity-provider-sso"></a>SSO de fournisseur d’identité fédéré

Un fournisseur d’identité de réseaux sociaux ou d’entreprise gère sa propre session. Le cookie est stocké sous le nom de domaine du fournisseur d’identité, par exemple `https://login.salesforce.com`. Azure AD B2C ne contrôle pas la session du fournisseur d’identité fédéré. Au lieu de cela, le comportement de session est déterminé par le fournisseur d’identité fédéré. 

Examinez le cas suivant :

1. Un utilisateur se connecte à Facebook pour vérifier son flux.
2. Plus tard, l’utilisateur ouvre votre application et démarre le processus de connexion. L’application redirige l’utilisateur vers Azure AD B2C pour terminer le processus de connexion.
3. Sur la page d’inscription ou de connexion d’Azure AD B2C, l’utilisateur choisit de se connecter avec son compte Facebook. L’utilisateur est redirigé vers Facebook. S’il existe une session active sur Facebook, l’utilisateur n’est pas invité à fournir ses informations d’identification et est immédiatement redirigé vers Azure AD B2C avec un jeton Facebook.

### <a name="application-sso"></a>Authentification unique des applications

Une application web, mobile ou monopage peut être protégée par un accès OAuth, des jetons d’ID ou des jetons SAML. Quand un utilisateur tente d’accéder à une ressource protégée sur l’application, celle-ci vérifie s’il existe une session active côté application. S’il n’y a pas de session d’application ou si la session a expiré, l’application dirige l’utilisateur vers la page de connexion d’Azure AD B2C.

La session d’application peut être une session basée sur un cookie stockée sous le nom de domaine de l’application, par exemple `https://contoso.com`. Les applications mobiles peuvent stocker la session d’une façon différente, mais dont l’approche est similaire.

## <a name="azure-ad-b2c-session-configuration"></a>Configuration de session Azure AD B2C

### <a name="session-scope"></a>Étendue de session

Vous pouvez configurer la session Azure AD B2C avec les étendues suivantes :

- **Client** : il s’agit du paramètre par défaut. L’utilisation de ce paramètre permet à plusieurs applications et flux d’utilisateur dans votre locataire B2C de partager la même session utilisateur. Par exemple, quand un utilisateur se connecte à une application, il peut également se connecter sans problème à une autre en y accédant.
- **Application**: ce paramètre vous permet de maintenir une session utilisateur exclusivement pour une application, indépendamment des autres applications. Par exemple, vous pouvez utiliser ce paramètre si vous voulez que l’utilisateur se connecte à Contoso Pharmacy, que l’utilisateur y soit ou non déjà connecté.
- **Stratégie** : ce paramètre vous permet de maintenir une session utilisateur exclusivement pour un flux d’utilisateur, indépendamment des applications qui l’utilisent. Par exemple, si l’utilisateur s’est déjà connecté et a effectué une étape d’authentification multifacteur, il peut obtenir l’accès à des parties plus sécurisées de plusieurs applications tant que la session liée au flux d’utilisateur n’expire pas.
- **Désactivé** : ce paramètre oblige l’utilisateur à réexécuter tout le flux d’utilisateur à chaque exécution de la stratégie.

### <a name="session-life-time"></a>Durée de vie de la session

La **durée de vie de la session** correspond à la durée pendant laquelle le cookie de session Azure AD B2C est stocké sur le navigateur de l’utilisateur après une authentification réussie. Vous pouvez définir la durée de vie de la session sur une valeur comprise entre 15 et 720 minutes.

### <a name="keep-me-signed-in"></a>Maintenir la connexion

La fonctionnalité [Maintenir la connexion](custom-policy-keep-me-signed-in.md) étend la durée de vie de la session à l’aide d’un cookie persistant. La session reste active lorsque l’utilisateur ferme et rouvre le navigateur. La session est révoquée uniquement quand un utilisateur se déconnecte. La fonctionnalité Maintenir la connexion s’applique uniquement à la connexion avec des comptes locaux.

La fonctionnalité Maintenir la connexion prend le pas sur la durée de vie de la session. Si la fonctionnalité Maintenir la connexion est activée et que l’utilisateur la sélectionne, elle détermine la date d’expiration de la session. 

### <a name="session-expiry-type"></a>Type d’expiration de session

Le **Type d’expiration de session** indique comment une session est étendue par le paramètre Durée de vie de la session ou le paramètre Maintenir la connexion.

- **Continue** : indique que la session est étendue chaque fois que l’utilisateur effectue une authentification basée sur un cookie (valeur par défaut).
- **Absolue** : indique que l’utilisateur est obligé de se réauthentifier après la période spécifiée.

## <a name="sign-out"></a>Se déconnecter

Quand vous souhaitez déconnecter l’utilisateur de l’application, il ne suffit pas de supprimer les cookies de l’application ou d’arrêter la session de l’utilisateur. Vous devez rediriger l’utilisateur vers Azure AD B2C pour le déconnecter. Autrement, l’utilisateur peut être en mesure de se réauthentifier auprès de votre application sans entrer à nouveau ses informations d’identification.

Lors d’une demande de déconnexion, Azure AD B2C effectue les opérations suivantes :

1. Invalide la session Azure AD B2C basée sur un cookie.
1. Tente de se déconnecter des fournisseurs d’identité fédérés :
   - OpenId Connect : si le point de terminaison de configuration connu du fournisseur d’identité spécifie un emplacement `end_session_endpoint`.
   - SAML : si les métadonnées du fournisseur d’identité contiennent l’emplacement `SingleLogoutService`.
1. Si vous le souhaitez, se déconnecte d’autres applications. Pour plus d’informations, consultez la section [Déconnexion unique](#single-sign-out).

> [!NOTE]
> La déconnexion efface l’état d’authentification unique de l’utilisateur auprès d’Azure AD B2C, mais il est possible qu’elle ne déconnecte pas l’utilisateur de sa session de fournisseur d’identité sociale. Si l’utilisateur sélectionne le même fournisseur d’identité lors d’une connexion ultérieure, il pourrait se réauthentifier sans avoir à entrer ses informations d’identification. Si un utilisateur veut se déconnecter de l’application, cela ne signifie pas nécessairement qu’il souhaite se déconnecter de son compte Facebook. Toutefois, si les comptes locaux sont utilisés, la session de l’utilisateur se termine correctement.

### <a name="single-sign-out"></a>Déconnexion unique

Lorsque vous redirigez l’utilisateur vers le point de terminaison de déconnexion Azure AD B2C (pour les protocoles OAuth2 et SAML), Azure AD B2C efface la session de l’utilisateur du navigateur. Toutefois, l’utilisateur pourrait rester connecté à d’autres applications utilisant Azure AD B2C pour l’authentification. Pour permettre à ces applications de déconnecter l’utilisateur simultanément, Azure AD B2C envoie une requête HTTP GET à la `LogoutUrl` inscrite de toutes les applications auxquelles l’utilisateur est actuellement connecté.

Les applications doivent répondre à cette requête en effaçant toute session qui identifie l’utilisateur et en renvoyant une réponse `200`. Si vous souhaitez prendre en charge la déconnexion unique dans votre application, vous devez implémenter une `LogoutUrl` dans le code de votre application. Vous pouvez définir le paramètre `LogoutUrl` à partir du portail Azure :

1. Accédez au [portail Azure](https://portal.azure.com).
1. Sélectionnez votre client Azure AD B2C en cliquant sur votre compte en haut à droite de la page.
1. Dans le volet de navigation de gauche, choisissez **Azure AD B2C**, **Inscriptions d’applications**, puis sélectionnez votre application.
1. Sélectionnez **Paramètres**, **Propriétés**, puis recherchez la zone de texte **URL de déconnexion**. 


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [configurer un comportement de session dans un flux d’utilisateur](session-behavior.md).
- Découvrez comment [configurer un comportement de session dans une stratégie personnalisée](custom-policy-manage-sso-and-token-config.md#session-behavior-and-sso).