---
title: Acquérir et mettre en cache des jetons avec MSAL | Azure
titleSuffix: Microsoft identity platform
description: Découvrez l’acquisition et la mise en cache des jetons à l’aide de Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 647dff9e6401322371ef795a25ca5ced2b517e9c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81534582"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>Acquérir et mettre en cache des jetons à l’aide de Microsoft Authentication Library (MSAL)

Les [jetons d’accès](access-tokens.md) permettent aux clients d’appeler de manière sécurisée des API web protégées par Azure. Il existe de nombreuses façons d’acquérir un jeton à l’aide de Microsoft Authentication Library (MSAL). Certaines d’entre elles exigent des interactions utilisateur par le biais d’un navigateur web. D’autres non. En règle générale, la façon d’acquérir un jeton dépend de la nature de l’application : s’agit-il d’une application cliente publique (application de bureau ou mobile) ou d’une application cliente confidentielle (application web, API web ou application de démon comme un service Windows) ?

MSAL met en cache un jeton après son acquisition.  Le code de l’application doit d’abord essayer d’obtenir un jeton silencieusement (à partir du cache), avant de l’acquérir par d’autres moyens.

Vous pouvez également effacer le cache de jetons, en supprimant les comptes du cache. Le cookie de session qui se trouve dans le navigateur n’est quant à lui pas supprimé.

## <a name="scopes-when-acquiring-tokens"></a>Étendues lors de l’acquisition de jetons

Les [étendues](v2-permissions-and-consent.md) correspondent aux autorisations qu’une API web expose pour que les applications clientes demandent à y accéder. Les applications clientes demandent le consentement de l’utilisateur pour ces étendues lors des demandes d’authentification pour obtenir des jetons permettant d’accéder aux API web. MSAL vous permet d’obtenir des jetons pour accéder à des API Azure AD pour développeurs (v1.0) et de la plateforme d’identités Microsoft (v2.0). Le protocole v2.0 utilise des étendues au lieu de ressources dans les demandes. Pour plus d’informations, consultez la [comparaison entre v1.0 et v2.0](active-directory-v2-compare.md). Selon la configuration de la version de jeton acceptée par l’API web, le point de terminaison v2.0 retourne un jeton d’accès à MSAL.

Plusieurs méthodes d’acquisition de jeton MSAL nécessitent un paramètre *scopes*. Ce paramètre est une simple liste de chaînes qui déclarent les autorisations souhaitées et les ressources demandées. Les [autorisations Microsoft Graph](/graph/permissions-reference) sont des étendues bien connues.

Il est également possible d’accéder à des ressources v1.0 dans MSAL. Pour plus d’informations, lisez [Étendues pour une application v1.0](msal-v1-app-scopes.md).

### <a name="request-specific-scopes-for-a-web-api"></a>Demander des étendues spécifiques pour une API web

Quand votre application a besoin de demander des jetons avec des autorisations spécifiques pour une API de ressource, vous devez passer les étendues contenant l’URI de l’ID d’application de l’API au format ci-dessous : *&lt;URI de l’ID d’application&gt;/&lt;étendue&gt;*

Par exemple, les étendues pour l’API Microsoft Graph sont : `https://graph.microsoft.com/User.Read`

Ou, par exemple, les étendues pour une API web personnalisée sont : `api://abscdefgh-1234-abcd-efgh-1234567890/api.read`

Pour l’API Microsoft Graph uniquement, une valeur d’étendue `user.read` est mappée au format `https://graph.microsoft.com/User.Read` et peut être utilisée de manière interchangeable.

