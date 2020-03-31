---
title: 'Tutoriel : Créer et exécuter une image personnalisée'
description: Découvrez comment créer une image Linux personnalisée qui peut s’exécuter sur Azure App Service, la déployer dans des registres de conteneurs Azure et l’exécuter dans App Service.
keywords: azure app service, application web, linux, docker, conteneur
author: msangapu-msft
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: a6c9eb354bce09a5f652895f4af34df1f6750bec
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80045755"
---
# <a name="tutorial-build-a-custom-image-and-run-in-app-service-from-a-private-registry"></a>Tutoriel : Créer une image personnalisée et l’exécuter dans App Service à partir d’un Registre privé

[App Service](app-service-linux-intro.md) fournit des images Docker intégrées sur Linux avec prise en charge de versions spécifiques, telles que PHP 7.3 et Node.js 10.14. App Services utilise la technologie de conteneur Docker pour héberger à la fois des images intégrées et des images personnalisées en tant que service PaaS (platform as a service). Dans ce tutoriel, vous allez apprendre à créer une image personnalisée et à l’exécuter dans App Service. Ce modèle est utile quand les images intégrées n’incluent pas la langue de votre choix ou quand votre application nécessite une configuration spécifique qui n’est pas fournie dans les images intégrées.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Déployer une image personnalisée dans un Registre de conteneurs privé
> * Exécuter l’image personnalisée dans App Service
> * Configuration des variables d’environnement
> * Mettre à jour et redéployer l’image
> * Accéder aux journaux de diagnostic
> * Se connecter au conteneur avec SSH

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* [Git](https://git-scm.com/downloads)
* [Docker](https://docs.docker.com/get-started/#setup)

## <a name="download-the-sample"></a>Télécharger l’exemple

Dans une fenêtre de terminal, exécutez la commande ci-après pour cloner le référentiel de l’exemple d’application sur votre ordinateur local, puis accédez au répertoire contenant l’exemple de code.

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>Créer l’image à partir du fichier Docker

Dans le dépôt Git, examinez _Dockerfile_. Ce fichier décrit l’environnement Python exigé pour exécuter votre application. En outre, l’image définit un serveur [SSH](https://www.ssh.com/ssh/protocol/) pour sécuriser la communication entre le conteneur et l’hôte. La dernière ligne dans _Dockerfile_, `ENTRYPOINT ["init.sh"]`, appelle `init.sh` pour démarrer le service SSH et le serveur Python.

```Dockerfile
FROM python:3.4

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222

#service SSH start
#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

Créer l’image Docker avec la commande `docker build`.

```bash
docker build --tag mydockerimage .
```

Vérifiez que la build fonctionne en exécutant le conteneur Docker. Exécutez la commande [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) puis passez le nom et la balise de l’image. Veillez à spécifier le port à l’aide de l’argument `-p`.

```bash
docker run -p 8000:8000 mydockerimage
```

Accédez à `http://localhost:8000` pour vérifier que l’application web et le conteneur fonctionnent correctement.

![Tester l’application web localement](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Déployer des applications dans Azure

Pour créer une application qui utilise l’image que vous venez de créer, vous exécutez les commandes Azure CLI qui créent un groupe de ressources, envoient l’image, puis créent l’application web du plan App Service permettant de l’exécuter.

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-container-registry"></a>Création d’un Azure Container Registry

Dans Cloud Shell, utilisez la commande [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) pour créer un registre de conteneurs Azure Container Registry.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

### <a name="sign-in-to-azure-container-registry"></a>Se connecter à Azure Container Registry

Pour envoyer une image au Registre, vous devez vous authentifier auprès du Registre privé. Dans Cloud Shell, utilisez la commande [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) pour récupérer les informations d’identification à partir du Registre que vous avez créé.

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

La sortie indique deux mots de passe ainsi que le nom d’utilisateur.

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "{password}"
    },
    {
      "name": "password2",
      "value": "{password}"
    }
  ],
  "username": "<registry-username>"
}
```

Dans la fenêtre de terminal locale, connectez-vous à Azure Container Registry à l’aide de la commande `docker login`, comme le montre l’exemple suivant. Remplacez *\<azure-container-registry-name>* et *\<registry-username>* par des valeurs de votre Registre. Lorsque vous y êtes invité, tapez l’un des mots de passe de l’étape précédente.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

Vérifiez que la connexion est bien établie.

### <a name="push-image-to-azure-container-registry"></a>Envoyer l’image à Azure Container Registry

Marquez votre image locale pour Azure Container Registry. Par exemple :
```bash
docker tag mydockerimage <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Envoyez l’image à l’aide de la commande `docker push`. Identifiez l’image avec le nom du registre, suivi du nom et de la balise de l’image.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Dans Cloud Shell, vérifiez que l’envoi a réussi.

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

Vous devez obtenir la sortie suivante.

```json
[
  "mydockerimage"
]
```

### <a name="create-app-service-plan"></a>Créer un plan App Service

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-web-app"></a>Créer une application web

Dans Cloud Shell, créez une [application web](app-service-linux-intro.md) dans le plan App Service `myAppServicePlan` avec la commande [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Remplacez _\<app-name>_ par un nom d’application unique et _\<azure-container-registry-name>_ par le nom de votre Registre.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --deployment-container-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
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
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-registry-credentials-in-web-app"></a>Configurer les informations d’identification du Registre dans l’application web

Pour extraire l’image privée, App Service a besoin d’informations sur votre Registre et sur l’image. Dans Cloud Shell, fournissez-lui la commande [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set). Remplacez *\<app-name>* , *\<azure-container-registry-name>* , _\<registry-username>_ et _\<password>_ .

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0 --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> Lorsque vous utilisez un registre autre que Docker Hub, `--docker-registry-server-url` doit être formaté en tant que `https://` suivi du nom de domaine complet du registre.
>

### <a name="configure-environment-variables"></a>Configuration des variables d’environnement

La plupart des images Docker utilisent des variables d’environnement personnalisées, par exemple un port autre que 80. Utilisez le paramètre d’application `WEBSITES_PORT` pour indiquer à App Service le port utilisé par votre image. La page GitHub de l’[exemple Python dans ce didacticiel](https://github.com/Azure-Samples/docker-django-webapp-linux) montre que vous devez définir `WEBSITES_PORT` avec la valeur _8000_.

Pour définir les paramètres de l’application, utilisez la commande [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) dans Cloud Shell. Les paramètres d’application respectent la casse et sont séparés par des espaces.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_PORT=8000
```

### <a name="test-the-web-app"></a>Tester l’application web

Accédez à `http://<app-name>.azurewebsites.net` pour vérifier que l’application web fonctionne.

> [!NOTE]
> Le premier accès à l’application peut prendre un certain temps, car App Service doit extraire toute l’image. En cas d’expiration du délai d’attente du navigateur, actualisez simplement la page.

![Test de la configuration du port de l’application web](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>Modification de l’application web et redéploiement

Dans votre dépôt Git local, ouvrez app/templates/app/index.html. Localisez le premier élément HTML et changez-le.

```python
<nav class="navbar navbar-inverse navbar-fixed-top">
    <div class="container">
      <div class="navbar-header">
        <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
      </div>
    </div>
  </nav>
```

Après avoir modifié et enregistré le fichier Python, vous devez reconstruire et transmettre la nouvelle image Docker. Redémarrez ensuite l’application web pour que les modifications prennent effet. Utilisez les mêmes commandes que vous avez utilisées précédemment dans ce didacticiel. Si vous le souhaitez, consultez [Créer l’image à partir du fichier Docker](#build-the-image-from-the-docker-file) et [Envoyer l’image à Azure Container Registry](#push-image-to-azure-container-registry). Testez l’application web en suivant les instructions dans [Tester l’application web](#test-the-web-app).

## <a name="access-diagnostic-logs"></a>Accéder aux journaux de diagnostic

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="enable-ssh-connections"></a>Activer les connexions SSH

SSH permet d’établir une communication sécurisée entre un conteneur et un client. Pour activer la connexion SSH à votre conteneur, votre image personnalisée doit être configurée pour cela. Jetons un coup d’œil sur l’exemple de référentiel qui a déjà la configuration nécessaire.

* Dans le fichier [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile), le code suivant installe le serveur SSH et définit également les informations d’identification de connexion.

    ```Dockerfile
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Cette configuration n’autorise pas les connexions externes avec le conteneur. SSH est disponible uniquement via le site Kudu/SCM. Le site Kudu/SCM est authentifié avec votre compte Azure.

* Le fichier [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L18) copie le fichier [sshd_config](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/sshd_config) du référentiel dans le répertoire */etc/ssh/* .

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

* Le fichier [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L22) expose le port 2222 dans le conteneur. Il s’agit d’un port interne accessible uniquement par les conteneurs au sein du réseau de pont d’un réseau privé virtuel. 

    ```Dockerfile
    EXPOSE 8000 2222
    ```

* Le [script d’entrée](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/init.sh#L5) démarre le serveur SSH.

    ```bash
    #!/bin/bash
    service ssh start
    ```

### <a name="open-ssh-connection-to-container"></a>Ouvrir la connexion SSH au conteneur

La connexion SSH est disponible seulement via le site Kudu, qui est accessible sur `https://<app-name>.scm.azurewebsites.net`.

Pour vous connecter, accédez à `https://<app-name>.scm.azurewebsites.net/webssh/host` et connectez-vous avec votre compte Azure.

Vous êtes ensuite redirigé vers une page affichant une console interactive.

Vous pouvez vérifier que certaines applications sont en cours d’exécution dans le conteneur. Pour inspecter le conteneur et vérifier les processus en cours d’exécution, exécutez `top` à l’invite de commandes.

```bash
top
```

La commande `top` expose tous les processus en cours d’exécution dans un conteneur.

```
PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 1 root      20   0  945616  35372  15348 S  0.0  2.1   0:04.63 node
20 root      20   0   55180   2776   2516 S  0.0  0.2   0:00.00 sshd
42 root      20   0  944596  33340  15352 S  0.0  1.9   0:05.80 node /opt/s+
56 root      20   0   59812   5244   4512 S  0.0  0.3   0:00.93 sshd
58 root      20   0   20228   3128   2664 S  0.0  0.2   0:00.00 bash
62 root      20   0   21916   2272   1944 S  0.0  0.1   0:03.15 top
63 root      20   0   59812   5344   4612 S  0.0  0.3   0:00.03 sshd
65 root      20   0   20228   3140   2672 S  0.0  0.2   0:00.00 bash
71 root      20   0   59812   5380   4648 S  0.0  0.3   0:00.02 sshd
73 root      20   0   20228   3160   2696 S  0.0  0.2   0:00.00 bash
77 root      20   0   21920   2304   1972 R  0.0  0.1   0:00.00 top
```

Félicitations ! Vous avez configuré un conteneur Linux personnalisé dans App Service.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Déployer une image personnalisée dans un Registre de conteneurs privé
> * Exécuter l’image personnalisée dans App Service
> * Configuration des variables d’environnement
> * Mettre à jour et redéployer l’image
> * Accéder aux journaux de diagnostic
> * Se connecter au conteneur avec SSH

Passez au didacticiel suivant pour découvrir comment mapper un nom DNS personnalisé à votre application.

> [!div class="nextstepaction"]
> [Tutoriel : Mappage d’un nom DNS personnalisé à une application](../app-service-web-tutorial-custom-domain.md)

Ou consultez les autres ressources :

> [!div class="nextstepaction"]
> [Configurer un conteneur personnalisé](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Tutoriel : Application WordPress avec plusieurs conteneurs](tutorial-multi-container-app.md)
