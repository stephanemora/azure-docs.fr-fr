---
title: Intégrer Application Insights au portail des développeurs
titleSuffix: Azure API Management
description: Découvrez comment intégrer Application Insights dans votre portail des développeurs managé ou auto-hébergé.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 5e1c9caa55d0b3b7820f766a30c878fdc01f5137
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741216"
---
# <a name="integrate-application-insights-to-developer-portal"></a>Intégrer Application Insights au portail des développeurs

L’une des fonctionnalités les plus populaires d’Azure Monitor est Application Insights. Il s’agit d’un service extensible de gestion des performances des applications (APM) qui est destiné aux développeurs et aux professionnels DevOps. Vous pouvez l’utiliser pour superviser votre portail des développeurs et détecter les anomalies de performances. Application Insights intègre de puissants outils d’analytique qui vous permettent de savoir ce que font les utilisateurs en visitant le portail des développeurs.

## <a name="add-application-insights-to-your-portal"></a>Ajouter Application Insights à votre portail

Effectuez les étapes suivantes pour connecter Application Insights à votre portail des développeurs managé ou auto-hébergé.

> [!IMPORTANT]
> Les étapes 1 et 2 ne sont pas nécessaires pour les portails managés. Si vous disposez d’un portail managé, passez à l’étape 4.

1. Configurez un [environnement local](developer-portal-self-host.md#step-1-set-up-local-environment) pour la dernière version du portail des développeurs.

1. Installez le package **npm** pour ajouter [Paperbits for Azure](https://github.com/paperbits/paperbits-azure) :

    ```console
    npm install @paperbits/azure --save
    ```

1. Dans le fichier `startup.publish.ts` du dossier `src`, importez puis inscrivez le module Application Insights :

    ```typescript
    import { AppInsightsPublishModule } from "@paperbits/azure";
    ...
    injector.bindModule(new AppInsightsPublishModule());
    ```

1. Récupérez la configuration du portail :

    ```http
    GET /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": {
                    "title": "Microsoft Azure API Management - developer portal",
                    "description": "Discover APIs, learn how to use them, try them out interactively, and sign up to acquire keys.",
                    "keywords": "Azure, API Management, API, developer",
                    "faviconSourceId": null,
                    "author": "Microsoft Azure API Management"
                }
            }
        ]
    }
    ```

1. Étendez la configuration du site de l’étape précédente avec la configuration Application Insights :

    ```http
    PUT /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": { ... },
                "integration": {
                    "appInsights": {
                        "instrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxx"
                    }
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le portail des développeurs :

- [Vue d’ensemble du portail des développeurs Gestion des API Azure](api-management-howto-developer-portal.md)
- [Automatiser un déploiement de portail](automate-portal-deployments.md)
- [Auto-héberger le portail des développeurs](developer-portal-self-host.md)
