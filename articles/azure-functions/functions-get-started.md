---
title: Bien démarrer avec Azure Functions
description: Suivez les premières étapes pour utiliser Azure Functions.
author: craigshoemaker
ms.topic: overview
ms.date: 11/19/2020
ms.author: cshoe
zone_pivot_groups: programming-languages-set-functions-lang-workers
ms.openlocfilehash: 559703401fa87980a5937e81eeab27ef44769c20
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2021
ms.locfileid: "113651478"
---
# <a name="getting-started-with-azure-functions"></a>Bien démarrer avec Azure Functions

## <a name="introduction"></a>Introduction

[Azure Functions](./functions-overview.md) vous permet d’implémenter la logique de votre système dans des blocs de code immédiatement disponibles. Ces blocs de code sont appelés « fonctions ».

Utilisez les ressources suivantes pour démarrer.

::: zone pivot="programming-language-csharp"

| Action | Ressources |
| --- | --- |
| **Créer votre première fonction** | À l’aide de l’un des outils suivants :<br><br><li>[Visual Studio](./functions-create-your-first-function-visual-studio.md)<li>[Visual Studio Code](./create-first-function-vs-code-csharp.md)<li>[Ligne de commande](./create-first-function-cli-csharp.md) |
| **Voir une fonction en cours d’exécution** | <li>[Navigateur d’exemples Azure](/samples/browse/?expanded=azure&languages=csharp&products=azure-functions)<li>[Bibliothèque de la communauté Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=C%23) |
| **Explorer un tutoriel interactif**| <li>[Choix de la meilleure technologie serverless Azure pour un scénario métier](/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework – Efficacité des performances](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Exécuter une fonction Azure avec des déclencheurs](/learn/modules/execute-azure-function-with-triggers/) <br><br>Consultez Microsoft Learn pour obtenir la [liste complète des tutoriels interactifs](/learn/browse/?expanded=azure&products=azure-functions).|
| **Passer en revue les meilleures pratiques** |<li>[Performances et fiabilité](./functions-best-practices.md)<li>[Gérer les connexions](./manage-connections.md)<li>[Gestion des erreurs et nouvelles tentatives des fonctions](./functions-bindings-error-pages.md?tabs=csharp)<li>[Sécurité](./security-concepts.md)|
| **En savoir plus** | <li>Découvrez comment les fonctions [augmentent ou diminuent automatiquement](./functions-scale.md) le nombre d’instances pour répondre à la demande<li>Explorez les différentes [méthodes de déploiement](./functions-deployment-technologies.md) disponibles<li>Utilisez des [outils de surveillance](./functions-monitoring.md) intégrés pour mieux analyser vos fonctions<li>Lisez la documentation de [référence du langage C#](./functions-dotnet-class-library.md)|

::: zone-end

::: zone pivot="programming-language-java"
| Action | Ressources |
| --- | --- |
| **Créer votre première fonction** | À l’aide de l’un des outils suivants :<br><br><li>[Visual Studio Code](./create-first-function-vs-code-java.md)<li>[Fonction Java/Maven avec le terminal/l’invite de commandes](./create-first-function-cli-java.md)<li>[Gradle](./functions-create-first-java-gradle.md)<li>[Eclipse](./functions-create-maven-eclipse.md)<li>[IntelliJ IDEA](./functions-create-maven-intellij.md) |
| **Voir une fonction en cours d’exécution** | <li>[Navigateur d’exemples Azure](/samples/browse/?expanded=azure&languages=java&products=azure-functions)<li>[Bibliothèque de la communauté Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Java) |
| **Explorer un tutoriel interactif**| <li>[Choix de la meilleure technologie serverless Azure pour un scénario métier](/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework – Efficacité des performances](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Développer une application avec le plug-in Maven pour Azure Functions](/learn/modules/develop-azure-functions-app-with-maven-plugin/) <br><br>Consultez Microsoft Learn pour obtenir la [liste complète des tutoriels interactifs](/learn/browse/?expanded=azure&products=azure-functions).|
| **Passer en revue les meilleures pratiques** |<li>[Performances et fiabilité](./functions-best-practices.md)<li>[Gérer les connexions](./manage-connections.md)<li>[Gestion des erreurs et nouvelles tentatives des fonctions](./functions-bindings-error-pages.md?tabs=java)<li>[Sécurité](./security-concepts.md)|
| **En savoir plus** | <li>Découvrez comment les fonctions [augmentent ou diminuent automatiquement](./functions-scale.md) le nombre d’instances pour répondre à la demande<li>Explorez les différentes [méthodes de déploiement](./functions-deployment-technologies.md) disponibles<li>Utilisez des [outils de surveillance](./functions-monitoring.md) intégrés pour mieux analyser vos fonctions<li>Lisez la documentation de [référence du langage Java](./functions-reference-java.md)|
::: zone-end

::: zone pivot="programming-language-javascript"
| Action | Ressources |
| --- | --- |
| **Créer votre première fonction** | À l’aide de l’un des outils suivants :<br><br><li>[Visual Studio Code](./create-first-function-vs-code-node.md)<li>[Terminal/invite de commandes Node.js](./create-first-function-cli-node.md) |
| **Voir une fonction en cours d’exécution** | <li>[Navigateur d’exemples Azure](/samples/browse/?expanded=azure&languages=javascript%2ctypescript&products=azure-functions)<li>[Bibliothèque de la communauté Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=JavaScript%2CTypeScript) |
| **Explorer un tutoriel interactif** | <li>[Choix de la meilleure technologie serverless Azure pour un scénario métier](/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework – Efficacité des performances](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Créer des API serverless avec Azure Functions](/learn/modules/build-api-azure-functions/)<li>[Créer une logique serverless avec Azure Functions](/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Refactoriser des API Node.js et Express en API serverless avec Azure Functions](/learn/modules/shift-nodejs-express-apis-serverless/) <br><br>Consultez Microsoft Learn pour obtenir la [liste complète des tutoriels interactifs](/learn/browse/?expanded=azure&products=azure-functions).|
| **Passer en revue les meilleures pratiques** |<li>[Performances et fiabilité](./functions-best-practices.md)<li>[Gérer les connexions](./manage-connections.md)<li>[Gestion des erreurs et nouvelles tentatives des fonctions](./functions-bindings-error-pages.md?tabs=javascript)<li>[Sécurité](./security-concepts.md)|
| **En savoir plus** | <li>Découvrez comment les fonctions [augmentent ou diminuent automatiquement](./functions-scale.md) le nombre d’instances pour répondre à la demande<li>Explorez les différentes [méthodes de déploiement](./functions-deployment-technologies.md) disponibles<li>Utilisez des [outils de surveillance](./functions-monitoring.md) intégrés pour mieux analyser vos fonctions<li>Lisez la documentation de référence du langage [JavaScript](./functions-reference-node.md) ou [TypeScript](./functions-reference-node.md#typescript)|
::: zone-end

::: zone pivot="programming-language-powershell"
| Action | Ressources |
| --- | --- |
| **Créer votre première fonction** | <li>Utilisation de [Visual Studio Code](./create-first-function-vs-code-powershell.md) |
| **Voir une fonction en cours d’exécution** | <li>[Navigateur d’exemples Azure](/samples/browse/?expanded=azure&languages=powershell&products=azure-functions)<li>[Bibliothèque de la communauté Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=PowerShell) |
| **Explorer un tutoriel interactif** | <li>[Choix de la meilleure technologie serverless Azure pour un scénario métier](/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework – Efficacité des performances](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Créer des API serverless avec Azure Functions](/learn/modules/build-api-azure-functions/)<li>[Créer une logique serverless avec Azure Functions](/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Exécuter une fonction Azure avec des déclencheurs](/learn/modules/execute-azure-function-with-triggers/) <br><br>Consultez Microsoft Learn pour obtenir la [liste complète des tutoriels interactifs](/learn/browse/?expanded=azure&products=azure-functions).|
| **Passer en revue les meilleures pratiques** |<li>[Performances et fiabilité](./functions-best-practices.md)<li>[Gérer les connexions](./manage-connections.md)<li>[Gestion des erreurs et nouvelles tentatives des fonctions](./functions-bindings-error-pages.md?tabs=powershell)<li>[Sécurité](./security-concepts.md)|
| **En savoir plus** | <li>Découvrez comment les fonctions [augmentent ou diminuent automatiquement](./functions-scale.md) le nombre d’instances pour répondre à la demande<li>Explorez les différentes [méthodes de déploiement](./functions-deployment-technologies.md) disponibles<li>Utilisez des [outils de surveillance](./functions-monitoring.md) intégrés pour mieux analyser vos fonctions<li>Lisez la documentation de [référence du langage PowerShell](./functions-reference-powershell.md)|
::: zone-end

::: zone pivot="programming-language-python"
| Action | Ressources |
| --- | --- |
| **Créer votre première fonction** | À l’aide de l’un des outils suivants :<br><br><li>[Visual Studio Code](./create-first-function-vs-code-python.md)<li>[Terminal/invite de commandes](./create-first-function-cli-python.md) |
| **Voir une fonction en cours d’exécution** | <li>[Navigateur d’exemples Azure](/samples/browse/?expanded=azure&languages=python&products=azure-functions)<li>[Bibliothèque de la communauté Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Python) |
| **Explorer un tutoriel interactif** | <li>[Choix de la meilleure technologie serverless Azure pour un scénario métier](/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework – Efficacité des performances](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Créer des API serverless avec Azure Functions](/learn/modules/build-api-azure-functions/)<li>[Créer une logique serverless avec Azure Functions](/learn/modules/create-serverless-logic-with-azure-functions/) <br><br>Consultez Microsoft Learn pour obtenir la [liste complète des tutoriels interactifs](/learn/browse/?expanded=azure&products=azure-functions).|
| **Passer en revue les meilleures pratiques** |<li>[Performances et fiabilité](./functions-best-practices.md)<li>[Gérer les connexions](./manage-connections.md)<li>[Gestion des erreurs et nouvelles tentatives des fonctions](./functions-bindings-error-pages.md?tabs=python)<li>[Sécurité](./security-concepts.md)<li>[Améliorer les performances de débit](./python-scale-performance-reference.md)|
| **En savoir plus** | <li>Découvrez comment les fonctions [augmentent ou diminuent automatiquement](./functions-scale.md) le nombre d’instances pour répondre à la demande<li>Explorez les différentes [méthodes de déploiement](./functions-deployment-technologies.md) disponibles<li>Utilisez des [outils de surveillance](./functions-monitoring.md) intégrés pour mieux analyser vos fonctions<li>Lisez la documentation de [référence du langage Python](./functions-reference-python.md)|
::: zone-end

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur l’anatomie d’une application Azure Functions](./functions-reference.md)
