---
title: Créer une fonction C# à l’aide de Visual Studio Code – Azure Functions
description: Apprenez à créer une fonction C#, puis à publier le projet local sur un hébergement serverless dans Azure Functions, à l’aide de l’extension Azure Functions dans Visual Studio code.
ms.topic: quickstart
ms.date: 09/14/2021
ms.custom: devx-track-csharp
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-vs-code-csharp-ieux
zone_pivot_groups: runtime-version-programming-functions
ms.openlocfilehash: 4ebb5e0ff94669cb273cda31b63cd16dd448c104
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577143"
---
# <a name="quickstart-create-a-c-function-in-azure-using-visual-studio-code"></a>Démarrage rapide : Créer une fonction C# dans Azure à l’aide de Visual Studio Code

[!INCLUDE [functions-runtime-version-dotnet](../../includes/functions-runtime-version-dotnet.md)]

Dans cet article, vous utilisez Visual Studio Code pour créer une fonction C# qui répond aux requêtes HTTP. Après avoir testé le code localement, vous le déployez dans l’environnement serverless d’Azure Functions.

::: zone pivot="programming-runtime-functions-v3"
[!INCLUDE [functions-dotnet-execution-model](../../includes/functions-dotnet-execution-model.md)]    
::: zone-end
::: zone pivot="programming-runtime-functions-v4"
> [!NOTE]
> Actuellement, Visual Studio Code prend en charge la création de fonctions C# qui s’exécutent uniquement sur .NET 6 à l’aide du [modèle d’exécution in-process](functions-dotnet-class-library.md).
::: zone-end

Le fait de suivre ce guide de démarrage rapide entraîne une petite dépense de quelques cents USD tout au plus dans votre compte Azure.

Il existe également une [version basée sur l’interface CLI](create-first-function-cli-csharp.md) de cet article.

## <a name="configure-your-environment"></a>Configurer votre environnement

Avant de commencer, veillez à disposer des éléments suivants :

::: zone pivot="programming-runtime-functions-v3"
# <a name="in-process"></a>[In-process](#tab/in-process)

+ [Kit SDK .NET Core 3.1](https://dotnet.microsoft.com/download)

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) version 3.x.

