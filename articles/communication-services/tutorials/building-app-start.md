---
title: 'Tutoriel : Préparer une application web pour Azure Communication Services (Node.js)'
titleSuffix: An Azure Communication Services tutorial
description: Découvrez comment créer une application web de base qui prend en charge Azure Communication Services
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: a5a23d6a06c8cdff4deabac5251597b7ffe0c833
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728043"
---
# <a name="tutorial-prepare-a-web-app-for-azure-communication-services-nodejs"></a>Tutoriel : Préparer une application web pour Azure Communication Services (Node.js)

Vous pouvez utiliser Azure Communication Services pour ajouter des communications en temps réel à vos applications. Dans ce tutoriel, vous allez apprendre à configurer une application web qui prend en charge Azure Communication Services. C’est un tutoriel d’introduction qui aidera les développeurs débutants à bien démarrer avec les communications en temps réel.

À la fin de ce tutoriel, vous disposerez d’une application web de référence qui est configurée avec les SDK Azure Communication Services. Vous pourrez alors utiliser cette application pour commencer à créer votre solution de communications en temps réel.

N’hésitez pas à consulter la [page Azure Communication Services sur GitHub](https://github.com/Azure/communication) pour nous faire part de vos commentaires.

Dans ce tutoriel, vous allez apprendre à :
> [!div class="checklist"]
> * Configurer votre environnement de développement
> * Configurer un serveur web local
> * Ajouter les packages Azure Communication Services à votre site web
> * Publier votre site web sur des sites web Azure statiques

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. Pour plus d’informations, consultez [Créer un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) Le compte gratuit vous octroie 200 $ de crédits Azure pour essayer la combinaison de services de votre choix.
- [Visual Studio Code](https://code.visualstudio.com/) pour la modification du code dans votre environnement de développement local.
- [webpack](https://webpack.js.org/) pour créer un bundle de votre code et l’héberger localement.
- [Node.js](https://nodejs.org/en/) pour installer et gérer les dépendances, telles que les SDK Azure Communication Services et le webpack.
- [nvm et npm](/windows/nodejs/setup-on-windows) pour la gestion de versions.
- L’[extension Stockage Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) pour Visual Studio Code. Vous avez besoin de cette extension pour publier votre application dans Stockage Azure. [Découvrez-en plus sur l’hébergement de sites web statiques dans le service Stockage Azure](../../storage/blobs/storage-blob-static-website.md).
- L’[extension Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Cette extension vous permet de déployer des sites web avec l’option de configurer l’intégration continue et la livraison continue (CI/CD) entièrement managées.
- L’[extension Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions), qui vous permet de créer vos propres applications serverless. Par exemple, vous pouvez héberger votre application d’authentification dans Azure Functions.
- Une ressource Communication Services active et la chaîne de connexion. [Découvrez comment créer une ressource Communication Services](../quickstarts/create-communication-resource.md).
- Un jeton d’accès utilisateur. Pour obtenir des instructions, consultez le [guide de démarrage rapide sur la création et la gestion de jetons d’accès](../quickstarts/access-tokens.md?pivots=programming-language-javascript) ou le [tutoriel sur la création d’un service d’authentification approuvé](./trusted-service-tutorial.md).


## <a name="configure-your-development-environment"></a>Configurer votre environnement de développement

Votre environnement de développement local sera configuré de la façon suivante :

:::image type="content" source="./media/step-one-pic-one.png" alt-text="Diagramme illustrant l’architecture de l’environnement de développement":::


### <a name="install-nodejs-nvm-and-npm"></a>Installer Node.js, nvm et npm

Nous utiliserons Node.js pour télécharger et installer les diverses dépendances dont nous avons besoin pour notre application côté client. Nous l’utiliserons pour générer les fichiers statiques qui seront ensuite hébergés dans Azure. Vous n’avez donc pas à vous soucier de sa configuration sur votre serveur.

Les développeurs Windows peuvent suivre [ce tutoriel Node.js](/windows/nodejs/setup-on-windows) pour configurer Node, nvm et npm. 

Ce tutoriel est basé sur la version LTS 12.20.0. Après avoir installé nvm, exécutez la commande PowerShell suivante pour déployer la version que vous souhaitez utiliser :

```PowerShell
nvm list available
nvm install 12.20.0
nvm use 12.20.0
```

:::image type="content" source="./media/step-one-pic-two.png" alt-text="Capture d’écran montrant les commandes de déploiement d’une version de Node":::

### <a name="configure-visual-studio-code"></a>Configurer Visual Studio Code

Vous pouvez télécharger [Visual Studio Code](https://code.visualstudio.com/) sur l’une des [plateformes prises en charge](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

### <a name="create-a-workspace-for-your-azure-communication-services-projects"></a>Créer un espace de travail pour vos projets Azure Communication Services

Créez un dossier pour y stocker vos fichiers projet, comme celui-ci : `C:\Users\Documents\ACS\CallingApp`. Dans Visual Studio Code, sélectionnez **Fichier** > **Ajouter un dossier à l’espace de travail**, puis ajoutez le dossier à votre espace de travail.

:::image type="content" source="./media/step-one-pic-three.png" alt-text="Capture d’écran montrant les sélections permettant d’ajouter un fichier à un espace de travail":::

Accédez à **EXPLORATEUR** dans le volet gauche. Vous voyez votre dossier `CallingApp` dans l’espace de travail **SANS TITRE**.

:::image type="content" source="./media/step-one-pic-four.png" alt-text="Capture d’écran montrant l’Explorateur et l’espace de travail sans titre":::

Changez le nom de votre espace de travail si vous le souhaitez. Vous pouvez vérifier votre version de Node.js en cliquant avec le bouton droit sur le dossier `CallingApp` et en sélectionnant **Ouvrir dans le terminal intégré**.

:::image type="content" source="./media/step-one-pic-five.png" alt-text="Capture d’écran qui montre la sélection de l’ouverture d’un dossier dans un terminal intégré":::

Dans le terminal, entrez la commande suivante pour vérifier la version de Node.js installée à l’étape précédente :

```JavaScript
node --version
```

:::image type="content" source="./media/step-one-pic-six.png" alt-text="Capture d’écran montrant la validation de la version de Node.":::

### <a name="install-azure-extensions-for-visual-studio-code"></a>Installer les extensions Azure pour Visual Studio Code

Installez l’[extension Stockage Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) par le biais de la Place de marché Visual Studio ou de Visual Studio Code (**Affichage**  > **Extensions** > **Stockage Azure**).

:::image type="content" source="./media/step-one-pic-seven.png" alt-text="Capture d’écran montrant le bouton permettant d’installer l’extension Stockage Azure":::

Suivez les mêmes étapes pour les extensions [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) et [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice).


## <a name="set-up-a-local-web-server"></a>Configurer un serveur web local

### <a name="create-an-npm-package"></a>Créer un package npm

Dans votre terminal, à partir du chemin de votre dossier d’espace de travail, entrez cette commande :

``` console
npm init -y
```

Cette commande initialise un nouveau package npm et ajoute `package.json` dans le dossier racine de votre projet.

:::image type="content" source="./media/step-one-pic-eight.png" alt-text="Capture d’écran montrant le package au format JSON":::

Pour plus d’informations sur la commande `npm init`, consultez la [page correspondante de la documentation de npm](https://docs.npmjs.com/cli/v6/commands/npm-init).

### <a name="install-webpack"></a>Installer webpack

Vous pouvez utiliser [webpack](https://webpack.js.org/) pour créer un bundle de code dans des fichiers statiques que vous pouvez ensuite déployer sur Azure. Il a également un serveur de développement, que vous allez configurer pour l’exemple Calling.

Dans votre terminal, entrez la commande suivante pour installer webpack :

``` Console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Ce tutoriel a été testé avec les versions spécifiées dans la commande précédente. Le paramètre `-dev` indique au gestionnaire de package que cette dépendance est utilisée à des fins de développement et qu’elle ne doit pas être incluse dans le code que vous déployez sur Azure.

Vous voyez deux nouveaux packages ajoutés à votre fichier `package.json` en tant que `devDependencies`. Les packages sont installés dans le répertoire `./CallingApp/node_modules/`.

:::image type="content" source="./media/step-one-pic-ten.png" alt-text="Capture d’écran montrant la configuration de webpack":::

### <a name="configure-the-development-server"></a>Configurer le serveur de développement

L’exécution d’une application statique (comme votre fichier `index.html`) à partir de votre navigateur nécessite le protocole `file://`. Pour que les modules npm fonctionnent correctement, vous avez besoin du protocole HTTP en utilisant webpack en tant que serveur de développement local.

Vous créez deux configurations : l’une pour le développement et l’autre pour la production. Les fichiers préparés pour la production sont minifiés, ce qui signifie que vous supprimez les caractères et espaces blancs inutiles. Cette configuration vaut pour les scénarios de production où la latence doit être réduite ou bien où le code doit être masqué.

Vous utilisez l’outil `webpack-merge` afin de travailler avec [différents fichiers de configuration pour webpack](https://webpack.js.org/guides/production/).

Commençons par l’environnement de développement. Vous devez d’abord installer `webpack merge`. Dans votre terminal, exécutez la commande suivante :

```Console
npm install --save-dev webpack-merge
```

Dans votre fichier `package.json`, vous pouvez voir une dépendance supplémentaire qui est ajoutée à `devDependencies`.

Ensuite, créez un fichier appelé `webpack.common.js` et ajoutez le code suivant :

```JavaScript
const path = require('path');
module.exports ={
    entry: './app.js',
    output: {
        filename:'app.js',
        path: path.resolve(__dirname, 'dist'),
    }
}
```

Ajoutez ensuite deux fichiers supplémentaires, un pour chaque configuration :

* `webpack.dev.js`
* `webpack.prod.js`

À présent, modifiez le fichier `webpack.dev.js` en y ajoutant le code suivant :

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
    mode: 'development',
    devtool: 'inline-source-map',
});
```
Dans cette configuration, vous importez les paramètres common du fichier `webpack.common.js`, vous fusionnez les deux fichiers, vous définissez le mode sur `development` et vous configurez le mappage de source sur `inline-source-map`.

Le mode development indique à webpack de ne pas minifier les fichiers et de ne pas générer de fichiers de production optimisés. Vous trouverez une documentation détaillée sur les modes webpack dans la [page web des modes webpack](https://webpack.js.org/configuration/mode/).

Les options de mappage de source sont listées dans la [page web Devtool de webpack](https://webpack.js.org/configuration/devtool/#root). La définition du mappage de source facilite le débogage dans votre navigateur.

:::image type="content" source="./media/step-one-pic-11.png" alt-text="Capture d’écran montrant le code de configuration de webpack":::

Pour exécuter le serveur de développement, accédez à `package.json` et ajoutez le code suivant sous `scripts` :

```JavaScript
    "build:dev": "webpack-dev-server --config webpack.dev.js"
```

Votre fichier doit maintenant ressembler à ceci :

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3"
  }
}
```

Vous avez ajouté la commande qui peut être utilisée à partir de npm.

:::image type="content" source="./media/step-one-pic-12.png" alt-text="Capture d’écran montrant la modification de package.js":::

### <a name="test-the-development-server"></a>Tester le serveur de développement

 Dans Visual Studio Code, créez trois fichiers sous votre projet :

* `index.html`
* `app.js`
* `app.css` (facultatif, pour appliquer un style à votre application)

Collez ce code dans `index.html` :

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My first ACS application</title>
    <link rel="stylesheet" href="./app.css"/>
    <script src="./app.js" defer></script>
</head>
<body>
    <h1>Hello from ACS!</h1>
</body>
</html>
```
:::image type="content" source="./media/step-one-pic-13.png" alt-text="Capture d’écran montrant le fichier HTML":::

Ajoutez le code suivant à `app.js` :

```JavaScript
alert('Hello world alert!');
console.log('Hello world console!');
```
Ajoutez le code suivant à `app.css` :

```CSS
html {
    font-family: sans-serif;
  }
```
N’oubliez pas d’enregistrer ! Le fichier non enregistré est indiqué par des points blancs en regard des noms de fichiers dans l’Explorateur.

:::image type="content" source="./media/step-one-pic-14.png" alt-text="Capture d’écran qui montre le fichier App.js avec du code JavaScript":::

Quand vous ouvrez cette page, vous devez voir votre message s’afficher avec une alerte dans la console de votre navigateur.

:::image type="content" source="./media/step-one-pic-15.png" alt-text="Capture d’écran qui montre le fichier app.css":::

Utilisez la commande de terminal suivante pour tester votre configuration de développement :

```Console
npm run build:dev
```

La console indique l’emplacement d’exécution du serveur. Par défaut, il s’agit de `http://localhost:8080`. La commande `build:dev` est la commande que vous avez ajoutée au fichier `package.json`.

:::image type="content" source="./media/step-one-pic-16.png" alt-text="Capture d’écran montrant le démarrage d’un serveur de développement":::
 
Accédez à l’adresse dans votre navigateur. Vous voyez normalement la page et l’alerte qui ont été configurées lors des étapes précédentes.
 
:::image type="content" source="./media/step-one-pic-17.png" alt-text="Capture d’écran de la page HTML":::
  
 
Durant l’exécution du serveur, vous pouvez changer le code et le serveur. La page HTML se recharge automatiquement. 

Ensuite, accédez au fichier `app.js` dans Visual Studio Code et supprimez `alert('Hello world alert!');`. Enregistrez votre fichier et vérifiez que l’alerte ne s’affiche plus dans votre navigateur.

Pour arrêter le serveur, vous pouvez exécuter `Ctrl+C` dans votre terminal. Pour démarrer le serveur, entrez `npm run build:dev` quand vous êtes prêt.

## <a name="add-the-azure-communication-services-packages"></a>Ajouter les packages Azure Communication Services

Utilisez la commande `npm install` pour installer le SDK Azure Communication Services Calling pour JavaScript.

```Console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Cette action ajoute les packages common et calling d’Azure Communication Services en tant que dépendances de votre package. Vous voyez deux nouveaux packages ajoutés au fichier `package.json`. Vous trouverez plus d’informations sur `npm install` dans la [page correspondante de la documentation de npm](https://docs.npmjs.com/cli/v6/commands/npm-install).

:::image type="content" source="./media/step-one-pic-nine.png" alt-text="Capture d’écran montrant le code d’installation des packages Azure Communication Services":::

Ces packages sont fournis par l’équipe Azure Communication Services ; ils incluent les bibliothèques d’authentification (authentication) et d’appel (calling). La commande `--save` signale que l’application dépend de ces packages pour une utilisation en production et elle sera incluse dans `devDependencies` au sein du fichier `package.json`. Au moment de la génération de l’application en production, les packages seront inclus dans votre code de production.


## <a name="publish-your-website-to-azure-static-websites"></a>Publier votre site web sur des sites web Azure statiques

### <a name="create-a-configuration-for-production-deployment"></a>Créer une configuration pour un déploiement de production

Ajoutez le code suivant à `webpack.prod.js` :

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
  mode: 'production',
});
```

Cette configuration sera fusionnée avec `webpack.common.js` (où vous avez spécifié le fichier d’entrée et où les résultats seront stockés). La configuration définira également le mode sur `production`.
 
Dans `package.json`, ajoutez le code suivant :

```JavaScript
"build:prod": "webpack --config webpack.prod.js"
```

Ce fichier doit se présenter comme suit :

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js",
    "build:prod": "webpack --config webpack.prod.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@azure/communication-calling": "^1.0.0-beta.6",
    "@azure/communication-common": "^1.0.0"
  },
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3",
    "webpack-merge": "^5.7.3"
  }
}
```

:::image type="content" source="./media/step-one-pic-20.png" alt-text="Capture d’écran montrant les fichiers configurés":::


Dans le terminal, exécutez :

```Console
npm run build:prod
```

La commande crée un dossier `dist` ainsi qu’un fichier statique `app.js` prêt pour la production. 

:::image type="content" source="./media/step-one-pic-21.png" alt-text="Capture d’écran qui montre la génération pour la production":::
 
 
### <a name="deploy-your-app-to-azure-storage"></a>Déployer votre application sur Stockage Azure

Copiez `index.html` et `app.css` dans le dossier `dist`.

Dans le dossier `dist`, créez un fichier et nommez-le `404.html`. Copiez le code ci-dessous dans ce nouveau fichier :

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="./app.css"/>
    <title>Document</title>
</head>
<body>
    <h1>The page does not exists.</h1>
</body>
</html>
```

Enregistrez le fichier (Ctrl+S).

Cliquez avec le bouton droit sur le dossier `dist` et sélectionnez **Deploy to Static Website via Azure Storage** (Déployer sur un site web statique via Stockage Azure).

:::image type="content" source="./media/step-one-pic-22.png" alt-text="Capture d’écran des sélections permettant de commencer le déploiement sur Azure":::
 
Sous **Select subscription** (Sélectionner l’abonnement), sélectionnez **Sign in to Azure** (Se connecter à Azure), ou bien **Create a Free Azure Account** (Créer un compte Azure gratuit) si vous n’avez pas encore créé d’abonnement.
 
:::image type="content" source="./media/step-one-pic-23.png" alt-text="Capture d’écran montrant les sélections pour la connexion à Azure":::
 
Sélectionnez **Create new Storage Account** > **Advanced** (Créer un compte de stockage > Avancé).

:::image type="content" source="./media/step-one-pic-24.png" alt-text="Capture d’écran montrant les sélections permettant de créer le groupe de comptes de stockage":::
 
Indiquez le nom du groupe de stockage.
 
:::image type="content" source="./media/step-one-pic-25.png" alt-text="Capture d’écran montrant l’ajout d’un nom pour le compte":::
 
Créez un groupe de ressources si nécessaire.
 
:::image type="content" source="./media/step-one-pic-26.png" alt-text="Capture d’écran qui montre la sélection pour la création d’un groupe de ressources":::
  
Pour la question **Would you like to enable static website hosting?** (Voulez-vous activer l’hébergement de sites web statiques ?), sélectionnez **Yes** (Oui).

:::image type="content" source="./media/step-one-pic-27.png" alt-text="Capture d’écran qui montre la sélection de l’option permettant d’activer l’hébergement de sites web statiques":::
  
Pour **Enter the index document name** (Entrer le nom du document d’index), acceptez le nom de fichier par défaut. Vous avez déjà créé le fichier `index.html`.

Pour **Enter the 404 error document path** (Entrer le chemin du document d’erreur 404), entrez **404.html**.  
  
Sélectionnez l’emplacement de l’application. Cet emplacement définit le processeur multimédia qui sera utilisé dans votre prochaine application d’appel (calling) dans les appels de groupe. 

Azure Communication Services sélectionne le processeur multimédia en fonction de l’emplacement de l’application.

:::image type="content" source="./media/step-one-pic-28.png" alt-text="Capture d’écran montrant une liste d’emplacements":::
  
Attendez la fin de la création de la ressource et de votre site web. 
 
Sélectionnez **Browse to website** (Accéder au site web).

:::image type="content" source="./media/step-one-pic-29.png" alt-text="Capture d’écran montrant un message indiquant que le déploiement est terminé, avec le bouton permettant d’accéder à un site web":::
 
À partir des outils de développement de votre navigateur, vous pouvez inspecter la source et examiner le fichier qui est préparé pour la production.
 
:::image type="content" source="./media/step-one-pic-30.png" alt-text="Capture d’écran de la source du site web avec le fichier":::

Accédez au [portail Azure](https://portal.azure.com/#home), sélectionnez votre groupe de ressources, puis sélectionnez l’application que vous avez créée. Ensuite, sélectionnez **Paramètres** > **Site web statique**. Vous pouvez voir que les sites web statiques sont activés. Notez le point de terminaison principal, le nom du document d’index et le chemin du document d’erreur.

:::image type="content" source="./media/step-one-pic-31.png" alt-text="Capture d’écran montrant la sélection d’un site web statique":::

Sous **Service Blob**, sélectionnez **Conteneurs**. La liste affiche deux conteneurs créés, l’un pour les journaux (`$logs`) et l’autre pour le contenu de votre site web (`$web`).

:::image type="content" source="./media/step-one-pic-32.png" alt-text="Capture d’écran montrant la configuration des conteneurs":::

Si vous ouvrez le conteneur `$web`, vous voyez les fichiers que vous avez créés dans Visual Studio et déployés sur Azure. 

:::image type="content" source="./media/step-one-pic-33.png" alt-text="Capture d’écran montrant les fichiers déployés sur Azure":::

Vous pouvez redéployer l’application à partir de Visual Studio Code à tout moment.

Vous êtes maintenant prêt à générer votre première application web Azure Communication Services.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter l’appel vocal à votre application](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Vous pouvez également :

- [Ajouter un système de conversation à votre application](../quickstarts/chat/get-started.md)
- [Créer des jetons d’accès utilisateur](../quickstarts/access-tokens.md)
- [En savoir plus sur l’architecture client et serveur](../concepts/client-and-server-architecture.md)
- [En savoir plus sur l’authentification](../concepts/authentication.md)
