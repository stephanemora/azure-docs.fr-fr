---
title: 'Démarrage rapide : Déployer un cluster AKS (Azure Kubernetes Service) avec des nœuds d’informatique confidentielle à l’aide d’Azure CLI'
description: Avec ce démarrage rapide, vous apprenez comment créer un cluster AKS avec des nœuds confidentiels et comment déployer une application hello world avec Azure CLI.
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: quickstart
ms.date: 03/18/2020
ms.author: amgowda
ms.custom: contentperf-fy21q3
ms.openlocfilehash: 73770acefc8a153e4a2f2fde146f9afd4c319cd3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933132"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-dcsv2-using-azure-cli"></a>Démarrage rapide : Déployer un cluster AKS (Azure Kubernetes Service) avec des nœuds d’informatique confidentielle (DCsv2) à l’aide d’Azure CLI

Ce guide de démarrage rapide est destiné aux développeurs ou aux opérateurs de cluster qui souhaitent créer rapidement un cluster AKS et déployer une application de surveillance des applications en utilisant le service Kubernetes managé dans Azure. Vous pouvez également approvisionner le cluster et ajouter des nœuds de calcul confidentiels à partir du Portail Azure.

## <a name="overview"></a>Vue d’ensemble

Dans ce guide de démarrage rapide, vous allez apprendre à déployer un cluster AKS (Azure Kubernetes Service) avec des nœuds de calcul confidentiels à l’aide d’Azure CLI, et à exécuter une application Hello World dans une enclave. AKS est un service Kubernetes géré qui vous permet de déployer et de gérer rapidement des clusters. Pour en savoir plus, consultez l' [Introduction AKS](../aks/intro-kubernetes.md) et la [Présentation des nœuds confidentiels AKS](confidential-nodes-aks-overview.md).

> [!NOTE]
> Les machines virtuelles DCsv2 pour l’informatique confidentielle utilisent du matériel spécialisé plus cher et dépendant de la disponibilité régionale. Pour plus d’informations sur les [références SKU disponibles et les régions prises en charge](virtual-machine-solutions.md), consultez la page consacrée aux machines virtuelles.

### <a name="confidential-computing-node-features-dcsv2"></a>Fonctionnalités des nœuds d’informatique confidentielle (DCsv2)

1. Nœuds Worker Linux prenant en charge les conteneurs Linux.
1. Machine virtuelle de deuxième génération avec des nœuds de machines virtuelles Ubuntu 18.04.
1. Processeur Intel SGX avec mémoire EPC (Encrypted Page Cache). En savoir plus [ici](./faq.md).
1. Support pour la version Kubernetes 1.16 et versions ultérieures.
1. Pilote Intel SGX DCAP préinstallé sur les nœuds AKS. En savoir plus [ici](./faq.md).

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

1. Un abonnement Azure actif. Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
1. Avoir la version 2.0.64 ou ultérieure d’Azure CLI installée et configurée sur votre machine de déploiement (exécutez `az --version` pour connaître la version). Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](../container-registry/container-registry-get-started-azure-cli.md).
1. Au moins six cœurs **DCsv2** disponibles dans votre abonnement. Par défaut, le quota de cœurs de machines virtuelles pour l’informatique confidentielle est de huit cœurs par abonnement Azure. Si vous envisagez d’approvisionner un cluster qui nécessite plus de 8 cœurs, suivez [ces](../azure-portal/supportability/per-vm-quota-requests.md) instructions pour créer un ticket d’augmentation du quota.

## <a name="create-a-new-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Créer un nouveau cluster AKS avec des nœuds d’informatique confidentielle et un module complémentaire

Suivez les instructions ci-dessous pour ajouter des nœuds de calcul confidentiels avec le module complémentaire.

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>Créer un cluster AKS avec un pool de nœuds du système

