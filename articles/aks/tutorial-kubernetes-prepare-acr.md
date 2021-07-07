---
title: Didacticiel Kubernetes sur Azure - Créer un registre de conteneurs
description: Dans le cadre de ce didacticiel Azure Kubernetes Service (AKS), vous allez créer une instance Azure Container Registry et y charger un exemple d’image conteneur d’application.
services: container-service
ms.topic: tutorial
ms.date: 05/20/2021
ms.custom: mvc, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 157aae551f4fa77c4739670081804af1b8dbf16b
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110697775"
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>Didacticiel : Déployer et utiliser Azure Container Registry

Azure Container Registry (ACR) est un registre privé destiné d’images de conteneur. Un registre de conteneurs privé vous permet de générer et déployer vos applications et codes personnalisés en toute sécurité. Au cours de ce didacticiel, le deuxième d’une série de sept, vous allez déployer une instance ACR et lui envoyer une image conteneur par une transmission de type push. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une instance Azure Container Registry (ACR)
> * Marquer une image conteneur pour ACR
> * Charger l’image dans ACR
> * Visualiser les images figurant dans votre registre

Dans les tutoriels ultérieurs, cette instance ACR est intégrée à un cluster Kubernetes dans AKS, et une application est déployée à partir de l’image.

## <a name="before-you-begin"></a>Avant de commencer

Dans le [didacticiel précédent][aks-tutorial-prepare-app], une image conteneur a été créée pour une application Azure Vote. Si vous n’avez pas créé l’image de l’application Azure Vote, retournez au [Didacticiel 1 : Créer des images conteneur][aks-tutorial-prepare-app].

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ce didacticiel nécessite l’exécution de l’interface de ligne de commande Azure CLI version 2.0.53 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][azure-cli-install].

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Ce tutoriel nécessite que vous exécutiez Azure PowerShell version 5.9.0 ou ultérieure. Exécutez `Get-InstalledModule -Name Az` pour trouver la version. Si vous avez besoin de procéder à une installation ou à une mise à niveau, consultez [Installer Azure PowerShell][azure-powershell-install].

---

## <a name="create-an-azure-container-registry"></a>Création d’un Azure Container Registry

Pour créer une instance Azure Container Registry, vous devez tout d’abord disposer d’un groupe de ressources. Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Créez un groupe de ressources avec la commande [az group create][az-group-create]. Dans l’exemple suivant, un groupe de ressources nommé *myResourceGroup* est créé dans la région *eastus* :

```azurecli
az group create --name myResourceGroup --location eastus
```

Créez une instance Azure Container Registry avec la commande [az acr create][az-acr-create] et fournissez votre propre nom de registre. Le nom du registre doit être unique dans Azure et contenir entre 5 et 50 caractères alphanumériques. Dans le reste de ce didacticiel, `<acrName>` est utilisé comme espace réservé pour le nom du registre de conteneurs. Indiquez un nom de registre unique. La référence SKU *De base* est un point d’entrée au coût optimisé fourni à des fins de développement qui offre un bon équilibre entre stockage et débit.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Créez un groupe de ressources avec l’applet de commande [New-AzResourceGroup][new-azresourcegroup]. Dans l’exemple suivant, un groupe de ressources nommé *myResourceGroup* est créé dans la région *eastus* :

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

Créez une instance Azure Container Registry avec l’applet de commande [New-AzContainerRegistry][new-azcontainerregistry] et fournissez votre propre nom de registre. Le nom du registre doit être unique dans Azure et contenir entre 5 et 50 caractères alphanumériques. Dans le reste de ce didacticiel, `<acrName>` est utilisé comme espace réservé pour le nom du registre de conteneurs. Indiquez un nom de registre unique. La référence SKU *De base* est un point d’entrée au coût optimisé fourni à des fins de développement qui offre un bon équilibre entre stockage et débit.

```azurepowershell
New-AzContainerRegistry -ResourceGroupName myResourceGroup -Name <acrname> -Sku Basic
```

---

## <a name="log-in-to-the-container-registry"></a>Se connecter au registre de conteneurs

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour utiliser l’instance ACR, vous devez commencer par vous y connecter. Utilisez la commande [az acr login][az-acr-login] et fournissez le nom unique que vous avez attribué au registre de conteneurs à l’étape précédente.

```azurecli
az acr login --name <acrName>
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Pour utiliser l’instance ACR, vous devez commencer par vous y connecter. Utilisez l’applet de commande [Connect-AzContainerRegistry][connect-azcontainerregistry] et fournissez le nom unique qui a été donné au registre de conteneurs à l’étape précédente.

```azurepowershell
Connect-AzContainerRegistry -Name <acrName>
```

---

Après son exécution, la commande retourne le message *Login Succeeded (Connexion réussie)* .

## <a name="tag-a-container-image"></a>Baliser une image conteneur

Pour afficher la liste des images locales actuelles, utilisez la commande [docker images][docker-images] :

```console
docker images
```
La sortie de la commande ci-dessus affiche la liste de vos images locales actuelles :

```output
REPOSITORY                                     TAG                 IMAGE ID            CREATED             SIZE
mcr.microsoft.com/azuredocs/azure-vote-front   v1                  84b41c268ad9        7 minutes ago       944MB
mcr.microsoft.com/oss/bitnami/redis            6.0.8               3a54a920bb6c        2 days ago          103MB
tiangolo/uwsgi-nginx-flask                     python3.6           a16ce562e863        6 weeks ago         944MB
```

Pour utiliser l’image conteneur *azure-vote-front* avec ACR, vous devez baliser cette image avec l’adresse du serveur de connexion de votre registre. Cette balise est utilisée pour l’acheminement lors de l’envoi des images de conteneur dans un registre d’images.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour obtenir l’adresse du serveur de connexion, utilisez la commande [az acr list][az-acr-list] et exécutez une requête portant sur l’élément *loginServer*, comme suit :

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```
### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Pour obtenir l’adresse du serveur de connexion, utilisez l’applet de commande [AzContainerRegistry][get-azcontainerregistry] et interrogez *loginServer* de la façon suivante :

