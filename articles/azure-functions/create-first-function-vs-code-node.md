---
title: Créer une fonction JavaScript à l’aide de Visual Studio Code – Azure Functions
description: Apprenez à créer une fonction JavaScript, puis à publier le projet Node.js local sur un hébergement serverless dans Azure Functions, à l’aide de l’extension Azure Functions dans Visual Studio code.
ms.topic: quickstart
ms.date: 07/01/2021
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-vs-code-node_uiex
ms.openlocfilehash: 09c028d71d7cef4b83220a7c93a24c6bc3c256d4
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113732539"
---
# <a name="quickstart-create-a-javascript-function-in-azure-using-visual-studio-code"></a>Démarrage rapide : Créer une fonction JavaScript dans Azure à l’aide de Visual Studio Code

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

Utilisez Visual Studio Code pour créer une fonction JavaScript qui répond aux requêtes HTTP. Testez le code localement, puis déployez-le dans l’environnement serverless d’Azure Functions.

Le fait de suivre ce guide de démarrage rapide entraîne une petite dépense de quelques cents USD tout au plus dans votre compte Azure.

Il existe également une [version basée sur l’interface CLI](create-first-function-cli-node.md) de cet article.

## <a name="configure-your-environment"></a>Configurer votre environnement

Avant de commencer, veillez à disposer des éléments suivants :

+ Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Node.js 10.14.1+](https://nodejs.org/). Utilisez la commande `node --version` pour vérifier la version que vous utilisez.  

+ [Visual Studio Code](https://code.visualstudio.com/) sur l’une des [plateformes prises en charge](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ [Extension Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pour Visual Studio Code.

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Créer votre projet local

Dans cette section, vous utilisez Visual Studio Code pour créer un projet Azure Functions local en JavaScript. Plus loin dans cet article, vous allez publier votre code de fonction sur Azure.

1. Choisissez l’icône Azure dans la barre d’activité, puis dans la zone **Azure : Fonctions**, sélectionnez l’icône **Créer un projet**.

    ![Choisir Créer un projet](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Choisissez un emplacement de répertoire pour votre espace de travail de projet et optez pour **Sélectionner**.

    > [!NOTE]
    > Ces étapes ont été conçues pour être terminées en dehors d’un espace de travail. Dans ce cas, ne sélectionnez pas de dossier de projet qui fait partie d’un espace de travail.

1. Quand vous y êtes invité, indiquez les informations suivantes :

    |Prompt|Sélection|
    |--|--|
    |**Sélectionner un langage pour votre projet de fonction**|Choisissez `JavaScript`.|
    |**Sélectionner un modèle pour la première fonction de votre projet**|Choisissez `HTTP trigger`.|
    |**Fournir un nom de fonction**|Tapez `HttpExample`.|
    |**Niveau d’autorisation**|Choisissez l’option `Anonymous`, qui permet à quiconque d’appeler le point de terminaison de votre fonction. Pour en savoir plus sur le niveau d’autorisation, consultez [Clés d’autorisation](functions-bindings-http-webhook-trigger.md#authorization-keys).|
    |**Sélectionner la façon dont vous souhaitez ouvrir votre projet**|Choisissez `Add to workspace`.|

    À l’aide de ces informations, Visual Studio Code génère un projet Azure Functions avec un déclencheur HTTP. Vous pouvez voir les fichiers de projet locaux dans l’Explorateur. Pour en savoir plus sur les fichiers créés, consultez [Fichiers projet générés](functions-develop-vs-code.md#generated-project-files). 

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Après avoir vérifié que la fonction s’exécute correctement sur votre ordinateur local, il est temps d’utiliser Visual Studio Code pour publier le projet directement sur Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

<a name="Publish the project to Azure"></a>

## <a name="deploy-the-project-to-azure"></a>Déployer le projet dans Azure

Dans cette section, vous créez une application de fonction et les ressources associées dans votre abonnement Azure, puis vous déployez votre code. 

> [!IMPORTANT]
> Le déploiement vers une application de fonction existante remplace le contenu de cette application dans Azure. 


1. Choisissez l’icône Azure dans la barre d’activité, puis dans la zone **Azure : Fonctions**, choisissez le bouton **Déployer sur une application de fonction**.

    ![Publier votre projet sur Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Quand vous y êtes invité, indiquez les informations suivantes :

    |Prompt| Sélection|
    |--|--|
    |**Sélectionner l’application de fonction dans Azure**|Choisissez `+ Create new Function App`. (Ne choisissez pas l’option `Advanced`, qui n’est pas abordée dans cet article.)|
    |**Entrer un nom global unique pour l’application de fonction**|Tapez un nom valide dans un chemin d’URL. Le système vérifie que le nom que vous tapez est unique dans Azure Functions.|
    |**Sélectionnez un runtime**|Choisissez la version de Node.js que vous avez exécutée localement. Vous pouvez utiliser la commande `node --version` pour vérifier votre version.|
    |**Sélectionnez un emplacement pour les nouvelles ressources**|Pour de meilleures performances, choisissez une [région](https://azure.microsoft.com/regions/) proche de vous.|

    L’extension montre l’état des ressources individuelles au fur et à mesure de leur création dans Azure, au sein de la zone de notification.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Notification de création de ressources Azure":::

    Quand vous avez terminé, les ressources Azure suivantes sont créées dans votre abonnement et leurs noms reposent sur le nom de votre application de fonction :

    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

1. Une notification s’affiche après que votre application de fonction a été créée et que le package de déploiement a été appliqué. 

    [!INCLUDE [functions-vs-code-create-tip](../../includes/functions-vs-code-create-tip.md)]

1. Sélectionnez **Afficher la sortie de** dans cette notification pour afficher les résultats de la création et du déploiement, y compris les ressources Azure que vous avez créées. Si vous manquez la notification, sélectionnez l’icône de cloche dans le coin inférieur droit pour la voir de nouveau.

    ![Créer une notification de fin](./media/functions-create-first-function-vs-code/function-create-notifications.png)

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

## <a name="change-the-code-and-redeploy-to-azure"></a>Modifiez le code et redéployer vers Azure

1. Dans la vue Explorer VSCode, sélectionnez le fichier `./HttpExample/index.js`. 
1. Remplacez le fichier par le code suivant pour construire un objet JSON et le retourner.

    ```javascript
    module.exports = async function (context, req) {
        
        try {
            context.log('JavaScript HTTP trigger function processed a request.');
    
            // Read incoming data
            const name = req.query.name;
            const sport = req.query.sport;
        
            // fail if incoming data is required
            if (!name || !sport) {
    
                context.res = {
                    status: 400
                };
                return;
            }
            
            // Add or change code here
            const message = `${name} likes ${sport}`;
        
            // Construct response
            const responseJSON = {
                "name": name,
                "sport": sport,
                "message": message,
                "success": true
            }
    
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: responseJSON,
                contentType: 'application/json'
            };
        } catch(err) {
            context.res = {
                status: 500
            };
        }
    }
    ```
1. [Exécuter à nouveau l’application](#run-the-function-locally) de fonction localement.
1. Dans l’invite **Entrer le corps de la demande**, remplacez le corps du message de la demande par {"name" : "Tom", "Sport" : "basketball"}. Appuyez sur Entrée pour envoyer ce message de requête à votre fonction.
1. Affichez la réponse dans la notification :

    ```json
    {
      "name": "Tom",
      "sport": "basketball",
      "message": "Tom likes basketball",
      "success": true
    }
    ```

1. [Déployer à nouveau la fonction](#deploy-the-project-to-azure) sur Azure.

## <a name="troubleshooting"></a>Dépannage

Utilisez la table ci-dessous pour résoudre les problèmes les plus courants rencontrés lors de l’utilisation de ce guide de démarrage rapide.

|Problème|Solution|
|--|--|
|Impossible de créer un projet de fonction local ?|Assurez-vous que [L’extension Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) est installée.|
|Impossible d’exécuter la fonction localement ?|Vérifiez que [Azure Functions Core Tools](functions-run-local.md?tabs=windows%2Ccsharp%2Cbash) sont installés. <br/>Lors de l’exécution sur Windows, veillez à ce que l’interpréteur de commandes du terminal par défaut pour Visual Studio Code ne soit pas défini sur WSL Bash.|
|Impossible de déployer une fonction sur Azure ?|Passez en revue la sortie et recherchez des informations d’erreur. L’icône en forme de cloche dans le coin inférieur droit est une autre façon d’afficher la sortie. Avez-vous publié dans une application de fonction existante ? Cette action remplace le contenu de cette application dans Azure.|
|Vous n’avez pas pu exécuter l’application de fonction basée sur le cloud ?|N’oubliez pas d’utiliser la chaîne de requêtes pour y envoyer les paramètres.|

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code-extension.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous avez utilisé [Visual Studio Code](functions-develop-vs-code.md?tabs=javascript) pour créer une application de fonction à l’aide d’une simple fonction déclenchée par HTTP. Dans le prochain article, vous allez développer cette fonction en vous connectant à Azure Cosmos DB ou à Stockage Azure. Pour en savoir plus sur la connexion à d’autres services Azure, consultez [Ajouter des liaisons à une fonction existante dans Azure Functions](add-bindings-existing-function.md?tabs=javascript).  

> [!div class="nextstepaction"]
> [Se connecter à une base de données](functions-add-output-binding-cosmos-db-vs-code.md?pivots=programming-language-javascript)
> [!div class="nextstepaction"]
> [Se connecter à une file d’attente Stockage Azure](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-javascript)
> [Sécurisation de votre fonction](security-concepts.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
