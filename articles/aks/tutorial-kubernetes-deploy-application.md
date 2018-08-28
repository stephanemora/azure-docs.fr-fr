---
title: Didacticiel Kubernetes sur Azure - Déployer une application
description: Dans le cadre de ce didacticiel Azure Kubernetes Service (AKS), vous allez déployer une application multiconteneur dans votre cluster à l’aide d’une image personnalisée stockée dans Azure Container Registry.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: bf817f553250ead449ec0d5db3d33acc2eff23f3
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2018
ms.locfileid: "41917559"
---
# <a name="tutorial-run-applications-in-azure-kubernetes-service-aks"></a>Didacticiel : Exécuter des applications dans Azure Kubernetes Service (ACS)

Kubernetes fournit une plateforme distribuée destinée aux applications en conteneur. Vous générez et déployez vos propres applications et services dans un cluster Kubernetes, et vous laissez le cluster gérer la disponibilité et la connectivité. Dans ce didacticiel (le quatrième d’une série de sept), un exemple d’application est déployé dans un cluster Kubernetes. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Mise à jour d’un fichier manifeste Kubernetes
> * Exécution d’une application dans Kubernetes
> * Test de l’application

Dans les didacticiels suivants, cette application est augmentée et mise à jour.

Ce didacticiel suppose une compréhension élémentaire des concepts de Kubernetes. Pour en savoir plus, consultez la [documentation Kubernetes][kubernetes-documentation].

## <a name="before-you-begin"></a>Avant de commencer

Dans les didacticiels précédents, une application a été empaquetée dans une image conteneur, l’image a été chargée dans Azure Container Registry et un cluster Kubernetes a été créé.

Pour effectuer ce didacticiel, vous avez besoin du fichier manifeste Kubernetes `azure-vote-all-in-one-redis.yaml`. Ce fichier a été téléchargé avec le code source de l’application dans un didacticiel précédent. Vérifiez que vous avez cloné le référentiel et que vous avez modifié des répertoires dans le référentiel cloné. Si vous n’avez pas accompli ces étapes et que vous souhaitez suivre cette procédure, revenez au [Tutoriel 1 – Créer des images conteneur][aks-tutorial-prepare-app].

Ce didacticiel nécessite l’exécution de l’interface de ligne de commande Azure (Azure CLI) version 2.0.44 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer Azure CLI 2.0][azure-cli-install].

## <a name="update-the-manifest-file"></a>Mettre à jour le fichier manifeste

Dans ces didacticiels, une instance Azure Container Registry (ACR) stocke l’image conteneur de l’exemple d’application. Pour déployer l’application, vous devez mettre à jour le nom de l’image dans le fichier manifeste Kubernetes afin d’inclure le nom du serveur de connexion ACR.

Obtenez le nom du serveur de connexion ACR à l’aide de la commande [az acr list][az-acr-list], comme suit :

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

L’exemple de fichier manifeste provenant du référentiel git cloné dans le premier didacticiel utilise le nom de serveur de connexion *microsoft*. Ouvrez ce fichier manifeste avec un éditeur de texte, tel que `vi` :

```console
vi azure-vote-all-in-one-redis.yaml
```

Remplacez *microsoft* par le nom de votre serveur de connexion ACR. Le nom de l’image figure sur la ligne 47 du fichier manifeste. L’exemple ci-après indique le nom de l’image par défaut :

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Fournissez le nom de votre propre serveur de connexion ACR afin que votre fichier manifeste ressemble à ceci :

```yaml
containers:
- name: azure-vote-front
  image: <acrName>.azurecr.io/azure-vote-front:v1
```

Enregistrez et fermez le fichier.

## <a name="deploy-the-application"></a>Déployer l’application

Pour déployer votre application, utilisez la commande [kubectl apply][kubectl-apply]. Cette commande analyse le fichier manifeste et crée les objets Kubernetes définis. Spécifiez l’exemple de fichier manifeste, comme indiqué dans l’exemple suivant :

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Les objets Kubernetes sont créés au sein du cluster, comme indiqué dans l’exemple suivant :

```
$ kubectl apply -f azure-vote-all-in-one-redis.yaml

deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Test de l’application

Un [service Kubernetes][kubernetes-service] est créé et expose l’application à Internet. Ce processus peut prendre plusieurs minutes. Pour surveiller la progression, utilisez la commande [kubectl get service][kubectl-get] avec l’argument `--watch` :

```console
kubectl get service azure-vote-front --watch
```

L’adresse *EXTERNAL-IP* pour le service *azure-vote-front* apparaît initialement comme étant en attente (valeur *pending*), comme dans l’exemple suivant :

```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Lorsque l’adresse *EXTERNAL-IP* passe de l’état *pending* à une adresse IP publique réelle, utilisez `CTRL-C` pour arrêter le processus espion kubectl. L’exemple ci-après indique qu’une adresse IP publique est désormais attribuée :

```
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Pour voir l’application en action, ouvrez un navigateur web en utilisant l’adresse IP externe.

![Image du cluster Kubernetes sur Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

Si l’application ne s’est pas chargée, il y a peut-être un problème d’autorisation avec votre registre d’images. Pour visualiser l’état de vos conteneurs, utilisez la commande `kubectl get pods`. Si les images conteneurs ne peuvent pas être extraites, consultez la section indiquant comment [autoriser l’accès à Container Registry à l’aide d’un secret Kubernetes](https://docs.microsoft.com/azure/container-registry/container-registry-auth-aks#access-with-kubernetes-secret).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, l’application de vote Azure a été déployée sur un cluster Kubernetes dans ACS. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Mise à jour d’un fichier manifeste Kubernetes
> * Exécution d’une application dans Kubernetes
> * Test de l’application

Passez au didacticiel suivant pour découvrir comment mettre à l’échelle une application Kubernetes et l’infrastructure Kubernetes sous-jacente.

> [!div class="nextstepaction"]
> [Mettre à l’échelle l’application et l’infrastructure Kubernetes][aks-tutorial-scale]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr#list
[azure-cli-install]: /cli/azure/install-azure-cli
