---
title: Développer sur Azure Kubernetes Service (AKS) avec Helm
description: Utilisez Helm avec AKS et Azure Container Registry pour empaqueter et exécuter des conteneurs d’applications dans un cluster.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 07/15/2021
ms.author: zarhoads
ms.openlocfilehash: cc060b0b23cbcef0551ec2660856d453b8a3b0e2
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114454145"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Démarrage rapide : Développer sur Azure Kubernetes Service (AKS) avec Helm

[Helm][helm] est un outil d’empaquetage open source qui vous aide à installer et à gérer le cycle de vie d’applications Kubernetes. À l'instar de gestionnaires de package Linux tels qu'*APT* et *Yum*, Helm sert à gérer les charts Kubernetes, qui sont des packages de ressources Kubernetes préconfigurées.

Dans ce guide de démarrage rapide, vous allez utiliser Helm pour empaqueter et exécuter une application sur AKS. Pour plus d’informations sur l’installation d’une application existante à l’aide de Helm, consultez le guide pratique [Installer des applications existantes avec Helm dans AKS][helm-existing].

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free).
* [Azure CLI](/cli/azure/install-azure-cli).
* [Helm v3 installé][helm-install].

## <a name="create-an-azure-container-registry"></a>Création d’un Azure Container Registry
Vous devrez stocker vos images conteneur dans un registre Azure Container Registry (ACR) pour exécuter votre application dans votre cluster AKS à l'aide de Helm. Fournissez votre propre nom de registre unique dans Azure et contenant 5 à 50 caractères alphanumériques. La référence SKU *De base* est un point d’entrée au coût optimisé fourni à des fins de développement qui offre un bon équilibre entre stockage et débit.

L’exemple ci-dessous utilise [az acr create][az-acr-create] pour créer un ACR nommé *MyHelmACR* dans *MyResourceGroup* avec la référence SKU *De base*.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

Vous devez obtenir un résultat semblable à l’exemple qui suit. Notez la valeur *loginServer* pour votre ACR, car vous l’utiliserez dans une étape ultérieure. Dans l’exemple ci-dessous, *myhelmacr.azurecr.io* est la valeur *loginServer* pour *MyHelmACR*.

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyHelmACR",
  "location": "eastus",
  "loginServer": "myhelmacr.azurecr.io",
  "name": "MyHelmACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="create-an-aks-cluster"></a>Créer un cluster AKS

Votre nouveau cluster AKS doit accéder à votre ACR pour extraire les images conteneur et les exécuter. Utilisez la commande suivante pour :
* Créez un cluster AKS appelé *MyAKS* et attachez *MyHelmACR*.
* Accordez au cluster *MyAKS* l’accès à votre ACR *MyHelmACR*.


```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

## <a name="connect-to-your-aks-cluster"></a>Se connecter à votre cluster AKS

Pour connecter localement un cluster Kubernetes, utilisez [kubectl][kubectl], le client de ligne de commande Kubernetes. Si vous utilisez Azure Cloud Shell, `kubectl` est déjà installé. 

1. Installez `kubectl` en local avec la commande `az aks install-cli` :

    ```azurecli
    az aks install-cli
    ```

2. Configurez `kubectl` afin de vous connecter à votre cluster Kubernetes avec la commande `az aks get-credentials`. L’exemple de commande suivant obtient les informations d’identification du cluster AKS nommé *MyAKS* dans *MyResourceGroup* :  

    ```azurecli
    az aks get-credentials --resource-group MyResourceGroup --name MyAKS
    ```

## <a name="download-the-sample-application"></a>Téléchargement de l'exemple d'application

Ce démarrage rapide utilise l’[application Azure Vote][azure-vote-app]. Clonez l’application à partir de GitHub, puis accédez au répertoire `azure-vote`.

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
cd azure-voting-app-redis/azure-vote/
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>Créer et envoyer (push) l’exemple d’application vers l’ACR

En utilisant le fichier Dockerfile précédent, exécutez la commande [az acr build][az-acr-build] pour générer et envoyer (push) une image au registre. Le `.` à la fin de la commande définit l’emplacement du fichier Dockerfile (dans ce cas, le répertoire actif).

```azurecli
az acr build --image azure-vote-front:v1 \
  --registry MyHelmACR \
  --file Dockerfile .
