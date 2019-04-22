---
title: Créer une application web Ruby sur Linux - Azure App Service | Microsoft Docs
description: Apprenez à créer une application Ruby on Rails avec App Service sur Linux.
keywords: azure app service, linux, oss, ruby, rails
services: app-service
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 29126171a2d808153c7578d911e0725641ec39ff
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545143"
---
# <a name="create-a-ruby-on-rails-app-in-app-service-on-linux"></a>Créer une application Ruby on Rails dans App Service sur Linux

[Azure App Service sur Linux](app-service-linux-intro.md) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques. Ce guide de démarrage rapide vous montre comment créer une application [Ruby on Rails](https://rubyonrails.org/) de base puis la déployer dans Azure en tant que Web App sur Linux.

> [!NOTE]
> La pile de développement Ruby ne prend en charge que Ruby on Rails pour l’instant. Si vous souhaitez utiliser une autre plateforme telle que Sinatra, ou si vous souhaitez utiliser une [version non prise en charge de Ruby](app-service-linux-intro.md), vous devez [l’exécuter dans un conteneur personnalisé](quickstart-docker-go.md).

![Hello-world](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">Installez Ruby 2.3 ou version ultérieure</a>
* <a href="https://git-scm.com/" target="_blank">Installez Git</a>

## <a name="download-the-sample"></a>Télécharger l’exemple

Dans une fenêtre de terminal, exécutez la commande ci-après pour cloner le référentiel de l’exemple d’application sur votre ordinateur local :

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

## <a name="run-the-application-locally"></a>Exécuter l’application localement

Exécutez l’application localement pour voir à quoi elle devrait ressembler lorsque vous la déploierez sur Azure. Ouvrez une fenêtre de terminal, accédez au répertoire `hello-world` et utilisez la commande `rails server` pour démarrer le serveur.

La première étape consiste à installer les gems nécessaires. Il est inutile de spécifier les gems à installer, car un `Gemfile` est inclus dans l’exemple. Nous utilisons bundler pour ceci :

```bash
bundle install
```

Une fois les gems installés, nous utilisons bundler pour démarrer l’application :

```bash
bundle exec rails server
```

À l’aide de votre navigateur web, accédez à `http://localhost:3000` pour tester l’application en local.

![Hello World configurée](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Créer une application web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

Accédez au site pour voir votre nouvelle application web avec une image intégrée. Remplacez _&lt;nom de l’application>_ par le nom de votre application web.

```bash
http://<app_name>.azurewebsites.net
```

Voici à quoi doit ressembler votre nouvelle application web :

![Page de démarrage](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>Déployer votre application

Exécutez les commandes suivantes pour déployer l’application locale sur votre site web Azure :

```bash
git remote add azure <Git deployment URL from above>
git add -A
git commit -m "Initial deployment commit"
git push azure master
```

Vérifiez que les opérations de déploiement à distance réussissent. Les commandes produisent une sortie semblable au texte suivant :

```bash
remote: Using sass-rails 5.0.6
remote: Updating files in vendor/cache
remote: Bundle gems are installed into ./vendor/bundle
remote: Updating files in vendor/cache
remote: ~site/repository
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<your web app name>.scm.azurewebsites.net/<your web app name>.git
  579ccb....2ca5f31  master -> master
myuser@ubuntu1234:~workspace/<app name>$
```

Une fois le déploiement terminé, redémarrez votre application web pour que le déploiement prenne effet en utilisant la commande [`az webapp restart`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-restart), comme présenté ici :

```azurecli-interactive
az webapp restart --name <app name> --resource-group myResourceGroup
```

Accédez à votre site et vérifiez les résultats.

```bash
http://<app name>.azurewebsites.net
```

![application web mise à jour](./media/quickstart-ruby/hello-world-updated.png)

> [!NOTE]
> Si vous tentez de parcourir les résultats du site pendant que l’application redémarre, vous recevrez une erreur HTTP `Error 503 Server unavailable`. Le redémarrage complet peut prendre quelques minutes.
>

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Ruby on Rails avec Postgres](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Configurer une application Ruby](configure-language-ruby.md)
