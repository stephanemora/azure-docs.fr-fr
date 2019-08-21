---
title: Créer une application web Node.js
description: Déploiement Node.js vers Azure App Services
author: KarlErickson
ms.author: karler
ms.date: 07/18/2019
ms.topic: quickstart
ms.service: app-service
ms.devlang: javascript
ms.openlocfilehash: ced2977509f16f8dab2abe5546e19b7e05fb2a3d
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68975830"
---
# <a name="create-a-nodejs-app-in-azure"></a>Créer une application Node.js dans Azure

Azure App Service offre un service d’hébergement web capable d’auto-correction et hautement scalable. Ce guide de démarrage rapide montre comment déployer une application Node.js sur Azure App Service.

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas de compte Azure, [inscrivez-vous dès maintenant](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) pour obtenir un compte gratuit avec 200 $ de crédits Azure, ce qui vous permettra d’essayer toutes les combinaisons de services.

Vous devez avoir installé [Visual Studio Code](https://code.visualstudio.com/) ainsi que [Node.js et npm](https://nodejs.org/en/download), le gestionnaire de package Node.js.

Vous devez également installer l’[extension Azure App Service](vscode:extension/ms-azuretools.vscode-azureappservice), que vous pouvez utiliser pour créer, gérer et déployer des Web Apps Linux sur la plateforme PaaS (Platform as a Service) Azure.

### <a name="sign-in"></a>Se connecter

Une fois l’extension installée, connectez-vous à votre compte Azure. Dans la barre d’activité, cliquez sur le logo Azure pour afficher l’Explorateur **AZURE APP SERVICE**. Cliquez sur **Se connecter à Azure...**  et suivez les instructions.

![se connecter à Azure](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Résolution de problèmes

Si vous rencontrez l’erreur **« Cannot find subscription with name [subscription ID] »** (L’abonnement avec le nom [ID d’abonnement] est introuvable), cela peut être dû au fait que vous vous trouvez derrière un proxy qui vous empêche d’atteindre l’API Azure. Configurez les variables d’environnement `HTTP_PROXY` et `HTTPS_PROXY` avec vos informations de proxy dans votre terminal à l’aide de `export`.

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Si la définition des variables d’environnement ne corrige pas le problème, contactez-nous en cliquant sur le bouton **J’ai rencontré un problème** ci-dessous.

### <a name="prerequisite-check"></a>Vérification du prérequis

Avant de continuer, assurez-vous que tous les composants requis ont bien été installés et configurés.

Dans VS Code, vous devriez voir votre adresse e-mail Azure dans la barre d’État et votre abonnement dans l’Explorateur **AZURE APP SERVICE**.

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Créer votre application Node.js

Créez ensuite une application Node.js pouvant être déployée dans le Cloud. Ce démarrage rapide utilise un générateur d’applications pour déployer rapidement l’application depuis un terminal.

> [!TIP]
> Si vous avez déjà terminé le [didacticiel Node.js](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial), vous pouvez passer directement à la section [Déployer le site web](#deploy-the-website).

### <a name="install-the-express-generator"></a>Installer le générateur Express

[Express](https://www.expressjs.com) est un framework couramment utilisé pour la création et l’exécution des applications Node.js. Vous pouvez créer/définir la structure d’une nouvelle application Express à l'aide de l’outil [Générateur Express](https://expressjs.com/en/starter/generator.html). Le générateur Express est fourni en tant que module npm et installé à l’aide de l’outil de ligne de commande npm `npm`.

```bash
npm install -g express-generator
```

Le commutateur `-g` installe le générateur Express sur votre ordinateur de manière globale, ce qui vous permet de l’exécuter depuis n’importe quel emplacement.

### <a name="scaffold-a-new-application"></a>Définir la structure d’une nouvelle application

Ensuite, définissez la structure d’une nouvelle application Express appelée `myExpressApp` en exécutant :

```bash
express myExpressApp --view pug --git
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

À présent, ouvrez votre navigateur et accédez à [http://localhost:3000](http://localhost:3000), où vous devriez voir s’afficher :

![Exécution de l’application Express](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-website"></a>Déployer le site web

Dans cette section, vous déployez votre site Web Node.js à l’aide de VS Code et de l’extension Azure App Service. Ce démarrage rapide utilise le modèle de déploiement le plus basique dans lequel votre application est compressée et déployée sur une application Web Azure sur Linux.

### <a name="deploy-using-azure-app-service"></a>Déployer à l’aide d’Azure App Service

Tout d’abord, ouvrez le dossier de votre application dans VS Code.

```bash
code .
```

Dans l'explorateur **AZURE APP SERVICE**, cliquez sur l’icône représentant une flèche bleue pointant vers le haut pour déployer votre application sur Azure.

![Déployer sur l'application web](./media/quickstart-nodejs/deploy.png)

> [!TIP]
> Vous pouvez également effectuer le déploiement à partir de la **palette de commandes** (Ctrl + Maj + P) en tapant « déployer sur l'application web » et en exécutant l’**Azure App Service : Commande Déployer sur l'application web**.

1. Choisissez le répertoire actuellement ouvert, `myExpressApp`.

2. Sélectionnez **Créer une application Web**.

3. Saisissez un nom unique pour votre application Web et appuyez sur Entrée. Les caractères valides dans un nom d’application sont les suivants : « a-z », « 0-9 » et « - ».

4. Choisissez votre **version Node.js** (LTS est recommandé).

    Le canal de notification affiche les ressources Azure qui sont créées pour votre application.

Cliquez sur **Oui** lorsque vous êtes invité à mettre à jour votre configuration pour exécuter `npm install` sur le serveur cible. Votre application est alors déployée.

![Déploiement configuré](./media/quickstart-nodejs/server-build.png)

Lorsque le déploiement démarre, vous êtes invité à mettre à jour votre espace de travail afin que les déploiements ultérieurs ciblent automatiquement la même application web App Service. Choisissez **Oui** pour vous assurer que vos modifications sont déployées sur la bonne application.

![Déploiement configuré](./media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> Assurez-vous que votre application utilise le port d’écoute fourni par la variable d’environnement PORT `process.env.PORT`.

### <a name="browse-the-website"></a>Parcourir le site Web

Une fois le déploiement terminé, cliquez sur **Parcourir le site Web** dans l’invite pour afficher le site Web que vous venez de déployer.

### <a name="troubleshooting"></a>Résolution de problèmes

Si vous voyez s’afficher le message d’erreur **« Vous n’êtes pas autorisé à afficher ce répertoire ou cette page. »** , c’est que l’application n’a probablement pas réussi à démarrer correctement. Accédez à la section suivante et affichez la sortie du journal pour rechercher et corriger l’erreur. Si vous n’êtes pas en mesure de la corriger, contactez-nous en cliquant sur le bouton **J’ai rencontré un problème** ci-dessous. Nous sommes là pour vous aider !

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="updating-the-website"></a>Mise à jour du site web

Vous pouvez déployer les modifications apportées à cette application en utilisant le même procédure et en choisissant l’application existante au lieu d’en créer une nouvelle.

## <a name="viewing-logs"></a>Consultation des journaux

Dans cette section, vous allez apprendre à visionner les journaux à partir du site Web en cours d’exécution. Tous les appels à `console.log` dans le site sont affichés dans la fenêtre de résultats de Visual Studio Code.

Recherchez l’application dans l'explorateur **AZURE APP SERVICE**, cliquez avec le bouton droit de la souris sur l’application, puis choisissez **Afficher les journaux d’activité de diffusion en continu**.

Lorsque vous y êtes invité, choisissez d’activer la journalisation et de redémarrer l’application. Une fois l’application redémarrée, la fenêtre de résultats de VS Code s’ouvre avec une connexion au flux de journaux.

![Afficher les journaux d’activité de diffusion en continu](./media/quickstart-nodejs/view-logs.png)

![Activer la journalisation et redémarrer](./media/quickstart-nodejs/enable-restart.png)

Après quelques secondes, vous verrez s’afficher un message indiquant que vous êtes connecté au service de diffusion en continu de journaux.

```bash
Connecting to log-streaming service...
2017-12-21 17:33:51.428 INFO  - Container practical-mustache_2 for site practical-mustache initialized successfully.
2017-12-21 17:33:56.500 INFO  - Container logs
```

Actualisez la page plusieurs fois dans le navigateur pour afficher la sortie du journal.

```bash
2017-12-21 17:35:17.774 INFO  - Container logs
2017-12-21T17:35:14.955412230Z GET / 304 141.798 ms - -
2017-12-21T17:35:15.248930479Z GET /stylesheets/style.css 304 3.180 ms - -
2017-12-21T17:35:15.378623115Z GET /favicon.ico 404 53.839 ms - 995
```

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
