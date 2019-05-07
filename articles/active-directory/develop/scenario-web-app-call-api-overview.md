---
title: Application Web par appels web API (vue d’ensemble) - plateforme d’identité Microsoft
description: Découvrez comment créer une application web par appels web API (vue d’ensemble)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
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
ms.openlocfilehash: ce45f11a697b72ebdd0fe01166a70e7b47aa8e9f
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076299"
---
# <a name="scenario-web-app-that-calls-web-apis"></a>Scénario : Application Web appelle des API web

Découvrez comment créer une application web d’ouverture de session des utilisateurs sur la plateforme Microsoft identity et qui appelle des API web part de l’utilisateur connecté.

## <a name="prerequisites"></a>Conditions préalables

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

Ce scénario suppose que vous avez parcouru le scénario suivant :

> [!div class="nextstepaction"]
> [Application Web que les utilisateurs se connecte](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Présentation

Vous ajoutez l’authentification à votre application Web, qui peuvent se connecter par conséquent, les utilisateurs et appelle une API web part de l’utilisateur connecté.

![Application Web appelle des API web](./media/scenario-webapp/web-app.svg)

Les applications Web qui appelle des API web :

- Sont des applications de client confidentiel.
- C’est pourquoi ils avez inscrit un secret (application de mot de passe ou certificat) avec Azure AD. Ce secret est passé lors de l’appel à Azure AD pour obtenir un jeton

## <a name="specifics"></a>Spécificités

> [!NOTE]
> Ajout de connexion à une application Web n’utilise pas les bibliothèques MSAL car il s’agit sur la protection de l’application Web. Protection de bibliothèques est obtenue en bibliothèques nommés intergiciel (middleware). C’était l’objet du scénario précédent [connexion, les utilisateurs à une application Web](scenario-web-app-sign-user-overview.md)
>
> Lors de l’appel d’API web à partir d’une application Web, vous devez obtenir des jetons d’accès pour ces API web. Vous pouvez utiliser des bibliothèques MSAL pour acquérir ces jetons.

Par conséquent, l’expérience de bout en bout des développeurs pour ce scénario présente des aspects spécifiques en tant que :

- Lors de la [inscription d’Application](scenario-web-app-call-api-app-registration.md), vous devez fournir un, ou plusieurs (si vous déployez votre application à plusieurs emplacements) répondent URI, secrets, ou les certificats doivent être partagée avec Azure AD.
- Le [configuration de l’Application](scenario-web-app-call-api-app-configuration.md) doit fournir des informations d’identification de client comme partagée avec Azure AD lors de l’inscription de l’application

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Inscription d’application](scenario-web-app-call-api-app-registration.md)
