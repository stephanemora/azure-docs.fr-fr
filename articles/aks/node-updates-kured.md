---
title: Mettre à jour et redémarrer les nœuds Linux avec kured dans Azure Kubernetes Service (ACS)
description: Découvrez comment mettre à jour des nœuds Linux et les redémarrer automatiquement avec kured dans Azure Kubernetes Service (ACS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 02/28/2019
ms.author: iainfou
ms.openlocfilehash: b426399f73375618a2084eff82abba5d4934b914
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074206"
---
# <a name="apply-security-and-kernel-updates-to-linux-nodes-in-azure-kubernetes-service-aks"></a>Appliquer la sécurité et mises à jour du noyau à des nœuds Linux dans Azure Kubernetes Service (ACS)

Pour protéger vos clusters, les mises à jour de sécurité sont automatiquement appliquées à des nœuds Linux dans ACS. Ces mises à jour incluent des correctifs de sécurité ou des mises à jour du noyau. Certaines de ces mises à jour nécessitent un redémarrage du nœud pour terminer le processus. ACS ne redémarre pas automatiquement ces nœuds Linux pour terminer le processus de mise à jour.

Le processus de nœuds Windows Server (actuellement en version préliminaire dans ACS) mise à jour est un peu différent. Mises à jour quotidiennes ne reçoivent pas les nœuds Windows Server. Au lieu de cela, vous effectuez une mise à niveau AKS qui déploie les nouveaux nœuds à la dernière image de Windows Server de base et les correctifs. Pour les clusters AKS qui utilisent des nœuds de serveur Windows, consultez [mise à niveau d’un pool de nœuds dans ACS][nodepool-upgrade].

Cet article vous montre comment utiliser l’open source [kured (KUbernetes redémarrer démon)] [ kured] à surveiller pour les nœuds Linux nécessitant un redémarrage, puis gérer automatiquement la replanification de pods et nœud en cours d’exécution Redémarrez le processus.

> [!NOTE]
> `Kured` est un projet open source par Weaveworks. La prise en charge de ce projet dans AKS est fournie dans la mesure du possible. Vous trouverez la prise en charge supplémentaire dans le canal slack #weave-Communauté.

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez un cluster AKS existant. Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec Azure CLI][aks-quickstart-cli] ou [avec le portail Azure][aks-quickstart-portal].

