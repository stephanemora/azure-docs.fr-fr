---
title: 'Démarrage rapide : Déployer un cluster AKS (Azure Kubernetes Service) avec des nœuds d’informatique confidentielle à l’aide d’Azure CLI'
description: Apprenez à créer un cluster AKS avec des nœuds d’informatique confidentielle et à déployer une application Hello World en utilisant Azure CLI.
author: agowdamsft
ms.service: container-service
ms.topic: quickstart
ms.date: 2/25/2020
ms.author: amgowda
ms.openlocfilehash: 51b0813849236d9335d1482019f740fc8b23749f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703284"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-dcsv2-using-azure-cli"></a>Démarrage rapide : Déployer un cluster AKS (Azure Kubernetes Service) avec des nœuds d’informatique confidentielle (DCsv2) à l’aide d’Azure CLI

Ce guide de démarrage rapide est destiné aux développeurs ou aux opérateurs de cluster qui souhaitent créer rapidement un cluster AKS et déployer une application de surveillance des applications en utilisant le service Kubernetes managé dans Azure. Vous pouvez également approvisionner le cluster et ajouter des nœuds de calcul confidentiels à partir du Portail Azure.

## <a name="overview"></a>Vue d’ensemble

Dans ce guide de démarrage rapide, vous allez apprendre à déployer un cluster AKS (Azure Kubernetes Service) avec des nœuds de calcul confidentiels à l’aide d’Azure CLI, et à exécuter une application Hello World dans une enclave. AKS est un service Kubernetes géré qui vous permet de déployer et de gérer rapidement des clusters. Vous trouverez [ici](../aks/intro-kubernetes.md) plus d’informations sur AKS.

> [!NOTE]
> Les machines virtuelles DCsv2 pour l’informatique confidentielle utilisent du matériel spécialisé plus cher et dépendant de la disponibilité régionale. Pour plus d’informations sur les [références SKU disponibles et les régions prises en charge](virtual-machine-solutions.md), consultez la page consacrée aux machines virtuelles.

### <a name="confidential-computing-node-features-dcxs-v2"></a>Fonctionnalités des nœuds d’informatique confidentielle (DC<x>s-v2)

1. Nœuds Worker Linux prenant en charge les conteneurs Linux
1. Machine virtuelle de deuxième génération avec des nœuds de machines virtuelles Ubuntu 18.04
1. Processeur Intel SGX avec mémoire EPC (Encrypted Page Cache). En savoir plus [ici](./faq.md)
1. Prise en charge de Kubernetes version 1.16+
1. Pilote Intel SGX DCAP préinstallé sur les nœuds AKS. En savoir plus [ici](./faq.md)

## <a name="deployment-prerequisites"></a>Conditions préalables au déploiement
Le didacticiel de déploiement requiert les éléments suivants :

1. Un abonnement Azure actif. Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer
1. Avoir la version 2.0.64 ou ultérieure d’Azure CLI installée et configurée sur votre machine de déploiement (exécutez `az --version` pour connaître la version). Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](../container-registry/container-registry-get-started-azure-cli.md).
1. Avoir au moins six cœurs **DC<x>s-v2** disponibles dans votre abonnement pour l’utilisation. Par défaut, le quota de cœurs de machine virtuelle pour l’informatique confidentielle est de 8 cœurs par abonnement Azure. Si vous envisagez de provisionner un cluster qui nécessite plus de 8 cœurs, suivez [ces instructions](../azure-portal/supportability/per-vm-quota-requests.md) pour créer un ticket de demande d’augmentation du quota

## <a name="creating-new-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Création d’un cluster AKS avec des nœuds de calcul confidentiels et un module complémentaire
Suivez les instructions ci-dessous pour ajouter des nœuds de calcul confidentiels avec le module complémentaire.

### <a name="step-1-creating-an-aks-cluster-with-system-node-pool"></a>Étape 1 : création d’un cluster AKS avec un pool de nœuds système

