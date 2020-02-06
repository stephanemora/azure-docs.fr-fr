---
title: Configurer le runtime PHP
description: Découvrez comment configurer l’installation PHP par défaut ou ajouter une installation PHP personnalisée pour Azure App Service.
author: msangapu-msft
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.devlang: php
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: c73fb55e485d0c92d27eac2ac197a81337b9d5e1
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016797"
---
# <a name="configure-php-in-azure-app-service"></a>Configurer PHP dans Azure App Service

## <a name="introduction"></a>Introduction

Ce guide vous explique comment configurer le runtime PHP intégré pour les applications web, back-ends mobiles et applications API dans [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714), fournir un runtime PHP personnalisé et activer des extensions. Pour utiliser App Service, souscrivez à la [version d’évaluation gratuite]. Pour tirer le meilleur parti de ce guide, commencez par créer une application PHP dans App Service.

## <a name="how-to-change-the-built-in-php-version"></a>Procédure : Modifier la version intégrée de PHP

Par défaut, PHP 5.6 est installé et immédiatement utilisable lorsque vous créez une application App Service. Pour afficher la révision de version disponible, sa configuration par défaut et les extensions activées, la méthode idéale consiste à déployer un script qui appelle la fonction [phpinfo()] .

Les versions PHP 7.0 et PHP 7.2 sont également disponibles, mais ne sont pas activées par défaut. Pour mettre à jour la version de PHP, procédez au choix comme suit :

### <a name="azure-portal"></a>Portail Azure