+ [Visual Studio Code](https://code.visualstudio.com/) sur l’une des [plateformes prises en charge](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ [Extension C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) pour Visual Studio Code.  

+ [Extension Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pour Visual Studio Code.

# <a name="isolated-process"></a>[Processus isolé](#tab/isolated-process)

+ [Kit SDK .NET 5.0](https://dotnet.microsoft.com/download)

+ [Kit SDK .NET Core 3.1](https://dotnet.microsoft.com/download). Exigé par le processus de génération.

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) version 3.x.

+ [Visual Studio Code](https://code.visualstudio.com/) sur l’une des [plateformes prises en charge](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ [Extension C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) pour Visual Studio Code.  

+ [Extension Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pour Visual Studio Code.

---
::: zone-end
::: zone pivot="programming-runtime-functions-v4"
+ [SDK .NET 6.0](https://dotnet.microsoft.com/download/dotnet/6.0)

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) version 4.x.

+ [Visual Studio Code](https://code.visualstudio.com/) sur l’une des [plateformes prises en charge](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ [Extension C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) pour Visual Studio Code.  

+ [Extension Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pour Visual Studio Code.
::: zone-end

Vous avez aussi besoin d’un compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Créer votre projet local

Dans cette section, vous utilisez Visual Studio Code pour créer un projet Azure Functions local en C#. Plus loin dans cet article, vous allez publier votre code de fonction sur Azure.

1. Choisissez l’icône Azure dans la barre d’activité, puis dans la zone **Azure : Fonctions**, sélectionnez l’icône **Créer un projet**.

    ![Choisir Créer un projet](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Choisissez un emplacement de répertoire pour votre espace de travail de projet et optez pour **Sélectionner**.

    > [!NOTE]
    > Ces étapes ont été conçues pour être terminées en dehors d’un espace de travail. Dans ce cas, ne sélectionnez pas de dossier de projet qui fait partie d’un espace de travail.

1. Quand vous y êtes invité, indiquez les informations suivantes :

    ::: zone pivot="programming-runtime-functions-v3"
    # <a name="in-process"></a>[In-process](#tab/in-process) 

    |Prompt|Sélection|
    |--|--|
    |**Sélectionner un langage pour votre projet de fonction**|Choisissez `C#`.|
    | **Sélectionner un runtime .NET** | Choisissez `.NET Core 3.1 LTS`.|
    |**Sélectionner un modèle pour la première fonction de votre projet**|Choisissez `HTTP trigger`.|
    |**Fournir un nom de fonction**|Tapez `HttpExample`.|
    |**Fournir un espace de noms** | Tapez `My.Functions`. |
    |**Niveau d’autorisation**|Choisissez l’option `Anonymous`, qui permet à quiconque d’appeler le point de terminaison de votre fonction. Pour en savoir plus sur le niveau d’autorisation, consultez [Clés d’autorisation](functions-bindings-http-webhook-trigger.md#authorization-keys).|
    |**Sélectionner la façon dont vous souhaitez ouvrir votre projet**|Choisissez `Add to workspace`.|

    # <a name="isolated-process"></a>[Processus isolé](#tab/isolated-process)

    |Prompt|Sélection|
    |--|--|
    |**Sélectionner un langage pour votre projet de fonction**|Choisissez `C#`.|
    | **Sélectionner un runtime .NET** | Choisissez `.NET 5.0 Isolated`.|
    |**Sélectionner un modèle pour la première fonction de votre projet**|Choisissez `HTTP trigger`.|
    |**Fournir un nom de fonction**|Tapez `HttpExample`.|
    |**Fournir un espace de noms** | Tapez `My.Functions`. |
    |**Niveau d’autorisation**|Choisissez l’option `Anonymous`, qui permet à quiconque d’appeler le point de terminaison de votre fonction. Pour en savoir plus sur le niveau d’autorisation, consultez [Clés d’autorisation](functions-bindings-http-webhook-trigger.md#authorization-keys).|
    |**Sélectionner la façon dont vous souhaitez ouvrir votre projet**|Choisissez `Add to workspace`.|

    ---
    ::: zone-end
    ::: zone pivot="programming-runtime-functions-v4"
    |Prompt|Sélection|
    |--|--|
    |**Sélectionner un langage pour votre projet de fonction**|Choisissez `C#`.|
    | **Sélectionner un runtime .NET** | Choisissez `.NET 6.0`.<sup>*</sup>|
    |**Sélectionner un modèle pour la première fonction de votre projet**|Choisissez `HTTP trigger`.|
    |**Fournir un nom de fonction**|Tapez `HttpExample`.|
    |**Fournir un espace de noms** | Tapez `My.Functions`. |
    |**Niveau d’autorisation**|Choisissez l’option `Anonymous`, qui permet à quiconque d’appeler le point de terminaison de votre fonction. Pour en savoir plus sur le niveau d’autorisation, consultez [Clés d’autorisation](functions-bindings-http-webhook-trigger.md#authorization-keys).|
    |**Sélectionner la façon dont vous souhaitez ouvrir votre projet**|Choisissez `Add to workspace`.|

    <sup>*</sup>Si vous ne voyez pas `.NET 6` en tant qu’option d’exécution, vérifiez que vous avez installé la version 4.x d’Azure Functions Core Tools.
    ::: zone-end
    
1. À l’aide de ces informations, Visual Studio Code génère un projet Azure Functions avec un déclencheur HTTP. Vous pouvez voir les fichiers de projet locaux dans l’Explorateur. Pour en savoir plus sur les fichiers créés, consultez [Fichiers projet générés](functions-develop-vs-code.md#generated-project-files).

[!INCLUDE [functions-run-function-test-local-vs-code-csharp](../../includes/functions-run-function-test-local-vs-code-csharp.md)]

Après avoir vérifié que la fonction s’exécute correctement sur votre ordinateur local, il est temps d’utiliser Visual Studio Code pour publier le projet directement sur Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous avez utilisé [Visual Studio Code](functions-develop-vs-code.md?tabs=csharp) pour créer une application de fonction à l’aide d’une simple fonction déclenchée par HTTP. Dans le prochain article, vous allez développer cette fonction en vous connectant à Azure Cosmos DB ou à Stockage File d’attente Azure. Pour en savoir plus sur la connexion à d’autres services Azure, consultez [Ajouter des liaisons à une fonction existante dans Azure Functions](add-bindings-existing-function.md?tabs=csharp). 

::: zone pivot="programming-runtime-functions-v3"
# <a name="in-process"></a>[In-process](#tab/in-process) 

> [!div class="nextstepaction"]
> [Se connecter à Azure Cosmos DB](functions-add-output-binding-cosmos-db-vs-code.md?pivots=programming-language-csharp&tabs=in-process)
> [Se connecter Stockage File d’attente Azure](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-csharp&tabs=in-process)

# <a name="isolated-process"></a>[Processus isolé](#tab/isolated-process)

> [!div class="nextstepaction"]
> [Se connecter à Azure Cosmos DB](functions-add-output-binding-cosmos-db-vs-code.md?pivots=programming-language-csharp&tabs=isolated-process)
> [Se connecter Stockage File d’attente Azure](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-csharp&tabs=isolated-process)

---
::: zone-end
::: zone pivot="programming-runtime-functions-v4"
> [!div class="nextstepaction"]
> [Se connecter à Azure Cosmos DB](functions-add-output-binding-cosmos-db-vs-code.md?pivots=programming-language-csharp&tabs=in-process)
> [Se connecter Stockage File d’attente Azure](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-csharp&tabs=in-process)
::: zone-end

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