```azurepowershell
(Get-AzContainerRegistry -ResourceGroupName myResourceGroup -Name <acrName>).LoginServer
```

---

À présent, étiquetez votre image *azure-vote-front* locale avec l’adresse *acrLoginServer* du registre de conteneurs. Pour indiquer la version de l’image, ajoutez *:v1* à la fin du nom de l’image :

```console
docker tag mcr.microsoft.com/azuredocs/azure-vote-front:v1 <acrLoginServer>/azure-vote-front:v1
```

Pour vérifier que les étiquettes sont appliquées, réexécutez la commande [docker images][docker-images].

```console
docker images
```

Une image est balisée avec l’adresse de l’instance ACR et un numéro de version.

```
REPOSITORY                                      TAG                 IMAGE ID            CREATED             SIZE
mcr.microsoft.com/azuredocs/azure-vote-front    v1                  84b41c268ad9        16 minutes ago      944MB
mycontainerregistry.azurecr.io/azure-vote-front v1                  84b41c268ad9        16 minutes ago      944MB
mcr.microsoft.com/oss/bitnami/redis             6.0.8               3a54a920bb6c        2 days ago          103MB
tiangolo/uwsgi-nginx-flask                      python3.6           a16ce562e863        6 weeks ago         944MB
```

## <a name="push-images-to-registry"></a>Envoyer des images au registre

Une fois votre image développée et balisée, envoyez l’image *azure-vote-front* à votre instance ACR. Utilisez la commande [docker push][docker-push] et fournissez votre propre adresse *acrLoginServer* pour le nom d’image, comme suit :

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

L’envoi de l’image à ACR peut nécessiter quelques minutes.

## <a name="list-images-in-registry"></a>Créer la liste des images du registre

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour retourner la liste des images qui ont été envoyées à votre instance ACR, utilisez la commande [az acr repository list][az-acr-repository-list]. Fournissez votre propre `<acrName>`, comme suit :

```azurecli
az acr repository list --name <acrName> --output table
```

L’exemple de sortie ci-après répertorie l’image *azure-vote-front* comme étant disponible dans le registre :

```output
Result
----------------
azure-vote-front
```

Pour visualiser les étiquettes d’une image spécifique, utilisez la commande [az acr repository show-tags][az-acr-repository-show-tags] comme suit :

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

L’exemple de sortie ci-après présente l’image *v1* balisée lors d’une étape précédente :

```output
Result
--------
v1
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Pour retourner la liste des images qui ont été envoyées (push) vers votre instance ACR, utilisez l’applet de commande [Get-AzContainerRegistryManifest][get-azcontainerregistrymanifest]. Fournissez votre propre `<acrName>`, comme suit :

```azurepowershell
Get-AzContainerRegistryManifest -RegistryName <acrName> -RepositoryName azure-vote-front
```

L’exemple de sortie ci-après répertorie l’image *azure-vote-front* comme étant disponible dans le registre :

```output
Registry  ImageName        ManifestsAttributes
--------  ---------        -------------------
<acrName> azure-vote-front {Microsoft.Azure.Commands.ContainerRegistry.Models.PSManifestAttributeBase}
```

Pour afficher les étiquettes d’une image, utilisez l’applet de commande [AzContainerRegistryTag][get-azcontainerregistrytag] de la façon suivante :

```azurepowershell
Get-AzContainerRegistryTag -RegistryName <acrName> -RepositoryName azure-vote-front
```

L’exemple de sortie ci-après présente l’image *v1* balisée lors d’une étape précédente :

```output
Registry  ImageName        Tags
--------  ---------        ----
<acrName> azure-vote-front {v1}
```

---

Vous disposez désormais d’une image conteneur stockée dans une instance Azure Container Registry privée. Au cours du didacticiel suivant, cette image est déployée à partir d’ACR vers un cluster Kubernetes.

## <a name="next-steps"></a>Étapes suivantes

Dans le cadre de ce didacticiel, vous avez créé une instance Azure Container Registry et vous lui avez envoyé une image à utiliser dans un cluster AKS. Vous avez appris à :

> [!div class="checklist"]
> * Créer une instance Azure Container Registry (ACR)
> * Marquer une image conteneur pour ACR
> * Charger l’image dans ACR
> * Visualiser les images figurant dans votre registre

Passez au didacticiel suivant pour découvrir comment déployer un cluster Kubernetes dans Azure.

> [!div class="nextstepaction"]
> [Déployer un cluster Kubernetes][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr
[az-acr-list]: /cli/azure/acr
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-list]: /cli/azure/acr#az_acr_list
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-powershell-install]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[new-azcontainerregistry]: /powershell/module/az.containerregistry/new-azcontainerregistry
[connect-azcontainerregistry]: /powershell/module/az.containerregistry/connect-azcontainerregistry
[get-azcontainerregistry]: /powershell/module/az.containerregistry/get-azcontainerregistry
[get-azcontainerregistrymanifest]: /powershell/module/az.containerregistry/get-azcontainerregistrymanifest
[get-azcontainerregistrytag]: /powershell/module/az.containerregistry/get-azcontainerregistrytag
