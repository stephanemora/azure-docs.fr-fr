---
title: 'Concepts : stockage dans AKS (Azure Kubernetes Service)'
description: Découvrez le stockage dans AKS (Azure Kubernetes Service), notamment les volumes, les volumes persistants, les classes de stockage et les revendications
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: a1f68b06c31597a1d2d044686274e86a79e6e9a3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105780"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Options de stockage pour les applications dans AKS (Azure Kubernetes Service)

Les applications qui s’exécutent dans Azure Kubernetes Service (AKS) peuvent avoir besoin de stocker et de récupérer des données. Bien que certaines charges de travail d’applications puissent utiliser un stockage local et rapide sur des nœuds superflus et vides, d’autres nécessitent un stockage qui persiste sur des volumes de données plus réguliers au sein de la plateforme Azure. 

Plusieurs pods doivent peut-être :
* Partager les mêmes volumes de données. 
* Ré-attacher des volumes de données si le pod est de nouveau planifié sur un nœud différent. 

Enfin, vous pouvez être amené à injecter des données sensibles ou des informations de configuration d’application dans des pods.

Cet article présente les concepts fondamentaux qui fournissent le stockage à vos applications dans AKS :

- [Volumes](#volumes)
- [Volumes persistants](#persistent-volumes)
- [Classes de stockage](#storage-classes)
- [Revendications de volume persistant](#persistent-volume-claims)

![Options de stockage pour les applications dans un cluster AKS (Azure Kubernetes Service)](media/concepts-storage/aks-storage-options.png)

## <a name="volumes"></a>Volumes

Kubernetes traite généralement des pods individuels comme des ressources éphémères et jetables. Les applications ont des approches différentes à leur disposition pour l’utilisation et la persistance des données. Un *volume* représente un moyen de stocker, de récupérer et de conserver les données entre les pods et tout au long du cycle de vie des applications.

Les volumes traditionnels sont créés en tant que ressources Kubernetes soutenues par Stockage Azure. Vous pouvez créer manuellement ces volumes de données en vue de les attribuer directement à des pods, ou vous pouvez laisser Kubernetes les créer automatiquement. Les volumes de données peuvent utiliser Disques Azure ou Azure Files.

### <a name="azure-disks"></a>Disques Azure

Utilisez *Disques Azure* pour créer une ressource *DataDisk* Kubernetes. Les Disques peuvent utiliser :
* Le stockage Premium Azure , soutenu par un disque SSD hautes performances, ou 
* Le stockage Standard Azure, soutenu par des HDD normaux. 

> [!TIP]
>Pour la plupart des charges de travail de production et de développement, utilisez le stockage Premium. 

Puisque les Disques Azure sont montés en tant que *ReadWriteOnce*, ils ne sont disponibles que pour un seul pod. Pour les volumes de stockage accessibles par plusieurs pods simultanément, utilisez Azure Files.

### <a name="azure-files"></a>Azure Files
Utiliser *Azure Files* permet de monter un partage SMB 3.0 pris en charge par un compte Stockage Azure sur des pods. Les fichiers vous permettent de partager des données entre plusieurs nœuds et pods, vous pouvez aussi utiliser :
* Le stockage Premium Azure , soutenu par un disque SSD hautes performances, ou 
* Le stockage Standard Azure, soutenu par des HDD normaux.

### <a name="volume-types"></a>Types de volume
Les volumes Kubernetes représentent plus qu’un simple disque traditionnel pour le stockage et la récupération d’informations. Les volumes Kubernetes peuvent également servir à injecter des données dans un pod en vue de leur utilisation par les conteneurs. 

Voici certains types de volumes courants dans Kubernetes :

#### <a name="emptydir"></a>emptyDir

Couramment utilisé comme espace temporaire pour un pod. Tous les conteneurs au sein d’un pod peuvent accéder aux données sur le volume. Les données écrites dans ce type de volume persistent uniquement pendant la durée de vie du pod. Une fois le pod supprimé, le volume est supprimé. Ce volume utilise généralement le stockage sur disque du nœud local sous-jacent, bien qu’il puisse aussi se trouver uniquement dans la mémoire du nœud.

#### <a name="secret"></a>secret

Vous pouvez utiliser les volumes *secrets* pour injecter des données sensibles dans des pods, telles que des mots de passe. 
1. Créez un secret en vous servant de l’API Kubernetes. 
1. Définissez votre pod ou déploiement et demandez un secret spécifique. 
    * Les secrets sont fournis uniquement aux nœuds avec un pod planifié qui en a besoin.
    * Le secret est stocké dans *tmpfs*, qui n’est pas écrit sur le disque. 
1. Lorsque vous supprimez le dernier pod sur un nœud nécessitant un secret, ce dernier est supprimé du tmpfs du nœud. 
   * Les secrets sont stockés dans un espace de noms donné et ne sont accessibles qu’aux pods se trouvant dans cet espace de noms.

#### <a name="configmap"></a>configMap

Vous pouvez utiliser *configMap* pour injecter des propriétés de paires clé-valeur dans des pods, telles que des informations de configuration d’application. Définissez des informations de configuration d’application en tant que ressource Kubernetes, facilement mises à jour et appliquées à de nouvelles instances de pods lorsqu’elles sont déployés. 

Tout comme l’utilisation d’un secret :
1. Créez un élément Configmap à l’aide de l’API Kubernetes. 
1. Demandez l’élément configmap quand vous définissez un pod ou un déploiement. 
   * Les volumes ConfigMap sont stockés dans un espace de noms donné et ne sont accessibles qu’aux pods se trouvant dans cet espace de noms.

## <a name="persistent-volumes"></a>Volumes persistants

Les volumes sont définis et créés dans le cadre du cycle de vie d’un pod et sont conservés jusqu’à ce que le pod soit supprimé. Le stockage d’un pod est censé être conservé si le pod est replanifié sur un autre hôte pendant un événement de maintenance, en particulier dans les ressources StatefulSet. Un *volume persistant* est une ressource de stockage créée et gérée par l’API Kubernetes, et qui peut exister au-delà de la durée de vie d’un pod donné.

Les disques ou les fichiers Azure sont utilisés pour fournir le PersistentVolume. Comme indiqué dans la section [Volumes](#volumes), le choix de disques ou de fichiers Azure est souvent déterminé par le niveau de performance ou la nécessité d’un accès simultané aux données.

![Volumes persistants dans un cluster AKS (Azure Kubernetes Service)](media/concepts-storage/persistent-volumes.png)

Un volume persistant peut être créé *statiquement* par un administrateur de cluster, ou *dynamiquement* par le serveur d’API Kubernetes. Si un pod est planifié et demande un stockage qui n’est pas disponible, Kubernetes peut créer le stockage sur un disque ou fichier Azure sous-jacent et l’attacher au pod. Le provisionnement dynamique utilise une classe de stockage (*StorageClass*) pour identifier le type de stockage Azure à créer.

## <a name="storage-classes"></a>Classes de stockage

Pour définir différents niveaux de stockage, tels que Premium et Standard, vous pouvez créer une classe de stockage (*StorageClass*). 

La classe de stockage définit également la stratégie de récupération (*reclaimPolicy*). reclaimPolicy permet de contrôler le comportement de la ressource de stockage Azure sous-jacente lorsque le pod est supprimé et que le volume persistant n’est plus nécessaire. La ressource de stockage sous-jacente peut être supprimée ou conservée en vue de son utilisation pour un pod futur.

Dans AKS, quatre `StorageClasses` initiales sont créées pour le cluster à l’aide des plug-ins de stockage dans l’arborescence :

| Autorisation | Motif |
|---|---|
| `default` | Utilise le stockage Azure StandardSSD pour créer un disque managé. La stratégie de récupération veille à ce que le disque Azure sous-jacent soit supprimé quand le volume persistant qui l’a utilisé est supprimé. |
| `managed-premium` | Utilise un stockage Premium Azure pour créer un disque managé. La stratégie de récupération veille à nouveau à ce que le disque Azure sous-jacent soit supprimé quand le volume persistant qui l’a utilisé est supprimé. |
| `azurefile` | Utilise le stockage Standard Azure pour créer un partage de fichiers Azure. La stratégie de récupération veille à ce que le partage de fichiers Azure sous-jacent soit supprimé quand le volume persistant qui l’a utilisé est supprimé. |
| `azurefile-premium` | Utilise le stockage Azure Premium pour créer un partage de fichiers Azure. La stratégie de récupération veille à ce que le partage de fichiers Azure sous-jacent soit supprimé quand le volume persistant qui l’a utilisé est supprimé.|

Pour les clusters qui utilisent les nouveaux plug-ins externes Container Storage Interface (CSI) (préversion), les `StorageClasses` supplémentaires suivants sont créés :

| Autorisation | Motif |
|---|---|
| `managed-csi` | Utilise un stockage localement redondant (LRS) Azure StandardSSD pour créer un disque managé. La stratégie de récupération veille à ce que le disque Azure sous-jacent soit supprimé quand le volume persistant qui l’a utilisé est supprimé. La classe de stockage configure également les volumes persistants pour qu’ils soient extensibles. Il vous suffit de modifier la revendication de volume persistant avec la nouvelle taille. |
| `managed-csi-premium` | Utilise un stockage localement redondant (LRS) Premium Azure pour créer un disque managé. La stratégie de récupération veille à nouveau à ce que le disque Azure sous-jacent soit supprimé quand le volume persistant qui l’a utilisé est supprimé. De même, cette classe de stockage permet l’extension des volumes persistants. |
| `azurefile-csi` | Utilise le stockage Standard Azure pour créer un partage de fichiers Azure. La stratégie de récupération veille à ce que le partage de fichiers Azure sous-jacent soit supprimé quand le volume persistant qui l’a utilisé est supprimé. |
| `azurefile-csi-premium` | Utilise le stockage Azure Premium pour créer un partage de fichiers Azure. La stratégie de récupération veille à ce que le partage de fichiers Azure sous-jacent soit supprimé quand le volume persistant qui l’a utilisé est supprimé.|

À moins que vous ne spécifiiez un StorageClass pour un volume persistant, le StorageClass par défaut sera utilisé. Lorsque vous demandez des volumes persistants, veillez à ce que le stockage approprié dont vous avez besoin soit utilisé. 

Vous pouvez créer une classe de stockage pour des besoins supplémentaires à l’aide de `kubectl`. L’exemple suivant utilise les disques managés Premium et spécifie que le disque Azure sous-jacent doit être *conservé* lorsque le pod est supprimé :

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

> [!NOTE]
> AKS rapproche les classes de stockage par défaut et remplace toutes les modifications que vous apportez à ces classes de stockage.

## <a name="persistent-volume-claims"></a>Revendications de volume persistant

Une revendication de volume persistant (PersistentVolumeClaim) demande un stockage sur disque Azure ou Azure Files selon une classe de stockage, un mode d’accès et une taille spécifiques. Le serveur d’API Kubernetes peut approvisionner dynamiquement la ressource de stockage sous-jacente dans Azure si aucune ressource existante ne satisfait la revendication basée sur le StorageClass défini. 

La définition du pod inclut le montage du volume une fois que ce dernier a été connecté au pod.

![Revendications de volumes persistants dans un cluster AKS (Azure Kubernetes Service)](media/concepts-storage/persistent-volume-claims.png)

PersistentVolume est *lié* à PersistentVolumeClaim une fois qu’une ressource de stockage disponible a été attribuée au pod demandant un stockage. Les volumes persistants sont mappés 1:1 à des revendications.

L’exemple de manifeste YAML suivant montre une revendication de volume persistant qui utilise la classe de stockage *managed-premium* et demande un disque ayant une taille de *5Gi* :

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

Lorsque vous créez une définition de pod, vous spécifiez également :
* La revendication de volume persistant pour demander le stockage souhaité. 
* L’élément *volumeMount* permettant à vos applications de lire et d’écrire des données. 

L’exemple de manifeste YAML suivant illustre l’utilisation de la revendication de volume persistant précédente pour monter un volume sur */mnt/azure* :

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

Pour monter un volume dans un conteneur Windows, spécifiez la lettre de lecteur et le chemin d’accès. Par exemple :

```yaml
...      
       volumeMounts:
        - mountPath: "d:"
          name: volume
        - mountPath: "c:\k"
          name: k-dir
...
```

## <a name="next-steps"></a>Étapes suivantes

Pour connaître les meilleures pratiques associées, consultez [Meilleures pratiques relatives au stockage et aux sauvegardes dans Azure Kubernetes Service (AKS)][operator-best-practices-storage].

Pour voir comment créer des volumes dynamiques et statiques qui utilisent des disques Azure ou Azure Files, consultez les articles de procédures suivants :

- [Créer un volume statique à l’aide de disques Azure][aks-static-disks]
- [Créer un volume statique à l’aide d’Azure Files][aks-static-files]
- [Créer un volume dynamique à l’aide de disques Azure][aks-dynamic-disks]
- [Créer un volume dynamique à l’aide d’Azure Files][aks-dynamic-files]

Pour plus d’informations sur les concepts fondamentaux de Kubernetes et d’AKS, consultez les articles suivants :

- [Clusters et charges de travail Kubernetes/AKS][aks-concepts-clusters-workloads]
- [Identité Kubernetes/AKS][aks-concepts-identity]
- [Sécurité Kubernetes/AKS][aks-concepts-security]
- [Réseaux virtuels Kubernetes/AKS][aks-concepts-network]
- [Mise à l’échelle Kubernetes/AKS][aks-concepts-scale]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[aks-static-disks]: azure-disk-volume.md
[aks-static-files]: azure-files-volume.md
[aks-dynamic-disks]: azure-disks-dynamic-pv.md
[aks-dynamic-files]: azure-files-dynamic-pv.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-storage]: operator-best-practices-storage.md
