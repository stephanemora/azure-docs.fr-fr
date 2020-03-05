---
title: Configurer un conteneur Linux personnalisé
description: Découvrez comment configurer un conteneur Linux personnalisé dans Azure App Service. Cet article présente les tâches de configuration les plus courantes.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 6baa1fbd4932aa83a54081ff166dcae7f258fff9
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255881"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Configurer un conteneur Linux personnalisé pour Azure App Service

Cet article vous montre comment configurer un conteneur Linux personnalisé à exécuter sur Azure App Service.

Ce guide fournit les principaux concepts et des instructions pour la mise en conteneur d’applications Linux dans App Service. Si vous n’avez jamais utilisé Azure App Service, commencez par suivre le [démarrage rapide](quickstart-docker-go.md) et le [tutoriel](tutorial-custom-docker-image.md) sur le conteneur personnalisé. Il existe également un [démarrage rapide](quickstart-multi-container.md) et un [tutoriel](tutorial-multi-container-app.md) pour les applications à conteneurs multiples.

## <a name="configure-port-number"></a>Configurer le numéro de port

Le serveur web dans votre image personnalisée peut utiliser un port autre que 80. Utilisez le paramètre d’application `WEBSITES_PORT` pour indiquer à Azure le port utilisé par votre conteneur personnalisé. La page GitHub de l’[exemple Python dans ce didacticiel](https://github.com/Azure-Samples/docker-django-webapp-linux) montre que vous devez définir `WEBSITES_PORT` avec la valeur _8000_. Vous pouvez la définir en exécutant la commande [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) dans Cloud Shell. Par exemple :

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Configuration des variables d’environnement

Votre conteneur personnalisé peut utiliser des variables d’environnement qui doivent être fournies en externe. Vous pouvez les transmettre en exécutant la commande [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) dans Cloud Shell. Par exemple :

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Cette méthode fonctionne à la fois pour les applications à conteneur unique et à plusieurs conteneurs, où les variables d’environnement sont spécifiées dans le fichier *docker-compose.yml*.

## <a name="use-persistent-shared-storage"></a>Utiliser le stockage partagé persistant

Vous pouvez utiliser le répertoire */home* dans le système de fichiers de votre application pour rendre persistants les fichiers entre les redémarrages et les partager entre plusieurs instances. Le répertoire `/home` dans votre application est fourni pour permettre à votre application de conteneur d’accéder au stockage persistant.

Lorsque le stockage persistant est désactivé, les écritures dans le répertoire `/home` ne sont pas rendues persistantes entre les redémarrages d’applications ou entre plusieurs instances. La seule exception concerne le répertoire `/home/LogFiles`, qui est utilisé pour stocker les journaux Docker et de conteneurs. Lorsque le stockage persistant est activé, toutes les écritures dans le répertoire `/home` sont rendues persistantes et sont accessibles par toutes les instances d’une application avec scale-out.

Par défaut, le stockage persistant est *activé* et le paramètre n’est pas exposé dans les paramètres d’application. Pour le désactiver, définissez le paramètre d’application `WEBSITES_ENABLE_APP_SERVICE_STORAGE` en exécutant la commande [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) dans Cloud Shell. Par exemple :

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> Vous pouvez également [configurer votre propre stockage persistant](how-to-serve-content-from-azure-storage.md).

## <a name="enable-ssh"></a>Activation de SSH

SSH permet d’établir une communication sécurisée entre un conteneur et un client. Pour qu’un conteneur personnalisé prenne en charge SSH, vous devez l’ajouter dans le fichier Dockerfile lui-même.

> [!TIP]
> Tous les conteneurs Linux intégrés ont ajouté les instructions SSH dans leurs dépôts d’images. Vous pouvez parcourir les instructions suivantes avec le [dépôt Node.js 10.14](https://github.com/Azure-App-Service/node/blob/master/10.14) afin de déterminer comment il y est activé.

- Utilisez l’instruction [Run](https://docs.docker.com/engine/reference/builder/#run) pour installer le serveur SSH et définir le mot de passe du compte racine sur `"Docker!"`. Par exemple, pour une image basée sur [Alpine Linux](https://hub.docker.com/_/alpine), vous avez besoin des commandes suivantes :

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Cette configuration n’autorise pas les connexions externes avec le conteneur. SSH est disponible uniquement via `https://<app-name>.scm.azurewebsites.net` et authentifié avec les informations d’identification de publication.

- Ajoutez [ce fichier sshd_config](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) à votre dépôt d’images et utilisez l’instruction [Copy](https://docs.docker.com/engine/reference/builder/#copy) pour copier le fichier dans le répertoire */etc/ssh/* . Pour plus d’informations sur les fichiers *sshd_config*, consultez [la documentation OpenBSD](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Le fichier *sshd_config* doit inclure les éléments suivants :
    > - `Ciphers` doit inclure au moins un élément dans cette liste : `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs` doit inclure au moins un élément dans cette liste : `hmac-sha1,hmac-sha1-96`.

- Utilisez l’instruction [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) pour ouvrir le port 2222 dans le conteneur. Bien que le mot de passe racine soit connu, le port 2222 n’est pas accessible à partir d’Internet. Il est accessible uniquement par les conteneurs au sein du pont réseau d’un réseau privé virtuel.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- Dans le script de démarrage pour votre conteneur, démarrez le serveur SSH.

    ```bash
    /usr/sbin/sshd
    ```

    Pour obtenir un exemple, voyez comment le [conteneur Node.js 10.14 par défaut](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) démarre le serveur SSH.

## <a name="access-diagnostic-logs"></a>Accéder aux journaux de diagnostic

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>Configurer des applications à plusieurs conteneurs

- [Utiliser le stockage persistant dans Docker Compose](#use-persistent-storage-in-docker-compose)
- [Limitations de la préversion](#preview-limitations)
- [Options Docker Compose](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Utiliser le stockage persistant dans Docker Compose

Les applications multiconteneurs telles que WordPress nécessitent un stockage persistant pour fonctionner correctement. Pour l’activer, votre configuration Docker Compose doit pointer vers un emplacement de stockage *à l’extérieur* de votre conteneur. Les emplacements de stockage à l’intérieur de votre conteneur ne rendent pas les modifications persistantes au-delà du redémarrage de l’application.

Activez le stockage persistant en définissant le paramètre d’application `WEBSITES_ENABLE_APP_SERVICE_STORAGE`, à l’aide de la commande [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) dans Cloud Shell.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Dans votre fichier *docker-compose.yml*, mappez l’option `volumes` sur `${WEBAPP_STORAGE_HOME}`. 

`WEBAPP_STORAGE_HOME` est une variable d’environnement d’App Service mappée sur le stockage persistant de votre application. Par exemple :

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Limitations de la version préliminaire

La fonctionnalité multiconteneurs est actuellement en préversion. Les fonctionnalités suivantes de la plateforme App Service ne sont pas prises en charge :

- Authentification / Autorisation
- Identités managées

### <a name="docker-compose-options"></a>Options Docker Compose

Les listes suivantes présentent des options de configuration Docker Compose prises en charge et non prises en charge :

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
> Les autres options qui ne sont pas explicitement appelées sont ignorées dans la préversion publique.

## <a name="configure-vnet-integration"></a>Configurer l’intégration au réseau virtuel

L’utilisation d’un conteneur personnalisé avec l’intégration au réseau virtuel peut nécessiter une configuration de conteneur supplémentaire. Consultez [Intégrer une application à un réseau virtuel Azure](../web-sites-integrate-with-vnet.md).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Déployer à partir du référentiel de conteneurs privé](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Tutoriel : Application WordPress avec plusieurs conteneurs](tutorial-multi-container-app.md)
