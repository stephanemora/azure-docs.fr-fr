---
title: Générer une application démon appelant des API web - Plateforme d’identités Microsoft | Azure
description: Découvrez comment générer une application démon appelant des API web
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70a8c97ee1e10d53cd8da9687a9109eb4ce8ae3d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965566"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Scénario : Application démon appelant des API web

Découvrez tout ce dont vous avez besoin pour créer une application démon appelant des API web.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>Vue d'ensemble

Votre application peut acquérir un jeton pour appeler une API web pour son propre compte (pas pour le compte d’un utilisateur). Ce scénario est utile pour les applications démon. Il utilise l’octroi [d’informations d’identification client](v2-oauth2-client-creds-grant-flow.md) OAuth 2.0 standard.

![Applications démon](./media/scenario-daemon-app/daemon-app.svg)

Voici quelques exemples de cas d’utilisation d’applications démon :

- Applications web utilisées pour approvisionner ou administrer des utilisateurs, ou effectuer des traitements par lots dans un annuaire.
- Applications de bureau (par exemple, services Windows sur Windows ou processus de démons sous Linux) qui effectuent des traitements par lots, ou un service de système d’exploitation s’exécutant en arrière-plan.
- API web que ont besoin de manipuler des annuaires, pas des utilisateurs spécifiques.

Il existe un autre cas courant où des applications non-démon utilisent les informations d’identification client. Même quand elles agissent pour le compte d’utilisateurs, pour des raisons techniques, elles ont besoin d’accéder à une API web ou à une ressource avec leur identité. Un exemple est l’accès aux secrets dans Key Vault ou dans une base de données SQL Azure pour un cache.

Applications qui acquièrent un jeton pour leurs propres identités :

- Applications clientes confidentielles. Ces applications, étant donné qu’elles accèdent à des ressources indépendamment d’un utilisateur, doivent prouver leur identité. Il s’agit également d’applications assez sensibles, qui doivent être approuvées par les administrateurs de locataire Azure Active Directory (Azure AD).
- Applications qui ont inscrit un secret (mot de passe ou certificat d’application) auprès d’Azure AD. Ce secret est transmis lors de l’appel à Azure AD pour l’obtention d’un jeton.

## <a name="specifics"></a>Spécificités

> [!IMPORTANT]
>
> - Aucune interaction d’utilisateur n’est possible avec une application démon. Une application démon nécessite sa propre identité. Ce type d’application demande un jeton d’accès en utilisant son identité d’application, et en présentant à Azure AD son ID d’application, ses informations d’identification (mot de passe ou certificat) et un URI d’ID d’application. Une fois l’authentification réussie, le démon reçoit un jeton d’accès (et un jeton d’actualisation) du point de terminaison de la Plateforme d’identité Microsoft, qui est ensuite utilisé pour appeler l’API web (et actualisé au besoin).
> - Étant donné que l’interaction d’un utilisateur n’est pas possible, aucun consentement incrémentiel n’est possible. Toutes les autorisations d’API requises doivent être configurées lors de l’inscription de l’application, et le code de celle-ci ne demande que des autorisations définies de manière statique. Cela signifie également que les applications démon ne prennent en charge le consentement incrémentiel.

Pour les développeurs, l’expérience de bout en bout pour ce scénario présente les aspects suivants :

- Les applications démon ne peuvent fonctionner que dans des locataires Azure AD. Il n’aurait aucun sens à créer une application démon qui tente de manipuler des comptes personnels Microsoft. Si vous êtes un développeur d’applications métier, vous allez créer votre application démon dans votre locataire. Si vous êtes un éditeur de logiciels indépendant, vous pouvez créer une application démon mutualisée. Elle devra recevoir le consentement de chaque administrateur de locataire.
- Lors de l’[inscription de l’application](./scenario-daemon-app-registration.md), l’**URI de réponse** n’est pas nécessaire. Vous devez partager secrets ou certificats ou encore assertions signées avec Azure AD, demander également des autorisations d’applications et obtenir le consentement d’un administrateur pour utiliser ces autorisations d’application.
- La [configuration de l’application](./scenario-daemon-app-configuration.md) doit fournir les informations d’identification client qui ont été partagées avec Azure AD lors de l’inscription de l’application.
- L’[étendue](scenario-daemon-acquire-token.md#scopes-to-request) utilisée pour acquérir un jeton avec le flux d’informations d’identification client doit être une étendue statique.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Application démon – Inscription de l’application](./scenario-daemon-app-registration.md)
