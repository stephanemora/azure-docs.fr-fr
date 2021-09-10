---
title: Installer des applications existantes avec Helm dans AKS
description: Apprendre à utiliser l’outil d’empaquetage Helm pour déployer des conteneurs dans un cluster AKS (Azure Kubernetes Service)
services: container-service
author: zr-msft
ms.topic: article
ms.date: 12/07/2020
ms.author: zarhoads
ms.openlocfilehash: c1370182d8ca13acb3d94856df784ecea34252ae
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562197"
---
# <a name="install-existing-applications-with-helm-in-azure-kubernetes-service-aks"></a>Installer des applications existantes avec Helm dans Azure Kubernetes service (AKS)

[Helm][helm] est un outil d’empaquetage open source qui vous aide à installer et à gérer le cycle de vie d’applications Kubernetes. À l’instar de gestionnaires de package Linux tels que *APT* et *Yum*, Helm sert à gérer les graphiques Kubernetes, qui sont des packages de ressources Kubernetes préconfigurés.

Cet article vous montre comment configurer et utiliser Helm dans un cluster Kubernetes AKS.

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez un cluster AKS existant. Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec Azure CLI][aks-quickstart-cli]ou avec le [Portail Azure][aks-quickstart-portal].

En outre, cet article suppose que vous disposez d’un cluster AKS avec un ACR intégré. Pour plus d’informations sur la création d’un cluster AKS avec un ACR intégré, consultez [S’authentifier auprès d’Azure Container Registry à partir du service Kubernetes Azure][aks-integrated-acr].

Vous devez aussi avoir installé l’interface CLI Helm, qui est le client s’exécutant sur votre système de développement. Il vous permet de démarrer, arrêter et gérer les applications avec Helm. Si vous utilisez Azure Cloud Shell, l’interface CLI Helm est déjà installée. Pour obtenir les instructions d’installation sur votre plateforme locale, consultez [Installing Helm][helm-install] (Installation de Helm).

> [!IMPORTANT]
> Helm est prévu pour s’exécuter sur des nœuds Linux. Si vous avez des nœuds Windows Server dans votre cluster, vous devez veiller à ce que l’exécution des pods Helm soit planifiée uniquement sur des nœuds Linux. Vous devez aussi veiller à ce que les graphiques Helm que vous installez s’exécutent sur les bons nœuds. Les commandes figurant dans cet article utilisent [node-selectors][k8s-node-selector] pour garantir que les pods sont planifiés sur les nœuds adéquats, mais il se peut que certains graphiques Helm n’exposent pas de sélecteur de nœud. Vous pouvez aussi envisager d’utiliser d’autres options sur votre cluster, comme des [teintes][taints] (« taints »).

## <a name="verify-your-version-of-helm"></a>Vérifier la version de Helm

Utilisez la commande `helm version` pour vérifier que Helm 3 est installé :

```console
helm version
```

L’exemple suivant montre la version 3.0.0 de Helm installée :

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

## <a name="install-an-application-with-helm-v3"></a>Installer une application avec Helm v3

### <a name="add-helm-repositories"></a>Ajouter des référentiels Helm

Utilisez la commande [helm repo][helm-repo-add] pour ajouter le référentiel *ingress-nginx*.

```console
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```

### <a name="find-helm-charts"></a>Rechercher des graphiques Helm

Les graphiques Helm servent à déployer des applications dans un cluster Kubernetes. Pour rechercher des graphiques Helm précréés, vous devez utiliser la commande [helm search][helm-search] :

```console
helm search repo ingress-nginx
```

La sortie condensée suivante montre certains des graphiques Helm disponibles :

```console
$ helm search repo ingress-nginx

NAME                            CHART VERSION   APP VERSION     DESCRIPTION                                       
ingress-nginx/ingress-nginx     2.12.0          0.34.1          Ingress controller for Kubernetes using NGINX a...
```

Pour mettre à jour la liste des graphiques, utilisez la commande [helm repo update][helm-repo-update].

```console
helm repo update
```

L’exemple suivant montre une mise à jour de référentiel réussie :

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "ingress-nginx" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="import-the-images-used-by-the-helm-chart-into-your-acr"></a>Importer les images utilisées par le graphique Helm dans votre ACR

Cet article utilise le [graphique Helm du contrôleur d’entrée NGINX][ingress-nginx-helm-chart], lequel repose sur trois images de conteneurs. Utilisez `az acr import` pour importer ces images dans votre ACR.

```azurecli
REGISTRY_NAME=<REGISTRY_NAME>
CONTROLLER_REGISTRY=k8s.gcr.io
CONTROLLER_IMAGE=ingress-nginx/controller
CONTROLLER_TAG=v0.48.1
PATCH_REGISTRY=docker.io
PATCH_IMAGE=jettech/kube-webhook-certgen
PATCH_TAG=v1.5.1
DEFAULTBACKEND_REGISTRY=k8s.gcr.io
DEFAULTBACKEND_IMAGE=defaultbackend-amd64
DEFAULTBACKEND_TAG=1.5

az acr import --name $REGISTRY_NAME --source $CONTROLLER_REGISTRY/$CONTROLLER_IMAGE:$CONTROLLER_TAG --image $CONTROLLER_IMAGE:$CONTROLLER_TAG
az acr import --name $REGISTRY_NAME --source $PATCH_REGISTRY/$PATCH_IMAGE:$PATCH_TAG --image $PATCH_IMAGE:$PATCH_TAG
az acr import --name $REGISTRY_NAME --source $DEFAULTBACKEND_REGISTRY/$DEFAULTBACKEND_IMAGE:$DEFAULTBACKEND_TAG --image $DEFAULTBACKEND_IMAGE:$DEFAULTBACKEND_TAG
```

