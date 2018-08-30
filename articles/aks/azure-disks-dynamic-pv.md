---
title: Créer des volumes persistants avec Azure Kubernetes Service
description: Découvrez comment utiliser des disques Azure pour créer des volumes persistants pour des pods dans Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/20/2018
ms.author: iainfou
ms.openlocfilehash: 7048ab4e08d25fd5181857a4e7592d0bcb7d3b5f
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42885592"
---
# <a name="create-persistent-volumes-with-azure-disks-for-azure-kubernetes-service-aks"></a>Créer des volumes persistants avec des disques Azure pour Azure Kubernetes Service (AKS)

Un volume persistant représente un élément de stockage provisionné pour une utilisation dans des pods Kubernetes. Un volume persistant peut être utilisé par un ou plusieurs pods et être provisionné de façon statique ou dynamique. Pour plus d’informations sur les volumes persistants Kubernetes, consultez [Volumes persistants Kubernetes][kubernetes-volumes]. Cet article vous montre comment utiliser des volumes persistants avec des disques Azure dans un cluster Azure Kubernetes Service (AKS).

> [!NOTE]
> Un disque Azure peut être monté uniquement avec le type de *mode d’accès* *ReadWriteOnce*, qui le rend disponible à uniquement un seul nœud AKS. Si vous avez besoin de partager une volume persistent entre plusieurs nœuds, envisagez d’utiliser [Azure Files][azure-files-pvc].

## <a name="built-in-storage-classes"></a>Classes de stockage intégrées

Une classe de stockage permet de définir la création dynamique d’une unité de stockage avec un volume persistant. Pour plus d’informations sur les classes de stockage Kubernetes, consultez [Classes de stockage Kubernetes][kubernetes-storage-classes].

Chaque cluster AKS comprend deux classes de stockage précréées, toutes deux configurées pour utiliser des disques Azure :

* La classe de stockage *par défaut* configure un disque Azure standard.
    * Le stockage Standard s’appuie sur des disques durs et offre un stockage économique qui n’en est pas moins performant. Les disques Standard constituent la solution idéale pour une charge de travail de développement et de test économique.
* La classe de stockage *Premium managée* provisionne un disque Azure Premium.
    * Les disques Premium reposent sur un disque SSD à faible latence et hautes performances. Ils conviennent parfaitement aux machines virtuelles exécutant une charge de travail en production. Si les nœuds AKS dans votre cluster utilisent le stockage Premium, sélectionnez la classe *Premium managée*.

Utilisez la commande [kubectl get sc][kubectl-get] pour afficher les classes de stockage créées au préalable. L’exemple suivant montre les classes de stockage pré-créées disponibles au sein d’un cluster AKS :

```
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Les revendications de volume persistant sont spécifiées dans Gio mais les disques managés Azure sont facturés par référence SKU pour une taille spécifique. Ces références SKU vont de 32 Gio pour les disques S4 ou P4 à 4 Tio pour les disques S50 ou P50. Le débit et les performances d’E/S d’un disque managé Premium dépendent à la fois de la référence SKU et de la taille d’instance des nœuds dans le cluster AKS. Pour plus d’informations, consultez [Tarification et performances des disques managés][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Créer une revendication de volume persistant

Une revendication de volume persistant (PVC) est utilisée pour configurer automatiquement le stockage basé sur une classe de stockage. Dans ce cas, une PVC peut utiliser une des classes de stockage créées au préalable pour créer un disque géré Azure standard ou Premium.

Créez un fichier nommé `azure-premium.yaml` et copiez-y le manifeste suivant. La revendication demande un disque nommé `azure-managed-disk`, d’une taille de *5 Go* avec un accès *ReadWriteOnce*. La classe de stockage *managed-premium* est spécifiée en tant que classe de stockage.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

> [!TIP]
> Pour créer un disque qui utilise le stockage standard, préférez `storageClassName: default` plutôt que *managed-premium*.

Créez la revendication de volume persistant avec la commande [kubectl create][kubectl-create] et spécifiez votre fichier *azure-premium.yaml* :

```
$ kubectl create -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Utiliser le volume persistant

