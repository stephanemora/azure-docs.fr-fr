---
title: Vue d’ensemble du scénario d’application monopage JavaScript - Plateforme d’identité Microsoft
description: Découvrez comment créer une application monopage (vue d’ensemble du scénario) qui intègre la plateforme d’identité Microsoft.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65076359"
---
# <a name="scenario-single-page-application"></a>Scénario : Application monopage

Découvrez tout ce que vous devez savoir pour créer une application monopage.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Prise en main

Vous pouvez créer votre première application en suivant le démarrage rapide sur les applications monopages JavaScript :

> [!div class="nextstepaction"]
> [Démarrage rapide : application monopage](./quickstart-v2-javascript.md)

## <a name="overview"></a>Vue d'ensemble

De nombreuses applications web modernes sont créées en tant qu’applications monopages côté client et écrites avec JavaScript ou une infrastructure de type SPA comme Angular, Vue.js et React.js. Ces applications s’exécutent dans un navigateur web et présentent des caractéristiques d’authentification différentes de celles des applications web côté serveur traditionnelles. La plateforme d’identité Microsoft permet aux applications monopages de connecter des utilisateurs et d’obtenir des jetons pour accéder aux services principaux ou aux API web à l’aide du [flux implicite OAuth 2.0](./v2-oauth2-implicit-grant-flow.md). Le flux implicite permet à l’application d’obtenir des jetons d’ID pour représenter l’utilisateur authentifié, mais également des jetons d’accès qui sont nécessaires pour appeler des API protégées.

![Applications monopages](./media/scenarios/spa-app.svg)

Ce flux d’authentification n’inclut pas de scénarios d’application utilisant des infrastructures JavaScript multiplateformes, telles qu’Electron, React-Native, etc. puisqu’elles requièrent davantage de fonctionnalités pour l’interaction avec les plateformes natives.

## <a name="specifics"></a>Spécificités

Les aspects suivants doivent être pris en compte pour mettre en œuvre ce scénario pour votre application :

* L’inscription d’application avec Azure AD implique l’activation du flux implicite et la définition d’un URI de redirection vers lequel les jetons sont renvoyés.
* La configuration de l’application avec les propriétés de l’application inscrite, comme l’ID d’application.
* L’utilisation de la bibliothèque MSAL pour que le flux d’authentification se connecte et obtienne les jetons.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Inscription d’application](scenario-spa-app-registration.md)
