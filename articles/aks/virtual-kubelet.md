---
title: Exécuter Virtual Kubelet dans un cluster Azure Kubernetes Service (AKS)
description: Découvrez comment utiliser Virtual Kubelet avec Azure Kubernetes Service (AKS) pour exécuter des conteneurs Linux et Windows sur Azure Container Instances.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: cc0c3becf21cb54b97a88e9ba35b38308af81a85
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475427"
---
# <a name="use-virtual-kubelet-with-azure-kubernetes-service-aks"></a>Utiliser Virtual Kubelet avec Azure Kubernetes Service (AKS)

Azure Container Instances (ACI) fournit un environnement hébergé pour l’exécution de conteneurs dans Azure. Lorsque vous utilisez ACI, il n’est pas nécessaire de gérer l’infrastructure sous-jacente de calcul ; Azure le fait pour vous. Lorsque vous exécutez des conteneurs dans ACI, vous êtes facturés à la seconde d’exécution de chaque conteneur.

Lorsque vous utilisez le fournisseur Virtual Kubelet pour Azure Container Instances, il est possible de programmer à la fois les conteneurs Linux et Windows sur une instance de conteneur, comme s’il s’agissait d’un nœud Kubernetes standard. Cette configuration vous permet de tirer parti à la fois des fonctionnalités de Kubernetes et de la facilité de gestion et des économies des instances de conteneurs.

> [!NOTE]
> AKS offre désormais une prise en charge intégrée pour la planification des conteneurs sur ACI, appelée *nœuds virtuels*. Ces nœuds virtuels prennent actuellement en charge les instances de conteneur Linux. Si vous avez besoin de planifier des instances de conteneur Windows, vous pouvez continuer à utiliser Virtual Kubelet. Sinon, vous devez utiliser les nœuds virtuels au lieu des instructions manuelles Virtual Kubelet indiquées dans cet article. Vous pouvez commencer avec des nœuds virtuels à l’aide d’[Azure CLI][virtual-nodes-cli] ou du [Portail Azure][virtual-nodes-portal].
>
> Virtual Kubelet est un projet open source expérimental, et doit donc être utilisé dans cette optique. Pour contribuer, signaler des problèmes avec les fichiers, et en savoir plus sur virtual kubelet, consultez la page du [projet Virtual Kubelet GitHub][vk-github].

## <a name="before-you-begin"></a>Avant de commencer

Ce document suppose que vous disposez d’un cluster AKS. Si vous avez besoin d’un cluster AKS, consultez le [Guide de démarrage rapide : déployer un cluster Azure Kubernetes Service (AKS)][aks-quick-start].

Vous devez également la version d’Azure CLI **2.0.65** ou version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

