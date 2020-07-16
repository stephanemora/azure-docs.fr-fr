---
title: Configurer des applications Windows Node.js
description: En savoir plus sur la procédure de configuration d’une application Node.js dans les instances Windows natives d’App Service. Cet article présente les tâches de configuration les plus courantes.
ms.devlang: nodejs
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 9f4ccdd04b8d57784f452dc28fa4507fb7ea94c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84907926"
---
# <a name="configure-a-windows-nodejs-app-for-azure-app-service"></a>Configurer une application Windows Node.js pour Azure App Service

Les applications Node.js doivent être déployées avec toutes les dépendances NPM nécessaires. Le moteur de déploiement App Service exécute automatiquement `npm install --production` pour vous lorsque vous déployez un [référentiel Git](deploy-local-git.md) ou un [package zip](deploy-zip.md) avec l’automatisation de la génération activée. Si vous déployez vos fichiers à l’aide de [FTP/S](deploy-ftp.md), vous devez cependant télécharger les packages requis manuellement. Pour plus d’informations sur les applications Linux, consultez [Configurer une application PHP Linux pour Azure App Service](containers/configure-language-nodejs.md).

Ce guide fournit les concepts et instructions clés aux développeurs Node.js qui déploient des applications sur App Service. Si vous n’avez jamais utilisé Azure App Service, suivez le [démarrage rapide Node.js](app-service-web-get-started-nodejs.md) et le [tutoriel Node.js avec MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md) au préalable.

## <a name="show-nodejs-version"></a>Afficher la version de Node.js

Pour afficher la version actuelle de Node.js, exécutez la commande suivante dans [Cloud Shell](https://shell.azure.com) :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION'].value"
```

Pour afficher toutes les versions de Node.js prises en charge, exécutez la commande suivante dans [Cloud Shell](https://shell.azure.com) :

```azurecli-interactive
az webapp list-runtimes | grep node
```

## <a name="set-nodejs-version"></a>Définir la version de Node.js

Pour définir votre application dans une [version de Node.js prise en charge](#show-nodejs-version), exécutez la commande suivante dans [Cloud Shell](https://shell.azure.com) pour définir `WEBSITE_NODE_DEFAULT_VERSION` sur une version prise en charge :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_NODE_DEFAULT_VERSION="10.15"
```

Ce paramètre spécifie la version de Node.js à utiliser, à la fois lors de l’exécution et pendant la restauration de package automatisée pendant l’automatisation de la génération App Service.

> [!NOTE]
> Vous devez définir la version de Node.js dans le fichier `package.json` de votre projet. Le moteur de déploiement s’exécute dans un processus distinct qui inclut toutes les versions de Node.js prises en charge.

## <a name="access-environment-variables"></a>Accéder aux variables d’environnement

Dans App Service, vous pouvez [définir les paramètres de l’application](configure-common.md) en dehors de votre code d’application. Vous pouvez ensuite y accéder à l’aide du modèle Node.js standard. Par exemple, pour accéder à un paramètre d’application nommé `NODE_ENV`, utilisez le code suivant :

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Exécuter Grunt/Bower/Gulp

Par défaut, l’automatisation de la génération App Service exécute `npm install --production` lorsqu’elle reconnaît qu’une applicationNode.js est déployée via Git (ou un déploiement Zip avec l’automatisation de la génération activée). Si votre application requiert des outils d’automatisation populaires, tels que Grunt, Bower ou Gulp, vous devez fournir un [script de déploiement personnalisé](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) pour l’exécution.

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

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="troubleshooting"></a>Dépannage

Quand une application Node.js en fonctionnement se comporte différemment dans App Service ou comporte des erreurs, essayez ce qui suit :

- [Accéder au flux de journaux](#access-diagnostic-logs).
- Testez l’application localement en mode de production. App Service exécute vos applications Node.js en mode de production et dès lors, vous devez vous assurer que votre projet fonctionne comme prévu en mode de production localement. Par exemple :
    - En fonction de votre *package.json*, différents packages peuvent être installés pour le mode de production (`dependencies` et `devDependencies`).
    - Certaines infrastructures web peuvent déployer des fichiers statiques différemment en mode de production.
    - Certaines infrastructures web peuvent utiliser des scripts de démarrage personnalisés lorsqu'elles s'exécutent en mode de production.
- Exécutez votre application dans App Service en mode de développement. Par exemple, dans [MEAN.js](https://meanjs.org/), vous pouvez configurer votre application en mode de développement dans le runtime en [configurant le paramètre d’application `NODE_ENV`](configure-common.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Application Node.js avec MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)

