---
title: Générer une application web appelant des API web – Plateforme d’identités Microsoft | Azure
description: Apprenez à générer une application web qui appelle des API web (vue d’ensemble)
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
ms.openlocfilehash: 3888c7f838d6009382f849bc7d3e34c49b3b70a4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962132"
---
# <a name="scenario-web-app-that-calls-web-apis"></a>Scénario : Application web qui appelle des API web

Découvrez comment générer une application web qui connecte des utilisateurs à la plateforme d’identités Microsoft et qui appelle des API web au nom de l’utilisateur connecté.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

Ce scénario repose sur l’hypothèse que vous avez parcouru le scénario suivant :

> [!div class="nextstepaction"]
> [Application web qui connecte les utilisateurs](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Vue d'ensemble

Vous ajoutez une authentification à votre application web, qui peut ainsi connecter des utilisateurs et qui appelle une API web au nom de l’utilisateur connecté.

![Application web qui appelle des API web](./media/scenario-webapp/web-app.svg)

Les applications web qui appellent des API web :

- sont des applications clientes confidentielles ;
- ont donc inscrit un secret (mot de passe ou certificat d’application) auprès d’Azure AD. Ce secret est transmis lors de l’appel à Azure AD pour permettre l’obtention d’un jeton.

## <a name="specifics"></a>Spécificités

> [!NOTE]
> L’ajout d’une fonctionnalité de connexion à une application web n’utilise pas les bibliothèques d’authentification Microsoft (MSAL), car cette opération implique de protéger l’application web. La protection des applications est assurée par des bibliothèques appelées intergiciels. Cette tâche est décrite dans le scénario précédent, [Application web qui connecte les utilisateurs](scenario-web-app-sign-user-overview.md).
>
> Lorsque vous appelez des API web à partir d’une application web, vous devez obtenir des jetons d’accès pour ces API. Vous pouvez acquérir ces jetons à l’aide des bibliothèques MSAL.

L’expérience de bout en bout des développeurs dans le cadre de ce scénario présente donc certaines spécificités :

- Lors de [l’inscription d’application](scenario-web-app-call-api-app-registration.md), vous devez fournir un ou plusieurs (si vous déployez votre application à différents emplacements) URI de réponse, secrets ou certificats qui doivent être partagés avec Azure AD.
- La [configuration de l’application](scenario-web-app-call-api-app-configuration.md) doit fournir les informations d’identification de client qui ont été partagées avec Azure AD lors de l’inscription d’application.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Inscription d’application](scenario-web-app-call-api-app-registration.md)
