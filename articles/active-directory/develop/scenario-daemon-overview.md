---
title: Générer une application démon appelant des API web | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment générer une application démon appelant des API web
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/14/2021
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 1a9aa754d09333b1384fe46e424deaf3c5525ff3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131018050"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Scénario : Application démon appelant des API web

Découvrez tout ce dont vous avez besoin pour créer une application démon appelant des API web.

## <a name="overview"></a>Vue d’ensemble

Votre application peut acquérir un jeton pour appeler une API web pour son propre compte (pas pour le compte d’un utilisateur). Ce scénario est utile pour les applications démon. Il utilise l’octroi d’[informations d’identification du client](v2-oauth2-client-creds-grant-flow.md) OAuth 2.0 standard.

![Applications démon](./media/scenario-daemon-app/daemon-app.svg)

Voici quelques exemples de cas d’utilisation d’applications démon :

- Applications web utilisées pour provisionner ou administrer des utilisateurs ou pour effectuer des traitements par lots dans un annuaire
- Applications de bureau (comme des services Windows sur Windows ou des processus démon sur Linux) qui effectuent des traitements par lots, ou un service de système d’exploitation qui s’exécute en arrière-plan
- API web que ont besoin de manipuler des annuaires, pas des utilisateurs spécifiques.

Il existe un autre cas courant où des applications non-démon utilisent les informations d’identification du client. Même quand elles agissent pour le compte d’utilisateurs, pour des raisons techniques, elles ont besoin d’accéder à une API web ou à une ressource sous leur propre identité. L’accès aux secrets dans Azure Key Vault ou Azure SQL Database pour un cache en est un exemple.

Applications qui acquièrent un jeton pour leurs propres identités :

- Applications clientes confidentielles. Ces applications, étant donné qu’elles accèdent à des ressources indépendamment des utilisateurs, doivent prouver leur identité. Il s’agit également d’applications assez sensibles. Elles doivent être approuvées par les administrateurs de locataire Azure Active Directory (Azure AD).
- Applications qui ont inscrit un secret (mot de passe ou certificat d’application) auprès d’Azure AD. Ce secret est transmis lors de l’appel à Azure AD pour obtenir un jeton.

## <a name="specifics"></a>Spécificités

Les utilisateurs ne peuvent pas interagir avec une application démon. Une application démon nécessite sa propre identité. Ce type d’application demande un jeton d’accès en utilisant son identité d’application et en présentant à Azure AD son ID d’application, ses informations d’identification (mot de passe ou certificat) et un URI d’ID d’application. Une fois l’authentification réussie, le démon reçoit un jeton d’accès (et un jeton d’actualisation) de la plateforme d’identités Microsoft. Ce jeton est ensuite utilisé pour appeler l’API web (et est actualisé si nécessaire).

Étant donné que les utilisateurs ne peuvent pas interagir avec les applications démon, le consentement incrémentiel n’est pas possible. Toutes les autorisations d’API nécessaires doivent être configurées lors de l’inscription de l’application. Le code de l’application ne demande que des autorisations définies de manière statique. Cela signifie également que les applications démon ne prennent en charge le consentement incrémentiel.

Pour les développeurs, l’expérience de bout en bout pour ce scénario présente les aspects suivants :

- Les applications démon peuvent fonctionner uniquement dans des locataires Azure AD. Il n’aurait aucun sens à créer une application démon qui tente de manipuler des comptes personnels Microsoft. Si vous êtes un développeur d’applications métier, vous allez créer votre application démon dans votre locataire. Si vous êtes éditeur de logiciels indépendant, vous pouvez créer une application démon multilocataire. Chaque administrateur de locataire doit fournir son consentement.
- Lors de l’[inscription de l’application](./scenario-daemon-app-registration.md), l’URI de réponse n’est pas nécessaire. Partagez des secrets, des certificats ou des assertions signées avec Azure AD. Vous devez également demander des autorisations d’application et obtenir le consentement d’un administrateur pour utiliser ces autorisations d’application.
- La [configuration de l’application](./scenario-daemon-app-configuration.md) doit fournir les informations d’identification du client qui ont été partagées avec Azure AD lors de l’inscription de l’application.
- L’[étendue](scenario-daemon-acquire-token.md#scopes-to-request) utilisée pour acquérir un jeton avec le flux d’informations d’identification client doit être une étendue statique.

## <a name="recommended-reading"></a>Lectures recommandées

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant de ce scénario, [Inscription d’application](./scenario-daemon-app-registration.md).
