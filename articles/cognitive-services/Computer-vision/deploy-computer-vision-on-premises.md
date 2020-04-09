---
title: Utiliser le conteneur Vision par ordinateur avec Kubernetes et Helm
titleSuffix: Azure Cognitive Services
description: Déployez le conteneur Vision par ordinateur sur Azure Container Instance, et testez-le dans un navigateur web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 9aac374de5af748eafbe4c22e5fc89f64e483c2a
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877969"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Utiliser le conteneur Vision par ordinateur avec Kubernetes et Helm

L’une des options permettant de gérer vos conteneurs Vision par ordinateur en local consiste à utiliser Kubernetes et Helm. En utilisant Kubernetes et Helm pour définir une image de conteneur Vision par ordinateur, nous allons créer un package Kubernetes. Ce package sera déployé sur un cluster Kubernetes local. Enfin, nous allons découvrir comment tester les services déployés. Pour plus d’informations sur l’exécution de conteneurs Docker sans orchestration Kubernetes, consultez [Installer et exécuter des conteneurs Vision par ordinateur](computer-vision-how-to-install-containers.md).

## <a name="prerequisites"></a>Prérequis

L’utilisation locale des conteneurs Vision par ordinateur est soumise aux prérequis suivants :

| Obligatoire | Objectif |
|----------|---------|
| Compte Azure. | Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit][free-azure-account] avant de commencer. |
| Kubernetes CLI | L’interface [Kubernetes CLI][kubernetes-cli] est requise pour gérer les informations d’identification partagées à partir du registre de conteneurs. Kubernetes est également nécessaire avant Helm, qui est le gestionnaire de package de Kubernetes. |
| Helm CLI | Installez l’[interface de ligne de commande Helm][helm-install], utilisée pour installer un graphique Helm (définition de package de conteneur). |
| Ressource Vision par ordinateur |Pour pouvoir utiliser le conteneur, vous devez disposer des éléments suivants :<br><br>Une ressource **Vision par ordinateur** Azure, la clé d’API associée et l’URI de point de terminaison. Les deux valeurs, disponibles dans les pages Vue d’ensemble et Clés de la ressource, sont nécessaires au démarrage du conteneur.<br><br>**{API_KEY}**  : L’une des deux clés de ressource disponibles à la page **Clés**<br><br>**{ENDPOINT_URI}**  : Le point de terminaison tel qu'il est fourni à la page**Vue d’ensemble**|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>L’ordinateur hôte

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Exigences et suggestions relatives au conteneur

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Se connecter au cluster Kubernetes

L’ordinateur hôte doit avoir un cluster Kubernetes disponible. Consultez ce didacticiel sur [le déploiement d’un cluster Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) pour des informations conceptuelles sur la façon de déployer un cluster Kubernetes sur un ordinateur hôte.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Partage des informations d’identification au docker avec le cluster Kubernetes

Pour permettre au cluster Kubernetes d’effectuer `docker pull` pour la ou les images configurées sur le registre de conteneurs `containerpreview.azurecr.io`, vous devez transférer les informations d’identification du docker dans le cluster. Exécutez la commande [`kubectl create`][kubectl-create] ci-dessous pour créer un *secret docker-registry* selon les informations d’identification fournies par les prérequis d’accès au registre de conteneurs.

À partir de l’interface de ligne de commande de votre choix, exécutez la commande suivante. Veillez à remplacer `<username>`, `<password>` et `<email-address>` par les informations d’identification du registre de conteneurs.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Si vous avez déjà accès au registre de conteneurs `containerpreview.azurecr.io`, vous pouvez créer un secret Kubernetes avec l’indicateur générique à la place. Envisagez la commande suivante qui s’exécute sur le JSON de configuration de votre docker.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

La sortie suivante s’affiche sur la console lorsque le secret a été créé avec succès.

```console
secret "containerpreview" created
```

Pour vérifier que le secret a été créé, exécutez [`kubectl get`][kubectl-get] avec l’indicateur `secrets`.

```console
kubectl get secrets
```

