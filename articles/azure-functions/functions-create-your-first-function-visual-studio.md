---
title: Créer votre première fonction dans Azure à l’aide de Visual Studio
description: Créez et publiez une fonction Azure HTTP déclenchée à l’aide de Visual Studio.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 07/19/2019
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: b343adeed5abeecdf55f71f8bc4298659a106e09
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198392"
---
# <a name="create-your-first-function-using-visual-studio"></a>Créer votre première fonction à l’aide de Visual Studio

Azure Functions vous permet d’exécuter votre code dans un environnement [sans serveur](https://azure.microsoft.com/solutions/serverless/) et sans avoir à créer une machine virtuelle ou à publier une application web au préalable.

Dans cet article, vous allez découvrir comment utiliser Visual Studio 2019 pour créer et tester en local une fonction « Hello World » et la publier sur Azure. Ce guide de démarrage rapide est conçu pour Visual Studio 2019. Lorsque vous créez un projet Functions à l’aide de Visual Studio 2017, vous devez commencer par installer les [derniers outils Azure Functions](functions-develop-vs.md#check-your-tools-version).

![Réponse de la fonction localhost dans le navigateur](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous devez commencer par installer [Visual Studio 2019](https://azure.microsoft.com/downloads/). Vérifiez que la charge de travail de **développement Azure** est également installée.

![Installez Visual Studio avec la charge de travail de développement Azure.](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Créer un projet d’application de fonction

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio crée un projet et une classe qui contient un code réutilisable pour le type de fonction de déclencheur HTTP. L’attribut `FunctionName` de la méthode définit le nom de la fonction qui, par défaut, est `Function1`. L’attribut `HttpTrigger` spécifie que l’exécution de la fonction est déclenchée par une requête HTTP. Le code réutilisable envoie une réponse HTTP qui inclut une valeur de la chaîne de requête ou du corps de requête.

Vous pouvez étendre les capacités de votre fonction à l’aide de liaisons d’entrée et de sortie en appliquant les attributs appropriés à la méthode. Pour plus d’informations, voir la section [Déclencheurs et liaisons](functions-dotnet-class-library.md#triggers-and-bindings) de l’article [Informations de référence pour les développeurs C# sur Azure Functions](functions-dotnet-class-library.md).

Maintenant que vous avez créé un projet de fonction et une fonction déclenchée via HTTP, vous pouvez la tester sur votre ordinateur local.

## <a name="run-the-function-locally"></a>Exécuter la fonction localement

Visual Studio s’intègre avec Azure Functions Core Tools pour vous permettre de tester vos fonctions en local à l’aide du runtime Functions complet.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Après avoir vérifié que la fonction s’exécute correctement sur votre ordinateur local, il est temps de publier le projet sur Azure.

## <a name="publish-the-project-to-azure"></a>Publication du projet sur Azure

Vous devez disposer d’une application de fonction dans votre abonnement Azure avant de pouvoir publier votre projet. La publication Visual Studio crée une application de fonction pour vous la première fois que vous publiez votre projet.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Tester votre fonction dans Azure

1. Copiez l’URL de base de l’application de fonction à partir de la page de profil de publication. Remplacez la partie `localhost:port` de l’URL que vous avez utilisée lors du test en local de la fonction par la nouvelle URL de base. Comme auparavant, assurez-vous d’ajouter la chaîne de requête `?name=<YOUR_NAME>` à cette URL et exécutez la demande.

    L’URL qui appelle la fonction déclenchée via HTTP doit être au format suivant :

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME> 

2. Collez cette nouvelle URL de requête HTTP dans la barre d’adresse de votre navigateur. La capture d’écran suivante du navigateur montre la requête renvoyée par la fonction suite à la demande distante GET :

    ![Réponse de la fonction dans le navigateur](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="next-steps"></a>Étapes suivantes

Vous avez utilisé Visual Studio pour créer et publier une application de fonction C# dans Azure à l’aide d’une simple fonction déclenchée via HTTP. Pour en savoir plus sur le développement de fonctions en tant que bibliothèques de classes, consultez [Informations de référence pour les développeurs C# sur Azure Functions](functions-dotnet-class-library.md).

> [!div class="nextstepaction"]
> [Ajouter une liaison de file d’attente Stockage Azure à votre fonction](functions-add-output-binding-storage-queue-vs.md)
