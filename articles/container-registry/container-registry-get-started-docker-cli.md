---
title: Transmission et extraction d’image de conteneur
description: Transmission et extraction d’images Docker à/de votre registre de conteneurs privé dans Azure à l’aide de l’interface de ligne de commande Docker
ms.topic: article
ms.date: 05/12/2021
ms.custom: seodec18, H1Hack27Feb2017, devx-track-azurepowershell
ms.openlocfilehash: 0fd44ae001bd7f120b6c903a4109dd0e6268e19e
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110062953"
---
# <a name="push-your-first-image-to-your-azure-container-registry-using-the-docker-cli"></a>Transmettre votre première image à votre registre de conteneurs Azure à l’aide de l’interface de ligne de commande Docker

Un registre de conteneurs Azure stocke et gère des images conteneurs privées ainsi que d’autres artefacts, de la même manière que [Docker Hub](https://hub.docker.com/) stocke des images conteneurs Docker publiques. Vous pouvez utiliser l’[interface de ligne de commande Docker](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) pour la [connexion](https://docs.docker.com/engine/reference/commandline/login/), la [transmission](https://docs.docker.com/engine/reference/commandline/push/), l’[extraction](https://docs.docker.com/engine/reference/commandline/pull/) et d’autres opérations d’image conteneur sur le registre de conteneurs.

Dans les étapes suivantes, vous allez télécharger une [image Nginx](https://store.docker.com/images/nginx) publique, l’étiqueter pour votre registre de conteneurs Azure privé, la transmettre à votre registre, puis l’extraire du registre.

## <a name="prerequisites"></a>Prérequis

* **Azure Container Registry** : créez un Registre de conteneur dans votre abonnement Azure. Par exemple, utilisez le [portail Azure](container-registry-get-started-portal.md), [Azure CLI](container-registry-get-started-azure-cli.md) ou [Azure PowerShell](container-registry-get-started-powershell.md).
* **Docker CLI** : Docker doit également être installé en local. Docker fournit des packages qui le configurent facilement sur n’importe quel système [macOS][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

## <a name="log-in-to-a-registry"></a>Se connecter à un Registre

Il existe [plusieurs façons de s’authentifier](container-registry-authentication.md) auprès de votre registre de conteneurs privé.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

La méthode recommandée avec une ligne de commande consiste à utiliser la commande Azure CLI [az acr login](/cli/azure/acr#az_acr_login). Par exemple, pour vous connecter à un registre nommé *myregistry*, connectez-vous à Azure CLI, puis authentifiez-vous auprès de votre registre :

```azurecli
az login
az acr login --name myregistry
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

La méthode recommandée lors de l’utilisation de PowerShell est la cmdlet Azure PowerShell [Connect-AzContainerRegistry](/powershell/module/az.containerregistry/connect-azcontainerregistry). Par exemple, pour vous connecter à un registre nommé *myregistry*, connectez-vous à Azure, puis authentifiez-vous auprès de votre registre :

```azurepowershell
Connect-AzAccount
Connect-AzContainerRegistry -Name myregistry
```

---

Vous pouvez également vous connecter avec la commande [docker login](https://docs.docker.com/engine/reference/commandline/login/). Par exemple, vous pouvez avoir [affecté un principal du service](container-registry-authentication.md#service-principal) à votre registre dans un scénario d’automatisation. Lorsque vous exécutez la commande suivante, fournissez de manière interactive l'appID (nom d'utilisateur) et le mot de passe du principal du service dès que vous y êtes invité. Pour connaître les meilleures pratiques de gestion des informations d'identification, consultez la référence de la commande [docker login](https://docs.docker.com/engine/reference/commandline/login/) :

```
docker login myregistry.azurecr.io
```

Les deux commandes retournent `Login Succeeded` une fois terminées.
> [!NOTE]
>* Vous souhaiterez peut-être utiliser Visual Studio Code avec l’extension Docker à des fins de connexion plus rapide et plus pratique.

> [!TIP]
> Spécifiez toujours le nom complet du registre (tout en minuscules) lorsque vous utilisez `docker login` et lorsque vous étiquetez des images à pousser dans votre registre. Dans les exemples de cet article, le nom complet est *myregistry.azurecr.io*.

## <a name="pull-a-public-nginx-image"></a>Extraire une image Nginx publique

Tout d’abord, extrayez une image Nginx publique sur votre ordinateur local. Cet exemple extrait une image de Microsoft Container Registry.

```
docker pull mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

## <a name="run-the-container-locally"></a>Exécutez localement le conteneur

Exécutez la commande [docker run](https://docs.docker.com/engine/reference/run/) suivante pour démarrer une instance locale du conteneur Nginx de manière interactive (`-it`) sur le port 8080. L’argument `--rm` spécifie que le conteneur doit être supprimé lorsque vous l’arrêtez.

```
docker run -it --rm -p 8080:80 mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

Accédez à `http://localhost:8080` pour afficher la page web par défaut servie par Nginx dans le conteneur en cours d’exécution. Une page similaire à celle ci-dessous doit s'afficher :

![Nginx sur un ordinateur local](./media/container-registry-get-started-docker-cli/nginx.png)

Étant donné que vous avez démarré le conteneur de manière interactive avec `-it`, vous pouvez voir la sortie du serveur Nginx sur la ligne de commande après y avoir accédé dans votre navigateur.

Pour arrêter et supprimer le conteneur, appuyez sur `Control`+`C`.

## <a name="create-an-alias-of-the-image"></a>Créer un alias de l’image

Utilisez une [balise Docker](https://docs.docker.com/engine/reference/commandline/tag/) pour créer un alias de l’image, avec un chemin complet vers votre registre. Cet exemple spécifie l’espace de noms `samples` pour éviter l’encombrement à la racine du Registre.

```
docker tag mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine myregistry.azurecr.io/samples/nginx
```

Pour plus d’informations sur le balisage avec des espaces de noms, consultez la section [Espaces de noms du référentiel](container-registry-best-practices.md#repository-namespaces) dans [Bonnes pratiques pour Azure Container Registry](container-registry-best-practices.md).

## <a name="push-the-image-to-your-registry"></a>Pousser (push) l’image dans votre registre

Maintenant que vous avez étiqueté l’image avec le chemin complet de votre registre privé, vous pouvez la pousser dans le registre avec [docker push](https://docs.docker.com/engine/reference/commandline/push/) :

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Tirer (pull) l’image de votre registre

Utilisez la commande [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) pour tirer l’image de votre registre :

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Démarrer le conteneur Nginx

Utilisez la commande [docker run](https://docs.docker.com/engine/reference/run/) pour exécuter l’image que vous avez tirée de votre registre :

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Accédez à `http://localhost:8080` pour afficher le conteneur en cours d’exécution.

Pour arrêter et supprimer le conteneur, appuyez sur `Control`+`C`.

## <a name="remove-the-image-optional"></a>Supprimer l’image (facultatif)

Si vous n’avez plus besoin de l’image Nginx, vous pouvez la supprimer localement à l’aide de la commande [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/).

```
docker rmi myregistry.azurecr.io/samples/nginx
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour supprimer des images de votre registre de conteneurs Azure, vous pouvez utiliser la commande Azure CLI [az acr repository delete](/cli/azure/acr/repository#az_acr_repository_delete). Par exemple, la commande suivante supprime le manifeste référencé par l'étiquette `samples/nginx:latest`, toutes les données de couche uniques et toutes les autres étiquettes référençant le manifeste.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Le module Azure PowerShell [Az.ContainerRegistry](/powershell/module/az.containerregistry) contient plusieurs commandes permettant de supprimer des images de votre instance de conteneur. [Remove-AzContainerRegistryRepository](/powershell/module/az.containerregistry/remove-azcontainerregistryrepository) supprime toutes les images dans un espace de noms spécifique comme `samples:nginx`, tandis que [Remove-AzContainerRegistryManifest](/powershell/module/az.containerregistry/remove-azcontainerregistrymanifest) supprime une balise ou un manifeste spécifique.

Dans l’exemple suivant, vous utilisez la cmdlet `Remove-AzContainerRegistryRepository` pour supprimer toutes les images dans l’espace de noms `samples:nginx`.

```azurepowershell
Remove-AzContainerRegistryRepository -RegistryName myregistry -Name samples/nginx
```

Dans l’exemple suivant, vous utilisez la cmdlet `Remove-AzContainerRegistryManifest` pour supprimer le manifeste référencé par l’étiquette `samples/nginx:latest`, toutes les données de couche uniques et toutes les autres étiquettes référençant le manifeste.

```azurepowershell
Remove-AzContainerRegistryManifest -RegistryName myregistry -RepositoryName samples/nginx -Tag latest
```

---

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous connaissez les principes de base, vous êtes prêt à utiliser votre registre. Par exemple, déployez des images de conteneur à partir de votre Registre vers :

* [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

Si vous le souhaitez, vous pouvez installer l’[extension Docker pour Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) et l’extension [Compte Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) pour utiliser vos registres de conteneurs Azure. Dans Visual Studio Code, vous pouvez tirer (pull) et envoyer (push) des images vers un registre de conteneurs Azure, et exécuter ACR Tasks.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
