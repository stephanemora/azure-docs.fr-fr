---
title: Présentation d’Azure Static Web Apps
description: Les fonctionnalités clés d’Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 04/01/2021
ms.author: cshoe
ms.openlocfilehash: c0a4156fb0015c6d319700c82f6285dbf0ed1443
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107947876"
---
# <a name="what-is-azure-static-web-apps-preview"></a>Présentation d’Azure Static Web Apps - Préversion

Azure Static Web Apps est un service qui génère et déploie automatiquement des applications web de pile complète sur Azure à partir d’un dépôt de code.

:::image type="content" source="media/overview/azure-static-web-apps-overview.png" alt-text="Diagramme de présentation d’Azure Static Web Apps":::

Le flux de travail Azure Static Web Apps est adapté au flux de travail quotidien des développeurs. Les applications sont générées et déployées sur la base des modifications du code.

Lorsque vous créez une ressource Azure Static Web Apps, Azure interagit directement avec GitHub ou Azure DevOps pour superviser une branche de votre choix. Chaque fois que vous envoyez des validations ou que vous acceptez des demandes de tirage (pull request) dans la branche surveillée, une build est automatiquement exécutée et votre application ainsi que l’API sont déployées sur Azure.

Les applications web statiques sont généralement créées à l’aide de bibliothèques et de frameworks comme Angular, React, Svelte, Vue ou Blazor, où le rendu côté serveur n’est pas obligatoire. Ces applications comprennent du code HTML, CSS, JavaScript et des images, qui composent l’application. Avec un serveur web traditionnel, ces ressources sont fournies à partir d’un seul serveur et de tous les points de terminaison d’API requis.

Avec les applications Static Web Apps, les ressources statiques sont séparées d’un serveur Web traditionnel et sont servies à partir de points répartis géographiquement dans le monde entier. Cette distribution rend le transfert des fichiers plus rapide, car les fichiers sont physiquement proches des utilisateurs finaux. En outre, les points de terminaison d’API sont hébergés à l’aide d’une [architecture serverless](../azure-functions/functions-overview.md), ce qui évite d’avoir à utiliser un serveur principal complet.

## <a name="key-features"></a>Fonctionnalités clés

- **Hébergement web** pour le contenu statique comme le HTML, le CSS, le JavaScript et les images.
- **Prise en charge d’API** intégrée fournie par Azure Functions.
- **Intégration Azure DevOps et GitHub de première classe** dans laquelle les modifications du dépôt déclenchent des builds et des déploiements.
- **contenu statique distribué à l’échelle mondiale**, ce qui rapproche le contenu de vos utilisateurs.
- **Certificats SSL gratuits**, automatiquement renouvelés.
- **Domaines personnalisés** pour fournir des personnalisations à votre application.
- **Modèle de sécurité transparent** avec proxy inverse lors de l’appel des API, ce qui ne requiert aucune configuration CORS.
- **Intégrations de fournisseurs d’authentification** avec Azure Active Directory, Facebook, Google, GitHub et Twitter.
- **Définition de rôle d’autorisation personnalisable** et assignations.
- **Règles d’acheminement principales** offrant un contrôle total sur le contenu et sur vos les itinéraires.
- **Les versions intermédiaires générées** basées sur les demandes de tirage (pull request) pour activer les versions préliminaires de votre site avant publication.

## <a name="what-you-can-do-with-static-web-apps"></a>Ce que vous pouvez faire avec les applications Static Web Apps

- **Créez des applications web modernes** avec des infrastructures et bibliothèques JavaScript comme [Angular](getting-started.md?tabs=angular), [React](getting-started.md?tabs=react), [Svelte](/learn/modules/publish-app-service-static-web-app-api/) et [Vue](getting-started.md?tabs=vue), ou utilisez [Blazor](./deploy-blazor.md) pour créer des applications WebAssembly, avec un back-end [Azure Functions](apis.md).
- **Publier des sites statiques** avec des frameworks comme [Gatsby](publish-gatsby.md), [Hugo](publish-hugo.md), [VuePress](publish-vuepress.md).
- **Déployer des applications web** avec des frameworks comme [Next.js](deploy-nextjs.md) et [Nuxt.js](deploy-nuxtjs.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer votre première application web statique](getting-started.md)