1. Accédez à votre application dans le [Portail Azure](https://portal.azure.com) et faites défiler jusqu’à la page **Configuration**.

2. Dans **Configuration**, sélectionnez **Paramètres généraux** et choisissez la nouvelle version PHP.

3. Cliquez sur le bouton **Enregistrer** en haut du panneau **Paramètres généraux**.

### <a name="azure-cli"></a>Azure CLI 

Pour utiliser l’interface de ligne de commande Azure, vous devez [installer l’interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) sur votre ordinateur.

1. Ouvrez Terminal et connectez-vous à votre compte.

        az login

1. Vérifiez la liste des runtimes pris en charge.

        az webapp list-runtimes | grep php

2. Configurez la version PHP pour l’application.

        az webapp config set --php-version {5.6 | 7.0 | 7.1 | 7.2} --name {app-name} --resource-group {resource-group-name}

3. La version de PHP est maintenant définie. Vous pouvez confirmer les paramètres suivants :

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Procédure : Modifier les configurations PHP intégrées

Quel que soit le runtime PHP intégré, vous pouvez changer toute option de configuration en procédant comme indiqué dans ces étapes. (Pour plus d’informations sur les directives de php.ini, consultez la page [Liste des directives de php.ini].)

### <a name="changing-php_ini_user-php_ini_perdir-php_ini_all-configuration-settings"></a>Modification des paramètres de configuration PHP\_INI\_USER, PHP\_INI\_PERDIR et PHP\_INI\_ALL

1. Ajoutez un fichier [.user.ini] à votre répertoire racine.
1. Ajoutez des paramètres de configuration au fichier `.user.ini` en utilisant la même syntaxe que pour le fichier `php.ini`. Par exemple, si vous souhaitez activer le paramètre `display_errors` et régler le paramètre `upload_max_filesize` sur 10M, votre fichier `.user.ini` doit contenir le texte suivant :

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
2. Déployez votre application.
3. Redémarrez l’application. (Le redémarrage est nécessaire, car la fréquence à laquelle PHP lit les fichiers `.user.ini` est régie par le paramètre `user_ini.cache_ttl`, un paramètre système réglé sur 300 secondes (5 minutes) par défaut. Le redémarrage de l’application force PHP à lire les nouveaux paramètres dans le fichier `.user.ini`.)

Au lieu d’un fichier `.user.ini`, vous pouvez utiliser la fonction [ini_set()] dans des scripts pour définir les options de configuration qui ne sont pas des directives de niveau système.

### <a name="changing-php_ini_system-configuration-settings"></a>Modification des paramètres de configuration PHP\_INI\_SYSTEM

1. Ajoutez un paramètre d’application à votre application avec la clé `PHP_INI_SCAN_DIR` et valeur `d:\home\site\ini`
1. Créez un fichier `settings.ini` à l’aide de la Console Kudu (http://&lt;nom-site&gt;.scm.azurewebsite.net) dans le répertoire `d:\home\site\ini`.
1. Ajoutez des paramètres de configuration au fichier `settings.ini` en utilisant la même syntaxe que pour le fichier `php.ini`. Par exemple, si vous souhaitiez pointer le paramètre `curl.cainfo` vers un fichier `*.crt` et régler le paramètre « wincache.maxfilesize » à 512 ko, votre fichier `settings.ini` doit contenir le texte :

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. Pour recharger les modifications, redémarrez votre application.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Procédure : Activer les extensions dans le runtime PHP par défaut

Comme indiqué dans la section précédente, la méthode idéale pour afficher la version PHP par défaut, sa configuration par défaut et les extensions activées consiste à déployer un script qui appelle [phpinfo()]. Pour activer des extensions supplémentaires, effectuez les étapes suivantes :

### <a name="configure-via-ini-settings"></a>Configuration par des paramètres ini

1. Ajoutez un répertoire `ext` dans le répertoire `d:\home\site`.
1. Placez les fichiers `.dll` dans le répertoire `ext` (par exemple, `php_xdebug.dll`). Assurez-vous que les extensions sont compatibles avec la version par défaut de PHP ainsi qu'avec VC9 et NTS (Non-Thread Safe).
1. Ajoutez un paramètre d’application à votre application avec la clé `PHP_INI_SCAN_DIR` et valeur `d:\home\site\ini`
1. Dans `d:\home\site\ini`, créez un fichier `ini` appelé `extensions.ini`.
1. Ajoutez des paramètres de configuration au fichier `extensions.ini` en utilisant la même syntaxe que pour le fichier `php.ini`. Par exemple, si vous souhaitez activer les extensions MongoDB et XDebug, votre fichier `extensions.ini` doit contenir le texte :

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. Redémarrez votre application pour charger les modifications.

### <a name="configure-via-app-setting"></a>Configuration par un paramètre d’application

1. Ajoutez un répertoire `bin` au répertoire racine.
2. Placez les fichiers `.dll` dans le répertoire `bin` (par exemple, `php_xdebug.dll`). Assurez-vous que les extensions sont compatibles avec la version par défaut de PHP ainsi qu'avec VC9 et NTS (Non-Thread Safe).
3. Déployez votre application.
4. Accédez à votre application dans le Portail Azure, puis cliquez sur le bouton **Configuration** situé sous la section **Paramètres**.
5. Dans le panneau **Configuration**, sélectionnez **Paramètres de l’application**.
6. Dans la section **Paramètres de l’application**, cliquez sur **+ Nouveau paramètre d’application** et créez une clé **PHP_EXTENSIONS**. La valeur de cette clé est un chemin d’accès relatif à la racine du site web : **bin\your-ext-file**.
7. Cliquez sur le bouton **Mettre à jour** situé en bas, puis cliquez sur **Enregistrer** au-dessus de l’onglet **Paramètres de l’application**.

Les extensions Zend sont également prises en charge à l’aide d’une clé **PHP_ZENDEXTENSIONS**. Pour activer plusieurs extensions, insérez une liste de fichiers `.dll` séparés par des virgules pour la valeur de paramètre d’application.

## <a name="how-to-use-a-custom-php-runtime"></a>Procédure : Utiliser un runtime PHP personnalisé

Au lieu du runtime PHP par défaut, App Service peut utiliser un runtime PHP que vous fournissez pour exécuter des scripts PHP. Le runtime en question peut être configuré par un fichier `php.ini` que vous avez également déclaré. Pour utiliser un runtime PHP personnalisé avec App Service, effectuez les étapes suivantes.

1. Obtenez une version de PHP pour Windows, compatible avec NTS (Non-Thread-Safe), VC9 ou VC11. Les versions récentes de PHP pour Windows sont disponibles à l’adresse suivante : [https://windows.php.net/download/]. Vous trouverez les versions plus anciennes dans l’archive ici : [https://windows.php.net/downloads/releases/archives/].
2. Modifiez le fichier `php.ini` de votre runtime. Tout paramètre de configuration correspondant à une directive de niveau système uniquement est ignoré par App Service. Pour plus d'informations sur les directives de niveau système uniquement, consultez la page [Liste des directives de php.ini].
3. Vous pouvez éventuellement ajouter des extensions à votre runtime PHP et les activer dans le fichier `php.ini` .
4. Ajoutez un répertoire `bin` à votre répertoire racine, puis placez-y le répertoire contenant votre runtime PHP (par exemple, `bin\php`).
5. Déployez votre application.
6. Accédez à votre application dans le Portail Azure, puis cliquez sur le panneau **Configuration**.
8. Dans le panneau **Configuration**, sélectionnez **Mappages de chemin d’accès**. 
9. Cliquez sur **+ Nouveau gestionnaire**, ajoutez `*.php` au champ Extension et ajoutez le chemin d’accès à l’exécutable `php-cgi.exe` dans **Processeur de script**. Si vous placez votre runtime PHP dans le répertoire `bin` situé à la racine de votre application, le chemin est `D:\home\site\wwwroot\bin\php\php-cgi.exe`.
10. Cliquez sur **Mettre à jour** situé en bas pour terminer d’ajouter du mappage de gestionnaire.
11. Cliquez sur **Enregistrer** pour enregistrer les modifications.

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Procédure : Activer l’automatisation du Compositeur dans Azure

Par défaut, App Service ne fait rien avec composer.json, si vous en avez un dans votre projet PHP. Si vous utilisez le [déploiement Git](deploy-local-git.md), vous pouvez activer le traitement de composer.json pendant `git push` en activant l’extension du Compositeur.

> [!NOTE]
> Vous pouvez [voter pour la prise en charge haut de gamme de Compositeur dans App Service ici](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)!
>

1. Dans le panneau de l’application PHP (dans le [portail Azure](https://portal.azure.com)), cliquez sur **Outils** > **Extensions**.

    ![Panneau de paramètres du portail Azure pour activer l’automatisation du Compositeur dans Azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Cliquez sur **Ajouter** puis sur **Compositeur**.

    ![Ajouter l’extension du Compositeur pour activer l’automatisation du Compositeur dans Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Cliquez sur **OK** pour accepter les conditions juridiques. Cliquez à nouveau sur **OK** pour ajouter l’extension.

    Le panneau **Extensions installées** affiche l’extension du Compositeur.
    ![Accepter les conditions juridiques pour activer l’automatisation de Compositeur dans Azure](./media/web-sites-php-configure/composer-extension-view.png)
4. À présent, dans une fenêtre de terminal sur votre ordinateur local, exécutez `git add`, `git commit` et `git push` sur votre application. Notez que Compositeur installe des interdépendances définies dans composer.json.

    ![Déploiement Git avec automatisation du Compositeur dans Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez le [Centre pour développeurs PHP](https://azure.microsoft.com/develop/php/).

[version d’évaluation gratuite]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: https://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Liste des directives de php.ini]: https://www.php.net/manual/en/ini.list.php
[.user.ini]: https://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: https://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[https://windows.php.net/download/]: https://windows.php.net/download/
[https://windows.php.net/downloads/releases/archives/]: https://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png