Vous également besoin d’Azure CLI version 2.0.59 ou ultérieur installé et configuré. Exécutez  `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez  [Installation d’Azure CLI 2.0][install-azure-cli].

## <a name="understand-the-aks-node-update-experience"></a>Comprendre l’expérience de mise à jour des nœuds AKS

Dans un cluster AKS, vos nœuds Kubernetes s’exécutent en tant que machines virtuelles Azure. Ces machines virtuelles Linux utilisent une image Ubuntu, avec le système d’exploitation configuré pour rechercher automatiquement les mises à jour toutes les nuits. Si des mises à jour de sécurité ou du noyau sont disponibles, elles sont automatiquement téléchargées et installées.

![Processus de mise à jour et de redémarrage des nœuds AKS avec kured](media/node-updates-kured/node-reboot-process.png)

Certaines mises à jour de sécurité, comme des mises à jour du noyau, nécessitent un redémarrage du nœud pour finaliser le processus. Un nœud Linux nécessitant un redémarrage crée un fichier nommé */var/run/reboot-required*. Ce processus de redémarrage ne se produit pas automatiquement.

Vous pouvez utiliser vos propres workflows et vos propres processus pour gère les redémarrages des nœuds, ou bien utiliser `kured` pour orchestrer le processus. Avec `kured`, un [DaemonSet] [ DaemonSet] est déployé qui exécute un pod sur chaque nœud du cluster Linux. Ces pods du DaemonSet recherchent l’existence du fichier */var/run/reboot-required*, puis lancent un processus pour redémarrer les nœuds.

### <a name="node-upgrades"></a>Mises à niveau de nœuds

Un processus supplémentaire dans AKS vous permet de *mettre à niveau* un cluster. Une mise à niveau consiste généralement à passer à une version plus récente de Kubernetes, et pas seulement à appliquer des mises à jour de sécurité du nœud. Une mise à niveau AKS effectue les actions suivantes :

* Un nouveau nœud est déployé avec les dernières mises à jour de sécurité et la version de Kubernetes la plus récente lui est appliquée.
* L’accès à l’ancien nœud est fermé et il est vidé.
* Des pods sont planifiés sur le nouveau nœud.
* L’ancien nœud est supprimé.

Vous ne pouvez pas rester sur la même version de Kubernetes lors d’un événement de mise à niveau. Vous devez spécifier une version plus récente de Kubernetes. Pour mettre à niveau vers la dernière version de Kubernetes, vous pouvez [mettre à niveau votre cluster AKS][aks-upgrade].

## <a name="deploy-kured-in-an-aks-cluster"></a>Déployer kured dans un cluster AKS

Pour déployer le DaemonSet `kured`, appliquez l’exemple de manifeste YAML suivant à partir de leur page de projet GitHub. Ce manifeste crée un rôle, et un rôle de cluster, des liaisons et un compte de service, puis déploie le DaemonSet avec `kured` version 1.1.0, qui prend en charge les clusters AKS 1.9 ou ultérieur.

```console
kubectl apply -f https://github.com/weaveworks/kured/releases/download/1.1.0/kured-1.1.0.yaml
```

Vous pouvez également configurer des paramètres supplémentaires pour `kured`, comme l’intégration à Prometheus ou Slack. Pour plus d’informations sur les paramètres de configuration supplémentaires, consultez les [documents d’installation de kured][kured-install].

## <a name="update-cluster-nodes"></a>Mettre à jour des nœuds de cluster

Par défaut, les nœuds Linux dans ACS vérifient les mises à jour tous les soirs. Si vous ne voulez pas attendre, vous pouvez effectuer manuellement une mise à jour pour vérifier que `kured` s’exécute correctement. Suivez d’abord les étapes pour [vous connecter avec SSH à un de vos nœuds AKS][aks-ssh]. Une fois que vous avez une connexion SSH au nœud Linux, vérifiez les mises à jour et les appliquer comme suit :

```console
sudo apt-get update && sudo apt-get upgrade -y
```

Si les mises à jour appliquées nécessitent un redémarrage du nœud, un fichier est écrit dans */var/run/reboot-required*. Par défaut, `Kured` recherche les nœuds qui nécessitent un redémarrage toutes les 60 minutes.

## <a name="monitor-and-review-reboot-process"></a>Surveiller et passer en revue le processus de redémarrage

Quand un des réplicas du DaemonSet a détecté que le redémarrage d’un nœud était nécessaire, un verrou est placé sur le nœud via l’API Kubernetes. Ce verrou empêche la planification d’autres pods sur le nœud. Le verrou indique également qu’un seul nœud doit être redémarré à la fois. Le nœud étant fermé, les pods en cours d’exécution sont vidés et le nœud est redémarré.

Vous pouvez surveiller l’état des nœuds avec la commande [kubectl get nodes][kubectl-get-nodes]. L’exemple de sortie suivant montre un nœud avec l’état *SchedulingDisabled* au moment où le nœud se prépare pour le processus de redémarrage :

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

Une fois que le processus de mise à jour est terminé, vous pouvez voir l’état des nœuds en utilisant la commande [kubectl get nodes][kubectl-get-nodes] avec le paramètre `--output wide`. Cette sortie supplémentaire vous permet de voir une différence dans *KERNEL-VERSION* des nœuds sous-jacents, comme le montre la sortie de l’exemple suivant. Le *aks-nodepool1-28993262-0* a été mis à jour dans une étape précédente et l’affiche la version du noyau *4.15.0-1039-azure*. Le nœud *aks-nodepool1-28993262-1* qui n’a pas été mis à jour affiche la version du noyau *4.15.0-1037-azure*.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a appris à utiliser `kured` pour redémarrer automatiquement en tant que partie du processus de mise à jour de sécurité des nœuds Linux. Pour mettre à niveau vers la dernière version de Kubernetes, vous pouvez [mettre à niveau votre cluster AKS][aks-upgrade].

Pour les clusters AKS qui utilisent des nœuds de serveur Windows, consultez [mise à niveau d’un pool de nœuds dans ACS][nodepool-upgrade].

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://github.com/weaveworks/kured#installation
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
