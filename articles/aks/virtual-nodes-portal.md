---
title: Créer des nœuds virtuels à l’aide du portail dans Azure Kubernetes Service (AKS)
description: Découvrez comment utiliser le Portail Azure pour créer un cluster Azure Kubernetes Service (AKS) qui s’appuie sur des nœuds virtuels pour exécuter des pods.
services: container-service
author: iainfoulds
ms.service: container-service
ms.date: 12/03/2018
ms.author: iainfou
ms.openlocfilehash: 3b99afe82f77b6bd89b5afa458179abee4c98e4f
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999129"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Créer et configurer un cluster Azure Kubernetes Service (AKS) pour utiliser des nœuds virtuels sur le Portail Azure

Pour déployer rapidement des charges de travail dans un cluster Azure Kubernetes Service (AKS), vous pouvez utiliser des nœuds virtuels. Les nœuds virtuels assurent un approvisionnement rapide des pods et sont facturés à la seconde d’exécution. Dans un scénario de mise à l’échelle, il n’est pas nécessaire d’attendre que le gestionnaire de mise à l’échelle automatique du cluster Kubernetes déploie des nœuds de calcul de machine virtuelle pour exécuter les pods supplémentaires. Cet article explique comment créer et configurer les ressources de réseau virtuel et un cluster AKS avec des nœuds virtuels activés.

