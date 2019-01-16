---
title: Vue d’ensemble de Fonctions durables - Azure
description: Introduction à l’extension Fonctions durables pour Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 12/22/2018
ms.author: azfuncdf, glenga
ms.openlocfilehash: caeeaae4dca99a2522d767a5a4a18f0818a2fa7f
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54044108"
---
# <a name="what-is-durable-functions"></a>Présentation de Durable Functions

*Durable Functions* est une extension d’[Azure Functions](../functions-overview.md) qui vous permet d’écrire des fonctions avec état dans un environnement serverless. L’extension gère l’état, les points de contrôle et les redémarrages à votre place.

## <a name="benefits"></a>Avantages

L’extension vous permet de définir des workflows avec état à l’aide d’une [*fonction d’orchestrateur*](durable-functions-types-features-overview.md#orchestrator-functions), qui peut offrir les avantages suivants :

* Elles vous permettent de définir vos workflows dans le code. Elles ne nécessitent aucun schéma JSON ou concepteur.
* D’autres fonctions peuvent être appelées de façon synchrone et asynchrone. La sortie des fonctions appelées peut être enregistrée dans des variables locales.
* La progression est automatiquement contrôlée quand la fonction attend. L’état local n’est jamais perdu quand le processus est recyclé ou que la machine virtuelle redémarre.

## <a name="application-patterns"></a>Modèles d’application

Le principal cas d’usage de Durable Functions est la simplification d’exigences complexes de coordination avec état dans des applications serverless. Voici quelques exemples de modèles d’application standard qui peuvent tirer parti de Durable Functions :

* [Chaînage](durable-functions-concepts.md#chaining)
* [Fan-out/fan-in](durable-functions-concepts.md#fan-in-out)
* [API HTTP Async](durable-functions-concepts.md#async-http)
* [Surveillance](durable-functions-concepts.md#monitoring)
* [Interaction humaine](durable-functions-concepts.md#human)

## <a name="language-support"></a>Langages pris en charge

Durable Functions prend actuellement en charge les langages suivants :

* **C#**  : les [bibliothèques de classes précompilées](../functions-dotnet-class-library.md) et le [script C#](../functions-reference-csharp.md).
* **F#**  : les bibliothèques de classes précompilées et le script F#. Le script F# est pris en charge uniquement pour la version 1.x du runtime Azure Functions.
* **JavaScript** : pris en charge uniquement pour la version 2.x du runtime Azure Functions. Nécessite la version 1.7.0 ou ultérieure de l’extension Durable Functions. 

À l’avenir, Durable Functions est censé prendre en charge tous les [langages pris en charge dans Azure Functions](../supported-languages.md). Consultez la [liste des problèmes avec Durable Functions](https://github.com/Azure/azure-functions-durable-extension/issues) pour connaître l’état actuel de la prise en charge de langages supplémentaires.

Comme dans Azure Functions, des modèles sont disponibles pour vous aider à développer des fonctions durables en utilisant [Visual Studio 2017](durable-functions-create-first-csharp.md), [Visual Studio Code](quickstart-js-vscode.md) et le [portail Azure](durable-functions-create-portal.md).

## <a name="billing"></a>Facturation

Durable Functions suit le même modèle de facturation qu’Azure Functions. Pour plus d’informations, consultez [Tarification d’Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="jump-right-in"></a>Démarrage rapide

Vous pouvez démarrer avec Durable Functions en moins de dix minutes en suivant l’un de ces tutoriels de démarrage rapide propres à chaque langage :

* [C# avec Visual Studio 2017](durable-functions-create-first-csharp.md)
* [JavaScript avec Visual Studio Code](quickstart-js-vscode.md)

Dans les deux guides de démarrage rapide, vous créez et testez localement une fonction durable « hello world ». Vous allez ensuite publier le code de la fonction dans Azure. La fonction que vous créez orchestre et chaîne des appels à d’autres fonctions.

## <a name="learn-more"></a>En savoir plus

La vidéo suivante présente les avantages d’utiliser Durable Functions :

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Durable Functions est une extension avancée d’[Azure Functions](../functions-overview.md) et ne convient donc pas à toutes les applications. Pour en savoir plus sur l’extension Durable Functions, consultez [Concepts techniques et modèles Durable Functions](durable-functions-concepts.md). Pour une comparaison avec d’autres technologies d’orchestration Azure, consultez [Comparer Azure Functions et Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Concepts techniques et modèles Durable Functions](durable-functions-concepts.md)
