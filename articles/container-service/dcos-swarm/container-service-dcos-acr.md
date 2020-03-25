---
title: (DÉCONSEILLÉ) Utilisation d’Azure Container Registry avec un cluster DC/OS Azure
description: Utiliser un Azure Container Registry avec un cluster DC/OS dans Azure Container Service
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 03/23/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: 9e69b66c7cee5a6e012ad7ed2477556fa840bfb5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78402073"
---
# <a name="deprecated-use-acr-with-a-dcos-cluster-to-deploy-your-application"></a>(DÉCONSEILLÉ) Utiliser un ACR avec un cluster DC/OS pour déployer votre application

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Dans cet article, nous explorons comment utiliser Azure Container Registry avec un cluster de contrôleur de domaine/système d’exploitation. L’utilisation d’ACR permet de stocker en privé et de gérer des images de conteneur. Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Déployer Azure Container Registry (le cas échéant)
> * Configurer l’authentification ACR sur un cluster de contrôleur de domaine/système d’exploitation
> * Télécharger une image dans Azure Container Registry
> * Exécuter une image de conteneur depuis Azure Container Registry

Vous avez besoin d’un cluster DC/OS ACS pour suivre les étapes de ce didacticiel. Le cas échéant, cet [exemple de script](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) peut en créer un pour vous.

Ce didacticiel requiert Azure CLI version 2.0.4 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-azure-container-registry"></a>Déployer Azure Container Registry

Si nécessaire, créez un registre de conteneurs Azure à l’aide de la commande [créer az acr](/cli/azure/acr#az-acr-create). 

L’exemple suivant crée un registre avec un nom généré de façon aléatoire. Le registre est également configuré avec un compte d’administrateur à l’aide de `--admin-enabled` l’argument.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry$RANDOM --sku Basic
```

Une fois le registre créé, Azure CLI fournit les informations suivantes. Notez `name` et `loginServer`, utilisés pour les étapes ultérieures.

```output
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-06T03:40:56.511597+00:00",
  "id": "/subscriptions/f2799821-a08a-434e-9128-454ec4348b10/resourcegroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry23489",
  "location": "eastus",
  "loginServer": "mycontainerregistry23489.azurecr.io",
  "name": "myContainerRegistry23489",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "mycontainerregistr034017"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Obtenez les informations d’identification du registre de conteneurs à l’aide de la commande [afficher les informations d’identification az acr](/cli/azure/acr/credential). Remplacez `--name` par celui indiqué dans la dernière étape. Notez un mot de passe, nécessaire pour une étape ultérieure.

```azurecli-interactive
az acr credential show --name myContainerRegistry23489
```

Pour plus d’informations sur Azure Container Registry, consultez [Introduction aux registres de conteneurs Docker privés](../../container-registry/container-registry-intro.md). 

## <a name="manage-acr-authentication"></a>Gérer l’authentification ACR

La méthode traditionnelle pour pousser et tirer une image d’un registre privé consiste tout d’abord à l’authentifier auprès de celui-ci. Pour ce faire, vous devez utiliser la commande `docker login` sur n’importe quel client qui a besoin d’accéder au registre privé. Un cluster de contrôleur de domaine/système d’exploitation peut contenir plusieurs nœuds qui doivent tous être authentifiés avec l’ACR. Il est donc utile d’automatiser ce processus sur chaque nœud. 

### <a name="create-shared-storage"></a>Créer un stockage partagé

Ce processus utilise un partage de fichiers Azure installé sur chaque nœud du cluster. Si vous n’avez pas déjà configuré de stockage partagé, consultez [Configurer un partage de fichier dans un cluster de contrôleur de domaine/système d’exploitation](container-service-dcos-fileshare.md).

### <a name="configure-acr-authentication"></a>Configurer une authentification ACR

Obtenez d’abord le FQDN du DC/OS maître et stockez-le dans une variable.

```azurecli-interactive
FQDN=$(az acs list --resource-group myResourceGroup --query "[0].masterProfile.fqdn" --output tsv)
```

Connectez-vous par SSH au maître (ou premier maître) de votre cluster DC/OS. Si une valeur non définie par défaut a été utilisée lors de la création du cluster, mettez à jour le nom d’utilisateur.

```console
ssh azureuser@$FQDN
```

Exécutez la commande suivante pour vous connecter à Azure Container Registry. Remplacez le `--username` par le nom du registre de conteneurs et le `--password` par l’un des mots de passe fournis. Remplacez le dernier argument *mycontainerregistry.azurecr.io* dans l’exemple par le nom loginServer du registre de conteneurs. 

Cette commande stocke les valeurs d’authentification localement sous le chemin d’accès `~/.docker`.

```console
docker -H tcp://localhost:2375 login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry.azurecr.io
```

Créez un fichier compressé qui contient les valeurs d’authentification du registre de conteneurs.

```console
tar czf docker.tar.gz .docker
```

Copiez ce fichier dans le stockage partagé de cluster. Cette étape rend le fichier disponible sur tous les nœuds du cluster de contrôleur de domaine/système d’exploitation.

```console
cp docker.tar.gz /mnt/share/dcosshare
```

## <a name="upload-image-to-acr"></a>Télécharger une image dans l’ACR

Créez à présent, à partir d’un ordinateur de développement ou de tout autre système avec Docker installé, une image et chargez-le dans Azure Container Registry.

Créez un conteneur à partir de l’image Ubuntu.

```console
docker run ubuntu --name base-image
```

Capturez maintenant le conteneur dans une nouvelle image. Le nom de l’image doit inclure le nom `loginServer` du registre de conteneurs avec le format `loginServer/imageName`.

```console
docker -H tcp://localhost:2375 commit base-image mycontainerregistry30678.azurecr.io/dcos-demo
```

Connectez-vous à Azure Container Registry. Remplacez le nom par le nom loginServer, le --nom d’utilisateur par le nom du registre de conteneurs et le --mot de passe par l’un des mots de passe fournis.

```console
docker login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry2675.azurecr.io
```

Enfin, téléchargez l’image dans le registre ACR. Cet exemple permet de télécharger une image nommée dcos-demo.

```console
docker push mycontainerregistry30678.azurecr.io/dcos-demo
```

## <a name="run-an-image-from-acr"></a>Exécuter une image à partir de l’ACR

Pour utiliser une image à partir du registre ACR, créez un nom de fichier *acrDemo.json* et copiez-y le texte suivant. Remplacez le nom de l’image par le nom de loginServer du registre de conteneurs et le nom de l’image, par exemple `loginServer/imageName`. Notez la propriété `uris`. Cette propriété contient l’emplacement du fichier d’authentification du registre de conteneurs, qui dans ce cas est le partage de fichiers Azure installé sur chaque nœud du cluster de contrôleur de domaine/système d’exploitation.

```json
{
  "id": "myapp",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "mycontainerregistry30678.azurecr.io/dcos-demo",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "uris":  [
       "file:///mnt/share/dcosshare/docker.tar.gz"
   ]
}
```

Déployez l’application avec le CLI DC/OC.

```console
dcos marathon app add acrDemo.json
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez configurer le contrôleur de domaine/système d’exploitation pour utiliser Azure Container Registry, y compris les tâches suivantes :

> [!div class="checklist"]
> * Déployer Azure Container Registry (le cas échéant)
> * Configurer l’authentification ACR sur un cluster de contrôleur de domaine/système d’exploitation
> * Télécharger une image dans Azure Container Registry
> * Exécuter une image de conteneur depuis Azure Container Registry
