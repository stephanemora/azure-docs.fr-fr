---
title: 'Démarrage rapide : Créer une application Ruby'
description: Commencez à utiliser Azure App Service en déployant votre première application Ruby sur un conteneur Linux dans App Service.
keywords: azure app service, linux, oss, ruby, rails
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.topic: quickstart
ms.date: 04/27/2021
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
ms.openlocfilehash: 55f7ec48ba940f57daf0b97cf4bf4d82b38c5e75
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730670"
---
# <a name="create-a-ruby-on-rails-app-in-app-service"></a>Créer une application Ruby on Rails dans App Service

[Azure App Service sur Linux](overview.md#app-service-on-linux) fournit un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques à l’aide du système d’exploitation Linux. Ce tutoriel de démarrage rapide montre comment déployer une application Ruby on Rails sur App Service sur Linux à l’aide de [Cloud Shell](../cloud-shell/overview.md).

> [!NOTE]
> La pile de développement Ruby ne prend en charge que Ruby on Rails pour l’instant. Si vous souhaitez utiliser une autre plateforme telle que Sinatra, ou si vous souhaitez utiliser une version non prise en charge de Ruby, vous devez [l’exécuter dans un conteneur personnalisé](./quickstart-custom-container.md?pivots=platform-linux%3fpivots%3dplatform-linux).

![Hello-world](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">Installez Ruby 2.6 ou version supérieure</a>
* <a href="https://git-scm.com/" target="_blank">Installez Git</a>

## <a name="download-the-sample"></a>Télécharger l’exemple

1. Dans une fenêtre de terminal, clonez l’exemple d’application sur votre ordinateur local et accédez au répertoire contenant l’exemple de code. 

    ```bash
    git clone https://github.com/Azure-Samples/ruby-docs-hello-world
    cd ruby-docs-hello-world
    ```

1. Assurez-vous que la branche par défaut est `main`.

    ```bash
    git branch -m main
    ```
    
    > [!TIP]
    > La modification du nom de la branche n’est pas requise par App Service. Toutefois, étant donné que de nombreux référentiels remplacent leur branche par défaut par `main`, ce tutoriel vous montre également comment déployer un référentiel à partir de `main`. Pour plus d’informations, consultez [Modifier la branche de déploiement](deploy-local-git.md#change-deployment-branch).

## <a name="run-the-application-locally"></a>Exécuter l’application localement

1. Installez les gemmes obligatoires. L’exemple contenant un `Gemfile`, il vous suffit d’exécuter la commande suivante :

    ```bash
    bundle install
    ```

1. Une fois les gemmes installées, démarrez l’application :

    ```bash
    bundle exec rails server
    ```

1. À l’aide de votre navigateur web, accédez à `http://localhost:3000` pour tester l’application en local.

    ![Hello World configurée](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [Try Cloud Shell](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Créer une application web

1. Créer une [application web](overview.md#app-service-on-linux) dans le plan App Service `myAppServicePlan`. 

    Dans Cloud Shell, vous pouvez utiliser la commande [`az webapp create`](/cli/azure/webapp). Dans l’exemple suivant, remplacez `<app-name>` par un nom d’application unique (les caractères autorisés sont `a-z`, `0-9` et `-`). Le runtime est défini sur `RUBY|2.6.2`. Pour voir tous les runtimes, exécutez [`az webapp list-runtimes --linux`](/cli/azure/webapp). 

    ```azurecli-interactive
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime 'RUBY|2.6.2' --deployment-local-git
    ```

    Une fois l’application web créée, Azure CLI affiche une sortie similaire à l’exemple suivant :

    <pre>
    Local git is configured with url of 'https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git'
    {
      "availabilityState": "Normal",
      "clientAffinityEnabled": true,
      "clientCertEnabled": false,
      "cloningInfo": null,
      "containerSize": 0,
      "dailyMemoryTimeQuota": 0,
      "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
      "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git",
      "enabled": true,
      &lt; JSON data removed for brevity. &gt;
    }
    </pre>
    
    Vous avez créé une application web vide, avec le déploiement Git activé.

    > [!NOTE]
    > L’URL du Git distant est indiquée dans la propriété `deploymentLocalGitUrl`, avec le format `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Enregistrez cette URL, car vous en aurez besoin ultérieurement.
    >

1. Accédez à l’application pour voir votre nouvelle application web avec une image intégrée. Remplacez _&lt;app-name>_ par le nom de votre application web.

    ```bash
    http://<app_name>.azurewebsites.net
    ```

    Voici à quoi doit ressembler votre nouvelle application web :

    ![Page de démarrage](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>Déployer votre application

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure-no-h.md)] 

   <pre>
   remote: Using turbolinks 5.2.0
   remote: Using uglifier 4.1.20
   remote: Using web-console 3.7.0
   remote: Bundle complete! 18 Gemfile dependencies, 78 gems now installed.
   remote: Bundled gems are installed into `/tmp/bundle`
   remote: Zipping up bundle contents
   remote: .......
   remote: ~/site/repository
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   remote: App container will begin restart within 10 seconds.
   To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
      a6e73a2..ae34be9  main -> main
   </pre>

## <a name="browse-to-the-app"></a>Accéder à l’application

Une fois le déploiement terminé, attendez environ 10 secondes que l’application web redémarre, puis accédez-y pour vérifiez les résultats.

```bash
http://<app-name>.azurewebsites.net
```

![application web mise à jour](./media/quickstart-ruby/hello-world-configured.png)

> [!NOTE]
> Pendant le redémarrage de l’application, vous pouvez observer le code d’état HTTP `Error 503 Server unavailable` dans le navigateur ou la page par défaut `Hey, Ruby developers!`. Le redémarrage complet de l’application peut prendre quelques minutes.
>

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Ruby on Rails avec Postgres](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Configurer une application Ruby](configure-language-ruby.md)