Une fois la revendication de volume persistant créée, et le disque provisionné convenablement, un pod peut être créé avec un accès au disque. Le manifeste suivant crée un pod NGINX de base qui utilise la revendication de volume persistant nommé *azure-managed-disk* pour monter le disque Azure à l’emplacement `/mnt/azure`.

Créez un fichier nommé `azure-pvc-disk.yaml` et copiez-y le manifeste suivant.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

Créez le pod avec la commande [kubectl create][kubectl-create], comme indiqué dans l’exemple suivant :

```
$ kubectl create -f azure-pvc-disk.yaml

pod/mypod created
```

Vous disposez maintenant d’un pod en cours d’exécution avec le disque Azure monté dans le répertoire `/mnt/azure`. Cette configuration peut être consultée en inspectant votre pod via `kubectl describe pod mypod`, comme illustré dans l’exemple condensé suivant :

```
$ kubectl describe pod mypod

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false

Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```

## <a name="back-up-a-persistent-volume"></a>Sauvegarder un volume persistant

Pour sauvegarder les données de votre volume persistant, prenez un instantané du disque managé pour le volume. Vous pouvez ensuite utiliser cet instantané pour créer un disque restauré et l’attacher aux pods comme moyen de restauration des données.

Tout d’abord, obtenez le nom de volume au moyen de la commande `kubectl get pvc`, par exemple pour la PVC nommée *azure-managed-disk* :

```
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Ce nom de volume constitue le nom du disque Azure sous-jacent. Recherchez l’ID de disque à l’aide de [az disk list][az-disk-list] et fournissez votre nom de volume PVC, comme indiqué dans l’exemple suivant :

```
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Utilisez l’ID de disque pour créer un disque de capture instantanée avec [az snapshot create][az-snapshot-create]. L’exemple suivant crée un instantané nommé *pvcSnapshot* dans le même groupe de ressources que le cluster AKS (*MC_myResourceGroup_myAKSCluster_eastus*). Vous pouvez rencontrer des problèmes d’autorisation si vous créez des instantanés et restaurez des disques dans des groupes de ressources auxquels le cluster AKS n’a pas accès.

```azurecli
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Selon la quantité de données présentes sur votre disque, quelques minutes peuvent être nécessaires pour créer l’instantané.

## <a name="restore-and-use-a-snapshot"></a>Restaurer et utiliser un instantané

Pour restaurer le disque et l’utiliser avec un pod Kubernetes, utilisez la capture instantanée comme source lorsque vous créez un disque avec [az disk create][az-disk-create]. Cette opération permet de conserver la ressource d’origine si vous devez ensuite accéder à l’instantané des données d’origine. L’exemple suivant crée un disque nommé *pvcRestored* à partir de l’instantané nommé *pvcSnapshot* :

```azurecli
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Pour utiliser le disque restauré avec un pod, spécifiez l’ID du disque dans le manifeste. Procurez-vous l’ID de disque par le biais de la commande [az disk show][az-disk-show]. L’exemple suivant récupère l’ID de disque pour *pvcRestored* qui a été créé à l’étape précédente :

```azurecli
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Créez un manifeste de pod nommé `azure-restored.yaml` et spécifiez l’URI de disque obtenu à l’étape précédente. L’exemple suivant crée un serveur web NGINX de base, avec le disque restauré monté comme volume à l’emplacement */mnt/azure* :

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
    - name: myfrontendrestored
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      azureDisk:
        kind: Managed
        diskName: pvcRestored
        diskURI: /subscriptions/<guid>/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
```

Créez le pod avec la commande [kubectl create][kubectl-create], comme indiqué dans l’exemple suivant :

```
$ kubectl create -f azure-restored.yaml

pod/mypodrestored created
```

Vous pouvez utiliser `kubectl describe pod mypodrestored` pour voir les détails du pod, tel que dans l’exemple condensé suivant qui affiche les informations du volume :

```
$ kubectl describe pod mypodrestored

[...]
Volumes:
  volume:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     pvcRestored
    DiskURI:      /subscriptions/19da35d3-9a1a-4f3b-9b9c-3c56ef409565/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
[...]
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus en détail les volumes persistants Kubernetes utilisant des disques Azure.

> [!div class="nextstepaction"]
> [Plug-in Kubernetes pour les disques Azure][azure-disk-volume]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
