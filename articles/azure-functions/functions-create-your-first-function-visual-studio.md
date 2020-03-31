---
title: 'Démarrage rapide : Créer votre première fonction dans Azure à l’aide de Visual Studio'
description: Dans ce guide de démarrage rapide, vous allez apprendre à créer et à publier une fonction Azure déclenchée par HTTP à l’aide de Visual Studio.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 03/06/2020
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: 13a03ca6820229fc90467236e0c23f236664d73f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80056673"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Démarrage rapide : Créer votre première fonction dans Azure à l’aide de Visual Studio

Azure Functions vous permet d’exécuter votre code dans un environnement serverless, et sans avoir à créer une machine virtuelle ou à publier une application web au préalable.

Dans ce guide de démarrage rapide, vous allez découvrir comment utiliser Visual Studio 2019 pour créer et tester en local une application de fonction C# déclenchée par HTTP, « Hello world », avant de la publier sur Azure. 

![Réponse de la fonction localhost dans le navigateur](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

Ce guide de démarrage rapide est conçu pour Visual Studio 2019. 

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, commencez par installer [Visual Studio 2019](https://azure.microsoft.com/downloads/). Assurez-vous de sélectionner la charge de travail **Développement Azure** lors de l’installation. Si vous préférez créer un projet Azure Functions avec Visual Studio 2017, vous devez commencer par installer les tout [derniers outils Azure Functions](functions-develop-vs.md#check-your-tools-version).

![Installez Visual Studio avec la charge de travail de développement Azure.](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Créer un projet d’application de fonction

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio crée un projet et une classe qui contient un code réutilisable pour le type de fonction de déclencheur HTTP. L’attribut de méthode `FunctionName` définit le nom de la fonction qui, par défaut, est `Function1`. L’attribut `HttpTrigger` spécifie que l’exécution de la fonction est déclenchée par une requête HTTP. Le code réutilisable envoie une réponse HTTP qui inclut une valeur de la chaîne de requête ou du corps de requête.

Étendez les capacités de votre fonction à l’aide de liaisons d’entrée et de sortie en appliquant les attributs appropriés à la méthode. Pour plus d’informations, voir la section [Déclencheurs et liaisons](functions-dotnet-class-library.md#triggers-and-bindings) de l’article [Informations de référence pour les développeurs C# sur Azure Functions](functions-dotnet-class-library.md).

Maintenant que vous avez créé un projet de fonction et une fonction déclenchée via HTTP, vous pouvez la tester sur votre ordinateur local.

## <a name="run-the-function-locally"></a>Exécuter la fonction localement

Visual Studio s’intègre à Azure Functions Core Tools pour vous permettre de tester vos fonctions en local avec le runtime Azure Functions complet.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Après avoir vérifié que la fonction s’exécute correctement sur votre ordinateur local, il est temps de publier le projet sur Azure.

## <a name="publish-the-project-to-azure"></a>Publication du projet sur Azure

Avant de pouvoir publier votre projet, vous devez disposer d’une application de fonction dans votre abonnement Azure. La publication Visual Studio crée une application de fonction pour vous la première fois que vous publiez votre projet.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Tester votre fonction dans Azure

1. Copiez l’URL de base de l’application de fonction à partir de la page de profil **Publier**. Remplacez la partie `localhost:port` de l’URL que vous avez utilisée pour tester en local la fonction par la nouvelle URL de base. Ajoutez la chaîne de requête `?name=<YOUR_NAME>` à cette URL et exécutez la demande.

    L’URL qui appelle la fonction déclenchée via HTTP est au format suivant :

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME>`

2. Collez cette nouvelle URL de requête HTTP dans la barre d’adresse de votre navigateur. L’image suivante montre dans le navigateur la requête retournée par la fonction suite à la demande distante GET :

    ![Réponse de la fonction dans le navigateur](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez utilisé Visual Studio pour créer et publier une application de fonction C# dans Azure à l’aide d’une simple fonction déclenchée via HTTP. 

Passez à l’article suivant pour savoir comment ajouter une liaison de file d’attente de stockage Azure à votre fonction :
> [!div class="nextstepaction"]
> [Ajouter une liaison de file d’attente Stockage Azure à votre fonction](functions-add-output-binding-storage-queue-vs.md)

