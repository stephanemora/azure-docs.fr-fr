---
title: Configurer des applications Windows PHP
description: Découvrez comment configurer une application PHP dans les instances Windows natives d’App Service. Cet article présente les tâches de configuration les plus courantes.
ms.devlang: php
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 1eb4e9d349fdd0097cbde4e4cef3d5c61a167193
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84907921"
---
# <a name="configure-a-windows-php-app-for-azure-app-service"></a>Configurer une application Windows PHP pour Azure App Service

Ce guide vous explique comment configurer les applications web PHP, les back-ends mobiles et les applications API dans Azure App Service. Ce guide s’applique aux applications hébergées sur la plateforme Windows. Pour plus d’informations sur les applications Linux, consultez [Configurer une application PHP Linux pour Azure App Service](containers/configure-language-php.md).

Ce guide fournit les concepts et instructions clés aux développeurs PHP qui déploient des applications sur App Service. Si vous n’avez jamais utilisé Azure App Service, commencez par suivre le [démarrage rapide PHP](app-service-web-get-started-php.md) et le [tutoriel PHP avec MySQL](app-service-web-tutorial-php-mysql.md).

## <a name="show-php-version"></a>Afficher la version PHP

Pour afficher la version actuelle de PHP, exécutez la commande suivante dans [Cloud Shell](https://shell.azure.com) :

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query phpVersion
```

Pour afficher toutes les versions prises en charge de PHP, exécutez la commande suivante dans [Cloud Shell](https://shell.azure.com) :

```azurecli-interactive
az webapp list-runtimes | grep php
```

## <a name="set-php-version"></a>Définir la version PHP

Exécutez la commande suivante dans [Cloud Shell](https://shell.azure.com) pour définir la version 7.4 de PHP :

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --php-version 7.4
```

## <a name="run-composer"></a>Exécuter Composer

Si vous souhaitez qu’App Service exécute [Composer](https://getcomposer.org/) au moment du déploiement, le moyen le plus simple consiste à inclure le Composer dans votre référentiel.

Dans une fenêtre de terminal local, accédez à la racine de votre référentiel, puis suivez les instructions de [Télécharger Composer](https://getcomposer.org/download/) pour télécharger *composer.phar* à la racine du répertoire.

Exécutez les commandes suivantes ([npm](https://www.npmjs.com/get-npm) doit être installé) :

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

Ajoutez la section de code dont vous avez besoin pour exécuter l’outil requis *à la fin* de la section `Deployment` :

```bash
# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
```

Validez toutes vos modifications et déployez votre code à l’aide de Git ou de Zip Deploy avec l’automatisation de build activée. Composer doit maintenant s’exécuter dans le cadre de l’automatisation du déploiement.

## <a name="run-gruntbowergulp"></a>Exécuter Grunt/Bower/Gulp

Si vous souhaitez qu’App Service exécute certains outils d’automatisation populaires au moment du déploiement, par exemple Grunt, Bower ou Gulp, vous devez fournir un [script de déploiement personnalisé](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script). App Service exécute ce script lorsque vous déployez avec Git, ou avec le [déploiement Zip](deploy-zip.md) avec l’automatisation de build activée. 

Pour permettre à votre référentiel d’exécuter ces outils, vous devez les ajouter aux dépendances dans *package.json*. Par exemple :

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Dans une fenêtre de terminal locale, remplacez le répertoire par la racine du référentiel et exécutez les commandes suivantes ([npm](https://www.npmjs.com/get-npm) doit être installé) :

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

## <a name="access-environment-variables"></a>Accéder aux variables d’environnement

Dans App Service, vous pouvez [définir les paramètres de l’application](configure-common.md#configure-app-settings) en dehors de votre code d’application. Vous pouvez ensuite y accéder à l’aide du modèle standard [getenv()](https://secure.php.net/manual/function.getenv.php). Par exemple, pour accéder à un paramètre d’application nommé `DB_HOST`, utilisez le code suivant :

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Modifier la racine du site

L’infrastructure web de votre choix peut utiliser un sous-répertoire en tant que racine du site. Par exemple, [Laravel](https://laravel.com/) utilise le sous-répertoire *public/* en tant que racine du site.

Pour personnaliser la racine du site, définissez le chemin d’accès de l’application virtuelle pour l’application à l’aide de la commande [`az resource update`](/cli/azure/resource#az-resource-update). L’exemple suivant définit la racine du site sur le sous-répertoire *public/* de votre référentiel. 

```azurecli-interactive
az resource update --name web --resource-group <group-name> --namespace Microsoft.Web --resource-type config --parent sites/<app-name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Par défaut, Azure App Service pointe le chemin d’accès à l’application virtuelle ( _/_ ) vers le répertoire racine des fichiers d’application déployés (_sites\wwwroot_).

## <a name="detect-https-session"></a>Détecter une session HTTPS

Dans App Service, une [terminaison SSL](https://wikipedia.org/wiki/TLS_termination_proxy) se produit au niveau des équilibreurs de charge réseau. Toutes les requêtes HTTPS accèdent donc à votre application en tant que requêtes HTTP non chiffrées. Si votre logique d’application doit vérifier si les requêtes utilisateur sont chiffrées ou non, inspectez l’en-tête `X-Forwarded-Proto`.

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

Les frameworks web populaires vous permettent d’accéder aux informations `X-Forwarded-*` dans votre modèle d’application standard. Dans [CodeIgniter](https://codeigniter.com/), [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) vérifie la valeur de `X_FORWARDED_PROTO` par défaut.

## <a name="customize-phpini-settings"></a>Personnaliser les paramètres php.ini

S'il vous faut apporter des modifications à votre installation PHP, vous pouvez modifier les [directives php.ini](https://www.php.net/manual/ini.list.php) en suivant ces étapes.

> [!NOTE]
> La meilleure façon de consulter la version de PHP et la configuration actuelle de *php.ini* consiste à appeler [phpinfo()](https://php.net/manual/function.phpinfo.php) dans votre application.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Directives Customize-non-PHP_INI_SYSTEM

Pour personnaliser les directives PHP_INI_USER, PHP_INI_PERDIR et PHP_INI_ALL (consultez les [directives php.ini](https://www.php.net/manual/ini.list.php)), ajoutez un fichier `.user.ini` au répertoire racine de votre application.

Ajoutez des paramètres de configuration au fichier `.user.ini` en utilisant la même syntaxe que pour le fichier `php.ini`. Par exemple, si vous souhaitez activer le paramètre `display_errors` et régler le paramètre `upload_max_filesize` sur 10M, votre fichier `.user.ini` doit contenir le texte suivant :

```
 ; Example Settings
 display_errors=On
 upload_max_filesize=10M

 ; Write errors to d:\home\LogFiles\php_errors.log
 ; log_errors=On
```

Redéployez votre application avec les modifications et redémarrez-la.

En guise d’alternative au fichier `.user.ini`, vous pouvez utiliser [ini_set()](https://www.php.net/manual/function.ini-set.php) dans votre application pour personnaliser ces directives non-PHP_INI_SYSTEM.

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>Personnaliser les directives PHP_INI_SYSTEM

Pour personnaliser les directives PHP_INI_SYSTEM (consultez les [directives php.ini](https://www.php.net/manual/ini.list.php)), vous ne pouvez pas utiliser l'approche *.htaccess*. App Service propose un mécanisme distinct utilisant le paramètre d’application `PHP_INI_SCAN_DIR`.

Commencez par exécuter la commande suivante dans [Cloud Shell](https://shell.azure.com) pour ajouter un paramètre d'application appelé `PHP_INI_SCAN_DIR` :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="d:\home\site\ini"
```

Accédez à la console Kudu (`https://<app-name>.scm.azurewebsites.net/DebugConsole`) et accédez à `d:\home\site`.

Créez un répertoire dans `d:\home\site` appelé `ini`, puis créez un fichier *.ini* dans le répertoire `d:\home\site\ini` (par exemple, *settings.ini)* avec les directives que vous souhaitez personnaliser. Utilisez la même syntaxe que pour un fichier *php.ini*. 

Par exemple, pour modifier la valeur de [expose_php](https://php.net/manual/ini.core.php#ini.expose-php), exécutez les commandes suivantes :

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Redémarrez l'application pour que les modifications soient prises en compte.

## <a name="enable-php-extensions"></a>Activer les extensions PHP

Les installations PHP intégrées contiennent les extensions les plus fréquemment utilisées. Vous pouvez activer des extensions supplémentaires de la même manière que vous [personnalisez les directives php.ini](#customize-php_ini_system-directives).

> [!NOTE]
> La meilleure façon de consulter la version de PHP et la configuration actuelle de *php.ini* consiste à appeler [phpinfo()](https://php.net/manual/function.phpinfo.php) dans votre application.
>

Pour activer des extensions supplémentaires, effectuez les étapes suivantes :

Ajoutez un répertoire `bin` au répertoire racine de votre application et placez-y les fichiers d'extension `.so` (par exemple, *mongodb.so*). Assurez-vous que les extensions sont compatibles avec la version de PHP dans Azure, ainsi qu'avec VC9 et NTS (Non-Thread Safe).

Déployez vos modifications.

Suivez les étapes décrites dans [Personnaliser les directives PHP_INI_SYSTEM](#customize-php_ini_system-directives), ajoutez les extensions au fichier *.ini* personnalisé avec l'[extension](https://www.php.net/manual/ini.core.php#ini.extension) ou les directives [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension).

```
extension=d:\home\site\wwwroot\bin\mongodb.so
zend_extension=d:\home\site\wwwroot\bin\xdebug.so
```

Redémarrez l'application pour que les modifications soient prises en compte.

## <a name="access-diagnostic-logs"></a>Accéder aux journaux de diagnostic

Utilisez l’utilitaire standard [error_log ()](https://php.net/manual/function.error-log.php) pour faire apparaître vos journaux de diagnostic dans Azure App Service.

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="troubleshooting"></a>Dépannage

Si une application PHP se comporte différemment dans App Service ou présente des erreurs, essayez ce qui suit :

- [Accéder au flux de journaux](#access-diagnostic-logs).
- Testez l’application localement en mode de production. App Service exécute votre application en mode de production et dès lors, vous devez vous assurer que votre projet fonctionne comme prévu en mode de production localement. Par exemple :
    - Certaines infrastructures web peuvent déployer des fichiers statiques différemment en mode de production.
    - Certaines infrastructures web peuvent utiliser des scripts de démarrage personnalisés lorsqu'elles s'exécutent en mode de production.
- Dans App Service, exécutez votre application en mode de débogage. Par exemple, dans [Laravel](https://meanjs.org/), vous pouvez configurer votre application de manière à envoyer les messages de débogage en production en [définissant le paramètre d'application `APP_DEBUG` sur `true`](configure-common.md#configure-app-settings).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Application PHP avec MySQL](app-service-web-tutorial-php-mysql.md)
