---
title: Utiliser un disque Azure avec AKS
description: Utiliser des disques Azure avec AKS
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.openlocfilehash: 3841222d08b23f43f69e77fa43c469793b70ce63
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801379"
---
# <a name="persistent-volumes-with-azure-disks"></a>Volumes persistants avec les disques Azure

Un volume persistant représente un élément de stockage provisionné pour une utilisation dans des pods Kubernetes. Un volume persistant peut être utilisé par un ou plusieurs pods et être provisionné de façon statique ou dynamique. Pour plus d’informations sur les volumes persistants Kubernetes, consultez [Volumes persistants Kubernetes][kubernetes-volumes].

Ce document explique comment utiliser des volumes persistants avec des disques Azure dans un cluster Azure Kubernetes Service (AKS).

> [!NOTE]
> Un disque Azure peut être monté uniquement avec le type de mode d’accès ReadWriteOnce, qui le rend disponible à uniquement un seul nœud AKS. Si vous avez besoin de partager une volume persistent entre plusieurs nœuds, envisagez d’utiliser [Azure Files][azure-files-pvc].

## <a name="built-in-storage-classes"></a>Classes de stockage intégrées

Une classe de stockage permet de définir la création dynamique d’une unité de stockage avec un volume persistant. Pour plus d’informations sur les classes de stockage Kubernetes, consultez [Classes de stockage Kubernetes][kubernetes-storage-classes].

Chaque cluster AKS comprend deux classes de stockage précréées, toutes deux configurées pour utiliser des disques Azure. La classe de stockage `default` configure un disque Azure standard. La classe de stockage `managed-premium` configure un disque Azure Premium. Si les nœuds AKS dans votre cluster utilisent le stockage Premium, sélectionnez la classe `managed-premium`.

Utilisez la commande [kubectl get sc][kubectl-get] pour afficher les classes de stockage créées au préalable.

```console
NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Les revendications de volume persistant sont spécifiées dans Gio mais les disques managés Azure sont facturés par référence SKU pour une taille spécifique. Ces références SKU vont de 32 Gio pour les disques S4 ou P4 à 4 Tio pour les disques S50 ou P50. En outre, le débit et les performances d’E/S d’un disque managé Premium dépend à la fois de la référence SKU et de la taille d’instance des nœuds dans le cluster AKS. Consultez [Tarification et performances des disques managés][managed-disk-pricing-performance].

## <a name="create-persistent-volume-claim"></a>Créer une revendication de volume persistant

Une revendication de volume persistant (PVC) est utilisée pour configurer automatiquement le stockage basé sur une classe de stockage. Dans ce cas, une PVC peut utiliser une des classes de stockage créées au préalable pour créer un disque géré Azure standard ou Premium.

Créez un fichier nommé `azure-premium.yaml` et copiez-y le manifeste suivant.

Notez que la classe de stockage `managed-premium` est spécifiée dans l’annotation et que la revendication demande un disque de taille `5GB` avec l’accès `ReadWriteOnce`.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
  annotations:
    volume.beta.kubernetes.io/storage-class: managed-premium
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

Créez la revendication de volume persistant avec la commande [kubectl apply][kubectl-apply].

```azurecli-interactive
kubectl apply -f azure-premium.yaml
```

## <a name="using-the-persistent-volume"></a>Utilisation du volume persistant

Une fois la revendication de volume persistant créée et le disque approvisionné convenablement, un pod peut être créé avec un accès au disque. Le manifeste suivant crée un pod qui utilise la revendication de volume persistant `azure-managed-disk` pour monter le disque Azure sur le chemin d’accès `/mnt/azure`.

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

Créez le pod avec la commande [kubectl apply][kubectl-apply].

```azurecli-interactive
kubectl apply -f azure-pvc-disk.yaml
```

Vous disposez maintenant d’un pod en cours d’exécution avec le disque Azure monté dans le répertoire `/mnt/azure`. Cette configuration peut s’afficher lors de l’inspection de votre pod par le biais de `kubectl describe pod mypod`.

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus en détail les volumes persistants Kubernetes utilisant des disques Azure.

> [!div class="nextstepaction"]
> [Plug-in Kubernetes pour les disques Azure][azure-disk-volume]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md 
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md