```

> [!NOTE]
> En plus d’importer des images de conteneur dans votre ACR, vous pouvez également importer des graphiques Helm dans votre ACR. Pour plus d’informations, consultez [Envoyer (push) et tirer (pull) des graphiques Helm vers un registre de conteneurs Azure][acr-helm].

## <a name="create-your-helm-chart"></a>Créer votre chart Helm

Générez votre chart Helm à l’aide de la commande `helm create`.

```console
helm create azure-vote-front
```

Mettez à jour *azure-vote-front/Chart.yaml* afin d’ajouter une dépendance pour le graphique *redis* à partir du référentiel de graphiques `https://charts.bitnami.com/bitnami` et mettez à jour `appVersion` vers `v1`. Par exemple :

```yml
apiVersion: v2
name: azure-vote-front
description: A Helm chart for Kubernetes

dependencies:
  - name: redis
    version: 14.7.1
    repository: https://charts.bitnami.com/bitnami

...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

Mettez à jour les dépendances de votre graphique Helm à l’aide de `helm dependency update` :

```console
helm dependency update azure-vote-front
```

Mettez à jour *azure-vote-front/values.yaml*:
* Ajoutez une section *redis* pour définir les détails d’image, le port de conteneur et le nom du déploiement.
* Ajoutez un *backendName* pour connecter la portion frontend au déploiement *redis*.
* Redéfinissez *image.repository* sur `<loginServer>/azure-vote-front`.
* Redéfinissez *image.tag* sur `v1`.
* Redéfinissez *service.type* sur *LoadBalancer*.

Par exemple :

```yml
# Default values for azure-vote-front.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1
backendName: azure-vote-backend-master
redis:
  image:
    registry: mcr.microsoft.com
    repository: oss/bitnami/redis
    tag: 6.0.8
  fullnameOverride: azure-vote-backend
  auth:
    enabled: false

image:
  repository: myhelmacr.azurecr.io/azure-vote-front
  pullPolicy: IfNotPresent
  tag: "v1"
...
service:
  type: LoadBalancer
  port: 80
...
```

Ajoutez une section `env` à *azure-vote-front/templates/deployment.yaml* pour transmettre le nom du déploiement *redis*.

```yml
...
      containers:
        - name: {{ .Chart.Name }}
          securityContext:
            {{- toYaml .Values.securityContext | nindent 12 }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag | default .Chart.AppVersion }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          env:
          - name: REDIS
            value: {{ .Values.backendName }}
...
```

## <a name="run-your-helm-chart"></a>Exécuter votre chart Helm

Installez votre application à l'aide de votre chart Helm en utilisant la commande `helm install`.

```console
helm install azure-vote-front azure-vote-front/
```

Le renvoi d’une adresse IP publique par le service prend plusieurs minutes. Surveillez la progression avec la commande `kubectl get service` et l’argument `--watch`.

```console
$ kubectl get service azure-vote-front --watch
NAME               TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.18.228   <pending>       80:32021/TCP   6s
...
azure-vote-front   LoadBalancer   10.0.18.228   52.188.140.81   80:32021/TCP   2m6s
```

Accédez à l’équilibreur de charge de votre application dans un navigateur en utilisant `<EXTERNAL-IP>` pour voir l’exemple d’application.

## <a name="delete-the-cluster"></a>Supprimer le cluster

Utilisez la commande [az group delete][az-group-delete] pour supprimer le groupe de ressources, le cluster AKS, le registre de conteneurs, les images conteneur qui sont stockées dans l’ACR, et toutes les ressources associées.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Lorsque vous supprimez le cluster, le principal de service Azure Active Directory utilisé par le cluster AKS n’est pas supprimé. Pour obtenir des instructions sur la façon de supprimer le principal de service, consultez [Considérations et suppression du principal de service AKS][sp-delete].
> 
> Si vous avez utilisé une identité managée, l’identité est managée par la plateforme et n’a pas besoin d’être supprimée.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de Helm, consultez la documentation de Helm.

> [!div class="nextstepaction"]
> [Documentation de Helm][helm-documentation]

[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-group-delete]: /cli/azure/group#az_group_delete
[az aks get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az aks install-cli]: /cli/azure/aks#az_aks_install_cli
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[acr-helm]: ../container-registry/container-registry-helm-repos.md