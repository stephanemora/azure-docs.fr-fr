---
title: Créer des fonctions Durable Functions à l’aide du portail Azure
description: Découvrez comment installer l’extension Durable Functions pour Azure Functions pour le développement de portails.
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: azfuncdf
ms.openlocfilehash: 0060088acb100036c094406e01d0d736a4af88eb
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769640"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Créer des fonctions Durable Functions à l’aide du portail Azure

L’extension [Fonctions durables](durable-functions-overview.md) d’Azure Functions est fournie dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Cette extension doit être installée dans votre application de fonction. Cet article explique comment installer ce package, pour vous permettre de développer des fonctions durables dans le portail Azure.

> [!NOTE]
> 
> * Si vous développez des fonctions durables dans C#, pensez plutôt à utiliser le [développement Visual Studio 2019](durable-functions-create-first-csharp.md).
> * Si vous développez des fonctions durables dans JavaScript, pensez plutôt à utiliser le [développement Visual Studio Code](./quickstart-js-vscode.md).

## <a name="create-a-function-app"></a>Créer une application de fonction

Vous devez disposer d’une application de fonction pour héberger l’exécution d’une fonction. Une application de fonctions vous permet de regrouper vos fonctions en une unité logique pour faciliter la gestion, le déploiement, la mise à l'échelle et le partage des ressources. Vous pouvez créer une application .NET ou JavaScript.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Par défaut, l’application de fonction créée utilise la version 2.x du runtime d’Azure Functions. L’extension Durable Functions fonctionne sur les deux versions 1.x et 2.x du runtime d’Azure Functions en C#, et dans la version 2.x en JavaScript. Toutefois, les modèles sont disponibles uniquement lorsque vous ciblez la version 2.x du runtime, quel que soit le langage choisi.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>Installer le package npm durable-functions (JavaScript uniquement)

Si vous créez des fonctions Durable Functions JavaScript, vous devez installer le [package npm `durable-functions`](https://www.npmjs.com/package/durable-functions).

1. Sélectionnez le nom de votre application de fonction, puis **Fonctionnalités de la plateforme** et enfin **Outils avancés (Kudu)** .

   ![Les fonctionnalités de la plateforme Functions choisissent Kudu](./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png)

2. Dans la console Kudu, sélectionnez **Console de débogage**, puis **CMD**.

   ![Console de débogage Kudu](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

3. La structure de répertoires de fichiers de votre application de fonction doit s’afficher. Accédez au dossier `site/wwwroot`. À partir de cet emplacement, vous pouvez charger un fichier `package.json` en le faisant glisser et en le déposant dans la fenêtre de répertoires de fichiers. Voici un exemple de fichier `package.json` :

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.3.1"
      }
    }
    ```

   ![Chargement du fichier package.json dans Kudu](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

4. Une fois votre fichier `package.json` chargé, exécutez la commande `npm install` dans la console d’exécution à distance Kudu.

   ![Exécution de la commande npm install dans Kudu](./media/durable-functions-create-portal/kudu-npm-install.png)

## <a name="create-an-orchestrator-function"></a>Créer une fonction d’orchestrateur

1. Développez votre Function App, puis cliquez sur le bouton **+** en regard de **Fonctions**. S’il s’agit de la première fonction de votre application de fonction, sélectionnez **Dans le portail**, puis **Continuer**. Sinon, passez à l’étape 3.

   ![Page de démarrage rapide des fonctions sur le portail Azure](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. Choisissez **Autres modèles**, puis **Terminer et afficher les modèles**.

    ![Page de démarrage rapide Functions permettant de choisir d’autres modèles](./media/durable-functions-create-portal/add-first-function.png)

1. Dans le champ de recherche, tapez `durable`, puis choisissez le modèle **Starter HTTP Durable Functions**.

1. Si vous y êtes invité, sélectionnez **Installer** pour installer l’extension Azure DurableTask et toutes les dépendances dans l’application de fonction. Il vous suffit d’installer l’extension une seule fois pour une application de fonction donnée. Une fois l’installation réussie, sélectionnez **Continuer**.

    ![Installer des extensions de liaison](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. Une fois l’installation terminée, nommez la nouvelle fonction `HttpStart` et choisissez **Créer**. La fonction créée est utilisée pour démarrer l’orchestration.

1. Créez une autre fonction dans l’application de fonction, cette fois en utilisant le modèle **Orchestrateur Durable Functions**. Nommez votre nouvelle fonction d’orchestration `HelloSequence`.

1. Créez une troisième fonction nommée `Hello` à l’aide du modèle **Activité Durable Functions**.

## <a name="test-the-durable-function-orchestration"></a>Tester l’orchestration de la fonction durable

1. Revenez à la fonction **HttpStart**, choisissez **</> Obtenir l’URL de la fonction** et **copiez** l’URL. Cette URL vous permet de démarrer la fonction **HelloSequence**.

1. Utilisez un outil HTTP tel que Postman ou cURL pour envoyer une requête POST à l’URL que vous avez copiée. L’exemple suivant est une commande cURL qui envoie une requête POST à la fonction durable :

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

    Dans cet exemple, `{your-function-app-name}` est le domaine qui est le nom de votre application de fonction. Le message de réponse contient un ensemble de points de terminaison d’URI que vous pouvez utiliser pour surveiller et gérer l’exécution, qui ressemble à l’exemple suivant :

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Appelez l’URI de point de terminaison `statusQueryGetUri` et l’état actuel de la fonction durable s’affiche. Il peut se présenter comme dans cet exemple :

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Continuez d’appeler le point de terminaison `statusQueryGetUri` jusqu’à ce que l’état passe à **Terminé**, et qu’une réponse s’affiche, similaire à l’exemple suivant :

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

Votre première fonction durable est maintenant opérationnelle dans Azure.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrez maintenant les modèles courants de fonctions durables](durable-functions-overview.md#application-patterns)
