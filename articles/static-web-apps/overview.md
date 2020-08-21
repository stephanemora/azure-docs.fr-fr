---
title: Présentation d’Azure Static Web Apps
description: Les fonctionnalités clés d’Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 884aa18b1a29b1c4f1bbf1159a5d48870529c72b
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88565704"
---
# <a name="what-is-azure-static-web-apps-preview"></a>Présentation d’Azure Static Web Apps - Préversion

Azure Static Web Apps est un service qui compile et déploie automatiquement des applications web de pile complètes dans Azure à partir d’un référentiel GitHub.

:::image type="content" source="media/overview/static-apps-overview.png" alt-text="Vue d’ensemble de Static Web Apps":::

Le flux de travail Azure Static Web Apps est adapté au flux de travail quotidien des développeurs. Les applications sont générées et déployées en fonction d’interactions GitHub.

Lorsque vous créez une ressource de Azure Static Web Apps, Azure configure un flux de travail GitHub Actions dans le référentiel de code source de l’application, qui surveille une branche de votre choix. Chaque fois que vous envoyez des validations ou que vous acceptez des demandes de tirage (pull request) dans la branche surveillée, GitHub Actions compile et déploie automatiquement votre application et son API sur Azure.

Les applications Static Web Apps sont généralement créées à l’aide de bibliothèques et de frameworks comme Angular, React, Svelte ou Vue. Ces applications comprennent du code HTML, CSS, JavaScript et des images, qui composent l’application. Avec un serveur web traditionnel, ces ressources sont fournies à partir d’un seul serveur et de tous les points de terminaison d’API requis.

Avec les applications Static Web Apps, les ressources statiques sont séparées d’un serveur Web traditionnel et sont servies à partir de points répartis géographiquement dans le monde entier. Cette distribution rend le transfert des fichiers plus rapide, car les fichiers sont physiquement proches des utilisateurs finaux. En outre, les points de terminaison d’API sont hébergés à l’aide d’une [architecture serverless](../azure-functions/functions-overview.md), ce qui évite d’avoir à utiliser un serveur principal complet.

## <a name="key-features"></a>Fonctionnalités clés

- **Hébergement web** pour le contenu statique comme le HTML, le CSS, le JavaScript et les images.
- **Prise en charge d’API** intégrée fournie par Azure Functions.
- **Intégration GitHub de première classe** dans laquelle les modifications du référentiel déclenchent des compilations et des déploiements.
- **contenu statique distribué à l’échelle mondiale**, ce qui rapproche le contenu de vos utilisateurs.
- **Certificats SSL gratuits**, automatiquement renouvelés.
- **Domaines personnalisés** pour fournir des personnalisations à votre application.
- **Modèle de sécurité transparent** avec proxy inverse lors de l’appel des API, ce qui ne requiert aucune configuration CORS.
- **Intégrations de fournisseurs d’authentification** avec Azure Active Directory, Facebook, Google, GitHub et Twitter.
- **Définition de rôle d’autorisation personnalisable** et assignations.
- **Règles d’acheminement principales** offrant un contrôle total sur le contenu et sur vos les itinéraires.
- **Les versions intermédiaires générées** basées sur les demandes de tirage (pull request) pour activer les versions préliminaires de votre site avant publication.

## <a name="what-you-can-do-with-static-web-apps"></a>Ce que vous pouvez faire avec les applications Static Web Apps

- **Créer des applications JavaScript modernes** avec des frameworks et des bibliothèques comme [Angular](getting-started.md?tabs=angular), [React](getting-started.md?tabs=react), [Svelte](https://docs.microsoft.com/learn/modules/publish-app-service-static-web-app-api/), [Vue](getting-started.md?tabs=react) avec un back-end [Azure Functions](apis.md).
- **Publier des sites statiques** avec des frameworks comme [Gatsby](publish-gatsby.md), [Hugo](publish-hugo.md), [VuePress](publish-vuepress.md).
- **Déployer des applications web** avec des frameworks comme [Next.js](deploy-nextjs.md) et [Nuxt.js](deploy-nuxtjs.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer votre première application web statique](getting-started.md)
