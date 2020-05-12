---
title: 'Démarrage rapide : Créer une application Linux Node.js'
description: Découvrez comment bien démarrer avec les applications Linux sur Azure App Service en déployant votre première application Node.js sur un conteneur Linux dans App Service.
author: msangapu-msft
ms.author: msangapu
ms.date: 08/12/2019
ms.topic: quickstart
ms.devlang: javascript
ms.openlocfilehash: 52466bac083f78002a8208ba52ca7d1b951c4064
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801482"
---
# <a name="create-a-nodejs-app-in-azure"></a>Créer une application Node.js dans Azure

Azure App Service offre un service d’hébergement web capable d’auto-correction et hautement scalable. Ce guide de démarrage rapide montre comment déployer une application Node.js sur Azure App Service.

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas de compte Azure, [inscrivez-vous dès maintenant](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) pour obtenir un compte gratuit avec 200 $ de crédits Azure, ce qui vous permettra d’essayer toutes les combinaisons de services.

Vous devez avoir installé [Visual Studio Code](https://code.visualstudio.com/) ainsi que [Node.js et npm](https://nodejs.org/en/download), le gestionnaire de package Node.js.

Vous devez également installer l’[extension Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice), que vous pouvez utiliser pour créer, gérer et déployer des Web Apps Linux sur la plateforme PaaS (Platform as a Service) Azure.

### <a name="sign-in"></a>Se connecter

Une fois l’extension installée, connectez-vous à votre compte Azure. Dans la barre d’activité, sélectionnez le logo Azure pour afficher l’Explorateur **AZURE APP SERVICE**. Sélectionnez **Se connecter à Azure...** et suivez les instructions.

![se connecter à Azure](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Dépannage

Si vous rencontrez l’erreur **« Cannot find subscription with name [subscription ID] »** (L’abonnement avec le nom [ID d’abonnement] est introuvable), cela peut être dû au fait que vous vous trouvez derrière un proxy qui vous empêche d’atteindre l’API Azure. Configurez les variables d’environnement `HTTP_PROXY` et `HTTPS_PROXY` avec vos informations de proxy dans votre terminal à l’aide de `export`.

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Si la définition des variables d’environnement ne corrige pas le problème, contactez-nous en sélectionnant le bouton **J’ai rencontré un problème** ci-dessous.

### <a name="prerequisite-check"></a>Vérification du prérequis

Avant de continuer, assurez-vous que tous les composants requis ont bien été installés et configurés.

Dans VS Code, vous devriez voir votre adresse e-mail Azure dans la barre d’État et votre abonnement dans l’Explorateur **AZURE APP SERVICE**.

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Créer votre application Node.js

Créez ensuite une application Node.js pouvant être déployée dans le Cloud. Ce démarrage rapide utilise un générateur d’applications pour déployer rapidement l’application depuis un terminal.

> [!TIP]
> Si vous avez déjà terminé le [tutoriel Node.js](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial), vous pouvez passer directement à la section [Déployer sur Azure](#deploy-to-azure).

### <a name="scaffold-a-new-application-with-the-express-generator"></a>Structurer une nouvelle application avec le générateur Express

[Express](https://www.expressjs.com) est un framework couramment utilisé pour la création et l’exécution des applications Node.js. Vous pouvez créer/définir la structure d’une nouvelle application Express à l'aide de l’outil [Générateur Express](https://expressjs.com/en/starter/generator.html). Le générateur Express est fourni en tant que module npm et peut être exécuté directement (sans installation) à l’aide de l’outil en ligne de commande npm `npx`.

```bash
npx express-generator myExpressApp --view pug --git
```

Les paramètres `--view pug --git` indiquent au générateur qu’il doit utiliser le moteur de modèle [pug](https://pugjs.org/api/getting-started.html) (auparavant appelé `jade`) et créer un fichier `.gitignore`.

Pour installer toutes les dépendances de l’application, accédez au nouveau dossier et exécutez `npm install`.

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Exécution de l'application

Assurez-vous ensuite que l’application s’exécute. À partir du terminal, démarrez l’application à l’aide de la commande `npm start` pour démarrer le serveur.

```bash
npm start
```

À présent, ouvrez votre navigateur et accédez à `http://localhost:3000`. Voici ce que vous devriez obtenir :

![Exécution de l’application Express](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>Déployer dans Azure

Dans cette section, vous déployez votre application Node.js à l’aide de VS Code et de l’extension Azure App Service. Ce démarrage rapide utilise le modèle de déploiement le plus basique dans lequel votre application est compressée et déployée sur une application Web Azure sur Linux.

### <a name="deploy-using-azure-app-service"></a>Déployer à l’aide d’Azure App Service

Tout d’abord, ouvrez le dossier de votre application dans VS Code.

```bash
code .
```

Dans l’explorateur **AZURE APP SERVICE**, sélectionnez l’icône représentant une flèche bleue pointant vers le haut pour déployer votre application sur Azure.

![Déployer sur l'application web](./media/quickstart-nodejs/deploy.png)

> [!TIP]
> Vous pouvez également effectuer le déploiement à partir de la **palette de commandes** (Ctrl + Maj + P) en tapant « déployer sur l'application web » et en exécutant l’**Azure App Service : Commande Déployer sur l'application web**.

1. Choisissez le répertoire actuellement ouvert, `myExpressApp`.

1. Choisissez **Créer une application web** pour déployer sur App Service sur Linux par défaut.

1. Saisissez un nom unique pour votre application Web et appuyez sur Entrée. Les caractères valides dans un nom d’application sont les suivants : « a-z », « 0-9 » et « - ».

1. Choisissez votre **version Node.js** (LTS est recommandé).

    Le canal de notification affiche les ressources Azure qui sont créées pour votre application.

1. Sélectionnez **Oui** lorsque vous êtes invité à mettre à jour votre configuration pour exécuter `npm install` sur le serveur cible. Votre application est alors déployée.

    ![Déploiement configuré](./media/quickstart-nodejs/server-build.png)

1. Lorsque le déploiement démarre, vous êtes invité à mettre à jour votre espace de travail afin que les déploiements ultérieurs ciblent automatiquement la même application web App Service. Choisissez **Oui** pour vous assurer que vos modifications sont déployées sur la bonne application.

    ![Déploiement configuré](./media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> Assurez-vous que votre application utilise le port d’écoute fourni par la variable d’environnement PORT `process.env.PORT`.

### <a name="browse-the-app-in-azure"></a>Accéder à l’application dans Azure

Une fois le déploiement terminé, sélectionnez **Parcourir le site web** dans l’invite pour afficher l’application web que vous venez de déployer.

### <a name="troubleshooting"></a>Dépannage

Si vous voyez s’afficher le message d’erreur **« Vous n’êtes pas autorisé à afficher ce répertoire ou cette page. »** , c’est que l’application n’a probablement pas réussi à démarrer correctement. Accédez à la section suivante et affichez la sortie du journal pour rechercher et corriger l’erreur. Si vous n’êtes pas en mesure de la corriger, contactez-nous en sélectionnant le bouton **J’ai rencontré un problème** ci-dessous. Nous sommes là pour vous aider !

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>Mettre à jour l’application

Vous pouvez déployer les modifications apportées à cette application en utilisant le même procédure et en choisissant l’application existante au lieu d’en créer une nouvelle.

## <a name="viewing-logs"></a>Consultation des journaux

Dans cette section, vous allez apprendre à visionner les journaux à partir de l’application App Service en cours d’exécution. Tous les appels à `console.log` dans l’application sont affichés dans la fenêtre de résultats de Visual Studio Code.

Recherchez l’application dans l'explorateur **AZURE APP SERVICE**, cliquez avec le bouton droit de la souris sur l’application, puis choisissez **Afficher les journaux d’activité de diffusion en continu**.

La fenêtre de résultats de VS Code s’ouvre avec une connexion au flux de journaux.

![Afficher les journaux d’activité de diffusion en continu](./media/quickstart-nodejs/view-logs.png)

![Activer la journalisation et redémarrer](./media/quickstart-nodejs/enable-restart.png)

Après quelques secondes, vous verrez s’afficher un message indiquant que vous êtes connecté au service de diffusion en continu de journaux. Actualisez la page plusieurs fois pour voir davantage d’activité.

<pre>
2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
</pre>

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Étapes suivantes

Félicitations, vous avez terminé ce démarrage rapide !

Consultez ensuite les autres extensions Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Outils Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Outils Azure CLI](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Outils Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Vous pouvez également tous les obtenir en installant le pack d’extension [Pack de nœuds pour Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack).
