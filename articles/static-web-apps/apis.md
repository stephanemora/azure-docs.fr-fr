---
title: Prise en charge des API dans Azure Static Web Apps avec Azure Functions
description: Découvrez les fonctionnalités d’API prises en charge parAzure Static Web Apps
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 4fa3acf0e6cc767aeee4504bbc4df382a75e256b
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758756"
---
# <a name="api-support-in-azure-static-web-apps-preview-with-azure-functions"></a>Prise en charge des API dans Azure Static Web Apps (préversion) avec Azure Functions

Azure Static Web Apps fournit des points de terminaison d’API serverless via [Azure Functions](../azure-functions/functions-overview.md). En tirant parti d’Azure Functions, les API sont mises à l’échelle dynamiquement en fonction de la demande et elles incluent les fonctionnalités suivantes :

- **Sécurité intégrée** avec accès direct aux données [d’authentification et d’autorisation basées sur les rôles](user-information.md).
- **Routage transparent** qui met l’itinéraire _api_ à la disposition de l’application web en toute sécurité sans nécessiter de règles CORS personnalisées.
- **Azure Functions** v3 compatible avec Node.js 12.
- **Déclencheurs HTTP** et liaisons de sortie.

## <a name="configuration"></a>Configuration

Les points de terminaison d’API sont disponibles pour l’application web via l’itinéraire _api_. Bien que cet itinéraire soit résolu, vous pouvez contrôler le dossier dans lequel vous recherchez l’application Azure Functions associée. Vous pouvez modifier cet emplacement en [modifiant le fichier YAML de flux de travail](github-actions-workflow.md#build-and-deploy) situé dans le dossier _. GitHub/workflows_ de votre référentiel.

## <a name="constraints"></a>Contraintes

Azure Static Web Apps fournit une API via Azure Functions. Les fonctionnalités d’Azure Functions sont axées sur un ensemble spécifique de fonctionnalités qui vous permettent de créer une API pour une application web et de permettre à celle-ci de se connecter à l’API en toute sécurité. Ces fonctionnalités sont fournies avec certaines contraintes, notamment :

- Le préfixe d’itinéraire de l’API doit être _api_.
- L’application de fonctions API doit être en JavaScript.
- Les déclencheurs et les liaisons sont limités à [HTTP](../azure-functions/functions-bindings-http-webhook.md).
  - Tous les autres [déclencheurs et liaisons Azure Functions](../azure-functions/functions-triggers-bindings.md#supported-bindings) sont limitées à l’exception des liaisons de sortie.
- Les journaux ne sont disponibles que si vous ajoutez [Application Insights](../azure-functions/functions-monitoring.md) à votre application Functions.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter une API](add-api.md)
