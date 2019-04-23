---
title: Configurer des conteneurs de client - Azure App Service | Microsoft Docs
description: Découvrez comment configurer des applications Node.js à travailler dans Azure App Service
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
ms.openlocfilehash: 1e5faa8d356b891d825586414c0a1a1b9fa47090
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001879"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Configurer un conteneur Linux personnalisé pour Azure App Service

Cet article vous montre comment configurer un conteneur Linux personnalisé à exécuter sur Azure App Service.

Ce guide fournit les principaux concepts et des instructions pour la mise en conteneur d’applications Linux dans App Service. Si vous n’avez jamais utilisé Azure App Service, suivez les [démarrage rapide de conteneur personnalisé](quickstart-docker-go.md) et [didacticiel](tutorial-custom-docker-image.md) première. Il existe également un [démarrage rapide d’application à conteneurs multiples](quickstart-multi-container.md) et [didacticiel](tutorial-multi-container-app.md).

## <a name="configure-port-number"></a>Configurer le numéro de port

Le serveur web dans votre image personnalisée peut utiliser un port autre que 80. Indiquer à Azure sur le port utilisé par votre personnalisé à l’aide de le `WEBSITES_PORT` paramètre d’application. La page GitHub de l’[exemple Python dans ce didacticiel](https://github.com/Azure-Samples/docker-django-webapp-linux) montre que vous devez définir `WEBSITES_PORT` avec la valeur _8000_. Vous pouvez la définir en exécutant [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) commande dans Cloud Shell. Par exemple : 

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Configuration des variables d’environnement

Votre conteneur personnalisé peut utiliser des variables d’environnement qui doivent être fournies en externe. Vous pouvez les transmettre dans en exécutant [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) commande dans Cloud Shell. Par exemple : 

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Cette méthode fonctionne à la fois pour les applications de conteneur unique ou plusieurs conteneurs, où les variables d’environnement sont spécifiés dans le *docker-compose.yml* fichier.

## <a name="use-persistent-shared-storage"></a>Utiliser le stockage partagé persistant

Vous pouvez utiliser la */home* répertoire dans le système de fichiers de votre application pour conserver les fichiers entre les redémarrages et les partager entre plusieurs instances. Le `/home` dans votre application est fournie pour permettre à votre application conteneur accéder au stockage persistant.

Lorsque le stockage persistant est désactivé, les écrit dans le `/home` directory ne sont pas conservées entre les redémarrages d’application ou entre plusieurs instances. La seule exception concerne le `/home/LogFiles` directory, qui est utilisé pour stocker les journaux Docker et des conteneurs. Lorsque le stockage persistant est activé, toutes les écritures dans le `/home` directory sont conservées et sont accessibles par toutes les instances d’une application à grande échelle.

Par défaut, le stockage persistant est *désactivé*. Pour activer ou désactiver, définissez la `WEBSITES_ENABLE_APP_SERVICE_STORAGE` paramètre d’application en exécutant [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) commande dans Cloud Shell. Par exemple : 

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

> [!NOTE]
> Vous pouvez également [configurer votre propre stockage persistant](how-to-serve-content-from-azure-storage.md).

## <a name="enable-ssh"></a>Activation de SSH

SSH permet d’établir une communication sécurisée entre un conteneur et un client. Dans l’ordre pour un conteneur personnalisé prendre en charge SSH, vous devez l’ajouter dans le fichier Dockerfile lui-même.

> [!TIP]
> Tous les conteneurs Linux intégrés ont ajouté les instructions SSH dans leurs référentiels d’images. Vous pouvez parcourir les instructions suivantes avec le [Node.js 10.14 référentiel](https://github.com/Azure-App-Service/node/blob/master/10.14) afin de déterminer comment il est activé il.

- Utilisez le [exécuter](https://docs.docker.com/engine/reference/builder/#run) instructions pour installer le serveur SSH et de définir le mot de passe du compte racine sur `"Docker!"`. Par exemple, pour une image basée sur [Linux Alpine](https://hub.docker.com/_/alpine), vous devez les commandes suivantes :

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Cette configuration n’autorise pas les connexions externes au conteneur. SSH est disponible uniquement via `https://<app-name>.scm.azurewebsites.net` et authentifié avec les informations d’identification de publication.

- Ajouter [ce fichier sshd_config](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) à votre référentiel d’images et utiliser le [copie](https://docs.docker.com/engine/reference/builder/#copy) instruction pour copier le fichier à la */etc/ssh/* directory. Pour plus d’informations sur *sshd_config* de fichiers, consultez [OpenBSD documentation](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Le fichier *sshd_config* doit inclure les éléments suivants :
    > - `Ciphers` doit inclure au moins un élément dans cette liste : `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs` doit inclure au moins un élément dans cette liste : `hmac-sha1,hmac-sha1-96`.

- Utilisez le [exposer](https://docs.docker.com/engine/reference/builder/#expose) instruction pour ouvrir le port 2222 dans le conteneur. Bien que le mot de passe racine soit connu, le port 2222 n’est pas accessible à partir d’internet. Il est accessible uniquement par les conteneurs au sein du réseau de pont d’un réseau privé virtuel.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- Dans le script de démarrage pour votre conteneur, démarrez le serveur SSH.

    ```bash
    /usr/sbin/sshd
    ```

    Pour obtenir un exemple, voir comment la valeur par défaut [Node.js 10.14 conteneur](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) démarre le serveur SSH.

## <a name="access-diagnostic-logs"></a>Accéder aux journaux de diagnostic

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>Configurer des applications à plusieurs conteneurs

- [Utiliser le stockage persistant dans Docker Compose](#use-persistent-storage-in-docker-compose)
- [Limitations de la version préliminaire](#preview-limitations)
- [Options de docker Compose](#docker-compose-options)
- [Options de configuration Kubernetes](#kubernetes-configuration-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Utiliser le stockage persistant dans Docker Compose

Plusieurs conteneurs d’applications telles que WordPress nécessitent un stockage persistant pour fonctionner correctement. Pour l’activer, votre configuration de Docker Compose doit pointer vers un emplacement de stockage *en dehors de* votre conteneur. Emplacements de stockage à l’intérieur de votre conteneur ne persistent pas modifications au-delà de redémarrage de l’application.

Activer le stockage persistant en définissant le `WEBSITES_ENABLE_APP_SERVICE_STORAGE` application la définition, à l’aide de la [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) dans Cloud Shell.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Dans votre *docker-compose.yml* de fichiers, mappez le `volumes` option `${WEBAPP_STORAGE_HOME}`. 

`WEBAPP_STORAGE_HOME` est une variable d’environnement d’App Service mappée sur le stockage persistant de votre application. Par exemple : 

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="use-custom-storage-in-docker-compose"></a>Utilisation du stockage personnalisé dans Docker Compose

Stockage Azure (Azure Files ou objets Blob Azure) peut être monté avec des applications à plusieurs conteneurs à l’aide de l’id personnalisé. Pour afficher le nom personnalisé-id, exécutez [ `az webapp config storage-account list --name <app_name> --resource-group <resource_group>` ](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list).

Dans votre *docker-compose.yml* de fichiers, mappez le `volumes` option `custom-id`. Par exemple : 

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

### <a name="preview-limitations"></a>Limitations de la version préliminaire

Plusieurs conteneurs est actuellement en version préliminaire. Les fonctionnalités de plateforme App Service suivantes ne sont pas prises en charge :

- Authentification / Autorisation
- Identités managées

### <a name="docker-compose-options"></a>Options de docker Compose

Les listes suivantes présentent des options configuration Docker Compose prises en charge et non pris en charge :

#### <a name="supported-options"></a>Options prises en charge

- command
- entrypoint
- Environnement
- image
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>Options non prises en charge

- build (non autorisée)
- depends_on (ignorée)
- networks (ignorée)
- secrets (ignorée)
- ports autres que 80 et 8080 (ignorées)

> [!NOTE]
> D’autres options pas explicitement indiquées sont ignorées en version préliminaire publique.

### <a name="kubernetes-configuration-options"></a>Options de configuration Kubernetes

Les options de configuration suivantes sont prises en charge pour Kubernetes :

- args
- command
- containers
- image
- Nom
- ports
- spec

> [!NOTE]
> D’autres options pas explicitement indiquées ne sont pas pris en charge en version préliminaire publique.
>

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Déployer à partir du référentiel de conteneurs privé](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Tutoriel : Application WordPress avec plusieurs conteneurs](tutorial-multi-container-app.md)
