---
title: Utiliser le service Azure Spot Virtual Machines dans un cluster Azure Red Hat OpenShift (ARO)
description: Découvrez comment utiliser le service Azure Red Hat OpenShift dans Azure Red Hat OpenShift (ARO)
author: nilsanderselde
ms.author: suvetriv
ms.service: azure-redhat-openshift
keywords: spot, nœuds, aro, déployer, openshift, red hat
ms.topic: how-to
ms.date: 10/21/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 941db40b96d9e7a00c32b42817a3f2a93e17643a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084423"
---
# <a name="use-azure-spot-virtual-machines-in-an-azure-red-hat-openshift-aro-cluster"></a>Utiliser le service Azure Spot Virtual Machines dans un cluster Azure Red Hat OpenShift (ARO)

Cet article fournit les informations nécessaires pour vous permettre de configurer votre cluster Azure Red Hat OpenShift (ARO) afin d’utiliser le service Azure Spot Virtual Machines.

L’utilisation de machines virtuelles Azure Spot vous permet de disposer de notre capacité inutilisée en réalisant des économies significatives. Dès qu’Azure aura besoin de récupérer la capacité, l’infrastructure Azure supprimera les machines virtuelles Azure Spot. Pour plus d’informations sur les instances spot, consultez [Spot Virtual Machines](../virtual-machines/spot-vms.md).

## <a name="before-you-begin"></a>Avant de commencer

Avant de commencer, assurez-vous que vous avez déployé un cluster Azure Red Hat Openshift. Si vous avez besoin d’un cluster ARO, consultez le [Démarrage rapide ARO](tutorial-create-cluster.md) pour un cluster public, ou le [tutoriel sur les clusters privés](howto-create-private-cluster-4x.md) pour un cluster privé. Les étapes de configuration de votre cluster pour utiliser des machines virtuelles spot sont les mêmes pour les clusters publics et privés.

## <a name="add-spot-vms"></a>Ajouter des machines virtuelles spot

