---
title: 'Tutoriel : Créer une application web sécurisée sur Azure App Service | Azure'
description: Dans ce tutoriel, vous allez apprendre à créer une application web avec Azure App Service, activer l’authentification, appeler le stockage Azure et appeler Microsoft Graph.
services: active-directory, app-service-web, storage, microsoft-graph
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 10/26/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: c6b236c3ce906c5b573db40e1c002924a3e75660
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131065510"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>Tutoriel : Activer l’authentification dans App Service et accéder au stockage et à Microsoft Graph

Ce tutoriel décrit un scénario d’application courant dans lequel vous allez apprendre à :

- [Configurer l’authentification pour une application web](scenario-secure-app-authentication-app-service.md) et limiter l’accès aux utilisateurs de votre organisation. Consultez A dans le diagramme.
- [Accéder de manière sécurisée au stockage Azure](scenario-secure-app-access-storage.md) pour le compte de l’application web à l’aide d’identités managées. Consultez B dans le diagramme.
- Accéder à des données dans Microsoft Graph [pour le compte de l’utilisateur connecté](scenario-secure-app-access-microsoft-graph-as-user.md) ou [pour le compte de l’application web](scenario-secure-app-access-microsoft-graph-as-app.md) à l’aide d’identités managées. Consultez C dans le diagramme.
- [Nettoyer les ressources](scenario-secure-app-clean-up-resources.md) que vous avez créées pour ce tutoriel.

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="Diagramme illustrant les scénarios d’application dans la plateforme d’identités Microsoft." border="false":::

Pour commencer, découvrez comment activer l’authentification pour une application web.

> [!div class="nextstepaction"]
> [Configurer l’authentification pour une application web](scenario-secure-app-authentication-app-service.md)
