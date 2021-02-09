---
title: Utiliser Azure Container Registry avec Azure Red Hat OpenShift
description: Découvrez comment extraire un conteneur d’Azure Container Registry et l’exécuter dans votre cluster Azure Red Hat OpenShift.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 01/10/2021
ms.openlocfilehash: 91b43aa939579f5df8115048cf52b9bcdd7ade3d
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99064321"
---
# <a name="use-azure-container-registry-with-azure-red-hat-openshift-aro"></a>Utiliser Azure Container Registry avec Azure Red Hat OpenShift (ARO)

Azure Container Registry (ACR) est un service de registre de conteneurs managé que vous pouvez utiliser pour stocker des images de conteneur Docker privées avec des fonctionnalités d’entreprise telles que la géoréplication. Pour accéder à ACR à partir d’un cluster ARO, celui-ci peut s’authentifier auprès d’ACR en stockant des informations d’identification de connexion Docker dans un secret Kubernetes.  De même, un cluster ARO peut utiliser un imagePullSecret dans la spécification pod pour s’authentifier auprès du registre lors de l’extraction de l’image. Cet article explique comment configurer un Azure Container Registry avec un cluster Azure Red Hat OpenShift pour stocker et extraire des images de conteneur Docker privé.

## <a name="prerequisites"></a>Prérequis

Ce guide part du principe que vous disposez déjà d’un Azure Container Registry. Si ce n’est pas le cas, utilisez le portail Azure ou suivez les [instructions Azure CLI](../container-registry/container-registry-get-started-azure-cli.md) pour créer un registre de conteneurs.

Cet article part également du principe que vous disposez d’un cluster Azure Red Hat OpenShift et que l’interface de ligne de commande `oc` est installée. Si ce n’est pas le cas, suivez les instructions du tutoriel [Créer un cluster ARO](tutorial-create-cluster.md).

## <a name="get-a-pull-secret"></a>Obtenir un secret d’extraction

Pour accéder au registre à partir de votre cluster ARO, vous aurez besoin d’un secret d’extraction d’ACR.

Pour obtenir les informations de connexion de votre secret d’extraction, vous pouvez utiliser le portail Azure ou l’interface de ligne de commande Azure.

Si vous utilisez le portail Azure, accédez à votre instance ACR, puis sélectionnez **Clés d’accès**.  Votre `docker-username` est le nom de votre registre de conteneurs. Utilisez le password ou le password2 pour `docker-password`.

![Clé d'accès](./media/acr-access-keys.png)

Au lieu de cela, vous pouvez utiliser l’interface de ligne de commande Azure pour obtenir ces informations d’identification :
```azurecli
az acr credential show -n <your registry name>
```

## <a name="create-the-kubernetes-secret"></a>Créer le secret Kubernetes

À présent, nous allons utiliser ces informations d’identification pour créer un secret Kubernetes. Exécutez la commande suivante avec vos informations d’identification ACR :

```
oc create secret docker-registry \
    --docker-server=<your registry name>.azurecr.io \
    --docker-username=<your registry name> \
    --docker-password=******** \
    --docker-email=unused \
    acr-secret
```

>[!NOTE]
>Ce secret sera stocké dans le projet OpenShift actif (espace de noms Kubernetes) et ne pourra être référencé que par des pods créés dans celui-ci.  Pour obtenir des instructions supplémentaires sur la création d’un secret d’extraction pour l’ensemble d’un cluster, consultez ce [document](https://docs.openshift.com/container-platform/4.4/openshift_images/managing_images/using-image-pull-secrets.html).

## <a name="create-a-pod-using-a-private-registry-image"></a>Créer un pod à l’aide d’une image de registre privé

Maintenant que nous avons connecté votre cluster ARO à votre ACR, nous allons extraire une image de votre ACR pour créer un pod.

Commencez par une podSpec et spécifiez le secret que vous avez créé en tant qu’imagePullSecret :

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-world
spec:
  containers:
  - name: hello-world
    image: <your registry name>.azurecr.io/hello-world:v1
  imagePullSecrets:
  - name: acr-secret
```

Pour vérifier que votre pod est opérationnel, exécutez la commande suivante et attendez que l’état soit **Exécution** :

```bash
$ oc get pods --watch
NAME         READY   STATUS    RESTARTS   AGE
hello-world  1/1     Running   0          30s
```

## <a name="next-steps"></a>Étapes suivantes

* [Azure Container Registry](../container-registry/container-registry-concepts.md)
* [Démarrage rapide : Créer un registre de conteneurs privé avec Azure CLI](../container-registry/container-registry-get-started-azure-cli.md)
