---
title: Activer des pilotes CSI (Container Storage interface) sur Azure Kubernetes Service (AKS)
description: Découvrez comment activer des pilotes CSI (Container Storage interface) pour des disques Azure et Azure Files dans un cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/31/2021
author: palma21
ms.openlocfilehash: 0f941b612c76811ba750a06036faf48c7359bedd
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129429848"
---
# <a name="enable-container-storage-interface-csi-drivers-for-azure-disks-and-azure-files-on-azure-kubernetes-service-aks"></a>Activer des pilotes CSI (Container Storage interface) pour des disques Azure et Azure Files sur Azure Kubernetes Service (AKS)

CSI (Container Storage Interface) est une norme pour exposer des systèmes de stockage de blocs et de fichiers arbitraires à des charges de travail conteneurisées sur Kubernetes. En adoptant et en utilisant CSI, AKS (Azure Kubernetes Service) peut écrire, déployer et itérer des plug-ins afin d’exposer de nouveaux systèmes de stockage ou d’améliorer les systèmes existants dans Kubernetes sans avoir à toucher au code Kubernetes principal ou à attendre ses cycles de publication.

La prise en charge du pilote de stockage CSI sur AKS vous permet d’utiliser en mode natif :
- Des [*disques Azure*](azure-disk-csi.md), qui peuvent être utilisés pour créer une ressource *DataDisk* Kubernetes. Les disques peuvent utiliser un Stockage Premium Azure, assorti de disques SSD haute performance, ou un Stockage Standard Azure, assorti de disques HDD ordinaires ou de disques SSD standard. Pour la plupart des charges de travail de production et de développement, utilisez le Stockage Premium. Les disques Azure étant montés avec le mode d’accès *ReadWriteOnce*, ils ne sont disponibles que pour un seul pod. Pour les volumes de stockage accessibles par plusieurs pods simultanément, utilisez Azure Files.
- [*Azure Files*](azure-files-csi.md), qui peut être utilisé pour monter un partage SMB 3.0 assorti d’un compte Stockage Azure sur des pods. Avec Azure Files, vous pouvez partager des données entre plusieurs nœuds et pods. Azure Files peut utiliser un Stockage Standard Azure, assorti de disques HDD standard, ou un Stockage Premium Azure, assorti de disques SSD haute performance.

> [!IMPORTANT]
> À partir de Kubernetes version 1.21, Kubernetes utilise les pilotes CSI uniquement et par défaut. Ces pilotes représentent l’avenir de la prise en charge du stockage dans Kubernetes.
> 
> Veuillez supprimer le disque Azure Open source installé manuellement et les pilotes CSI du fichier Azure avant de procéder à la mise à niveau vers AKS 1.21.
> 
> Les *pilotes dans l’arborescence* font référence aux pilotes de stockage actuels, qui font partie du code Kubernetes principal, par opposition aux nouveaux pilotes CSI, qui sont des plug-ins.

## <a name="limitations"></a>Limites

- Cette fonctionnalité ne peut être définie qu’au moment de la création du cluster.
- La version mineure minimale Kubernetes qui prend en charge les pilotes CSI est v1.17.
- La classe de stockage par défaut sera la classe de stockage `managed-csi`.

## <a name="create-a-new-cluster-that-can-use-csi-storage-drivers"></a>Créer un cluster qui peut utiliser des pilotes de stockage CSI

Créez un cluster capable d’utiliser des pilotes de stockage CSI pour les disques Azure et Azure Files à l’aide des commandes CLI suivantes. Utilisez l’indicateur `--aks-custom-headers` pour définir la fonctionnalité `EnableAzureDiskFileCSIDriver`.

Créez un groupe de ressources Azure :

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location canadacentral
```

Créez le cluster AKS avec prise en charge des pilotes de stockage CSI :

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --network-plugin azure  --aks-custom-headers EnableAzureDiskFileCSIDriver=true
```

Si vous souhaitez créer des clusters dans des pilotes de stockage dans l’arborescence au lieu de pilotes de stockage CSI, vous pouvez le faire en omettant le paramètre `--aks-custom-headers` personnalisé.


Vérifiez le nombre de volumes sur disque Azure que vous pouvez attacher à ce nœud en exécutant :

```console
$ kubectl get nodes
aks-nodepool1-25371499-vmss000000
aks-nodepool1-25371499-vmss000001
aks-nodepool1-25371499-vmss000002

$ echo $(kubectl get CSINode <NODE NAME> -o jsonpath="{.spec.drivers[1].allocatable.count}")
8
```

## <a name="migrating-custom-in-tree-storage-classes-to-csi"></a>Migration vers CSI de classes de stockage personnalisées dans l’arborescence
Si vous avez créé des classes de stockage personnalisées basées sur des pilotes de stockage dans l’arborescence, vous devez migrer ceux-ci lorsque vous avez mis à niveau votre cluster vers 1.21.x.

Alors que la migration explicite vers le fournisseur CSI n’est pas nécessaire pour que vos classes de stockage soient toujours valides, pour pouvoir utiliser les fonctionnalités CSI (capture d’instantanés, etc.), vous devrez effectuer la migration.

La migration de ces classes de stockage implique de supprimer les classes de stockage existantes et de les réapprovisionner avec le provisionneur défini sur **disk.csi.azure.com** si vous utilisez des disques azure et **files.csi.azure.com** si vous utilisez Azure files.  Par exemple, pour les disques Azure :

### <a name="original-in-tree-storage-class-definition"></a>Définition d’origine de la classe de stockage dans l’arborescence

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: kubernetes.io/azure-disk
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

### <a name="csi-storage-class-definition"></a>Définition de la classe de stockage CSI

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: disk.csi.azure.com
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

Le système de stockage CSI prend en charge les mêmes fonctionnalités que les pilotes dans l’arborescence, donc la seule modification nécessaire est le provisionneur.


## <a name="next-steps"></a>Étapes suivantes

- Pour utiliser le lecteur CSI pour les disques Azure, consultez [Utiliser des disques Azure avec des pilotes CSI](azure-disk-csi.md).
- Pour utiliser le lecteur CSI pour Azure Files, consultez [Utiliser Azure Files avec des pilotes CSI](azure-files-csi.md).
- Pour plus d’informations sur les bonnes pratiques en matière de stockage, consultez [Meilleures pratiques relatives au stockage et aux sauvegardes dans Azure Kubernetes Service (AKS)][operator-best-practices-storage].
- Pour plus d’informations sur la migration CSI, consultez [Migration de volume Kubernetes dans l’arborescence vers CSI][csi-migration-community].

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[csi-migration-community]: https://kubernetes.io/blog/2019/12/09/kubernetes-1-17-feature-csi-migration-beta
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/disks-types.md
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-snapshot-create]: /cli/azure/snapshot#az_snapshot_create
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-disk-show]: /cli/azure/disk#az_disk_show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