Pour installer Virtual Kubelet, installer et configurer [Helm] [ aks-helm] dans votre cluster AKS. Assurez-vous que votre Tiller est [configuré pour une utilisation avec Kubernetes RBAC](#for-rbac-enabled-clusters), si nécessaire.

### <a name="register-container-instances-feature-provider"></a>Inscrire le fournisseur de fonctionnalités de Container Instances

Si vous n’avez pas précédemment utilisé le service Azure Container Instance (ACI), inscrire le fournisseur de services à votre abonnement. Vous pouvez vérifier l’état d’inscription du fournisseur d’ACI à l’aide de la commande [az provider list][az-provider-list], comme dans l’exemple suivant :

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Le fournisseur de *Microsoft.ContainerInstance* doivent être signalé comme *Registered* (Inscrit), comme dans l’exemple de sortie suivant :

```console
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Si le fournisseur apparaît *NotRegistered* (Non-inscrit), inscrivez-le à l’aide de la commande [az provider register][az-provider-register] comme dans l’exemple suivant :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

### <a name="for-rbac-enabled-clusters"></a>Pour les clusters où RBAC est activé

Si RBAC est activé dans votre cluster AKS, vous devez créer un compte de service et une liaison de rôle pour une utilisation avec Tiller. Pour plus d’informations, consultez [Contrôle d'accès à Helm basé sur un rôle][helm-rbac]. Pour créer un compte de service et une liaison de rôle, créez un fichier nommé *rbac-virtual-kubelet.yaml* et collez-y la définition suivante :

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Appliquez le compte de service et la liaison avec la commande [kubectl apply][kubectl-apply] et spécifiez votre fichier *rbac-virtual-kubelet.yaml*, comme indiqué dans l’exemple suivant :

```console
$ kubectl apply -f rbac-virtual-kubelet.yaml

clusterrolebinding.rbac.authorization.k8s.io/tiller created
```

Configurez Helm pour utiliser le compte de service tiller :

```console
helm init --service-account tiller
```

Vous pouvez maintenant continuer à installer Virtual Kubelet dans votre cluster AKS.

## <a name="installation"></a>Installation

Utilisez la commande [az aks install-connector][aks-install-connector] pour installer Virtual Kubelet. L’exemple suivant permet de déployer les connecteurs Linux et Windows.

```azurecli-interactive
az aks install-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

Ces arguments sont disponibles pour le [az aks install-connecteur] [ aks-install-connector] commande.

| Argument : | Description | Obligatoire |
|---|---|:---:|
| `--connector-name` | Nom du connecteur ACI.| Oui |
| `--name``-n` | Nom du cluster géré. | Oui |
| `--resource-group``-g` | Nom du groupe de ressources. | Oui |
| `--os-type` | Type de système d’exploitation des instances de conteneur. Valeurs autorisées : Both, Linux, Windows. Valeur par défaut : Linux. | Non |
| `--aci-resource-group` | Le groupe de ressources dans lequel créer les groupes de conteneurs ACI. | Non |
| `--location``-l` | L’emplacement de création des groupes de conteneurs ACI. | Non |
| `--service-principal` | Principal de service utilisé pour l’authentification auprès des API Azure. | Non |
| `--client-secret` | Secret associé au principal du service. | Non |
| `--chart-url` | URL du chart Helm qui installe le connecteur ACI. | Non |
| `--image-tag` | La balise d’image de l’image du conteneur virtual kubelet. | Non |

## <a name="validate-virtual-kubelet"></a>Valider Virtual Kubelet

Pour valider l’installation de Virtual Kubelet, retourner une liste de nœuds Kubernetes à l’aide de la [kubectl obtenir des nœuds] [ kubectl-get] commande :

```console
$ kubectl get nodes

NAME                                             STATUS   ROLES   AGE   VERSION
aks-nodepool1-56577038-0                         Ready    agent   11m   v1.12.8
virtual-kubelet-virtual-kubelet-linux-eastus     Ready    agent   39s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
virtual-kubelet-virtual-kubelet-windows-eastus   Ready    agent   37s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
```

## <a name="run-linux-container"></a>Exécuter un conteneur Linux

Créez un fichier nommé `virtual-kubelet-linux.yaml` et copiez-y le YAML suivant. Veuillez noter que [nodeSelector][node-selector] et [toleration][toleration] sont utilisés pour planifier le conteneur au niveau du nœud.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        beta.kubernetes.io/os: linux
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

Utilisez la commande [kubectl create][kubectl-create] pour exécuter l’application.

```console
kubectl create -f virtual-kubelet-linux.yaml
```

Utilisez la commande [kubectl get pods][kubectl-get] avec l’argument `-o wide` pour sortir une liste des pods avec le nœud planifié. Remarquez que le pod `aci-helloworld` a été planifié sur le nœud `virtual-kubelet-virtual-kubelet-linux`.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
aci-helloworld-7b9ffbf946-rx87g   1/1     Running   0          22s     52.224.147.210   virtual-kubelet-virtual-kubelet-linux-eastus
```

## <a name="run-windows-container"></a>Exécuter un conteneur Windows

Créez un fichier nommé `virtual-kubelet-windows.yaml` et copiez-y le YAML suivant. Veuillez noter que [nodeSelector][node-selector] et [toleration][toleration] sont utilisés pour planifier le conteneur au niveau du nœud.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nanoserver-iis
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: microsoft/iis:nanoserver
        ports:
        - containerPort: 80
      nodeSelector:
        beta.kubernetes.io/os: windows
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

Utilisez la commande [kubectl create][kubectl-create] pour exécuter l’application.

```console
kubectl create -f virtual-kubelet-windows.yaml
```

Utilisez la commande [kubectl get pods][kubectl-get] avec l’argument `-o wide` pour sortir une liste des pods avec le nœud planifié. Remarquez que le pod `nanoserver-iis` a été planifié sur le nœud `virtual-kubelet-virtual-kubelet-windows`.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
nanoserver-iis-5d999b87d7-6h8s9   1/1     Running   0          47s     52.224.143.39    virtual-kubelet-virtual-kubelet-windows-eastus
```

## <a name="remove-virtual-kubelet"></a>Supprimer Virtual Kubelet

Utilisez la commande [az aks remove-connector][aks-remove-connector] pour désinstaller Virtual Kubelet. Remplacez les valeurs de l’argument par le nom du connecteur, le cluster AKS, et le groupe de ressources du cluster AKS.

```azurecli-interactive
az aks remove-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

> [!NOTE]
> Si vous rencontrez des erreurs lors de la suppression des deux connecteurs de système d’exploitation, ou si vous souhaitez supprimer uniquement le connecteur de système d’exploitation Windows ou Linux, vous pouvez spécifier manuellement le type du système d’exploitation. Ajoutez le paramètre `--os-type` à la commande `az aks remove-connector` précédente, puis spécifiez `Windows` ou `Linux`.

## <a name="next-steps"></a>Étapes suivantes

Pour connaître les problèmes susceptibles d’affecter Virtual Kubelet, consultez [Bizarreries connues et solutions de contournement][vk-troubleshooting]. Pour signaler des problèmes avec Virtual Kubelet, [ouvrez un problème GitHub][vk-issues].

Pour en savoir plus sur Virtual Kubelet, consultez la page du [projet Virtual Kubelet GitHub][vk-github].

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az-aks-list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector
[virtual-nodes-cli]: virtual-nodes-cli.md
[virtual-nodes-portal]: virtual-nodes-portal.md
[aks-helm]: kubernetes-helm.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[vk-troubleshooting]: https://github.com/virtual-kubelet/virtual-kubelet#known-quirks-and-workarounds
[vk-issues]: https://github.com/virtual-kubelet/virtual-kubelet/issues