> [!NOTE]
> En plus d’importer des images de conteneur dans votre ACR, vous pouvez également importer des graphiques Helm dans votre ACR. Pour plus d’informations, consultez [Envoyer (push) et tirer (pull) des graphiques Helm vers un registre de conteneurs Azure][acr-helm].

### <a name="run-helm-charts"></a>Exécuter des graphiques Helm

Pour installer des charts avec Helm, utilisez la commande [helm install][helm-install-command] et spécifiez un nom de version et le nom du chart à installer. Pour vous montrer comment se déroule l’installation d’un graphique Helm, nous allons effectuer un déploiement nginx de base à l’aide d’un graphique Helm.

> [!TIP]
> L’exemple suivant crée un espace de noms Kubernetes pour les ressources d’entrée *ingress-basic* et est destiné à fonctionner dans cet espace de noms. Spécifiez un espace de noms de votre propre environnement, si besoin.

```console
ACR_URL=<REGISTRY_URL>

# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."kubernetes\.io/os"=linux \
    --set controller.image.registry=$ACR_URL \
    --set controller.image.image=$CONTROLLER_IMAGE \
    --set controller.image.tag=$CONTROLLER_TAG \
     --set controller.image.digest="" \
    --set controller.admissionWebhooks.patch.nodeSelector."kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.image.registry=$ACR_URL \
    --set controller.admissionWebhooks.patch.image.image=$PATCH_IMAGE \
    --set controller.admissionWebhooks.patch.image.tag=$PATCH_TAG \
    --set defaultBackend.nodeSelector."kubernetes\.io/os"=linux \
    --set defaultBackend.image.registry=$ACR_URL \
    --set defaultBackend.image.image=$DEFAULTBACKEND_IMAGE \
    --set defaultBackend.image.tag=$DEFAULTBACKEND_TAG
```

La sortie condensée suivante montre l’état du déploiement des ressources Kubernetes créées par le graphique Helm :

```console
NAME: nginx-ingress
LAST DEPLOYED: Wed Jul 28 11:35:29 2021
NAMESPACE: ingress-basic
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The ingress-nginx controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller'
...
```

Utilisez la commande `kubectl get services` pour accéder à l’*EXTERNAL-IP* de votre service.

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

Par exemple, la commande ci-dessous affiche l’*EXTERNAL-IP* pour le service *nginx-ingress-ingress-nginx-controller* :

```console
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.254.93   <EXTERNAL_IP>   80:30004/TCP,443:30348/TCP   61s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

### <a name="list-releases"></a>Répertorier les versions

Pour afficher la liste des versions installées sur votre cluster, utilisez la commande `helm list`.

```console
helm list --namespace ingress-basic
```

L’exemple suivant montre la version *my-nginx-ingress* déployée à l’étape précédente :

```console
$ helm list --namespace ingress-basic
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress   ingress-basic   1               2021-07-28 11:35:29.9623734 -0500 CDT   deployed        ingress-nginx-3.34.0    0.47.0
```

### <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous déployez un graphique Helm, une série de ressources Kubernetes est créée. Ces ressources incluent des pods, des déploiements et des services. Pour nettoyer ces ressources, utilisez la commande [helm uninstall][helm-cleanup] et spécifiez le nom de votre version, tel que fourni par la commande `helm list` précédente.

```console
helm uninstall --namespace ingress-basic nginx-ingress
```

L’exemple suivant illustre la version nommée *my-nginx-ingress* qui a été désinstallé :

```console
$ helm uninstall --namespace ingress-basic nginx-ingress

release "nginx-ingress" uninstalled
```

Pour supprimer l’espace de noms exemple en entier, utilisez la commande `kubectl delete` et spécifiez le nom de votre espace de noms. Toutes les ressources de l’espace de noms sont supprimées.

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la gestion des déploiements d’applications Kubernetes, consultez la documentation Helm.

> [!div class="nextstepaction"]
> [Documentation de Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-cleanup]: https://helm.sh/docs/intro/using_helm/#helm-uninstall-uninstalling-a-release
[helm-documentation]: https://helm.sh/docs/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-command]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-repo-add]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helm-search]: https://helm.sh/docs/intro/using_helm/#helm-search-finding-charts
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[ingress-nginx-helm-chart]: https://github.com/kubernetes/ingress-nginx/tree/main/charts/ingress-nginx
            
<!-- LINKS - internal -->
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-integrated-acr]: cluster-container-registry-integration.md?tabs=azure-cli#create-a-new-aks-cluster-with-acr-integration
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[taints]: operator-best-practices-advanced-scheduler.md