---
title: Configurer des applications Node.js
description: Découvrez comment configurer un conteneur Node.js prédéfini pour votre application. Cet article présente les tâches de configuration les plus courantes.
ms.devlang: nodejs
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 6cf60472307a378d2fd4258a9777152344a11ded
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670269"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Configurer une application Node.js Linux pour Azure App Service

Les applications Node.js doivent être déployées avec toutes les dépendances NPM nécessaires. Le moteur de déploiement App Service (Kudu) exécute automatiquement `npm install --production` pour vous quand vous déployez un [référentiel Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) ou un [package zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) avec les processus de génération activés. Si vous déployez vos fichiers à l’aide de [FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), vous devez cependant télécharger les packages requis manuellement.

Ce guide fournit des concepts et des instructions clés pour les développeurs Node.js qui utilisent un conteneur Linux intégré dans App Service. Si vous n’avez jamais utilisé Azure App Service, suivez le [démarrage rapide Node.js](quickstart-nodejs.md) et le [tutoriel Node.js avec MongoDB](tutorial-nodejs-mongodb-app.md) au préalable.

## <a name="show-nodejs-version"></a>Afficher la version de Node.js

Pour afficher la version actuelle de Node.js, exécutez la commande suivante dans [Cloud Shell](https://shell.azure.com) :

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Pour afficher toutes les versions de Node.js prises en charge, exécutez la commande suivante dans [Cloud Shell](https://shell.azure.com) :

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Définir la version de Node.js

Pour définir votre application dans une [version de Node.js prise en charge](#show-nodejs-version), exécutez la commande suivante dans [Cloud Shell](https://shell.azure.com) :

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Ce paramètre spécifie la version de Node.js à utiliser, à la fois lors de l’exécution et pendant la restauration de package automatisée dans Kudu.

> [!NOTE]
> Vous devez définir la version de Node.js dans le fichier `package.json` de votre projet. Le moteur de déploiement s’exécute dans un conteneur distinct qui inclut toutes les versions de Node.js prises en charge.

## <a name="configure-nodejs-server"></a>Configurer un serveur Node.js

Les conteneurs Node.js sont fournis avec [PM2](https://pm2.keymetrics.io/), un gestionnaire de processus de production. Vous pouvez configurer votre application pour qu’elle démarre avec PM2, NPM ou une commande personnalisée.

- [Exécuter une commande personnalisée](#run-custom-command)
- [Exécuter npm start](#run-npm-start)
- [Exécuter avec PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Exécuter une commande personnalisée

App Service peut démarrer votre application à l’aide d’une commande personnalisée, comme un fichier exécutable tel que *run.sh*. Par exemple, pour exécuter `npm run start:prod`, exécutez la commande suivante dans [Cloud Shell](https://shell.azure.com) :

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Exécuter npm start

Pour démarrer votre application en utilisant `npm start`, assurez-vous qu’un script `start` se trouve dans le fichier *package.json*. Par exemple :

```json
{
  ...
  "scripts": {
    "start": "gulp",
    ...
  },
  ...
}
```

Pour utiliser un fichier *package.json* personnalisé dans votre projet, exécutez la commande suivante dans [Cloud Shell](https://shell.azure.com) :

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Exécuter avec PM2

Le conteneur démarre automatiquement votre application avec PM2 lorsqu’un des fichiers Node.js communs se trouve dans votre projet :

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- Un des [fichiers PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) suivants : *process.json* et *ecosystem.config.js*

Vous pouvez également configurer un fichier de démarrage personnalisé avec les extensions suivantes :

- Fichier *.js*
- [Fichier PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) avec l’extension *.json*, *.config.js*, *.yaml* ou *.yml*

Pour ajouter un fichier de démarrage personnalisé, exécutez la commande suivante dans [Cloud Shell](https://shell.azure.com) :

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Déboguer à distance

> [!NOTE]
> Le débogage à distance est actuellement en préversion.

Vous pouvez déboguer votre application Node.js à distance dans [Visual Studio Code](https://code.visualstudio.com/) si vous la configurez de manière à [s’exécuter avec PM2](#run-with-pm2), sauf lorsque vous l’exécutez à l’aide d’un fichier *.config.js, *yml, ou *.yaml*.

Dans la plupart des cas, aucune configuration supplémentaire n’est nécessaire pour votre application. Si votre application est exécutée avec un fichier *process.json* (par défaut ou personnalisé), elle doit avoir une propriété `script` dans la racine JSON. Par exemple :

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Pour configurer Visual Studio Code pour le débogage à distance, installez l’[extension App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Suivez les instructions sur la page d’extension et connectez-vous à Azure dans Visual Studio Code.

Dans l’Explorateur Azure, recherchez l’application que vous souhaitez déboguer, cliquez dessus avec le bouton droit et sélectionnez **Start Remote Debugging (Démarrer le débogage à distance)** . Cliquez sur **Oui** afin de l’activer pour votre application. App Service démarre un proxy de tunnel pour vous et joint le débogueur. Vous pouvez ensuite effectuer des requêtes auprès de l’application et voir les interruptions du débogueur au niveau des points d’arrêt.

Une fois que vous avez fini le débogage, arrêtez le débogueur en sélectionnant **Déconnexion**. Lorsque vous y êtes invité, vous devez cliquer sur **Oui** pour désactiver le débogage à distance. Pour le désactiver ultérieurement, cliquez une nouvelle fois sur votre application dans l’Explorateur Azure, puis sélectionnez **Désactiver le débogage à distance**.

## <a name="access-environment-variables"></a>Accéder aux variables d’environnement

Dans App Service, vous pouvez [définir les paramètres de l’application](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) en dehors de votre code d’application. Vous pouvez ensuite y accéder à l’aide du modèle Node.js standard. Par exemple, pour accéder à un paramètre d’application nommé `NODE_ENV`, utilisez le code suivant :

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Exécuter Grunt/Bower/Gulp

Par défaut, Kudu exécute `npm install --production` lorsqu’il reconnaît qu’une application Node.js est déployée. Si votre application requiert des outils d’automatisation populaires, tels que Grunt, Bower ou Gulp, vous devez fournir un [script de déploiement personnalisé](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) pour l’exécution.

Pour permettre à votre référentiel d’exécuter ces outils, vous devez les ajouter aux dépendances dans *package.json*. Par exemple :

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Dans une fenêtre de terminal locale, remplacez le répertoire par la racine du référentiel et exécutez les commandes suivantes :

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

La racine du référentiel comporte désormais deux fichiers supplémentaires : *.deployment* et *deploy.sh*.

Ouvrez *deploy.sh* et recherchez la section `Deployment`, qui ressemble à ce qui suit :

```bash
##################################################################################################################################
# Deployment
# ----------
```

Cette section se termine par l’exécution de `npm install --production`. Ajoutez la section de code dont vous avez besoin pour exécuter l’outil requis *à la fin* de la section `Deployment` :

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Consultez un [exemple dans l’échantillon MEAN.js](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), où le script de déploiement exécute également une commande `npm install` personnalisée.

### <a name="bower"></a>Bower

Cet extrait de code exécute `bower install`.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Cet extrait de code exécute `gulp imagemin`.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Cet extrait de code exécute `grunt`.

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>Détecter une session HTTPS

Dans App Service, une [terminaison SSL](https://wikipedia.org/wiki/TLS_termination_proxy) se produit au niveau des équilibreurs de charge réseau. Toutes les requêtes HTTPS accèdent donc à votre application en tant que requêtes HTTP non chiffrées. Si votre logique d’application doit vérifier si les requêtes utilisateur sont chiffrées ou non, inspectez l’en-tête `X-Forwarded-Proto`.

Les frameworks web populaires vous permettent d’accéder aux informations `X-Forwarded-*` dans votre modèle d’application standard. Dans [Express](https://expressjs.com/), vous pouvez utiliser des [proxies de confiance](https://expressjs.com/guide/behind-proxies.html). Par exemple :

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Accéder aux journaux de diagnostic

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Ouvrir une session SSH dans un navigateur

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Résolution de problèmes

Quand une application Node.js en fonctionnement se comporte différemment dans App Service ou comporte des erreurs, essayez ce qui suit :

- [Accéder au flux de journaux](#access-diagnostic-logs).
- Testez l’application localement en mode de production. App Service exécute vos applications Node.js en mode de production et dès lors, vous devez vous assurer que votre projet fonctionne comme prévu en mode de production localement. Par exemple :
    - En fonction de votre *package.json*, différents packages peuvent être installés pour le mode de production (`dependencies` et `devDependencies`).
    - Certaines infrastructures web peuvent déployer des fichiers statiques différents selon le mode de production.
    - Certaines infrastructures web peuvent utiliser des scripts de démarrage personnalisés lors de l’exécution en mode de production.
- Exécutez votre application dans App Service en mode de développement. Par exemple, dans [MEAN.js](https://meanjs.org/), vous pouvez configurer votre application en mode de développement dans le runtime en [configurant le paramètre d’application `NODE_ENV`](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Application Node.js avec MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Questions fréquentes (FAQ) sur App Service sur Linux](app-service-linux-faq.md)
