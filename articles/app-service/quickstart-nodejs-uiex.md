---
title: 'Démarrage rapide : Créer une application web Node.js'
description: Déployez votre premier programme Hello World Node.js dans Azure App Service en quelques minutes.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7cd9add699d9bd4a3eff9cdcf1e65af0d754b70a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101747386"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Créer une application web Node.js dans Azure

Prise en main de <abbr title="Service HTTP pour l’hébergement d’applications web, d’API REST et d’applications back-end mobiles.">Azure App Service</abbr> en créant une application Node.js/Express localement avec Visual Studio Code et en la déployant sur le cloud Azure. Comme vous utilisez un <abbr title="Dans Azure App Service, un niveau de base dans lequel votre application s’exécute sur les mêmes machines virtuelles que d’autres applications, y compris les applications d’autres clients. Ce niveau est destiné seulement au développement et aux tests.">niveau gratuit</abbr>, vous pouvez suivre ce guide de démarrage rapide sans engendrer de coûts.

## <a name="1-prepare-your-environment"></a>1. Préparation de votre environnement

- Un compte Azure avec un <abbr title="Un abonnement Azure est un conteneur logique utilisé pour provisionner des ressources dans Azure. Il contient les détails de toutes les ressources, parmi lesquelles les machines virtuelles, les bases de données, etc.">abonnement actif</abbr>. [Créez un compte gratuitement](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- <a href="https://git-scm.com/" target="_blank">Installez Git</a>
- [Node.js et npm](https://nodejs.org). Exécutez la commande `node --version` pour vérifier que Node.js est installé.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Extension Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) pour Visual Studio Code.

[Signaler un problème](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)




<br>
<hr/>

## <a name="2-clone-and-run-a-local-nodejs-application"></a>2. Cloner et exécuter une application Node.js locale

1. Sur votre ordinateur local, ouvrez un terminal et clonez l’exemple de référentiel :

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Accédez au nouveau dossier de l’application :

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Installez les dépendances :

    ```bash
    npm install
    ```

1. Démarrez l’application pour la tester localement :

    ```bash
    npm start
    ```
    
1. Ouvrez votre navigateur et accédez à `http://localhost:1337`. Le navigateur doit afficher le message « Hello World! ».

1. Appuyez sur <kbd>Ctrl</kbd> + <kbd>C</kbd> dans le terminal pour arrêter le serveur.

[Signaler un problème](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br>
<hr/>




<!-- VS Code extension works differently for Windows/Linus - Step 3 -->

::: zone pivot="platform-windows"  

[!INCLUDE [Windows](./includes/quickstart-nodejs-uiex-windows.md)]


::: zone-end

::: zone pivot="platform-linux"  

[!INCLUDE [Windows](./includes/quickstart-nodejs-uiex-linux.md)]

::: zone-end


## <a name="4-viewing-logs-from-visual-studio-code"></a>4. Visualisation des journaux de Visual Studio Code

Visualisez le <abbr title="Tous les appels à `console.log` dans l’application sont affichés dans la fenêtre de résultats de Visual Studio Code.">journal</abbr> de l’application App Service en cours d’exécution.

1. Recherchez l’application dans l’explorateur **AZURE APP SERVICE**, cliquez avec le bouton droit sur le nom de l’application, puis choisissez **Démarrer le streaming des journaux**.

1. La fenêtre de sortie de Visual Studio Code s’ouvre.

    ![Afficher les journaux d’activité de diffusion en continu](./media/quickstart-nodejs/view-logs.png)

    :::image type="content" source="./media/quickstart-nodejs/enable-restart.png" alt-text="Capture d’écran de l’invite de VS Code pour permettre la journalisation des fichiers et le redémarrage de l’application web avec le bouton Oui sélectionné.":::

1. Après quelques secondes, vous verrez s’afficher un message indiquant que vous êtes connecté au service de diffusion en continu de journaux. 
1. Actualisez la page plusieurs fois pour voir davantage d’activité.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    2020-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
    2020-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
    2020-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
    2020-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
    </pre>

<br>

[Signaler un problème](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)

<br>
<hr/>

## <a name="5-clean-up-resources"></a>5. Nettoyer les ressources

Recherchez l’application dans l’explorateur **AZURE APP SERVICE**, cliquez avec le bouton droit sur le nom de l’application, puis choisissez **Supprimer**. 

:::image type="content" source="./media/quickstart-nodejs/delete-resource-ieux.png" alt-text="Recherchez l’application dans l’explorateur **AZURE APP SERVICE**, cliquez avec le bouton droit sur le nom de l’application, puis choisissez « Supprimer »":::

## <a name="next-steps"></a>Étapes suivantes

Félicitations, vous avez terminé ce démarrage rapide ! Vous pouvez déployer les modifications apportées à cette application en utilisant le même procédure et en choisissant l’application existante au lieu d’en créer une nouvelle.

Consultez ensuite les autres extensions Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Outils Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Outils Azure CLI](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Outils Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Vous pouvez également tous les obtenir en installant le pack d’extension [Pack de nœuds pour Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack).