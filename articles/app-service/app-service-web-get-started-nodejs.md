---
title: 'Démarrage rapide : Créer une application web Node.js'
description: Déployez votre premier programme Hello World Node.js dans Azure App Service en quelques minutes. Vous effectuez le déploiement à l’aide de Visual Studio Code, qui figure parmi les nombreuses façons de déployer sur App Service.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 03/04/2020
ms.custom: mvc, devcenter, seodec18, devx-track-javascript
ms.openlocfilehash: 0e72c17ab20d092a710bb21b1ff6d3d6418e452f
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170261"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Créer une application web Node.js dans Azure 

Commencez avec Azure App Service en créant une application Node.js/Express localement à l’aide de Visual Studio Code et en la déployant dans le cloud. Comme vous utilisez un niveau App Service gratuit, vous pouvez suivre ce guide de démarrage rapide gratuitement.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- [Node.js et npm](https://nodejs.org). Exécutez la commande `node --version` pour vérifier que Node.js est installé.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Extension Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) pour Visual Studio Code.

## <a name="clone-and-run-a-local-nodejs-application"></a>Cloner et exécuter une application Node.js locale

1. Sur votre ordinateur local, ouvrez un terminal et clonez l’exemple de référentiel :

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Accédez au nouveau dossier de l’application :

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Démarrez l’application pour la tester localement :

    ```bash
    npm start
    ```
    
1. Ouvrez votre navigateur et accédez à `http://localhost:1337`. Le navigateur doit afficher le message « Hello World! ».

1. Appuyez sur **Ctrl**+**C** dans le terminal pour arrêter le serveur.

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>Déploiement de l’application dans Azure

Dans cette section, vous déployez votre application Node.js sur Azure à l’aide de VS Code et de l’extension Azure App Service.

1. Dans le terminal, vérifiez que vous vous trouvez dans le dossier *nodejs-docs-hello-world*, puis démarrez Visual Studio Code à l’aide de la commande suivante :

    ```bash
    code .
    ```

1. Dans la barre d’activité de VS Code, sélectionnez le logo Azure pour afficher l’explorateur **AZURE APP SERVICE**. Sélectionnez **Se connecter à Azure...** et suivez les instructions. (Consultez la section [Résolution des problèmes de connexion à Azure](#troubleshooting-azure-sign-in) plus bas si vous rencontrez des erreurs.) Quand vous êtes connecté, l’explorateur doit afficher le nom de votre abonnement Azure.

    ![Connexion à Azure](containers/media/quickstart-nodejs/sign-in.png)

1. Dans l’explorateur **AZURE APP SERVICE** de VS Code, sélectionnez l’icône représentant une flèche bleue pointant vers le haut pour déployer votre application sur Azure. (Vous pouvez également appeler la même commande à partir de la **palette de commandes** (**Ctrl**+**Maj**+**P**) en tapant « déployer sur l’application web » et en choisissant **Azure App Service : Déployer sur l’application web**).

    ![Déployer sur l'application web](containers/media/quickstart-nodejs/deploy.png)
        
1. Choisissez le dossier *nodejs-docs-hello-world*.

1. Choisissez une option de création basée sur le système d’exploitation sur lequel vous souhaitez effectuer le déploiement :

    - Linux : Sélectionnez **Créer une application web**.
    - Windows : Sélectionnez **Créer une application web... Avancé**

1. Saisissez un nom unique pour votre application web et appuyez sur **Entrée**. Le nom doit être unique sur l’ensemble d’Azure et comporter uniquement des caractères alphanumériques (« A-Z », « a-z » et « 0-9 ») et des traits d’union (« - »).

1. Si vous ciblez Linux, sélectionnez une version de Node.js quand vous y êtes invité. Une version **LTS** est recommandée.

1. Si vous ciblez Windows, suivez les invites supplémentaires :
    1. Sélectionnez **Créer un groupe de ressources** et entrez un nom pour le groupe de ressources, par exemple `AppServiceQS-rg`.
    1. Sélectionnez **Windows** comme système d’exploitation.
    1. Sélectionnez **Créer un plan App Service**, entrez un nom pour le plan (par exemple, `AppServiceQS-plan`), puis sélectionnez le niveau tarifaire **F1 Gratuit**.
    1. À l’invite Application Insights, choisissez **Ignorer pour le moment**.
    1. Choisissez une région près de chez vous ou à proximité des ressources auxquelles vous souhaitez accéder.

1. Quand vous avez répondu à toutes les invites, VS Code affiche les ressources Azure qui sont créées pour votre application dans sa fenêtre de notification.

    Si vous effectuez un déploiement dans Linux, sélectionnez **Oui** quand vous êtes invité à mettre à jour votre configuration pour exécuter `npm install` sur le serveur Linux cible.

    ![Invite concernant la mise à jour de la configuration sur le serveur Linux cible](containers/media/quickstart-nodejs/server-build.png)

1. Sélectionnez **Oui** quand le message suivant s’affiche : **Toujours déployer l’espace de travail "nodejs-docs-hello-world" sur (nom de l’application)"** . Quand vous sélectionnez **Oui**, vous indiquez à VS Code de cibler automatiquement la même application web App Service pour les déploiements suivants.

1. Si vous effectuez le déploiement sur Linux, à l’issue du déploiement, sélectionnez **Parcourir le site Web** dans l’invite pour voir l’application web que vous venez de déployer. Le navigateur doit afficher le message « Hello World! ».

1. Si vous effectuez le déploiement sur Windows, vous devez d’abord définir le numéro de version Node.js pour l’application web :

    1. Dans VS Code, développez le nœud de votre nouveau service d’application, cliquez avec le bouton droit sur **Paramètres de l’application**, puis sélectionnez **Ajouter un nouveau paramètre...**  :

        ![Commande d’ajout de paramètre d’application](containers/media/quickstart-nodejs/add-setting.png)

    1. Entrez `WEBSITE_NODE_DEFAULT_VERSION` pour la clé du paramètre.
    1. Entrez `10.15.2` pour la valeur du paramètre.
    1. Cliquez avec le bouton droit sur le nœud du service d’application et sélectionnez **Redémarrer**.

        ![Commande de redémarrage du service d’application](containers/media/quickstart-nodejs/restart.png)

    1. Cliquez de nouveau sur le nœud du service d’application avec le bouton droit et sélectionnez **Parcourir le site Web**.

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Résolution des problèmes de connexion à Azure

Si vous rencontrez l’erreur **« Cannot find subscription with name [subscription ID] »** (L’abonnement avec le nom [ID d’abonnement] est introuvable) quand vous vous connectez à Azure, cela peut être dû au fait que vous vous trouvez derrière un proxy qui vous empêche d’atteindre l’API Azure. Configurez les variables d’environnement `HTTP_PROXY` et `HTTPS_PROXY` avec vos informations de proxy dans votre terminal à l’aide de `export`.

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Si la définition des variables d’environnement ne corrige pas le problème, contactez-nous en sélectionnant le bouton **J’ai rencontré un problème** plus haut.

### <a name="update-the-app"></a>Mettre à jour l’application

Pour déployer les modifications apportées à cette application, vous pouvez effectuer les modifications dans VS Code, enregistrer vos fichiers, puis appliquer la procédure suivie précédemment en choisissant l’application existante au lieu d’en créer une nouvelle.

## <a name="viewing-logs"></a>Consultation des journaux

Vous pouvez voir la sortie de journal (appels à `console.log`) à partir de l’application directement dans la fenêtre de sortie de VS Code.

1. Dans l’explorateur **AZURE APP SERVICE**, cliquez avec le bouton droit sur le nœud de l’application, puis choisissez **Commencer le streaming des journaux**.

    ![Commencer le streaming des journaux](containers/media/quickstart-nodejs/view-logs.png)

1. Lorsque vous y êtes invité, choisissez d’activer la journalisation et de redémarrer l’application. Une fois l’application redémarrée, la fenêtre de résultats de VS Code s’ouvre avec une connexion au flux de journaux. 

    ![Activer la journalisation et redémarrer](containers/media/quickstart-nodejs/enable-restart.png)

1. Après quelques secondes, la fenêtre de sortie affiche un message indiquant que vous êtes connecté au service de streaming de journaux. Vous pouvez générer plus d’activités de sortie en actualisant la page dans le navigateur.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).    
    </pre>

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Étapes suivantes

Félicitations, vous avez terminé ce démarrage rapide !

> [!div class="nextstepaction"]
> [Tutoriel : Application Node.js avec MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Configurer une application Node.js](configure-language-nodejs.md)

Consultez les autres extensions Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Outils Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Outils Azure CLI](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Outils Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Vous pouvez également tous les obtenir en installant le pack d’extension [Pack de nœuds pour Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack).