L’exécution de `kubectl get secrets` affiche tous les secrets configurés.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Configurer les valeurs du graphique Helm pour le déploiement

Commencez par créer un dossier nommé *read*, puis collez le contenu YAML suivant dans un nouveau fichier nommé *Chart.yml*.

```yaml
apiVersion: v1
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
```

Pour configurer les valeurs par défaut du graphique Helm, copiez et collez le YAML suivant dans un fichier nommé `values.yaml`. Remplacez les commentaires `# {ENDPOINT_URI}` et `# {API_KEY}` par vos propres valeurs.

```yaml
# These settings are deployment specific and users can provide customizations

read:
  enabled: true
  image:
    name: cognitive-services-read
    registry: containerpreview.azurecr.io/
    repository: microsoft/cognitive-services-read
    tag: latest
    pullSecret: containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Si les valeurs `billing` et `apikey` ne sont pas fournies, les services expireront après 15 minutes. De même, la vérification échouera, car les services ne seront pas disponibles.

Créez un dossier de *modèles* sous le répertoire *read*. Copiez et collez la configuration YAML suivante dans un fichier nommé `deployment.yaml`. Le fichier `deployment.yaml` servira de modèle Helm.

> Les modèles génèrent des fichiers manifeste, qui sont des descriptions de ressources au format YAML que Kubernetes peut comprendre. [- Guide du modèle de graphique Helm][chart-template-guide]

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: read
spec:
  template:
    metadata:
      labels:
        app: read-app
    spec:
      containers:
      - name: {{.Values.read.image.name}}
        image: {{.Values.read.image.registry}}{{.Values.read.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.read.image.args.eula}}
        - name: billing
          value: {{.Values.read.image.args.billing}}
        - name: apikey
          value: {{.Values.read.image.args.apikey}}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}

--- 
apiVersion: v1
kind: Service
metadata:
  name: read
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

Le modèle spécifie un service d’équilibrage de charge et le déploiement de votre conteneur/image pour Lire.

### <a name="the-kubernetes-package-helm-chart"></a>Le package Kubernetes (graphique Helm)

Le *graphique Helm* contient la configuration de la ou des images docker à extraire du registre de conteneurs `containerpreview.azurecr.io`.

> Un [graphique Helm][helm-charts] est une collection de fichiers qui décrivent un ensemble de ressources Kubernetes. Un graphique unique peut être utilisé pour déployer quelque chose de simple comme un pod mis en cache, ou quelque chose de complexe, comme une pile d’application web complète avec des serveurs HTTP, des bases de données, des caches et ainsi de suite.

Les *graphiques Helm* fournis tirent les images docker du service Vision par ordinateur, et le service correspondant du registre de conteneurs `containerpreview.azurecr.io`.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Installer le graphique Helm sur le cluster Kubernetes

Pour installer le *graphique Helm*, vous devez exécuter la commande [`helm install`][helm-install-cmd]. Veillez à exécuter la commande d’installation à partir du répertoire situé au-dessus du dossier `read`.

```console
helm install read ./read
```

Voici un exemple de sortie que vous pouvez vous attendre à voir pour une exécution d’installation réussie :

```console
NAME: read
LAST DEPLOYED: Thu Sep 04 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                    READY  STATUS             RESTARTS  AGE
read-57cb76bcf7-45sdh   0/1    ContainerCreating  0         0s

==> v1/Service
NAME     TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
read     LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME    READY  UP-TO-DATE  AVAILABLE  AGE
read    0/1    1           0          0s
```

Le déploiement de Kubernetes peut prendre plusieurs minutes. Pour vérifier que les pods et les services sont correctement déployés et disponibles, exécutez la commande suivante :

```console
kubectl get all
```

Vous devriez obtenir un graphique similaire à la sortie suivante :

```console
kubectl get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/read-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes     ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/read           LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   1/1     1            1           17s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/read-57cb76bcf7   1         1         1       17s
```
<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’installation d’applications avec Helm dans Azure Kubernetes Service (AKS), [consultez ceci][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Conteneurs Cognitive Services][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
