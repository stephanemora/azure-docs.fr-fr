---
title: Tutoriel - Créer une application web sécurisée sur Azure App Service | Azure
description: Dans ce tutoriel, vous allez découvrir comment créer une application web avec Azure App Service, activer l’authentification, appeler le stockage Azure et appeler Microsoft Graph.
services: active-directory, app-service-web, storage, microsoft-graph
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: fcddf99c3a4c53fe25db1ed653983e8ddac0edb7
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428324"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>Tutoriel - Activer l’authentification dans App Service et accéder au stockage et à Microsoft Graph

Dans ce tutoriel, qui décrit un scénario d’application courant, vous allez découvrir comment :

- [(A) Configurer l’authentification pour une application web](scenario-secure-app-authentication-app-service.md) et limiter l’accès aux utilisateurs de votre organisation.
- [(B) Accéder de manière sécurisée au stockage Azure](scenario-secure-app-access-storage.md) pour le compte de l’application web à l’aide d’identités managées.
- (C) Accéder à des données dans Microsoft Graph [pour le compte de l’utilisateur connecté](scenario-secure-app-access-microsoft-graph-as-user.md) ou [pour le compte de l’application web](scenario-secure-app-access-microsoft-graph-as-app.md) à l’aide d’identités managées.
- [Nettoyer les ressources](scenario-secure-app-clean-up-resources.md) que vous avez créées pour ce tutoriel.

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="Scénarios d’application dans la plateforme d’identités Microsoft" border="false":::

Pour commencer, découvrez comment activer l’authentification pour une application web.

> [!div class="nextstepaction"]
> [Configurer l’authentification pour une application web](scenario-secure-app-authentication-app-service.md)