La gestion des machines dans Azure Red Hat Openshift s’effectue à l’aide de MachineSet. Les ressources MachineSet sont des groupes de machines. Les MachineSets sont aux machines ce que les ReplicaSets sont aux pods. Si vous avez besoin de davantage de machines ou si vous devez en réduire l’échelle, modifiez le champ *Réplicas* sur le groupe de machines en fonction de vos besoins de calcul. Pour en savoir plus, consultez notre [documentation OpenShift MachineSet](https://docs.openshift.com/container-platform/4.8/machine_management/creating_machinesets/creating-machineset-azure.html)

L’utilisation de machines virtuelles spot est spécifiée en ajoutant le champ `spotVMOptions` dans la spécification de modèle d’un MachineSet.
Pour que ce MachineSet soit créé, nous allons effectuer les opérations suivantes :

1. Obtenir une copie d’un MachineSet s’exécutant sur votre cluster.
2. Créer une configuration MachineSet modifiée.
3. Déployer ce MachineSet sur votre cluster

Tout d’abord, [connectez-vous à votre cluster OpenShift à l’aide de l’interface de ligne de commande](tutorial-connect-cluster.md).

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

Ensuite, vous allez répertorier les MachineSets présents sur votre cluster. Un cluster par défaut dispose de trois MachineSets déployés : 
```azurecli-interactive
oc get machinesets -n openshift-machine-api
```

Voici un exemple de sortie de cette commande : 
```
NAME                                    DESIRED   CURRENT   READY   AVAILABLE   AGE
aro-cluster-5t2dj-worker-eastus1   1         1         1       1           2d22h
aro-cluster-5t2dj-worker-eastus2   1         1         1       1           2d22h
aro-cluster-5t2dj-worker-eastus3   1         1         1       1           2d22h
```

Vous allez ensuite décrire le MachineSet déployé. Remplacez \<machineset\> par l’un des MachineSets répertoriés ci-dessus et dirigez cette sortie vers un fichier.

```azurecli-interactive
oc get machineset <machineset> -n openshift-machine-api -o yaml > spotmachineset.yaml
```

Vous allez devoir modifier les paramètres suivants dans le MachineSet :
- `metadata.name`
- `spec.selector.matchLabels.machine.openshift.io/cluster-api-machineset`
- `spec.template.metadata.labels.machine.openshift.io/cluster-api-machineset`
- `spec.template.spec.providerSpec.value.spotVMOptions` (Ajoutez ce champ et définissez sa valeur sur `{}`.)


Un exemple abrégé de YAML de MachineSet Spot figure ci-dessous. Cet exemple met en évidence les modifications clés que vous devez apporter lors de la création d’un MachineSet Spot sur un MachineSet de travail existant, ainsi que des informations supplémentaires pour le contexte. Il ne représente cependant pas l’intégralité d’un MachineSet fonctionnel, car de nombreux champs ont été omis.

```
apiVersion: machine.openshift.io/v1beta1
kind: MachineSet
metadata:
  name: aro-cluster-abcd1-spot-eastus
spec:
  replicas: 2
  selector:
    matchLabels:
      machine.openshift.io/cluster-api-cluster: aro-cluster-abcd1
      machine.openshift.io/cluster-api-machineset: aro-cluster-abcd1-spot-eastus
  template:
    metadata:
        machine.openshift.io/cluster-api-machineset: aro-cluster-abcd1-spot-eastus
    spec:
      providerSpec:
        value:
          spotVMOptions: {}
      taints:
        - effect: NoExecute
          key: spot
          value: 'true'
```

Une fois le fichier mis à jour, appliquez-le.

```azurecli-interactive
oc create -f spotmachineset.yaml
```

Pour vérifier que votre MachineSet a bien été créée, exécutez la commande suivante :
```azurecli-interactive
oc get machinesets -n openshift-machine-api
```

Voici un exemple de sortie. Votre MachineSet est prêt une fois que vous avez des machines dans l’état « Prêt ».
```
  NAME                                    DESIRED   CURRENT   READY   AVAILABLE   AGE
aro-cluster-5t2dj-worker-eastus1           1         1         1       1           3d1h
aro-cluster-5t2dj-worker-eastus2           1         1         1       1           3d1h
aro-cluster-5t2dj-worker-eastus3           1         1         1       1           3d1h
spot                                       1         1         1       1           2m47s
```

## <a name="schedule-interruptible-workloads"></a>Planifier des charges de travail interruptibles

Il est recommandé d’ajouter une teinte aux nœuds spot pour éviter la planification de nœuds non interruptibles sur ceux-ci, ainsi que d’ajouter des tolérances de cette teinte aux pods que vous souhaitez planifier dessus. Vous pouvez teinter les nœuds via la spécification MachineSet.

Par exemple, vous pouvez ajouter le YAML suivant à `spec.template.spec` :

```
     taints:
        - effect: NoExecute
          key: spot
          value: 'true'
```

Cela a pour effet d’empêcher la planification de pods sur le nœud résultant, sauf s’il a une tolérance pour la teinte `spot='true'`, et d’écarter les pods dépourvus de cette tolérance.

Pour en savoir plus sur l’application de teintes et de tolérances, consultez [Contrôle du positionnement de pod à l’aide de teintes de nœud](https://docs.openshift.com/container-platform/4.7/nodes/scheduling/nodes-scheduler-taints-tolerations.html).

## <a name="quota"></a>Quota

Des machines peuvent passer à un état d’échec en raison de problèmes de quota si le quota pour le type de machine que vous utilisez est trop faible pendant un bref instant, même s’il finit par être suffisant (par exemple, un nœud continue de supprimer alors qu’un autre nœud est en cours de création). Pour cette raison, il est recommandé de définir un quota pour le type de machine que vous allez utiliser pour les instances Spot de façon à ce qu’il soit légèrement plus élevé que nécessaire (par 2*n, où n est le nombre de cœurs qu’une machine utilise). Cette surcharge évite d’avoir à réparer des machines défaillantes, ce qui nécessite toujours une intervention manuelle, même si elle est relativement simple.

## <a name="node-readiness"></a>Préparation du nœud

Comme expliqué dans la documentation sur les machines virtuelles Spot, dont le lien figure ci-dessus, les machines virtuelles passent à un état d’approvisionnement libéré quand elles ne sont plus disponibles ou deviennent indisponibles au tarif maximal spécifié.

Cela se traduit dans OpenShift par des nœuds dans l’état **Non prêt**. Les machines restent saines, en phase **Approvisionnée en tant que nœud**.

Les nœuds reviennent à l’état **Prêt** une fois les machines virtuelles de nouveau disponibles

## <a name="troubleshooting"></a>Dépannage

### <a name="node-stuck-in-not-ready-state-underlying-vm-deallocated"></a>Nœud bloqué dans l’état Non prêt, machine virtuelle sous-jacente libérée

Si un nœud est bloqué pendant une longue période dans l’état Non prêt après la désallocation de sa machine virtuelle, vous pouvez essayer de le supprimer ou de supprimer l’objet machine OpenShift correspondant.

### <a name="spot-machine-stuck-in-failed-state"></a>Machine Spot bloquée dans l’état Échec

Si une machine (objet OpenShift) utilisant une machine virtuelle Spot est bloquée dans l’état Échec, essayez de la supprimer manuellement. Si la suppression de la machine est impossible en raison d’une erreur 403 parce que la machine virtuelle n’existe plus, modifiez-la et supprimez les finaliseurs.
