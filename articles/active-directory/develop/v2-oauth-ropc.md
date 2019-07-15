---
title: Utilisez la plateforme d’identité Microsoft pour connecter des utilisateurs à l’aide des informations d'identification du mot de passe du propriétaire de la ressource (ROPC) | Azure
description: Prise en charge des flux d’authentification sans navigateur avec l’octroi des informations d’identification de mot de passe du propriétaire des ressources (ROPC, Resource Owner Password Credential).
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/20/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: da111311de7b873be6453862ffcbd56fe546ea7f
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482382"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-resource-owner-password-credential"></a>La plateforme d’identité Microsoft et les informations d’identification de mot de passe du propriétaire des ressources OAuth 2.0

La plateforme d’identité Microsoft prend en charge l’[octroi des informations d’identification de mot de passe du propriétaire des ressources](https://tools.ietf.org/html/rfc6749#section-4.3), qui permet à une application de connecter l’utilisateur en gérant directement son mot de passe. Le flux ROPC nécessite un degré élevé de confiance et d’exposition des utilisateurs, et vous devez utiliser ce flux seulement quand il est impossible d’utiliser d’autres flux plus sécurisés.

> [!IMPORTANT]
>
> * Le point de terminaison de la plateforme d’identité Microsoft prend en charge ROPC seulement pour les locataires Azure AD, et non pas pour les comptes personnels. Cela signifie que vous devez utiliser un point de terminaison spécifique au locataire (`https://login.microsoftonline.com/{TenantId_or_Name}`) ou le point de terminaison `organizations`.
> * Les comptes personnels qui sont invités sur un locataire Azure AD ne peuvent pas utiliser ROPC.
> * Les comptes qui n’ont pas de mots de passe ne peuvent pas se connecter via ROPC. Pour ce scénario, nous vous recommandons d’utiliser à la place un autre flux pour votre application.
> * Si les utilisateurs doivent utiliser l’authentification multifacteur (MFA) pour se connecter à l’application, ils seront au lieu de cela bloqués.

## <a name="protocol-diagram"></a>Schéma de protocole

Le diagramme qui suit montre le flux ROPC.

![Diagramme montrant le flux d’informations d’identification de mot de passe du propriétaire de la ressource](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Demande d’autorisation.

Le flux ROPC est une demande unique : il envoie l’identification du client et les informations d’identification de l’utilisateur au fournisseur d’identité, puis il reçoit en retour des jetons. Le client doit demander l’adresse e-mail (UPN) et le mot de passe de l’utilisateur avant de continuer. Immédiatement après une demande réussie, le client doit supprimer de la mémoire les informations d’identification de l’utilisateur de façon sécurisée. Il ne doit jamais les enregistrer.

> [!TIP]
> Essayez d'exécuter cette requête dans Postman !
> [![Essayez d’exécuter cette requête dans Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```
// Line breaks and spaces are for legibility only.

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| Paramètre | Condition | Description |
| --- | --- | --- |
| `tenant` | Obligatoire | Locataire de l’annuaire auquel vous voulez connecter l’utilisateur. Peut être au format GUID ou sous forme de nom convivial. Ce paramètre ne peut pas être défini sur `common` ou `consumers`, mais peut être défini sur `organizations`. |
| `grant_type` | Obligatoire | Cette propriété doit être définie sur `password`. |
| `username` | Obligatoire | Adresse e-mail de l’utilisateur. |
| `password` | Obligatoire | Mot de passe de l’utilisateur. |
| `scope` | Recommandé | Une liste (séparée par des espaces) d’[étendues](v2-permissions-and-consent.md), ou d’autorisations, exigées par l’application. Dans un flux interactif, l’administrateur ou l’utilisateur doit accepter ces étendues au préalable. |

### <a name="successful-authentication-response"></a>Réponse d’authentification réussie

L’exemple suivant montre une réponse de jeton réussie :

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Paramètre | Format | Description |
| --------- | ------ | ----------- |
| `token_type` | Chaîne | Toujours défini sur `Bearer`. |
| `scope` | Chaînes séparées par un espace | Si un jeton d’accès est retourné, ce paramètre liste les étendues pour lesquelles le jeton d’accès est valide. |
| `expires_in`| int | Nombre de secondes pendant lesquelles le jeton d’accès inclus est valide. |
| `access_token`| Chaîne opaque | Émise pour les [étendues](v2-permissions-and-consent.md) qui ont été demandées. |
| `id_token` | JWT | Émis si le paramètre `scope` d’origine inclut l’étendue `openid`. |
| `refresh_token` | Chaîne opaque | Émise si le paramètre `scope` d’origine inclut `offline_access`. |

Vous pouvez utiliser le jeton d’actualisation pour acquérir de nouveaux jetons d’accès et actualiser des jetons avec le même flux détaillé décrit dans la [documentation du flux de code OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Réponse d’erreur

Si l’utilisateur n’a pas fourni le nom d’utilisateur ou le mot de passe correct, ou si le client n’a pas reçu le consentement demandé, l’authentification échoue.

| Error | Description | Action du client |
|------ | ----------- | -------------|
| `invalid_grant` | Échec de l’authentification | Les informations d’identification étaient incorrectes ou le client n’a pas le consentement pour les étendues demandées. Si les étendues ne sont pas accordées, une erreur `consent_required` est retournée. Si cela se produit, le client doit envoyer l’utilisateur sur une invite interactive avec une vue web ou un navigateur. |
| `invalid_request` | La demande a été incorrectement construite | Le type d’octroi n’est pas pris en charge sur les contextes d’authentification `/common` ou `/consumers`.  Utilisez `/organizations` à la place. |
| `invalid_client` | L’application est incorrectement configurée | Ceci peut se produire si la propriété `allowPublicClient` n’est pas définie sur true dans le [manifeste de l’application](reference-app-manifest.md). La propriété `allowPublicClient` est nécessaire, car l’octroi ROPC n’a pas d’URI de redirection. Azure AD ne peut pas déterminer si l’application est une application cliente publique ou une application cliente confidentielle, sauf si la propriété est définie. ROPC est pris en charge seulement pour les applications clientes publiques. |

## <a name="learn-more"></a>En savoir plus

* Essayez ROPC par vous-même en utilisant l’[exemple d’application de console](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limitations de la plateforme d’identité Microsoft](active-directory-v2-limitations.md).
