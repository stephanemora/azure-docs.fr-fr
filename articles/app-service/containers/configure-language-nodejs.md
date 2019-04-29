---
title: Configurer des applications Node.js - Azure App Service | Microsoft Docs
description: Découvrez comment configurer des applications Node.js à travailler dans Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 43dc76e6d1e1ec2a6167f1d3e3cc7b8780f843db
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850243"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Configurer une application Node.js de Linux pour Azure App Service

Applications Node.js doivent être déployées avec toutes les dépendances NPM requis. Le moteur de déploiement App Service (Kudu) s’exécute automatiquement `npm install --production` pour vous lorsque vous déployez un [référentiel Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), ou un [package Zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) avec les processus de génération sous tension. Si vous déployez vos fichiers à l’aide de [FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), toutefois, vous devez télécharger les packages requis manuellement.

Ce guide fournit les principaux concepts et des instructions pour les développeurs Node.js qui utilisent un conteneur Linux intégré dans App Service. Si vous n’avez jamais utilisé Azure App Service, suivez les [Guide de démarrage rapide Node.js](quickstart-nodejs.md) et [Node.js avec MongoDB didacticiel](tutorial-nodejs-mongodb-app.md) première.

## <a name="show-nodejs-version"></a>Affiche la version de Node.js

Pour afficher la version de Node.js actuelle, exécutez la commande suivante le [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Pour afficher toutes les versions de Node.js prises en charge, exécutez la commande suivante le [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Définir la version de Node.js

Pour définir votre application sur un [pris en charge de la version de Node.js](#show-nodejs-version), exécutez la commande suivante le [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Ce paramètre spécifie la version de Node.js à utiliser, à la fois lors de l’exécution et pendant la restauration de package automatisés dans Kudu.

> [!NOTE]
> Vous devez définir la version de Node.js dans votre projet `package.json`. Le moteur de déploiement s’exécute dans un conteneur distinct qui contient toutes les versions prises en charge de Node.js.

## <a name="configure-nodejs-server"></a>Configurer le serveur Node.js

Les conteneurs Node.js sont fournis avec [PM2](http://pm2.keymetrics.io/), un gestionnaire de processus de production. Vous pouvez configurer votre application pour démarrer avec PM2, ou avec NPM ou avec une commande personnalisée.

- [Exécutez la commande personnalisée](#run-custom-command)
- [Vous pouvez exécuter npm](#run-npm-start)
- [Exécuter avec PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Exécutez la commande personnalisée

App Service peut démarrer votre application à l’aide d’une commande personnalisée, comme un fichier exécutable comme *run.sh*. Par exemple, pour exécuter `npm run start:prod`, exécutez la commande suivante le [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Vous pouvez exécuter npm

Pour démarrer votre application en utilisant `npm start`, assurez-vous qu’un `start` script se trouve dans le *package.json* fichier. Par exemple : 

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

Utiliser un personnalisé *package.json* dans votre projet, exécutez la commande suivante le [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Exécuter avec PM2

Le conteneur démarre automatiquement votre application avec PM2 lorsqu’un des fichiers communs Node.js se trouve dans votre projet :

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- Une de ces [PM2 fichiers](http://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *process.json* et *ecosystem.config.js*

Vous pouvez également configurer un fichier de démarrage personnalisée avec les extensions suivantes :

- Un *.js* fichier
- Un [fichier PM2](http://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) avec l’extension *.json*, *. config.js*, *.yaml*, ou *.yml*

Pour ajouter un fichier de démarrage personnalisée, exécutez la commande suivante le [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Déboguer à distance

> [!NOTE]
> Débogage à distance est actuellement en version préliminaire.

Vous pouvez déboguer votre application Node.js à distance dans [Visual Studio Code](https://code.visualstudio.com/) si vous le configurez [exécuter avec PM2](#run-with-pm2), sauf lorsque vous exécutez à l’aide d’un *. config.js, *.yml, ou *.yaml*.

Dans la plupart des cas, aucune configuration supplémentaire n’est nécessaire pour votre application. Si votre application est exécutée avec un *process.json* fichier (par défaut ou personnalisé), il doit avoir un `script` propriété dans la racine JSON. Par exemple : 

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Pour configurer Visual Studio Code pour le débogage distant, installez le [extension du Service d’application](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Suivez les instructions sur la page d’extension et connectez-vous à Azure dans Visual Studio Code.

Dans l’Explorateur Azure, recherchez l’application que vous souhaitez déboguer, faites un clic droit et sélectionnez **démarrer le débogage à distance**. Cliquez sur **Oui** pour l’activer pour votre application. App Service démarre un proxy de tunnel pour vous et attache le débogueur. Vous pouvez ensuite effectuer des demandes à l’application et consultez le débogueur pause au niveau des points d’arrêt.

Une fois que vous avez terminé avec le débogage, arrêtez le débogueur en sélectionnant **déconnexion**. Lorsque vous y êtes invité, vous devez cliquer sur **Oui** pour désactiver le débogage à distance. Pour le désactiver ultérieurement, cliquez sur votre application dans l’Explorateur Azure, puis sélectionnez **désactiver le débogage à distance**.

## <a name="access-environment-variables"></a>Accéder aux variables d’environnement

Dans App Service, vous pouvez [définir les paramètres de l’application](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) en dehors de votre code d’application. Vous pouvez ensuite accéder à l’aide du modèle de Node.js standard. Par exemple, pour accéder à un paramètre d’application nommé `NODE_ENV`, utilisez le code suivant :

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Exécutez Grunt/Bower/Gulp

Par défaut, exécute Kudu `npm install --production` lorsqu’il reconnaît une application Node.js est déployée. Si votre application requiert des outils d’automation populaires, tels que Grunt, Bower ou Gulp, vous devez fournir un [script de déploiement personnalisé](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) pour l’exécuter.

Pour activer votre référentiel exécuter ces outils, vous devez les ajouter aux dépendances dans *package.json.* Par exemple : 

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

À partir d’une fenêtre de terminal locale, accédez à la racine du référentiel et exécutez les commandes suivantes :

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

La racine du référentiel a maintenant deux fichiers supplémentaires : *.deployment* et *deploy.sh*.

Ouvrez *deploy.sh* et recherchez le `Deployment` section, qui ressemble à ceci :

```bash
##################################################################################################################################
# Deployment
# ----------
```

Cette section se termine par en cours d’exécution `npm install --production`. Ajoutez la section de code que vous avez besoin pour exécuter l’outil requis *à la fin* de la `Deployment` section :

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Consultez un [exemple dans l’exemple MEAN.js](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), où le script de déploiement exécute également une personnalisée `npm install` commande.

### <a name="bower"></a>Bower

Cet extrait de code s’exécute `bower install`.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Cet extrait de code s’exécute `gulp imagemin`.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Cet extrait de code s’exécute `grunt`.

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

Les frameworks web populaires vous permettent d’accéder aux informations `X-Forwarded-*` dans votre modèle d’application standard. Dans [Express](https://expressjs.com/), vous pouvez utiliser [confiance proxys](http://expressjs.com/guide/behind-proxies.html). Par exemple : 

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

Quand une application Node.js de travail se comporte différemment dans App Service ou comporte des erreurs, essayez ce qui suit :

- [Accéder au flux de journaux](#access-diagnostic-logs).
- Tester l’application localement en mode de production. App Service s’exécute vos applications Node.js en mode de production, vous devez vous assurer que votre projet fonctionne comme prévu en mode production localement. Par exemple : 
    - Selon votre *package.json*, différents packages peuvent être installés pour le mode de production (`dependencies` et `devDependencies`).
    - Certaines infrastructures web peuvent déployer des fichiers statiques différemment en mode de production.
    - Certaines infrastructures web peuvent utiliser des scripts de démarrage personnalisés lors de l’exécution en mode de production.
- Exécutez votre application dans App Service en mode de développement. Par exemple, dans [MEAN.js](http://meanjs.org/), vous pouvez configurer votre application en mode de développement dans le runtime par [paramètre la `NODE_ENV` paramètre d’application](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Application Node.js avec MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Questions fréquentes (FAQ) sur App Service sur Linux](app-service-linux-faq.md)