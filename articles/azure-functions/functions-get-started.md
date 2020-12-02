---
title: Bien démarrer avec Azure Functions
description: Suivez les premières étapes pour utiliser Azure Functions.
author: craigshoemaker
ms.topic: overview
ms.date: 11/19/2020
ms.author: cshoe
zone_pivot_groups: programming-languages-set-functions-lang-workers
ms.openlocfilehash: 6aba58dad9853714bf26442592f74fc77e39765c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94975007"
---
# <a name="getting-started-with-azure-functions"></a>Bien démarrer avec Azure Functions

## <a name="introduction"></a>Introduction

[Azure Functions](./functions-overview.md) vous permet d’implémenter la logique de votre système dans des blocs de code immédiatement disponibles. Ces blocs de code sont appelés « fonctions ».

Utilisez les ressources suivantes pour démarrer.

::: zone pivot="programming-language-csharp"

| Action | Ressources |
| --- | --- |
| **Créer votre première fonction** | À l’aide de l’un des outils suivants :<br><br><li>[Visual Studio Code](./functions-create-your-first-function-visual-studio.md)<li>[Visual Studio](./create-first-function-vs-code-csharp.md)<li>[Ligne de commande](./create-first-function-cli-csharp.md) |
| **Voir une fonction en cours d’exécution** | <li>[Navigateur d’exemples Azure](https://docs.microsoft.com/samples/browse/?languages=csharp&expanded=azure&products=azure-functions)<li>[Bibliothèque de la communauté Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=C%23) |
| **Explorer un tutoriel interactif**| <li>[Choix de la meilleure technologie serverless Azure pour un scénario métier](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework – Efficacité des performances](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Exécuter une fonction Azure avec des déclencheurs](https://docs.microsoft.com/learn/modules/execute-azure-function-with-triggers/) <br><br>Consultez Microsoft Learn pour obtenir la [liste complète des tutoriels interactifs](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions).|
| **En savoir plus** | <li>Découvrez comment les fonctions [augmentent ou diminuent automatiquement](./functions-scale.md) le nombre d’instances pour répondre à la demande<li>Explorez les différentes [méthodes de déploiement](./functions-deployment-technologies.md) disponibles<li>Utilisez des [outils de surveillance](./functions-monitoring.md) intégrés pour mieux analyser vos fonctions<li>Lisez la documentation de [référence du langage C#](./functions-dotnet-class-library.md)|

::: zone-end

::: zone pivot="programming-language-java"
| Action | Ressources |
| --- | --- |
| **Créer votre première fonction** | À l’aide de l’un des outils suivants :<br><br><li>[Visual Studio Code](./create-first-function-vs-code-java.md)<li>[Fonction Java/Maven avec le terminal/l’invite de commandes](./create-first-function-cli-java.md)<li>[Gradle](./functions-create-first-java-gradle.md)<li>[Eclipse](./functions-create-maven-eclipse.md)<li>[IntelliJ IDEA](./functions-create-maven-intellij.md) |
| **Voir une fonction en cours d’exécution** | <li>[Navigateur d’exemples Azure](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=java)<li>[Bibliothèque de la communauté Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Java) |
| **Explorer un tutoriel interactif**| <li>[Choix de la meilleure technologie serverless Azure pour un scénario métier](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework – Efficacité des performances](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Développer une application avec le plug-in Maven pour Azure Functions](https://docs.microsoft.com/learn/modules/develop-azure-functions-app-with-maven-plugin/) <br><br>Consultez Microsoft Learn pour obtenir la [liste complète des tutoriels interactifs](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions).|
| **En savoir plus** | <li>Découvrez comment les fonctions [augmentent ou diminuent automatiquement](./functions-scale.md) le nombre d’instances pour répondre à la demande<li>Explorez les différentes [méthodes de déploiement](./functions-deployment-technologies.md) disponibles<li>Utilisez des [outils de surveillance](./functions-monitoring.md) intégrés pour mieux analyser vos fonctions<li>Lisez la documentation de [référence du langage Java](./functions-reference-java.md)|
::: zone-end

::: zone pivot="programming-language-javascript"
| Action | Ressources |
| --- | --- |
| **Créer votre première fonction** | À l’aide de l’un des outils suivants :<br><br><li>[Visual Studio Code](./create-first-function-vs-code-node.md)<li>[Terminal/invite de commandes Node.js](./create-first-function-cli-java.md) |
| **Voir une fonction en cours d’exécution** | <li>[Navigateur d’exemples Azure](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=javascript%2Ctypescript)<li>[Bibliothèque de la communauté Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=JavaScript%2CTypeScript) |
| **Explorer un tutoriel interactif** | <li>[Choix de la meilleure technologie serverless Azure pour un scénario métier](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework – Efficacité des performances](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Créer des API serverless avec Azure Functions](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[Créer une logique serverless avec Azure Functions](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Refactoriser des API Node.js et Express en API serverless avec Azure Functions](https://docs.microsoft.com/learn/modules/shift-nodejs-express-apis-serverless/) <br><br>Consultez Microsoft Learn pour obtenir la [liste complète des tutoriels interactifs](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions).|
| **En savoir plus** | <li>Découvrez comment les fonctions [augmentent ou diminuent automatiquement](./functions-scale.md) le nombre d’instances pour répondre à la demande<li>Explorez les différentes [méthodes de déploiement](./functions-deployment-technologies.md) disponibles<li>Utilisez des [outils de surveillance](./functions-monitoring.md) intégrés pour mieux analyser vos fonctions<li>Lisez la documentation de référence du langage [JavaScript](./functions-reference-node.md) ou [TypeScript](./functions-reference-node.md#typescript)|
::: zone-end

::: zone pivot="programming-language-powershell"
| Action | Ressources |
| --- | --- |
| **Créer votre première fonction** | <li>Utilisation de [Visual Studio Code](./create-first-function-vs-code-powershell.md) |
| **Voir une fonction en cours d’exécution** | <li>[Navigateur d’exemples Azure](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=powershell)<li>[Bibliothèque de la communauté Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=PowerShell) |
| **Explorer un tutoriel interactif** | <li>[Choix de la meilleure technologie serverless Azure pour un scénario métier](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework – Efficacité des performances](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Créer des API serverless avec Azure Functions](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[Créer une logique serverless avec Azure Functions](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Exécuter une fonction Azure avec des déclencheurs](https://docs.microsoft.com/learn/modules/execute-azure-function-with-triggers/) <br><br>Consultez Microsoft Learn pour obtenir la [liste complète des tutoriels interactifs](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions).|
| **En savoir plus** | <li>Découvrez comment les fonctions [augmentent ou diminuent automatiquement](./functions-scale.md) le nombre d’instances pour répondre à la demande<li>Explorez les différentes [méthodes de déploiement](./functions-deployment-technologies.md) disponibles<li>Utilisez des [outils de surveillance](./functions-monitoring.md) intégrés pour mieux analyser vos fonctions<li>Lisez la documentation de [référence du langage PowerShell](./functions-reference-powershell.md)|
::: zone-end

::: zone pivot="programming-language-python"
| Action | Ressources |
| --- | --- |
| **Créer votre première fonction** | À l’aide de l’un des outils suivants :<br><br><li>[Visual Studio Code](./functions-create-first-function-vs-code.md?pivots=programming-language-python)<li>[Terminal/invite de commandes](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-python) |
| **Voir une fonction en cours d’exécution** | <li>[Navigateur d’exemples Azure](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=python)<li>[Bibliothèque de la communauté Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Python) |
| **Explorer un tutoriel interactif** | <li>[Choix de la meilleure technologie serverless Azure pour un scénario métier](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework – Efficacité des performances](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Créer des API serverless avec Azure Functions](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[Créer une logique serverless avec Azure Functions](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/) <br><br>Consultez Microsoft Learn pour obtenir la [liste complète des tutoriels interactifs](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions).|
| **En savoir plus** | <li>Découvrez comment les fonctions [augmentent ou diminuent automatiquement](./functions-scale.md) le nombre d’instances pour répondre à la demande<li>Explorez les différentes [méthodes de déploiement](./functions-deployment-technologies.md) disponibles<li>Utilisez des [outils de surveillance](./functions-monitoring.md) intégrés pour mieux analyser vos fonctions<li>Lisez la documentation de [référence du langage Python](./functions-reference-python.md)|
::: zone-end

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur l’anatomie d’une application Azure Functions](./functions-reference.md)