> [!NOTE]
> Certaines API web comme l’API Azure Resource Manager (https://management.core.windows.net/) ) attendent une barre oblique (/) de fin dans la revendication d’audience (aud) du jeton d’accès. Le cas échéant, il est important de passer l’étendue en tant que https://management.core.windows.net//user_impersonation (notez la double barre oblique), pour que le jeton soit valide dans l’API.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Demander des étendues dynamiques pour un consentement incrémentiel

Quand vous génériez des applications avec v1.0, vous deviez enregistrer le jeu complet d’autorisations (étendues statiques) exigé par l’application pour que l’utilisateur donne son consentement au moment de la connexion. Avec v2.0, vous pouvez demander des autorisations supplémentaires en fonction des besoins à l’aide du paramètre d’étendue. Ces étendues sont qualifiées de dynamiques et permettent à l’utilisateur de donner un consentement incrémentiel aux étendues.

Par exemple, vous pouvez initialement connecter l’utilisateur, puis leur refuser tout type d’accès. Plus tard, vous pouvez leur donner la possibilité de lire le calendrier de l’utilisateur en demandant l’étendue du calendrier dans les méthodes d’acquisition de jeton afin d’obtenir le consentement de l’utilisateur.

Par exemple : `https://graph.microsoft.com/User.Read` et `https://graph.microsoft.com/Calendar.Read`

## <a name="acquiring-tokens-silently-from-the-cache"></a>Acquisition silencieuse de jetons (à partir du cache)

MSAL gère un cache de jetons (ou deux caches pour les applications clientes confidentielles) et met en cache un jeton après qu’il a été acquis.  Dans de nombreux cas, une tentative d’obtention silencieuse d’un jeton acquiert un autre jeton avec d’autres étendues basées sur un jeton dans le cache. MSAL est également capable d’actualiser un jeton quand il arrive à expiration (étant donné que le cache de jetons contient également un jeton d’actualisation).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Modèle d’appel recommandé pour les applications clientes publiques

Le code de l’application doit d’abord essayer d’obtenir un jeton silencieusement (à partir du cache).  Si l’appel de méthode retourne une erreur ou exception de type « Interface utilisateur requise », essayez d’acquérir un jeton par d’autres moyens.

En revanche, il existe deux flux avant lesquels vous **ne devez pas** essayer d’acquérir silencieusement un jeton :

- Le [flux des informations d’identification du client](msal-authentication-flows.md#client-credentials), qui n’utilise pas le cache de jetons d’utilisateur, mais un cache de jeton d’application. Cette méthode se charge de vérifier ce cache de jetons d’application avant d’envoyer une demande au STS.
- Le [flux du code d’autorisation](msal-authentication-flows.md#authorization-code) dans les applications web, car il accepte un code que l’application a obtenu en connectant l’utilisateur et en lui demandant son consentement pour d’autres d’étendues. Dans la mesure où du code est passé comme paramètre, et non un compte, la méthode ne peut pas examiner le cache avant d’accepter le code, ce qui exige de toute façon un appel au service.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Modèle d’appel recommandé dans les applications web avec le flux du code d’autorisation

Pour les applications web qui utilisent le [flux du code d’autorisation OpenID Connect](v2-protocols-oidc.md), le modèle recommandé dans les contrôleurs consiste à :

- Instancier une application cliente confidentielle avec un cache de jetons avec sérialisation personnalisée.
- Acquérir le jeton en utilisant le flux du code d’autorisation

## <a name="acquiring-tokens"></a>Acquisition des jetons

En règle générale, la méthode d’acquisition d’un jeton varie selon qu’il s’agit d’une application cliente publique ou confidentielle.

### <a name="public-client-applications"></a>Applications clientes publiques

Pour les applications clientes publiques (application de bureau ou mobile) :
- Vous acquérez souvent les jetons de manière interactive, en obligeant l’utilisateur à se connecter par le biais d’une interface utilisateur ou d’une fenêtre contextuelle.
- Vous pouvez [obtenir un jeton silencieusement pour l’utilisateur connecté](msal-authentication-flows.md#integrated-windows-authentication) à l’aide de l’authentification Windows intégrée (IWA/Kerberos) si l’application de bureau est en cours d’exécution sur un ordinateur Windows joint à un domaine ou à Azure.
- Vous pouvez [obtenir un jeton avec un nom d’utilisateur et un mot de passe](msal-authentication-flows.md#usernamepassword) dans les applications clientes de bureau .NET Framework, mais nous le déconseillons. Vous n’utilisez pas de nom d’utilisateur/mot de passe dans les applications clientes confidentielles.
- Vous pouvez acquérir un jeton par le biais du [flux de code d’appareil](msal-authentication-flows.md#device-code) dans les applications qui s’exécutent sur des appareils qui n’ont pas de navigateur web. L’utilisateur reçoit une URL et un code. Il accède alors à un navigateur web sur un autre appareil, puis entre le code pour se connecter.  Azure AD renvoie alors un jeton à l’appareil sans navigateur.

### <a name="confidential-client-applications"></a>Applications clientes confidentielles

Pour les applications clientes confidentielles (application web, API web ou application de démon comme un service Windows) :
- Vous acquérez des jetons **pour l’application elle-même** et non pour un utilisateur, à l’aide du [flux des informations d’identification du client](msal-authentication-flows.md#client-credentials). Ce dernier peut être utilisé pour des outils de synchronisation ou des outils qui traitent des utilisateurs en général et non un utilisateur spécifique.
- Vous utilisez le [flux OBO (On-Behalf-Of)](msal-authentication-flows.md#on-behalf-of) pour une API web afin d’appeler une API pour le compte de l’utilisateur. L’application est identifiée avec des informations d’identification du client afin d’acquérir un jeton basé sur une assertion d’utilisateur (par exemple, SAML ou un jeton JWT). Ce flux est utilisé par les applications qui ont besoin d’accéder aux ressources d’un utilisateur particulier dans des appels de service à service.
- Vous acquérez des jetons à l’aide du [flux du code d’autorisation](msal-authentication-flows.md#authorization-code) dans les applications web une fois que l’utilisateur s’est connecté par le biais de l’URL de la demande d’autorisation. L’application OpenID Connect utilisent généralement ce mécanisme, ce qui permet à l’utilisateur de se connecter à l’aide d’Open ID Connect, puis d’accéder aux API web pour le compte de l’utilisateur.

## <a name="authentication-results"></a>Résultats d’authentification

Quand votre client demande un jeton d’accès, Azure AD retourne également un résultat d’authentification qui inclut des métadonnées sur le jeton d’accès. Ces informations incluent le délai d’expiration du jeton d’accès et les étendues dans lesquelles il est valide. Ces données permettent à votre application d’effectuer une mise en cache intelligente des jetons d’accès sans avoir à les analyser eux-mêmes.  Le résultat d’authentification expose :

- Le [jeton d’accès](access-tokens.md) pour que l’API web accède aux ressources. Il s’agit d’une chaîne, généralement une méthode JWT encodée en base64, mais le client ne doit jamais regarder à l’intérieur du jeton d’accès. La stabilité du format n’est pas garantie et ce dernier peut être chiffré pour la ressource. L’écriture humaine de code qui dépend du contenu des jetons d’accès sur le client constitue l’une des sources d’erreurs et des ruptures de logique client les plus importantes.
- Le [jeton d’ID](id-tokens.md) pour l’utilisateur (il s’agit d’un jeton JWT).
- L’expiration du jeton, qui indique la date/heure auxquelles le jeton expire.
- L’ID de locataire contient le locataire dans lequel l’utilisateur a été trouvé. Pour les utilisateurs invités (scénarios d’Azure AD B2B), l’ID de locataire correspond au locataire invité, pas au locataire unique. Quand le jeton est remis au nom d’un utilisateur, le résultat d’authentification contient également des informations sur cet utilisateur. Pour les flux de clients confidentiels où les jetons sont demandés sans aucun utilisateur (pour l’application), ces informations sur l’utilisateur sont manquantes ou inconnues.
- Les étendues pour lesquelles le jeton a été émis.
- L’ID unique de l’utilisateur.

## <a name="next-steps"></a>Étapes suivantes

Si vous utilisez MSAL pour Java, découvrez la [Sérialisation du cache de jeton personnalisé dans MSAL pour Java](msal-java-token-cache-serialization.md).

Découvrez la [gestion des erreurs et des exceptions](msal-handling-exceptions.md).