> [!IMPORTANT]
> Les nœuds virtuels d’AKS sont actuellement en **préversion**. Les préversions sont à votre disposition, à condition que vous acceptiez les [conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Créer un cluster AKS

Sélectionnez **Créer une ressource** > **Kubernetes Service** dans le coin supérieur gauche du portail Azure.

Sur la page **Bases**, configurez les options suivantes :

- *DÉTAILS DU PROJET* : Choisissez un abonnement Azure, puis sélectionnez ou créez un groupe de ressources Azure, comme *myResourceGroup*. Entrez un **nom du cluster Kubernetes**, tel que *myAKSCluster*.
- *DÉTAILS DU CLUSTER* : Sélectionnez une région, une version de Kubernetes et le préfixe du nom DNS du cluster AKS.
- *MISE À L’ÉCHELLE* : Sélectionnez une taille de machine virtuelle pour les nœuds AKS. Elle ne sera **pas modifiable** une fois le cluster AKS déployé.
    - Sélectionnez également le nombre de nœuds à déployer dans le cluster. Dans le cadre de cet article, définissez **Nombre de nœuds** sur *1*. Le nombre de nœuds est **modifiable** après le déploiement du cluster.
    - Sous **Nœuds virtuels**, sélectionnez *Activé*.

![Créer un cluster AKS et activer les nœuds virtuels](media/virtual-nodes-portal/enable-virtual-nodes.png)

Par défaut, un principal de service Azure Active Directory est créé. Il a pour fonctions la communication entre les clusters et la compatibilité avec d’autres services Azure.

Le cluster est également configuré pour offrir une mise en réseau avancée. Les nœuds virtuels sont paramétrés de façon à utiliser leur propre sous-réseau de réseau virtuel Azure. Ce sous-réseau possède des permissions déléguées pour se connecter à des ressources Azure entre le cluster AKS. Si vous ne disposez pas de sous-réseau délégué, le Portail Azure crée et configure le réseau virtuel Azure et le sous-réseau d’une manière compatible avec les nœuds virtuels.

Sélectionnez **Revoir + créer**. Une fois la validation terminée, sélectionnez **Créer**.

Il ne faut que quelques minutes pour créer le cluster AKS et que celui-ci soit prêt à être utilisé.

## <a name="connect-to-the-cluster"></a>Connexion au cluster

Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. Pour gérer un cluster Kubernetes, utilisez [kubectl][kubectl], le client de ligne de commande Kubernetes. Le client `kubectl` est préinstallé dans Azure Cloud Shell.

Pour ouvrir Cloud Shell, sélectionnez **Essayer** en haut à droite d’un bloc de code. Vous pouvez également lancer Cloud Shell dans un onglet distinct du navigateur en accédant à [https://shell.azure.com/bash](https://shell.azure.com/bash). Sélectionnez **Copier** pour copier les blocs de code, collez-les dans Cloud Shell, puis appuyez sur Entrée pour les exécuter.

Exécutez la commande [az aks get-credentials][az-aks-get-credentials] pour configurer `kubectl` afin de vous connecter à votre cluster Kubernetes. L’exemple suivant obtient les informations d’identification du nom du cluster *myAKSCluster* dans le groupe de ressources nommé *myResourceGroup* :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pour vérifier la connexion à votre cluster, utilisez la commande [kubectl get][kubectl-get] pour retourner une liste des nœuds du cluster.

```azurecli-interactive
kubectl get nodes
```

L’exemple de sortie suivant illustre l’unique nœud de machine virtuelle créé, puis le nœud virtuel Linux, *virtual-node-aci-linux* :

```
$ kubectl get nodes

NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Déployer un exemple d’application

Dans Azure Cloud Shell, créez un fichier nommé `virtual-node.yaml` et copiez-y le code YAML suivant. Pour planifier le conteneur au niveau du nœud, [nodeSelector][node-selector] et [toleration][toleration] sont utilisés. Ces paramètres permettent de planifier le pod sur le nœud virtuel et de vérifier que la fonctionnalité est activée.

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
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

Exécutez l’application avec la commande [kubectl apply][kubectl-apply].

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Utilisez la commande [kubectl get pods][kubectl-get] avec l’argument `-o wide` pour obtenir la liste des pods et le nœud planifié. Remarquez que le pod `virtual-node-helloworld` a été planifié sur le nœud `virtual-node-linux`.

```
$ kubectl get pods -o wide

NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Le pod reçoit une adresse IP interne du sous-réseau de réseau virtuel Azure délégué de façon à être utilisé avec des nœuds virtuels.

## <a name="test-the-virtual-node-pod"></a>Tester le pod de nœud virtuel

Pour tester le pod en cours d’exécution sur le nœud virtuel, accédez à l’application de démonstration avec un client web. Comme le pod possède une adresse IP interne, vous pouvez tester facilement cette connectivité à partir d’un autre pod sur le cluster AKS. Créez un pod de test et attachez-y une session Terminal :

```azurecli-interactive
kubectl run -it --rm virtual-node-test --image=debian
```

Installez `curl` dans le pod en utilisant `apt-get` :

```azurecli-interactive
apt-get update && apt-get install -y curl
```

Maintenant, accédez à l’adresse de votre pod avec `curl` (par exemple, *http://10.241.0.4*). Indiquez votre propre adresse IP interne, obtenue avec la commande `kubectl get pods` précédente :

```azurecli-interactive
curl -L http://10.241.0.4
```

L’application de démonstration s’affiche, comme l’illustre l’exemple de sortie condensé suivant :

```
$ curl -L 10.241.0.4

<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Fermez la session de terminal sur votre pod de test avec `exit`. Une fois la session terminée, le pod est supprimé.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, un pod a été planifié sur le nœud virtuel et a reçu une adresse IP interne privée. Vous auriez tout aussi bien pu créer un déploiement de service et acheminer le trafic sur votre pod au moyen d’un équilibreur de charge ou d’un contrôleur d’entrée. Pour plus d’informations, consultez [Créer un contrôleur d’entrée de base dans AKS][aks-basic-ingress].

Les nœuds virtuels constituent l’un des composants d’une solution de mise à l’échelle dans AKS. Pour plus d’informations sur ces solutions, voir les articles suivants :

- [Utiliser le gestionnaire de mise à l’échelle horizontale automatique de pods Kubernetes][aks-hpa]
- [Utiliser le gestionnaire de mise à l’échelle automatique de clusters Kubernetes][aks-cluster-autoscaler]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[aks-network]: ./networking-overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: autoscaler.md
[aks-basic-ingress]: ingress-basic.md
