---
title: En savoir plus sur les protocoles d’autorisation pris en charge par Azure AD v2.0 | Microsoft Docs
description: Un guide sur les protocoles pris en charge par le point de terminaison Azure AD v2.0.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed27830aa1f4212e4bc26af8da4febc1b61a76cc
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175090"
---
# <a name="v20-protocols---oauth-20-and-openid-connect"></a>Protocoles v2.0 - OAuth 2.0 et OpenID Connect

Le point de terminaison v2.0 peut utiliser Azure Active Directory (Azure AD) pour l’identité en tant que service avec les protocoles standard, OpenID Connect et OAuth 2.0. Bien que ce service soit conforme aux normes, vous pouvez constater de subtiles différences entre deux implémentations différentes de ces protocoles. Les informations fournies ici vous seront utiles si vous choisissez d’écrire votre code en envoyant et en traitant directement des requêtes HTTP ou si vous utilisez une bibliothèque open source tierce, plutôt qu’en utilisant l’une de nos [bibliothèques open source](reference-v2-libraries.md).

> [!NOTE]
> Les scénarios et les fonctionnalités d’Azure AD ne sont pas tous pris en charge par le point de terminaison v2.0. Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limites de v2.0](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Concepts de base

Dans presque tous les flux OAuth 2.0 et OpenID Connect, quatre parties sont concernées par l’échange :

![Rôles OAuth 2.0](../../media/active-directory-v2-flows/protocols_roles.png)

* Le **Serveur d’autorisation** est le point de terminaison v2.0 chargé de garantir l’identité de l’utilisateur, l’octroi et la révocation de l’accès aux ressources, et l’émission de jetons. Le serveur d’autorisation est également connu sous le nom du fournisseur d’identité. Il traite de manière sécurisée les informations de l’utilisateur, leur accès et les relations de confiance entre les parties d’un flux.
* Le **Propriétaire de la ressource** est généralement l’utilisateur final. Il s’agit de la partie détentrice des données, qui a le pouvoir d’autoriser les tierces parties à accéder à ces données ou à cette ressource.
* Le **Client OAuth** est votre application, identifiée par son ID d’application. Le client OAuth, qui est en général la partie avec laquelle l’utilisateur final interagit, demande des jetons provenant du serveur d’autorisation. Le client doit se voir octroyer une autorisation d’accès à la ressource par le propriétaire de cette dernière.
* Le **serveur de ressources** héberge la ressource ou les données. Il approuve le serveur d’autorisation pour authentifier et autoriser de manière sûre le client OAuth et utilise les jetons d’accès porteurs pour garantir l’octroi de l’accès à une ressource.

## <a name="app-registration"></a>Inscription d’application
Toutes les applications qui utilisent le point de terminaison v2.0 doivent être inscrites auprès de [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) ou par le biais de la nouvelle expérience **Inscriptions d’applications (préversion)** dans le [portail Azure](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) avant de pouvoir interagir à l’aide d’OAuth ou d’OpenID Connect. Le processus d’inscription des applications collecte quelques valeurs et les affecte à votre application :

* un **ID d’application** qui identifie de manière unique votre application ;
* un **URI de redirection** ou un **identificateur de package** pouvant être utilisé pour diriger des réponses vers votre application ;
* quelques valeurs spécifiques au scénario.

Pour plus d’informations, découvrez comment [inscrire une application](quickstart-v2-register-an-app.md).

## <a name="endpoints"></a>Points de terminaison

Une fois inscrite, l’application communique avec Azure AD en transmettant les requêtes au point de terminaison v2.0 :

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Où le `{tenant}` peut prendre l’une de quatre valeurs différentes :

| Valeur | Description |
| --- | --- |
| `common` | Permet aux utilisateurs avec des comptes Microsoft personnels et des comptes professionnels/scolaires Azure AD de se connecter à l’application. |
| `organizations` | Permet uniquement aux utilisateurs avec des comptes professionnels/scolaires Azure AD de se connecter à l’application. |
| `consumers` | Permet uniquement aux utilisateurs avec des comptes personnels Microsoft (MSA) de se connecter à l’application. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` | Permet uniquement aux utilisateurs avec des comptes professionnels/scolaires d’un client Azure AD spécifique de se connecter à l’application. Le nom de domaine convivial du client Azure AD ou l’identificateur GUID du client peut être utilisé. |

Pour savoir comment interagir avec ces points de terminaison, choisissez un type particulier d’application dans la section [Protocoles](#protocols) et suivez les liens.

## <a name="tokens"></a>Jetons

L’implémentation d’OAuth 2.0 et d’OpenID Connect par v2.0 utilise massivement les jetons du porteur, y compris ceux représentés sous forme de JWT. Un jeton porteur est un jeton de sécurité léger qui octroie l’accès à une ressource protégée au « porteur ». En ce sens, le « porteur » désigne toute partie qui peut présenter le jeton. Une partie doit certes d’abord s’authentifier auprès d’Azure AD pour recevoir le jeton porteur, mais si les mécanismes nécessaires à la sécurité du jeton lors de la transmission et du stockage ne sont pas en place, il peut être intercepté et utilisé par une partie non autorisée. Bien que certains jetons de sécurité intègrent un mécanisme de protection contre l’utilisation par des parties non autorisées, les jetons porteurs n’en sont pas dotés et doivent donc être acheminés sur un canal sécurisé, par exemple à l’aide du protocole TLS (HTTPS). Si un jeton porteur est transmis en clair, une partie malveillante peut utiliser une attaque d’intercepteur pour s’approprier le jeton et l’utiliser afin d’accéder sans autorisation à une ressource protégée. Les mêmes principes de sécurité s’appliquent au stockage ou à la mise en cache des jetons porteurs pour une utilisation ultérieure. Veillez systématiquement à ce que votre application transmette et stocke les jetons porteurs de manière sécurisée. Pour en savoir plus sur les aspects de sécurité des jetons porteurs, consultez [RFC 6750 Section 5](https://tools.ietf.org/html/rfc6750).

Pour plus d’informations sur les différents types de jetons utilisés dans le point de terminaison v2.0, consultez la page de [Référence sur les jetons du point de terminaison v2.0](v2-id-and-access-tokens.md).

## <a name="protocols"></a>Protocoles

Si vous êtes prêt à voir des exemples de demandes, entamez l’un des didacticiels ci-dessous. Chacun d’eux correspond à un scénario d’authentification particulier. Si vous avez besoin d’aide pour déterminer le flux qui vous convient, consultez les [types d’applications que vous pouvez créer avec le point de terminaison v2.0](v2-app-types.md).

* [Générer une application mobile et native avec OAuth 2.0](v2-oauth2-auth-code-flow.md)
* [Générer des applications web avec OpenID Connect](v2-protocols-oidc.md)
* [Générer des applications monopages avec le flux implicite OAuth 2.0](v2-oauth2-implicit-grant-flow.md)
* [Générer des démons ou des processus côté serveur avec le flux d’informations d’identification du client OAuth 2.0](v2-oauth2-client-creds-grant-flow.md)
* [Obtenir des jetons dans une API web avec le flux Au nom de d’OAuth 2.0](v2-oauth2-on-behalf-of-flow.md)