Si vous disposez déjà d’un cluster AKS qui remplit les exigences ci-dessus, [passez à la section avec cluster existant](#existing-cluster) pour ajouter un nouveau pool de nœuds d’informatique confidentielle.

Tout d’abord, créez un groupe de ressources pour le cluster avec la commande [az group create][az-group-create]. L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* dans la région *westus2* :

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Créez maintenant un cluster AKS avec la commande [az aks create][az-aks-create] :

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```

La procédure ci-dessus crée un nouveau cluster AKS avec un pool de nœuds du système et un module complémentaire activé. Ensuite, ajoutez un pool de nœuds d’utilisateur avec des fonctionnalités d’informatique confidentielle au cluster AKS.

### <a name="add-a-confidential-computing-node-pool-to-the-aks-cluster"></a>Ajoutez un pool de nœuds d’informatique confidentielle au cluster AKS 

Exécutez la commande suivante pour ajouter un pool de nœuds d’utilisateur dont la taille `Standard_DC2s_v2` est de trois nœuds. Vous pouvez choisir un SKU à partir de la liste prise en charge de [SKU DCsv2 et des régions](../virtual-machines/dcv2-series.md).

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

Après avoir réalisé cette étape, un nouveau pool de nœuds avec **DCsv2** doit s’afficher avec des daemonsets de modules complémentaires d’informatique confidentielle ([SGX Device Plugin](confidential-nodes-aks-overview.md#sgx-plugin)).

### <a name="verify-the-node-pool-and-add-on"></a>Vérifier le pool de nœuds et le module complémentaire

Récupérez les informations d’identification de votre cluster AKS avec la commande [az aks get-credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Vérifiez que les nœuds sont correctement créés et que les daemonsets SGX s’exécutent sur des pools de nœuds **DCsv2**, en utilisant la commande kubectl get pods & nodes comme indiqué ci-dessous :

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Si vous obtenez la sortie ci-dessus, votre cluster AKS est maintenant prêt à exécuter des applications confidentielles.

Passez à la section de déploiement de [Hello World à partir de l’enclave](#hello-world) pour tester une application dans une enclave. Vous pouvez également suivre les instructions suivantes pour ajouter des pools de nœuds supplémentaires sur AKS (AKS prend en charge la combinaison de pools de nœuds SGX et de pools de nœuds non-SGX).

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>Ajouter un pool de nœuds d’informatique confidentielle à un cluster AKS existant<a id="existing-cluster"></a>

Cette section suppose que vous disposez déjà d’un cluster AKS qui répond aux critères indiqués dans la section des conditions préalables (s’applique au module complémentaire).

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>Activer le module complémentaire AKS de calcul confidentiel sur le cluster existant

Exécutez la commande suivante pour activer le module complémentaire de l’informatique confidentielle :

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2-user-node-pool-to-the-cluster"></a>Ajouter maintenant un pool de nœuds **DCsv2** au cluster

> [!NOTE]
> Pour utiliser la fonctionnalité d’informatique confidentielle, il faut que votre cluster AKS existant dispose au minimum d’un pool de nœuds basé sur la référence SKU de machine virtuelle **DCsv2**. Pour en savoir plus sur les références SKU de machine virtuelle DCs-v2 de calcul confidentiel, consultez les [références SKU disponibles et régions prises en charge](virtual-machine-solutions.md).

Exécutez la commande suivante pour créer un nouveau pool de nœuds :

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

Vérifiez que le nouveau pool de nœuds portant le nom confcompool1 a été créé :

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Vérifier que les daemonsets s’exécutent sur des pools de nœuds confidentiels

Connectez-vous à votre cluster AKS existant pour effectuer la vérification suivante.

```console
kubectl get nodes
```

La sortie affiche normalement le pool confcompool1 qui vient d’être ajouté au cluster AKS. Vous pouvez également voir d’autres daemonsets.

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Si vous obtenez la sortie ci-dessus, votre cluster AKS est maintenant prêt à exécuter des applications confidentielles. Pour déployer l’application test, suivez les instructions suivantes.

## <a name="hello-world-from-isolated-enclave-application"></a>Hello World de l’application isolée en enclave <a id="hello-world"></a>
Créez un fichier nommé *hello-world-enclave.yaml* et collez le manifeste YAML suivant. Cet exemple de code d’application Open Enclave est disponible dans le [projet Open Enclave](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). Le déploiement suivant suppose que vous avez déployé le module complémentaire « confcom ».

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 5 # This limit will automatically place the job into confidential computing node. Alternatively you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
  ```

À présent, utilisez la commande kubectl apply pour créer un exemple de travail qui s’exécutera dans une enclave sécurisée, comme dans l’exemple de sortie suivant :

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

Vous pouvez vérifier que la charge de travail a correctement créé un environnement TEE (l’enclave) en exécutant les commandes suivantes :

```console
$ kubectl get jobs -l app=sgx-test

NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test

NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test

Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour supprimer les pools de nœuds associés ou supprimer le cluster AKS, utilisez les commandes suivantes :

### <a name="remove-the-confidential-computing-node-pool"></a>Supprimer le pool de nœuds de calcul confidentiel

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name myNodePoolName --resource-group myResourceGroup
```

### <a name="delete-the-aks-cluster"></a>Supprimer le cluster AKS

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

## <a name="next-steps"></a>Étapes suivantes

* Exécutez des applications comme Python, Node, etc. de manière confidentielle dans des conteneurs confidentiels en consultant [échantillons de conteneurs confidentiels](https://github.com/Azure-Samples/confidential-container-samples).

* Exécutez des applications d’enclave en utilisant ces [exemples de conteneur Azure reconnaissant l’enclave](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/).

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials