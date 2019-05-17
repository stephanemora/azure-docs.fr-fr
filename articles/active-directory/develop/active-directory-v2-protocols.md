---
title: En savoir plus sur les protocoles d’autorisation pris en charge par la plateforme d’identité Microsoft | Azure
description: Un guide pour les protocoles OAuth 2.0 et OpenID Connect qui sont pris en charge par le point de terminaison Microsoft identity platform.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 536210922d13f66aaa5a09bd87bd2d92da8d416c
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546112"
---
# <a name="microsoft-identity-platform-protocols"></a>Protocoles de plateforme d’identité Microsoft

Microsoft identity plateforme point de terminaison pour l’identité-as-a-service avec les protocoles standard, OpenID Connect et OAuth 2.0. Bien que ce service soit conforme aux normes, vous pouvez constater de subtiles différences entre deux implémentations différentes de ces protocoles. Les informations fournies ici vous seront utiles si vous choisissez d’écrire votre code en envoyant et en traitant directement des requêtes HTTP ou si vous utilisez une bibliothèque open source tierce, plutôt qu’en utilisant l’une de nos [bibliothèques open source](reference-v2-libraries.md).

> [!NOTE]
> Pas tous les scénarios Azure AD et les fonctionnalités sont prises en charge par le point de terminaison Microsoft identity platform. Pour déterminer si vous devez utiliser le point de terminaison Microsoft identity plateforme, consultez les [limitations de plateforme d’identité Microsoft](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Concepts de base

Dans presque tous les flux OAuth 2.0 et OpenID Connect, quatre parties sont concernées par l’échange :

![Rôles OAuth 2.0](./media/active-directory-v2-flows/protocols-roles.svg)

* Le **serveur d’autorisation** est le point de terminaison Microsoft identity platform et responsable de l’identité l’utilisateur, l’octroi et de révocation de l’accès aux ressources et d’émission de jetons. Le serveur d’autorisation est également connu sous le nom du fournisseur d’identité. Il traite de manière sécurisée les informations de l’utilisateur, leur accès et les relations de confiance entre les parties d’un flux.
* Le **Propriétaire de la ressource** est généralement l’utilisateur final. Il s’agit de la partie détentrice des données, qui a le pouvoir d’autoriser les tierces parties à accéder à ces données ou à cette ressource.
* Le **Client OAuth** est votre application, identifiée par son ID d’application. Le client OAuth, qui est en général la partie avec laquelle l’utilisateur final interagit, demande des jetons provenant du serveur d’autorisation. Le client doit se voir octroyer une autorisation d’accès à la ressource par le propriétaire de cette dernière.
* Le **serveur de ressources** héberge la ressource ou les données. Il approuve le serveur d’autorisation pour authentifier et autoriser le OAuth Client en toute sécurité et utilise des jetons d’accès du porteur pour garantir que l’accès à une ressource peut être accordé.

## <a name="app-registration"></a>Inscription d’application

Chaque application qui souhaite accepter à la fois personnelles et comptes professionnels ou scolaires doive être inscrits via le **inscriptions** d’expérience dans le [portail Azure](https://aka.ms/appregistrations) avant il peut se connecter à ces utilisateurs à l’aide d’OAuth 2.0 ou OpenID Connect. Le processus d’inscription des applications collecte quelques valeurs et les affecte à votre application :

* un **ID d’application** qui identifie de manière unique votre application ;
* un **URI de redirection** ou un **identificateur de package** pouvant être utilisé pour diriger des réponses vers votre application ;
* quelques valeurs spécifiques au scénario.

Pour plus d’informations, découvrez comment [inscrire une application](quickstart-register-app.md).

## <a name="endpoints"></a>Points de terminaison

Une fois inscrit, l’application communique avec la plateforme d’identité Microsoft en envoyant des requêtes au point de terminaison :

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Où le `{tenant}` peut prendre l’une de quatre valeurs différentes :

| Valeur | Description  |
| --- | --- |
| `common` | Permet aux utilisateurs avec des comptes Microsoft personnels et des comptes professionnels/scolaires Azure AD de se connecter à l’application. |
| `organizations` | Permet uniquement aux utilisateurs avec des comptes professionnels/scolaires Azure AD de se connecter à l’application. |
| `consumers` | Permet uniquement aux utilisateurs avec des comptes personnels Microsoft (MSA) de se connecter à l’application. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` | Permet uniquement aux utilisateurs avec des comptes professionnels/scolaires d’un client Azure AD spécifique de se connecter à l’application. Le nom de domaine convivial du client Azure AD ou l’identificateur GUID du client peut être utilisé. |

Pour savoir comment interagir avec ces points de terminaison, choisissez un type particulier d’application dans la section [Protocoles](#protocols) et suivez les liens.

> [!TIP]
> N’importe quelle application inscrite dans Azure AD peut utiliser le point de terminaison Microsoft identity platform, même si elles ne vous connectez des comptes personnels.  De cette façon, vous pouvez migrer des applications existantes vers la plateforme d’identité Microsoft et [MSAL](reference-v2-libraries.md) sans recréer votre application.  

## <a name="tokens"></a>Jetons

L’implémentation de plateforme d’identité Microsoft de OAuth 2.0 et OpenID Connect utilisent beaucoup les jetons du porteur, y compris ceux représentés sous forme de Jwt. Un jeton porteur est un jeton de sécurité léger qui octroie l’accès à une ressource protégée au « porteur ». En ce sens, le « porteur » désigne toute partie qui peut présenter le jeton. Si un tiers doit tout d’abord s’authentifier avec la plateforme d’identité Microsoft pour recevoir le jeton du porteur, si les étapes requises pour sécuriser le jeton dans la transmission et du stockage ne sont pas prises, il peut être interceptée et utilisé par un tiers indésirable. Bien que certains jetons de sécurité intègrent un mécanisme de protection contre l’utilisation par des parties non autorisées, les jetons porteurs n’en sont pas dotés et doivent donc être acheminés sur un canal sécurisé, par exemple à l’aide du protocole TLS (HTTPS). Si un jeton porteur est transmis en clair, une partie malveillante peut utiliser une attaque d’intercepteur pour s’approprier le jeton et l’utiliser afin d’accéder sans autorisation à une ressource protégée. Les mêmes principes de sécurité s’appliquent au stockage ou à la mise en cache des jetons porteurs pour une utilisation ultérieure. Veillez systématiquement à ce que votre application transmette et stocke les jetons porteurs de manière sécurisée. Pour en savoir plus sur les aspects de sécurité des jetons porteurs, consultez [RFC 6750 Section 5](https://tools.ietf.org/html/rfc6750).

Plus d’informations de différents types de jetons utilisés dans le Microsoft endpoint de plateforme d’identité est disponible dans [la référence de jeton de point de terminaison de plateforme d’identité Microsoft](v2-id-and-access-tokens.md).

## <a name="protocols"></a>Protocoles

Si vous êtes prêt à voir des exemples de demandes, entamez l’un des didacticiels ci-dessous. Chacun d’eux correspond à un scénario d’authentification particulier. Si vous avez besoin d’aide pour déterminer qui est le flux pour vous, consultez [les types d’applications que vous pouvez créer avec la plateforme d’identité Microsoft](v2-app-types.md).

* [Générer une application mobile et native avec OAuth 2.0](v2-oauth2-auth-code-flow.md)
* [Générer des applications web avec OpenID Connect](v2-protocols-oidc.md)
* [Générer des applications monopages avec le flux implicite OAuth 2.0](v2-oauth2-implicit-grant-flow.md)
* [Générer des démons ou des processus côté serveur avec le flux d’informations d’identification du client OAuth 2.0](v2-oauth2-client-creds-grant-flow.md)
* [Obtenir des jetons dans une API web avec le flux Au nom de d’OAuth 2.0](v2-oauth2-on-behalf-of-flow.md)
