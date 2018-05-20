---
title: Créer une application Ruby et la déployer sur App Service sur Linux | Microsoft Docs
description: Apprenez à créer des applications Ruby avec App Service sur Linux.
keywords: azure app service, linux, oss, ruby
services: app-service
documentationcenter: ''
author: SyntaxC4
manager: cfowler
editor: ''
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/10/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 0707835cf4d6891397579cc41de49fc0462b4521
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2018
---
# <a name="create-a-ruby-app-in-app-service-on-linux"></a>Créer une application Ruby dans App Service sur Linux

[Azure App Service sur Linux](app-service-linux-intro.md) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques. Ce guide de démarrage rapide vous montre comment créer une application [Ruby on Rails](https://rubyonrails.org/) de base puis la déployer dans Azure en tant que Web App sur Linux.

![Hello-world](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis


* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">Installez Ruby 2.3 ou version ultérieure</a>
* <a href="https://git-scm.com/" target="_blank">Installez Git</a>

## <a name="download-the-sample"></a>Téléchargez l’exemple

Dans une fenêtre de terminal, exécutez la commande ci-après pour cloner le référentiel de l’exemple d’application sur votre ordinateur local :

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

## <a name="run-the-application-locally"></a>Exécuter l’application localement

Le serveur Rails doit être en cours d’exécution pour que l’application fonctionne. Accédez au répertoire `hello-world` et utilisez la commande `rails server` pour démarrer le serveur.

```bash
cd hello-world\bin
rails server
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

Une fois le déploiement terminé, redémarrez votre application web pour que le déploiement prenne effet en utilisant la commande [`az webapp restart`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_restart), comme présenté ici :

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
> [Build a Ruby and MySQL web app in Azure App Service on Linux](tutorial-ruby-mysql-app.md) (Créer une application web Ruby et MySQL dans Azure App Service sur Linux)
