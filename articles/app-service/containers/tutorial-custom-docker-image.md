---
title: 'Tutoriel : Générer et exécuter une image personnalisée dans Azure App Service'
description: Guide pas à pas pour créer une image Linux personnalisée, envoyer (push) l’image à Azure Container Registry, puis déployer cette image dans Azure App Service.
keywords: azure app service, application web, linux, docker, conteneur
author: msangapu-msft
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: msangapu
ms.custom: mvc, seodec18, tracking-python
ms.openlocfilehash: bfe1e9fd2532e308c474aee6983615e28a8081f5
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86506833"
---
# <a name="tutorial-run-a-custom-docker-image-in-app-service"></a>Tutoriel : Exécuter une image Docker personnalisée dans App Service

App Services utilise la technologie de conteneur Docker pour héberger à la fois des images prédéfinies et des images personnalisées. Pour voir une liste d’images prédéfinies, exécutez la commande Azure CLI ['az webapp list-runtimes --linux'](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes). Si ces images ne répondent pas à vos besoins, vous pouvez générer et déployer une image personnalisée.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Générer une image personnalisée si aucune image prédéfinie ne répond à vos besoins
> * Envoyer (push) l’image personnalisée à un registre de conteneurs privé sur Azure
> * Exécuter l’image personnalisée dans App Service
> * Configuration des variables d’environnement
> * Mettre à jour et redéployer l’image
> * Accéder aux journaux de diagnostic
> * Se connecter au conteneur avec SSH

L’exécution de ce tutoriel entraîne des frais réduits dans votre compte Azure pour le registre de conteneurs et peut engendrer des coûts supplémentaires pour l’hébergement du conteneur pendant plus d’un mois.

## <a name="set-up-your-initial-environment"></a>Configurer votre environnement initial

