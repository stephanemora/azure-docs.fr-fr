---
title: Vue d’ensemble du scénario JavaScript application à page unique - plateforme d’identité Microsoft
description: Découvrez comment créer une application à page unique (vue d’ensemble du scénario) qui intègre la plateforme d’identité Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07a21e83f304f3e1acc0ed4033d832dd8e901ac9
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076359"
---
# <a name="scenario-single-page-application"></a>Scénario : Application monopage

En savoir plus il que vous suffit de créer une application monopage (SPA).

## <a name="prerequisites"></a>Conditions préalables

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Prise en main

Vous pouvez créer votre première application en suivant le Guide de démarrage rapide JavaScript SPA :

> [!div class="nextstepaction"]
> [Démarrage rapide : Application à page unique](./quickstart-v2-javascript.md)

## <a name="overview"></a>Présentation

De nombreuses applications web modernes sont générées en tant que client-side applications à page unique écrites à l’aide de JavaScript ou un framework SPA comme Angular, Vue.js et React.js. Ces applications s’exécutent dans un navigateur web et présentent les caractéristiques d’authentification que les applications web côté serveur traditionnelles. La plateforme Microsoft identity permet aux applications de page unique de connecter des utilisateurs et obtenir des jetons pour accéder aux services principaux ou à l’aide des API web le [flux implicite OAuth 2.0](./v2-oauth2-implicit-grant-flow.md). Le flux implicite permet à l’application obtenir des jetons d’ID pour représenter l’utilisateur authentifié et également accéder aux jetons nécessaires pour appeler une API protégée.

![Applications monopages](./media/scenarios/spa-app.svg)

Ce flux d’authentification n’inclut pas de scénarios d’application à l’aide des infrastructures de JavaScript inter-plateformes telles que d’électrons, React-Native et ainsi de suite. depuis, ils nécessitent davantage fonctionnalités pour l’interaction avec les plateformes natives.

## <a name="specifics"></a>Spécificités

Les aspects suivants sont nécessaires pour activer ce scénario pour votre application :

* Inscription d’application avec Azure AD implique l’activation du flux implicit et en définissant un URI de redirection à laquelle les jetons sont retournés.
* Configuration de l’application avec les propriétés de l’application inscrite comme ID d’Application.
* À l’aide la bibliothèque MSAL pour effectuer le flux d’authentification pour se connecter et acquérir des jetons.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Inscription d’application](scenario-spa-app-registration.md)
