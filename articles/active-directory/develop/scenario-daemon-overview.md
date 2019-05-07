---
title: Démon application appeler des API web (vue d’ensemble) - plateforme d’identité Microsoft
description: Découvrez comment créer une application démon qui appelle des API web
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 578b7cdb38b7df3fab5885d773354a36f76a4cfb
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075879"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Scénario : Application démon appelle des API web

En savoir plus il que vous suffit de créer une application démon qui appelle l’API web.

## <a name="prerequisites"></a>Conditions préalables

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>Présentation

Votre application peut acquérir un jeton pour appeler une API web pour le compte lui-même (pas pour le compte d’un utilisateur). Ce scénario est utile pour les applications de démon. Il utilise le standard OAuth 2.0 [informations d’identification client](v2-oauth2-client-creds-grant-flow.md) accorder.

![Applications démon](./media/scenario-daemon-app/daemon-app.svg)

Voici quelques exemples de cas d’utilisation pour les applications de démon :

- Applications Web qui sont utilisés pour configurer ou administrer les utilisateurs ou traiter par lot des processus dans un répertoire
- Applications de bureau tels que (services windows sur Windows), ou des processus de démons sur Linux qui effectuent le traitement par lots ou un service de système d’exploitation en cours d’exécution en arrière-plan
- API Web que vous avez besoin de manipuler des répertoires, les utilisateurs non spécifiques

Il existe un autre cas courant où les applications non-daemon utilisent les informations d’identification du client : même lorsqu’ils agissent pour le compte d’utilisateurs, dont ils ont besoin d’accéder à une API web ou une ressource avec leur identité pour des raisons techniques. Accéder aux secrets dans le coffre de clés ou une base de données SQL Azure pour un cache est un exemple.

Applications qui acquièrent un jeton pour leurs propres identités :

- Sont des applications de client confidentiel. Ces applications, étant donné qu’ils accèdent aux ressources indépendamment d’un utilisateur, doivent prouver leur identité. Elles sont également les applications sensibles au lieu de cela, ils doivent être approuvées par les administrateurs de locataire Azure Active Directory (Azure AD).
- Avez inscrit un secret (application de mot de passe ou certificat) avec Azure AD. Ce secret est passé lors de l’appel à Azure AD pour obtenir un jeton.

## <a name="specifics"></a>Spécificités

> [!IMPORTANT]
>
> - Interaction de l’utilisateur n’est pas possible avec une application démon. Une application démon nécessite sa propre identité. Ce type d’application demande un jeton d’accès en utilisant son identité d’application et en fournissant son ID d’application, les informations d’identification (mot de passe ou certificat) et application URI ID d’Azure AD. Après une authentification réussie, le démon reçoit un jeton d’accès (et un jeton d’actualisation) à partir de Microsoft identity plateforme point de terminaison, qui est ensuite utilisé pour appeler l’API web (et est actualisé en fonction des besoins).
> - Étant donné que l’interaction de l’utilisateur n’est pas possible, consentement incrémentiel ne sera pas possible. Toutes les autorisations d’API requises doivent être configurés lors de l’inscription d’application et le code de l’application se contente de demander les autorisations définies de manière statique. Cela signifie également que les applications de démon ne prennent en charge le consentement incrémentiel.

Pour les développeurs, l’expérience de bout en bout pour ce scénario présente les aspects suivants :

- Applications de démon ne peuvent fonctionner dans les locataires Azure AD. Il n’aurait aucun sens pour créer une application démon qui tente de manipuler des comptes personnels Microsoft. Si vous êtes un développeur d’applications line of business (LOB), vous allez créer votre application démon dans votre client. Si vous êtes un ISV, vous souhaiterez créer une application démon d’architecture mutualisée. Elle devra être consentie par chaque administrateur de locataire.
- Lors de la [inscription d’Application](./scenario-daemon-app-registration.md), le **URI de réponse** n’est pas nécessaire. Vous devez partager les secrets ou certificats auprès d’Azure AD, et vous devez demander des autorisations d’applications et de donner leur consentement d’administrateur à utiliser ces autorisations d’application.
- Le [configuration de l’Application](./scenario-daemon-app-configuration.md) doit fournir des informations d’identification de client comme partagée avec Azure AD lors de l’inscription de l’application.
- Le [étendue](scenario-daemon-acquire-token.md#scopes-to-request) utilisé pour acquérir un jeton avec les informations d’identification client flux doit être une étendue statique.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Application démon - inscription de l’application](./scenario-daemon-app-registration.md)
