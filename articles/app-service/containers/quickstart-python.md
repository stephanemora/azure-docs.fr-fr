---
title: Déployer une application Python dans Azure Web App pour conteneurs
description: Comment déployer une image Docker exécutant une application Python dans Web App pour conteneurs.
keywords: azure app service, application web, python, docker, conteneur
services: app-service
author: cephalin
manager: jeconnoc
ms.service: app-service
ms.devlang: python
ms.topic: quickstart
ms.date: 07/13/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 5686266774603413fc255c53a0d1ad30f9baa8eb
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173858"
---
# <a name="deploy-a-python-web-app-in-web-app-for-containers"></a>Déployer une application Web Python dans Web App pour conteneurs

[App Service sur Linux](app-service-linux-intro.md) fournit un service d’hébergement web hautement scalable appliquant des mises à jour correctives automatiques à l’aide du système d’exploitation Linux. Ce guide de démarrage rapide vous montre comment créer une application web et y déployer une application Flask simple à partir d’une image Docker Hub personnalisée. Vous allez créer l’application web à l’aide d’[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

![Exemple d’application s’exécutant dans Azure](media/quickstart-python/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel :

* <a href="https://git-scm.com/" target="_blank">Installez Git</a>
* <a href="https://www.docker.com/community-edition" target="_blank">Installez Docker Community Edition</a>
* <a href="https://hub.docker.com/" target="_blank">Créez un compte Docker Hub</a>

## <a name="download-the-sample"></a>Télécharger l’exemple

Dans une fenêtre de terminal, exécutez les commandes suivantes pour cloner l’exemple d’application sur votre machine locale, puis accédez au répertoire contenant l’exemple de code.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
cd python-docs-hello-world
```

Ce référentiel contient une application Flask simple dans le dossier _/app_ et un élément _Dockerfile_ qui spécifie trois opérations :

- Utiliser l’image de base [tiangolo/uwsgi-nginx-flask:python3.6-alpine3.7](https://hub.docker.com/r/tiangolo/uwsgi-nginx-flask/).
- Le conteneur doit écouter sur le port 8000.
- Copier le répertoire `/app` vers le répertoire `/app` du conteneur.

La configuration suit les [instructions pour l’image de base](https://hub.docker.com/r/tiangolo/uwsgi-nginx-flask/).

## <a name="run-the-app-locally"></a>Exécutez l’application localement.

Exécutez l’application dans un conteneur Docker.

```bash
docker build --rm -t flask-quickstart .
docker run --rm -it -p 8000:8000 flask-quickstart
```

Ouvrez un navigateur web et accédez à l’application exemple à l’adresse `http://localhost:8000`.

Vous pouvez voir sur la page le message **Hello World** de l’exemple d’application.

![Exemple d’application s’exécutant localement](media/quickstart-python/localhost-hello-world-in-browser.png)

Dans la fenêtre de terminal, appuyez sur **Ctrl+C** pour arrêter le conteneur.

## <a name="deploy-image-to-docker-hub"></a>Déployer l’image vers Docker Hub

Connectez-vous à votre compte Docker Hub. Suivez l’invite à entrer vos informations d’identification Docker Hub.

```bash
docker login
```

Marquez votre image et envoyez-la vers un nouveau référentiel _public_ de votre compte Docker Hub, vers un référentiel appelé `flask-quickstart`. Remplacez *\<dockerhub_id>* par votre ID Docker Hub.

```bash
docker tag flask-quickstart <dockerhub_id>/flask-quickstart
docker push <dockerhub_id>/flask-quickstart
```

> [!NOTE]
> `docker push` crée un référentiel public si le référentiel spécifié est introuvable. Ce guide de démarrage rapide suppose un référentiel public Docker Hub. Si vous préférez envoyer l’image vers un référentiel privé, vous devez configurer vos informations d’identification Docker Hub dans Azure App Service ultérieurement. Voir [Créer une application web](#create-a-web-app).

Une fois que l’envoi de l’image est terminé, vous êtes prêt à l’utiliser dans votre application web Azure.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Créer une application web

Créez une [application web](../app-service-web-overview.md) dans le plan App Service `myAppServicePlan` avec la commande [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). Remplacez *\<app name>* par un nom d’application unique, et remplacez *\<dockerhub_id>* par votre ID Docker Hub.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name <dockerhub_id>/flask-quickstart
```

Une fois l’application web créée, Azure CLI affiche une sortie similaire à l’exemple suivant :

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Si vous avez précédemment téléchargé à un référentiel privé, vous devez également configurer les informations d’identification Docker Hub dans App Service. Pour plus d’informations, reportez-vous à [Utiliser une image privée à partir de Docker Hub](tutorial-custom-docker-image.md#use-a-private-image-from-docker-hub-optional).

### <a name="specify-container-port"></a>Indiquer le port du conteneur

Comme indiqué dans _Dockerfile_, votre conteneur écoute le port 8000. Pour que App Service achemine votre requête vers le bon port, vous devez définir le paramètre d’application *WEBSITES_PORT*.

Dans Cloud Shell, exécutez la commande [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set).


```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings WEBSITES_PORT=8000
```

## <a name="browse-to-the-app"></a>Accéder à l’application

```bash
http://<app_name>.azurewebsites.net/
```

![Exemple d’application s’exécutant dans Azure](media/quickstart-python/hello-world-in-browser.png)

> [!NOTE]
> L’application web met un certain temps à démarrer, car l’image Docker Hub doit être téléchargée et exécutée lors de la première sollicitation de l’application. Si vous voyez une erreur après un certain temps, actualisez simplement la page.

**Félicitations !** Vous avez déployé une image Docker personnalisée exécutant une application Python dans Web App pour conteneurs.

## <a name="update-locally-and-redeploy"></a>Mise à jour locale et redéploiement

À l’aide d’un éditeur de texte local, ouvrez le fichier `app/main.py` dans l’application Python, puis apportez une petite modification au texte figurant en regard de l’instruction `return` :

```python
return 'Hello, Azure!'
```

Régénérez l’image et renvoyez-la vers Docker Hub.

```bash
docker build --rm -t flask-quickstart .
docker tag flask-quickstart <dockerhub_id>/flask-quickstart
docker push <dockerhub_id>/flask-quickstart
```

Dans Cloud Shell, redémarrez l’application. Le redémarrage de l’application garantit que tous les paramètres sont appliqués et que le dernier conteneur est extrait du registre.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

Patientez environ 15 secondes pendant qu’App Service extrait l’image mise à jour. Revenez à la fenêtre du navigateur que vous avez ouverte à l’étape **Accéder à l’application**, puis actualisez la page.

![Mise à jour de l’exemple d’application s’exécutant dans Azure](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-azure-web-app"></a>Gérer votre application web Azure

Accédez au [portail Azure](https://portal.azure.com) pour voir l’application web que vous avez créée.

Dans le menu de gauche, cliquez sur **App Services**, puis cliquez sur le nom de votre application web Azure.

![Navigation au sein du portail pour accéder à l’application web Azure](./media/quickstart-python/app-service-list.png)

Par défaut, le portail affiche la page **Vue d’ensemble** de votre application web. Cette page propose un aperçu de votre application. Ici, vous pouvez également effectuer des tâches de gestion de base (parcourir, arrêter, démarrer, redémarrer et supprimer des éléments, par exemple). Les onglets sur le côté gauche de la page affichent les différentes pages de configuration que vous pouvez ouvrir.

![Page App Service du Portail Azure](./media/quickstart-python/app-service-detail.png)

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Python avec PostgreSQL](tutorial-docker-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Utiliser des images personnalisées](tutorial-custom-docker-image.md)