* Vous devez disposer d’un compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Installez [Docker](https://docs.docker.com/get-started/#setup), que vous utilisez pour générer des images Docker. L’installation de Docker peut nécessiter un redémarrage de l’ordinateur.
* Installez l’interface <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a>2.0.80 ou ultérieur, avec laquelle vous exécutez des commandes dans n’importe quel interpréteur de commandes pour provisionner et configurer des ressources Azure.

Après l’installation de Docker et de l’interface de ligne de commande Azure CLI, ouvrez une fenêtre de terminal et vérifiez que Docker est installé :

```bash
docker --version
```

Vérifiez également que votre version d’Azure CLI est 2.0.80 ou une version ultérieure :

```azurecli
az --version
```

Connectez-vous ensuite à Azure par le biais de l’interface CLI :

```azurecli
az login
```

La commande `az login` ouvre un navigateur pour collecter vos informations d’identification. Une fois l’exécution de la commande terminée, elle affiche une sortie JSON contenant des informations sur vos abonnements.

Une fois connecté, vous pouvez exécuter des commandes Azure avec l’interface de ligne de commande Azure CLI pour utiliser des ressources de votre abonnement.

## <a name="clone-or-download-the-sample-app"></a>Cloner ou télécharger l’exemple d’application

Vous pouvez obtenir l’exemple de ce tutoriel par le biais d’un clone ou d’un téléchargement Git.

### <a name="clone-with-git"></a>Cloner avec Git

Clonez l’exemple de dépôt :

```terminal
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
```

Veillez à inclure l’argument `--config core.autocrlf=input` pour garantir des fins de ligne appropriées dans les fichiers qui sont utilisés dans le conteneur Linux :

Ensuite, accédez à ce dossier :

```terminal
cd docker-django-webapp-linux
```

### <a name="download-from-github"></a>Télécharger à partir de GitHub

Au lieu d’utiliser un clone Git, vous pouvez consulter [https://github.com/Azure-Samples/docker-django-webapp-linux](https://github.com/Azure-Samples/docker-django-webapp-linux), sélectionner **Cloner**, puis **Télécharger le Zip**. 

Décompressez le fichier Zip dans un dossier nommé *docker-django-webapp-linux*. 

Ouvrez ensuite une fenêtre de terminal dans ce dossier *docker-django-webapp-linux*.

## <a name="optional-examine-the-docker-file"></a>(Facultatif) Examiner le fichier Docker

Dans l’exemple, le fichier nommé _Dockerfile_ qui décrit l’image Docker et qui contient des instructions de configuration :

```Dockerfile
FROM tiangolo/uwsgi-nginx-flask:python3.6

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt --no-cache-dir
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

#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

* Le premier groupe de commandes installe la configuration requise de l’application dans l’environnement.
* Le deuxième groupe de commande crée un serveur [SSH](https://www.ssh.com/ssh/protocol/) pour sécuriser la communication entre le conteneur et l’hôte.
* Dans `ENTRYPOINT ["init.sh"]`, la dernière ligne appelle `init.sh` pour démarrer le service SSH et le serveur Python.

## <a name="build-and-test-the-image-locally"></a>Générer et tester l’image localement

1. Exécutez la commande suivante pour générer l’image :

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```
    
1. Vérifiez que la build fonctionne en exécutant le conteneur Docker localement :

    ```bash
    docker run -p 8000:8000 appsvc-tutorial-custom-image
    ```
    
    Cette commande [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) spécifie le port avec l’argument `-p` suivi du nom de l’image. 
    
    > [!TIP]
    > Si votre système d’exploitation est Windows et que vous voyez l’erreur *standard_init_linux.go:211: exec user process caused "no such file or directory"* (l’exécution du processus utilisateur a provoqué l’erreur « Pas de fichier ou de répertoire correspondant »), le fichier *init.sh* contient des fins de ligne CR-LF à la place des terminaisons LF attendues. Cette erreur se produit si vous avez utilisé Git pour cloner l’exemple de dépôt, mais que vous avez omis le paramètre `--config core.autocrlf=input`. Dans ce cas, reclonez le dépôt avec l’argument « --config ». L’erreur peut également s’afficher si vous avez modifié *init.sh* et que vous l’avez enregistré avec des terminaisons CRLF. Dans ce cas, enregistrez de nouveau le fichier avec uniquement des terminaisons LF.

1. Accédez à `http://localhost:8000` pour vérifier que l’application web et le conteneur fonctionnent correctement.

    ![Tester l’application web localement](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Dans cette section et celles qui suivent, vous allez configurer des ressources dans Azure vers lesquelles vous allez envoyer (push) l’image, puis déployer un conteneur sur Azure App Service. Vous allez commencer par créer un groupe de ressources dans lequel collecter toutes ces ressources.

Exécutez la commande [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) pour créer un groupe de ressources :

```azurecli-interactive
az group create --name AppSvc-DockerTutorial-rg --location westus2
```

Vous pouvez changer la valeur `--location` pour spécifier une région proche de chez vous.

## <a name="push-the-image-to-azure-container-registry"></a>Envoyer l’image vers Azure Container Registry

Dans cette section, vous envoyez (push) l’image à Azure Container Registry, emplacement à partir duquel App Service peut la déployer.

1. Exécutez la commande [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) pour créer un registre de conteneurs Azure (Azure Container Registry) :

    ```azurecli-interactive
    az acr create --name <registry-name> --resource-group AppSvc-DockerTutorial-rg --sku Basic --admin-enabled true
    ```
    
    Remplacez `<registry-name>` par un nom approprié pour votre registre. Le nom ne doit contenir que des lettres et des chiffres, et doit être unique dans tout Azure.

1. Exécutez la commande [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) afin de récupérer les informations d’identification pour le registre :

    ```azurecli-interactive
    az acr credential show --resource-group AppSvc-DockerTutorial-rg --name <registry-name>
    ```
    
    La sortie JSON de cette commande fournit deux mots de passe, ainsi que le nom d’utilisateur du registre.
    
1. Utilisez la commande `docker login` pour vous connecter au registre de conteneurs :

    ```bash
    docker login <registry-name>.azurecr.io --username <registry-username>
    ```
    
    Remplacez `<registry-name>` et `<registry-username>` par les valeurs des étapes précédentes. Lorsque vous y êtes invité, tapez l’un des mots de passe de l’étape précédente.

    Vous utilisez le même nom de registre dans toutes les étapes restantes de cette section.

1. Une fois la connexion établie, étiquetez votre image Docker locale pour le registre :

    ```bash
   docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```    

1. Utilisez la commande `docker push` pour envoyer (push) l’image vers le registre :

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    Le premier chargement de l’image peut prendre quelques minutes car il inclut l’image de base. Les chargements suivants sont généralement plus rapides.

    Pendant que vous êtes en attente, vous pouvez effectuer les étapes de la section suivante pour configurer App Service afin d’effectuer un déploiement à partir du registre.

1. Utilisez la commande `az acr repository list` pour vérifier que l’envoi a réussi :

    ```azurecli-interactive
    az acr repository list -n <registry-name>
    ```
    
    La sortie doit afficher le nom de votre image.


## <a name="configure-app-service-to-deploy-the-image-from-the-registry"></a>Configurer App Service pour déployer l’image à partir du registre

Pour déployer un conteneur sur Azure App Service, vous commencez par créer une application web sur App Service, puis vous connectez l’application web au registre de conteneurs. Quand l’application web démarre, App Service tire (pull) automatiquement l’image du registre.

1. Créez un plan App Service à l’aide de la commande [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) :

    ```azurecli-interactive
    az appservice plan create --name AppSvc-DockerTutorial-plan --resource-group AppSvc-DockerTutorial-rg --is-linux
    ```

    Un plan App Service correspond à la machine virtuelle qui héberge l’application web. Par défaut, la commande précédente utilise un [niveau tarifaire B1](https://azure.microsoft.com/pricing/details/app-service/linux/) pas cher qui est gratuit le premier mois. Vous pouvez contrôler le niveau à l’aide du paramètre `--sku`.

1. Créez l’application web à l’aide de la commande [`az webpp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) :

    ```azurecli-interactive
    az webapp create --resource-group AppSvc-DockerTutorial-rg --plan AppSvc-DockerTutorial-plan --name <app-name> --deployment-container-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```
    
    Remplacez `<app-name>` par le nom de l’application web, qui doit être unique dans tout Azure. Remplacez également `<registry-name>` par le nom de votre registre spécifié à la section précédente.

1. Utilisez [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) pour définir la variable d’environnement `WEBSITES_PORT` conformément aux attentes du code d’application : 

    ```azurecli-interactive
    az webapp config appsettings set --resource-group AppSvc-DockerTutorial-rg --name <app-name> --settings WEBSITES_PORT=8000
    ```

    Remplacez `<app-name>` par le nom que vous avez utilisé à l’étape précédente.
    
    Pour plus d’informations sur cette variable d’environnement, consultez le fichier [Lisez-moi disponible dans le dépôt GitHub de l’exemple](https://github.com/Azure-Samples/docker-django-webapp-linux).

1. Activez l’[identité managée](/azure/app-service/overview-managed-identity) pour l’application web à l’aide de la commande [`az webapp identity assign`](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) :

    ```azurecli-interactive
    az webapp identity assign --resource-group AppSvc-DockerTutorial-rg --name <app-name> --query principalId --output tsv
    ```

    Remplacez `<app-name>` par le nom que vous avez utilisé à l’étape précédente. La sortie de la commande (filtrée par les arguments `--query` et `--output`) est le principal du service de l’identité affectée, que vous utiliserez sous peu.

    L’identité managée vous permet d’accorder des autorisations à l’application web pour accéder à d’autres ressources Azure sans qu’aucune information d’identification spécifique ne soit nécessaire.

1. Récupérez votre ID d’abonnement avec la commande [`az account show`](/cli/azure/account?view=azure-cli-latest#az-account-show). Vous en aurez besoin à la prochaine étape :

    ```azurecli-interactive
    az account show --query id --output tsv
    ``` 

1. Accordez à l’application web l’autorisation d’accéder au registre de conteneurs :

    ```azurecli-interactive
    az role assignment create --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourceGroups/AppSvc-DockerTutorial-rg/providers/Microsoft.ContainerRegistry/registries/<registry-name> --role "AcrPull"
    ```

    Remplacez les valeurs suivantes :
    - `<principal-id>` par l’ID de principal du service récupéré à partir de la commande `az webapp identity assign`
    - `<registry-name>` par le nom de votre registre de conteneurs
    - `<subscription-id>` par l’ID d’abonnement récupéré à partir de la commande `az account show`

Pour plus d’informations sur ces autorisations, consultez [Présentation du contrôle d’accès en fonction du rôle Azure](/azure/role-based-access-control/overview) et 

## <a name="deploy-the-image-and-test-the-app"></a>Déployer l’image et tester l’application

Vous pouvez effectuer ces étapes une fois que l’image est envoyée (par push) au registre de conteneurs et qu’App Service est entièrement provisionné.

1. Utilisez la commande [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) pour spécifier le registre de conteneurs et l’image à déployer pour l’application web :

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-custom-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest --docker-registry-server-url https://<registry-name>.azurecr.io
    ```
    
    Remplacez `<app_name>` par le nom de votre application web et remplacez `<registry-name>` à deux endroits par le nom de votre registre. 

    - Quand vous utilisez un registre autre que Docker Hub (comme le montre cet exemple), `--docker-registry-server-url` doit être au format `https://` suivi du nom de domaine complet du registre.
    - Le message « No credential was provided to access Azure Container Registry. Trying to look up...» (Aucune information d’identification n’a été fournie pour accéder à Azure Container Registry. Tentative de recherche... ») vous indique qu’Azure utilise l’identité managée de l’application pour s’authentifier auprès du registre de conteneurs au lieu de demander un nom d’utilisateur et un mot de passe.
    - Si l’erreur « AttributeError : 'NoneType' object has no attribute 'reserved' » (L’objet 'NoneType' n’a pas d’attribut 'reserved') s’affiche, vérifiez que votre valeur `<app-name>` est correcte.

    > [!TIP]
    > Vous pouvez récupérer les paramètres de conteneur de l’application web à tout moment avec la commande `az webapp config container show --name <app-name> --resource-group AppSvc-DockerTutorial-rg`. L’image est spécifiée dans la propriété `DOCKER_CUSTOM_IMAGE_NAME`. Quand l’application web est déployée par le biais de modèles Azure DevOps ou Azure Resource Manager, l’image peut également apparaître dans une propriété nommée `LinuxFxVersion`. Les deux propriétés remplissent la même fonction. Si les deux sont présentes dans la configuration de l’application web, `LinuxFxVersion` est prioritaire.

1. Une fois l’exécution de la commande `az webapp config container set` terminée, l’application web doit s’exécuter dans le conteneur sur App Service.

    Pour tester l’application, accédez à `http://<app-name>.azurewebsites.net`, en remplaçant `<app-name>` par le nom de votre application web. Quand l’utilisateur accède à l’application pour la première fois, celle-ci peut mettre un certain temps à répondre car App Service doit tirer (pull) l’image entière du registre. En cas d’expiration du délai d’attente du navigateur, actualisez simplement la page. Une fois l’image initiale tirée (pull), les tests suivants s’exécutent beaucoup plus rapidement.

    ![Test réussi de l’application web sur Azure](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="modify-the-app-code-and-redeploy"></a>Modifier le code d’application et effectuer au redéploiement

Dans cette section, vous apportez un changement au code de l’application web, vous regénérez le conteneur, puis envoyez (push) le conteneur vers le registre. App Service tire (pull) ensuite automatiquement l’image mise à jour du registre pour mettre à jour l’application web en cours d’exécution.

1. Dans votre dossier *docker-django-webapp-linux* local, ouvrez le fichier *app/templates/app/index.html*.

1. Modifiez le premier élément HTML pour qu’il corresponde au code suivant.

    ```html
    <nav class="navbar navbar-inverse navbar-fixed-top">
      <div class="container">
        <div class="navbar-header">
          <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
        </div>
      </div>
    </nav>
    ```
    
1. Enregistrez vos modifications.

1. Accédez au dossier *docker-django-webapp-linux*, puis regénérez l’image :

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```

1. Mettez à jour le numéro de version indiqué dans la balise de l’image vers v1.0.1 :

    ```bash
    docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    Remplacez `<registry-name>` par le nom de votre registre.

1. Envoyez l’image vers le registre :

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

1. Redémarrez l’application web :

    ```azurecli-interactive
    az webapp restart --name <app_name> --resource-group AppSvc-DockerTutorial-rg
    ```

    Remplacez `<app_name>` par le nom de votre application web. Lors du redémarrage, App Service tire (pull) l’image mise à jour du registre de conteneurs.

1. Vérifiez que la mise à jour a été déployée en accédant à `http://<app-name>.azurewebsites.net`.

## <a name="access-diagnostic-logs"></a>Accéder aux journaux de diagnostic

1. Activez la journalisation de conteneur :

    ```azurecli-interactive
    az webapp log config --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-container-logging filesystem
    ```
    
1. Activez le flux de journaux :

    ```azurecli-interactive
    az webapp log tail --name <app-name> --resource-group AppSvc-DockerTutorial-rg
    ```
    
    Si vous ne voyez pas les journaux d’activité de la console, attendez 30 secondes et vérifiez à nouveau.

    Vous pouvez également inspecter les fichiers journaux à partir du navigateur sur `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

1. Pour arrêter le streaming de journaux à tout moment, appuyez sur **Ctrl**+**C**.

## <a name="connect-to-the-container-using-ssh"></a>Se connecter au conteneur avec SSH

SSH permet d’établir une communication sécurisée entre un conteneur et un client. Pour activer la connexion SSH à votre conteneur, votre image personnalisée doit être configurée pour cela. Une fois que le conteneur est en cours d’exécution, vous pouvez ouvrir une connexion SSH.

### <a name="configure-the-container-for-ssh"></a>Configurer le conteneur pour SSH

L’exemple d’application utilisé dans ce tutoriel dispose déjà de la configuration nécessaire dans le fichier *Dockerfile*, qui installe le serveur SSH et définit les informations d’identification de connexion. Cette section est fournie à titre d’information uniquement. Pour vous connecter au conteneur, passez à la section suivante.

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

Le fichier *Dockerfile* copie également le fichier *sshd_config* dans le dossier */etc/ssh/* et expose le port 2222 sur le conteneur :

```Dockerfile
COPY sshd_config /etc/ssh/

# ...

EXPOSE 8000 2222
```

Le port 2222 est un port interne accessible uniquement par les conteneurs présents dans le réseau de pont d’un réseau virtuel privé. 

Enfin, le script d’entrée *init.sh* démarre le serveur SSH.

```bash
#!/bin/bash
service ssh start
```

### <a name="open-ssh-connection-to-container"></a>Ouvrir la connexion SSH au conteneur

1. Accédez à `https://<app-name>.scm.azurewebsites.net/webssh/host` et connectez-vous avec votre compte Azure. Remplacez `<app-name>` par le nom de votre application web.

1. Une fois connecté, vous êtes redirigé vers une page d’informations concernant l’application web. Sélectionnez **SSH** en haut de la page pour ouvrir l’interpréteur de commandes et utiliser des commandes.

    Par exemple, vous pouvez examiner les processus en cours d’exécution dans celui-ci à l’aide de la commande `top`.
    
## <a name="clean-up-resources"></a>Nettoyer les ressources

Les ressources que vous avez créées avec cet article peuvent occasionner des frais récurrents. Pour nettoyer les ressources, il vous suffit de supprimer le groupe de ressources qui les contient :

```azurecli
az group delete --name AppSvc-DockerTutorial-rg
```

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Déployer une image personnalisée dans un Registre de conteneurs privé
> * Déployer et exécuter l’image personnalisée dans App Service
> * Mettre à jour et redéployer l’image
> * Accéder aux journaux de diagnostic
> * Se connecter au conteneur avec SSH

Dans le tutoriel suivant, vous allez apprendre à mapper un nom DNS personnalisé à votre application.

> [!div class="nextstepaction"]
> [Tutoriel : Mappage d’un nom DNS personnalisé à une application](../app-service-web-tutorial-custom-domain.md)

Ou consultez les autres ressources :

> [!div class="nextstepaction"]
> [Configurer un conteneur personnalisé](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Tutoriel : Application WordPress avec plusieurs conteneurs](tutorial-multi-container-app.md)
