---
title: Configurer des applications PHP - Azure App Service | Microsoft Docs
description: Découvrez comment configurer des applications PHP pour travailler dans Azure App Service
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
ms.openlocfilehash: 637feb855c7816dfb26229c5a65a069260a58cd3
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003092"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Configurer une application PHP de Linux pour Azure App Service

Ce guide vous montre comment configurer le runtime PHP intégré pour les applications web, back-ends mobiles et API apps dans Azure App Service.

Ce guide fournit les principaux concepts et des instructions pour les développeurs PHP qui utilisent un conteneur Linux intégré dans App Service. Si vous n’avez jamais utilisé Azure App Service, suivez les [PHP quickstart](quickstart-php.md) et [PHP avec MySQL didacticiel](tutorial-php-mysql-app.md) première.

## <a name="show-php-version"></a>Affiche la version PHP

Pour afficher la version actuelle de PHP, exécutez la commande suivante le [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Pour afficher les versions PHP tout pris en charge, exécutez la commande suivante le [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>Définir la version PHP

Exécutez la commande suivante le [Cloud Shell](https://shell.azure.com) pour définir la version PHP 7.2 :

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="run-composer"></a>Exécutez Composer

Par défaut, ne s’exécute pas Kudu [Composer](https://getcomposer.org/). Pour activer l’automatisation du compositeur lors du déploiement de Kudu, vous devez fournir un [script de déploiement personnalisé](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

À partir d’une fenêtre de terminal locale, accédez à la racine du référentiel. Suivez le [étapes d’installation de ligne de commande](https://getcomposer.org/download/) télécharger *composer.phar*.

Exécutez les commandes suivantes :

```bash
npm install kuduscript -g
kuduscript --php --scriptType bash --suppressPrompt
```

La racine du référentiel a maintenant deux nouveaux fichiers en plus de *composer.phar*: *.deployment* et *deploy.sh*. Ces fichiers fonctionnent à la fois pour les versions de Windows et Linux d’App Service.

Ouvrez *deploy.sh* et recherchez le `Deployment` section. Remplacez l’intégralité de la section avec le code suivant :

```bash
##################################################################################################################################
# Deployment
# ----------

echo PHP deployment

# 1. KuduSync
if [[ "$IN_PLACE_DEPLOYMENT" -ne "1" ]]; then
  "$KUDU_SYNC_CMD" -v 50 -f "$DEPLOYMENT_SOURCE" -t "$DEPLOYMENT_TARGET" -n "$NEXT_MANIFEST_PATH" -p "$PREVIOUS_MANIFEST_PATH" -i ".git;.hg;.deployment;deploy.sh"
  exitWithMessageOnError "Kudu Sync failed"
fi

# 3. Initialize Composer Config
initializeDeploymentConfig

# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
##################################################################################################################################
```

Validez toutes vos modifications et redéployer votre code. Composer doit maintenant s’exécuter dans le cadre de l’automatisation du déploiement.

## <a name="customize-start-up"></a>Personnaliser le démarrage

Par défaut, le conteneur PHP intégré, exécutez le serveur Apache. Au démarrage, elle s’exécute `apache2ctl -D FOREGROUND"`. Si vous le souhaitez, vous pouvez exécuter une commande différente au démarrage, en exécutant la commande suivante dans le [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Accéder aux variables d’environnement

Dans App Service, vous pouvez [définir les paramètres de l’application](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) en dehors de votre code d’application. Vous pouvez ensuite accéder à l’aide de la norme [getenv()](https://secure.php.net/manual/function.getenv.php) modèle. Par exemple, pour accéder à un paramètre d’application nommé `DB_HOST`, utilisez le code suivant :

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Racine du site modification

L’infrastructure web de votre choix peut utiliser un sous-répertoire comme la racine du site. Par exemple, [Laravel](https://laravel.com/), utilise le `public/` sous-répertoire en tant que la racine du site.

L’image PHP par défaut pour App Service utilise Apache, et il ne vous permettent de personnaliser la racine du site pour votre application. Pour contourner cette limitation, ajoutez un *.htaccess* fichier à la racine du référentiel avec le contenu suivant :

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^.*$ /public/$1 [NC,L,QSA]
</IfModule>
```

Si vous préférez ne pas utiliser la réécriture *.htaccess*, vous pouvez à la place déployer votre application Laravel avec une [image Docker personnalisée](quickstart-docker-go.md).

## <a name="detect-https-session"></a>Détecter une session HTTPS

Dans App Service, une [terminaison SSL](https://wikipedia.org/wiki/TLS_termination_proxy) se produit au niveau des équilibreurs de charge réseau. Toutes les requêtes HTTPS accèdent donc à votre application en tant que requêtes HTTP non chiffrées. Si votre logique d’application doit vérifier si les requêtes utilisateur sont chiffrées ou non, inspectez l’en-tête `X-Forwarded-Proto`.

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

Les frameworks web populaires vous permettent d’accéder aux informations `X-Forwarded-*` dans votre modèle d’application standard. Dans [CodeIgniter](https://codeigniter.com/), [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) vérifie la valeur de `X_FORWARDED_PROTO` par défaut.

## <a name="customize-phpini-settings"></a>Personnaliser les paramètres de php.ini

Si vous avez besoin apporter des modifications à votre installation de PHP, vous pouvez modifier le [directives de php.ini](https://www.php.net/manual/ini.list.php) en suivant ces étapes.

> [!NOTE]
> La meilleure façon de voir la version de PHP et actuel *php.ini* configuration consiste à appeler [phpinfo()](https://php.net/manual/function.phpinfo.php) dans votre application.
>

### <a name="Customize-non-PHP_INI_SYSTEM directives"></a>Directives de personnalisation-non-PHP_INI_SYSTEM

Pour personnaliser les directives PHP_INI_USER, PHP_INI_PERDIR et PHP_INI_ALL (consultez [directives de php.ini](https://www.php.net/manual/ini.list.php)), ajoutez un *.htaccess* fichier dans le répertoire racine de votre application.

Dans le *.htaccess* , ajoutez les directives à l’aide de la `php_value <directive-name> <value>` syntaxe. Exemple :

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Redéployez votre application avec les modifications et redémarrez-le. Si vous le déployez avec Kudu (par exemple, à l’aide de [Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)), il est redémarré automatiquement après le déploiement.

Comme alternative à l’utilisation de *.htaccess*, vous pouvez utiliser [ini_set()](https://www.php.net/manual/function.ini-set.php) dans votre application pour personnaliser ces directives non PHP_INI_SYSTEM.

### <a name="customize-phpinisystem-directives"></a>Personnaliser les directives PHP_INI_SYSTEM

Pour personnaliser les directives PHP_INI_SYSTEM (consultez [directives de php.ini](https://www.php.net/manual/ini.list.php)), vous ne pouvez pas utiliser le *.htaccess* approche. App Service fournit un mécanisme distinct à l’aide du `PHP_INI_SCAN_DIR` paramètre d’application.

Tout d’abord, exécutez la commande suivante le [Cloud Shell](https://shell.azure.com) pour ajouter le paramètre d’application `PHP_INI_SCAN_DIR`:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` est le répertoire par défaut où *php.ini* existe. `/home/site/ini` est le répertoire personnalisé dans lequel vous allez ajouter un personnalisé *.ini* fichier. Vous séparez les valeurs avec un `:`.

Accédez à la session SSH web avec votre conteneur Linux (`https://cephalin-container.scm.azurewebsites.net/webssh/host`).

Créez un répertoire dans `/home/site` appelé `ini`, puis créez un *.ini* de fichiers dans le `/home/site/ini` répertoire (par exemple, *settings.ini)* avec les directives que vous souhaitez personnaliser. Utilisez la même syntaxe que vous utiliseriez dans un *php.ini* fichier. 

> [!TIP]
> Dans les conteneurs Linux intégrés dans App Service, */home* est utilisé en tant que stockage partagé persistant. 
>

Par exemple, pour modifier la valeur de [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) exécutez les commandes suivantes :

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Pour que les modifications entrent en vigueur, redémarrez l’application.

## <a name="enable-php-extensions"></a>Activer les extensions PHP

Les installations PHP intégrées contiennent les extensions couramment utilisées. Vous pouvez activer des extensions supplémentaires dans la même façon que vous [personnaliser les directives de php.ini](#customize-php_ini_system-directives).

> [!NOTE]
> La meilleure façon de voir la version de PHP et actuel *php.ini* configuration consiste à appeler [phpinfo()](https://php.net/manual/function.phpinfo.php) dans votre application.
>

Pour activer des extensions supplémentaires, effectuez les étapes suivantes :

Ajouter un `bin` répertoire vers le répertoire racine de votre application et de placer le `.so` des fichiers d’extension qu’il contient (par exemple, *mongodb.so*). Assurez-vous que les extensions sont compatibles avec la version de PHP dans Azure et qu’avec VC9 et compatible avec non thread-safe (nts).

Déployez vos modifications.

Suivez les étapes de [directives de personnaliser les PHP_INI_SYSTEM](#customize-php_ini_system-directives), ajoutez les extensions dans personnalisé *.ini* de fichiers avec le [extension](https://www.php.net/manual/ini.core.php#ini.extension) ou [zend_extension ](https://www.php.net/manual/ini.core.php#ini.zend-extension) directives.

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Pour que les modifications entrent en vigueur, redémarrez l’application.

## <a name="access-diagnostic-logs"></a>Accéder aux journaux de diagnostic

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Ouvrir une session SSH dans un navigateur

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Résolution de problèmes

Quand une application PHP se comporte différemment dans App Service ou comporte des erreurs, essayez ce qui suit :

- [Accéder au flux de journaux](#access-diagnostic-logs).
- Tester l’application localement en mode de production. App Service s’exécute vos applications Node.js en mode de production, vous devez vous assurer que votre projet fonctionne comme prévu en mode production localement. Exemple :
    - Selon votre *composer.json*, différents packages peuvent être installés pour le mode de production (`require` et `require-dev`).
    - Certaines infrastructures web peuvent déployer des fichiers statiques différemment en mode de production.
    - Certaines infrastructures web peuvent utiliser des scripts de démarrage personnalisés lors de l’exécution en mode de production.
- Exécutez votre application dans App Service en mode débogage. Par exemple, dans [Laravel](https://meanjs.org/), vous pouvez configurer votre application pour la sortie des messages de débogage en production par [paramètre la `APP_DEBUG` paramètre d’application à `true` ](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

### <a name="robots933456"></a>robots933456

Vous pouvez voir le message suivant dans les journaux du conteneur :

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

Vous pouvez ignorer ce message. `/robots933456.txt` est un chemin d’URL factice qui App Service utilise pour vérifier si le conteneur est capable de servir les requêtes. Une réponse 404 indique simplement que le chemin d’accès n’existe pas, mais il informe App Service que le conteneur est intègre et prêt à répondre aux demandes.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Application PHP avec MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Questions fréquentes (FAQ) sur App Service sur Linux](app-service-linux-faq.md)
