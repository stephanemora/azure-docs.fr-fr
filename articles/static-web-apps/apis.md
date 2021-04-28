---
title: Prise en charge des API dans Azure Static Web Apps avec Azure Functions
description: Découvrez les fonctionnalités d’API prises en charge parAzure Static Web Apps
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 1fc5e1e6982686e7042e5b8ad55d72a4560b6aee
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107737475"
---
# <a name="api-support-in-azure-static-web-apps-preview-with-azure-functions"></a>Prise en charge des API dans Azure Static Web Apps (préversion) avec Azure Functions

Azure Static Web Apps fournit des points de terminaison d’API serverless via [Azure Functions](../azure-functions/functions-overview.md). En tirant parti d’Azure Functions, les API sont mises à l’échelle dynamiquement en fonction de la demande et elles incluent les fonctionnalités suivantes :

- **Sécurité intégrée** avec accès direct aux données [d’authentification et d’autorisation basées sur les rôles](user-information.md).
- **Routage transparent** qui met l’itinéraire _api_ à la disposition de l’application web en toute sécurité sans nécessiter de règles CORS personnalisées.
- **Déclencheurs HTTP** et liaisons d’entrée/sortie.

## <a name="configuration"></a>Configuration

Les points de terminaison d’API sont disponibles pour l’application web via l’itinéraire _api_. Bien que cet itinéraire soit résolu, vous pouvez contrôler le dossier et le projet dans lequel vous recherchez l’application Azure Functions associée. Vous pouvez modifier cet emplacement en [modifiant le fichier YAML de flux de travail](github-actions-workflow.md#build-and-deploy) situé dans le dossier _. GitHub/workflows_ de votre référentiel.

## <a name="constraints"></a>Contraintes

Azure Static Web Apps fournit une API via Azure Functions. Les fonctionnalités d’Azure Functions sont axées sur un ensemble spécifique de fonctionnalités qui vous permettent de créer une API pour une application web et de permettre à celle-ci de se connecter à l’API en toute sécurité. Ces fonctionnalités sont fournies avec certaines contraintes, notamment :

- Le préfixe d’itinéraire de l’API doit être _api_.
- L’API doit être une application Azure Functions Node.js 12, .NET Core 3.1 ou Python 3.8.
- Les règles de routage pour les fonctions d’API prennent uniquement en charge les [redirections](routes.md#redirects) et la [sécurisation](routes.md#securing-routes-with-roles) des itinéraires avec des rôles.
- Les déclencheurs sont limités à [HTTP](../azure-functions/functions-bindings-http-webhook.md).
  - Toutes les [liaisons](../azure-functions/functions-triggers-bindings.md#supported-bindings) d’entrée et de sortie sont prises en charge.
- Les journaux ne sont disponibles que si vous ajoutez [Application Insights](../azure-functions/functions-monitoring.md) à votre application Functions.
- Certains paramètres d’application sont gérés par le service. Vous ne pouvez pas configurer les paramètres d’application qui commencent par les préfixes suivants :`APPSETTING_`, `AZUREBLOBSTORAGE_`, `AZUREFILESSTORAGE_`, `AZURE_FUNCTION_`, `CONTAINER_`, `DIAGNOSTICS_`, `DOCKER_`, `FUNCTIONS_`, `IDENTITY_`, `MACHINEKEY_`, `MAINSITE_`, `MSDEPLOY_`, `SCMSITE_`, `SCM_`, `WEBSITES_`, `WEBSITE_`, `WEBSOCKET_`, `AzureWeb`.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter une API](add-api.md)