Si vous disposez déjà d’un cluster AKS qui remplit les exigences ci-dessus, [passez à la section avec cluster existant](#existing-cluster) pour ajouter un nouveau pool de nœuds d’informatique confidentielle.

Tout d’abord, créez un groupe de ressources pour le cluster avec la commande az group create. L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* dans la région *westus2* :

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Créez maintenant un cluster AKS avec la commande az aks create.

```azurecli-interactive
# Create a new AKS cluster with system node pool with Confidential Computing addon enabled
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```
La procédure ci-dessus crée un cluster AKS avec un pool de nœuds système et un module complémentaire activé. Maintenant, ajoutez un nœud utilisateur de type de pool de nœuds d’informatique confidentielle sur AKS (DCsv2)

### <a name="step-2-adding-confidential-computing-node-pool-to-aks-cluster"></a>Étape 2 : Ajout d’un pool de nœuds d’informatique confidentielle à un cluster AKS existant 

Exécutez la commande ci-dessous à un pool de nœuds utilisateur de `Standard_DC2s_v2` avec 3 nœuds. Vous pouvez choisir une autre liste de régions et références SKU DCsv2 prises en charge [ici](../virtual-machines/dcv2-series.md) :

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```
La commande ci-dessus est terminée. Un nouveau pool de nœuds avec **DC<x>s-v2** doit être visible avec les ensembles de démons module complémentaire de calcul confidentiel ([plug-in d’appareil SGX](confidential-nodes-aks-overview.md#sgx-plugin))
 
### <a name="step-3-verify-the-node-pool-and-add-on"></a>Étape 3 : Vérifier le pool de nœuds et le module complémentaire
Récupérez les informations d’identification de votre cluster AKS avec la commande az aks get-credentials :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```
Vérifiez que les nœuds sont correctement créés et que les ensembles de démons SGX s’exécutent sur des pools de nœuds **DC<x>s-v2**, en utilisant la commande kubectl get pods et nodes comme indiqué ci-dessous :

```console
$ kubectl get pods --all-namespaces

output
kube-system     sgx-device-plugin-xxxx     1/1     Running
```
Si vous obtenez la sortie ci-dessus, votre cluster AKS est maintenant prêt à exécuter des applications confidentielles.

Passez à la section de déploiement de l’application [Hello World à partir de l’enclave](#hello-world) pour tester une application dans une enclave. Ou alors, suivez les instructions ci-dessous pour ajouter des pools de nœuds supplémentaires sur AKS (AKS prend en charge la combinaison de pools de nœuds SGX et de pools de nœuds non-SGX)

## <a name="adding-confidential-computing-node-pool-to-existing-aks-cluster"></a>Ajout d’un pool de nœuds d’informatique confidentielle à un cluster AKS existant<a id="existing-cluster"></a>

Cette section suppose que vous disposez déjà d’un cluster AKS qui répond aux critères indiqués dans la section des conditions préalables (s’applique au module complémentaire).

### <a name="step-1-enabling-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>Étape 1 : Activation du module complémentaire AKS de l’informatique confidentielle sur le cluster existant

Exécutez la commande ci-dessous pour activer le module complémentaire de l’informatique confidentielle

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```
### <a name="step-2-add-dcxs-v2-user-node-pool-to-the-cluster"></a>Étape 2 : Ajoutez maintenant un pool de nœuds utilisateur **DC<x>s-v2** au cluster
    
> [!NOTE]
> Pour utiliser la fonctionnalité d’informatique confidentielle, il faut que votre cluster AKS dispose au minimum d’un pool de nœuds basés sur la référence SKU de machine virtuelle **DC<x>s-v2**. Pour en savoir plus sur les référence SKU de machine virtuelle DCsv2 d’informatique confidentielle, consultez [Références SKU disponibles et régions prises en charge](virtual-machine-solutions.md).
    
  ```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2

output node pool added

Verify

az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```
La commande ci-dessus doit répertorier le pool de nœuds récent que vous avez ajouté avec le nom confcompool1.

### <a name="step-3-verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Étape 3 : Vérifiez que les ensembles de démons s’exécutent sur des pools de nœuds confidentiels

Connectez-vous à votre cluster AKS existant pour effectuer la vérification ci-dessous. 

```console
kubectl get nodes
```
La sortie affiche normalement le pool confcompool1 qui vient d’être ajouté au cluster AKS.

```console
$ kubectl get pods --all-namespaces

output (you may also see other daemonsets along SGX daemonsets as below)
kube-system     sgx-device-plugin-xxxx     1/1     Running
```
Si vous obtenez la sortie ci-dessus, votre cluster AKS est maintenant prêt à exécuter des applications confidentielles. Veuillez suivre le déploiement de l’application de test.

## <a name="hello-world-from-isolated-enclave-application"></a>Hello World de l’application isolée en enclave <a id="hello-world"></a>
Créez un fichier nommé *hello-world-enclave.yaml* et collez le manifeste YAML suivant. Cet exemple de code d’application Open Enclave est disponible dans le [projet Open Enclave](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). Le déploiement ci-dessous suppose que vous avez déployé le module complémentaire « confcom ».

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
```

```console
$ kubectl get jobs -l app=sgx-test
NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test
```

```console
$ kubectl get pods -l app=sgx-test
NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test
```

```console
$ kubectl logs -l app=sgx-test
Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour supprimer les pools de nœuds associés ou supprimer le cluster AKS, utilisez les commandes ci-dessous :

Suppression du cluster AKS
``````azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```
Removing the confidential computing node pool

``````azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name myNodePoolName --resource-group myResourceGroup
``````

## <a name="next-steps"></a>Étapes suivantes

Exécutez des applications Python, Node, etc. de manière confidentielle dans des conteneurs confidentiels en utilisant ces [exemples de conteneurs confidentiels](https://github.com/Azure-Samples/confidential-container-samples).

Exécutez des applications d’enclave en utilisant ces [exemples de conteneur Azure reconnaissant l’enclave](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/).
