---
title: Intégrer Google Tag Manager au portail des développeurs
titleSuffix: Azure API Management
description: Découvrez comment connecter Google Tag Manager à votre portail des développeurs managé ou auto-hébergé dans la Gestion des API Azure.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: c209eb782787146d947b4684d41c5d1e9bb6364e
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741217"
---
# <a name="integrate-google-tag-manager-to-api-management-developer-portal"></a>Intégrer Google Tag Manager au portail des développeurs Gestion des API

[Google Tag Manager](https://developers.google.com/tag-manager) est un système de gestion des étiquettes créé par Google. Vous pouvez l’utiliser pour gérer les étiquettes JavaScript et HTML qui sont utilisées pour le suivi et l’analytique sur les sites web. Par exemple, vous pouvez utiliser Google Tag Manager pour intégrer Google Analytics, des cartes thermiques ou des chatbots comme LiveChat.

Suivez les étapes de cet article pour connecter Google Tag Manager à votre portail des développeurs managé ou auto-hébergé dans la Gestion des API Azure.

## <a name="add-google-tag-manager-to-your-portal"></a>Ajouter Google Tag Manager à votre portail

Effectuez les étapes suivantes pour connecter Google Tag Manager à votre portail des développeurs managé ou auto-hébergé.

> [!IMPORTANT]
> Les étapes 1 et 2 ne sont pas nécessaires pour les portails managés. Si vous disposez d’un portail managé, passez à l’étape 4.

1. Configurez un [environnement local](developer-portal-self-host.md#step-1-set-up-local-environment) pour la dernière version du portail des développeurs.

1. Installez le package **npm** pour ajouter [Paperbits for Google Tag Manager](https://github.com/paperbits/paperbits-gtm) :

    ```sh
    npm install @paperbits/gtm --save
    ```

1. Dans le fichier `startup.publish.ts` du dossier `src`, importez et inscrivez le module GTM :

    ```typescript
    import { GoogleTagManagerPublishModule } from "@paperbits/gtm/gtm.publish.module";
    ...
    injector.bindModule(new GoogleTagManagerPublishModule());
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

1. Étendez la configuration du site de l’étape précédente avec la configuration Google Tag Manager :

    ```http
    PUT /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": { ... },
                "integration": {
                    "googleTagManager": {
                        "containerId": "GTM-..."
                    }
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Étapes suivantes

- [Automatiser un déploiement de portail](automate-portal-deployments.md)
- [Auto-héberger le portail des développeurs](developer-portal-self-host.md)