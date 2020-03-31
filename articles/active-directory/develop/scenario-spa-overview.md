---
title: Scénario d’application monopage JavaScript - Plateforme d’identités Microsoft | Azure
description: Découvrez comment créer une application monopage (vue d’ensemble du scénario) à l’aide de la plateforme d’identités Microsoft.
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
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: b430778bed811656b5c8aadc75ba3cf35917f737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701873"
---
# <a name="scenario-single-page-application"></a>Scénario : Application monopage

Découvrez tout ce que vous devez savoir pour créer une application monopage.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Prise en main

Vous pouvez créer votre première application en suivant le démarrage rapide sur les applications monopages JavaScript :

> [!div class="nextstepaction"]
> [Démarrage rapide : application monopage](./quickstart-v2-javascript.md)

## <a name="overview"></a>Vue d’ensemble

De nombreuses applications web modernes sont créées en tant qu’applications monopages côté client. Les développeurs les écrivent à l’aide de JavaScript ou d’un framework SPA, comme Angular, Vue.js et React.js. Ces applications s’exécutent sur un navigateur web et présentent des caractéristiques d’authentification différentes de celles des applications web classiques côté serveur. 

La plateforme d’identités Microsoft permet aux applications monopages de connecter des utilisateurs et d’obtenir des jetons pour accéder aux services principaux ou aux API web à l’aide du [Flux implicite OAuth 2.0](./v2-oauth2-implicit-grant-flow.md). Le flux implicite permet à l’application d’obtenir des jetons d’ID pour représenter l’utilisateur authentifié, mais également des jetons d’accès qui sont nécessaires pour appeler des API protégées.

![Applications monopages](./media/scenarios/spa-app.svg)

Ce flux d’authentification n’inclut pas de scénarios d’application utilisant des frameworks JavaScript multiplateformes, tels qu’Electron et React-Native. Ils exigent davantage de fonctionnalités pour l’interaction avec les plateformes natives.

## <a name="specifics"></a>Spécificités

Afin d’activer ce scénario pour votre application, vous avez besoin des éléments suivants :

* Inscription de l’application auprès d’Azure Active Directory (Azure AD). Cette inscription implique l’activation du flux implicite et la définition d’un URI de redirection vers lequel les jetons sont retournés.
* Configuration de l’application avec les propriétés de l’application inscrite, comme l’ID d’application.
* Utilisation de la bibliothèque MSAL (Microsoft Authentication Library) pour effectuer le flux d’authentification afin de se connecter et d’acquérir des jetons.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Inscription d’application](scenario-spa-app-registration.md)
