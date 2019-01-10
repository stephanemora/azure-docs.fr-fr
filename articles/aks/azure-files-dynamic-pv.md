---
title: Créer un volume de fichiers de manière dynamique pour plusieurs pods dans Azure Kubernetes Service (AKS)
description: Découvrir comment créer un volume persistant de manière dynamique avec Azure Files pour une utilisation simultanée avec plusieurs pods dans Azure Kubernetes Service (ACS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: iainfou
ms.openlocfilehash: 841c65fd8420fdfe681cb99ee7054cb4edd5fcd3
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53968982"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Créer et utiliser un volume persistant de manière dynamique avec Azure Files dans Azure Kubernetes Service (AKS)

Un volume persistant représente un élément de stockage provisionné pour une utilisation dans des pods Kubernetes. Un volume persistant peut être utilisé par un ou plusieurs pods, et être provisionné de façon statique ou dynamique. Si plusieurs pods doivent accéder simultanément au même volume de stockage, vous pouvez utiliser Azure Files pour vous connecter à l’aide du [protocole SMB (Server Message Block)][smb-overview]. Cet article vous montre comment créer un partage Azure Files de manière dynamique utilisé par plusieurs pods dans un cluster Azure Kubernetes Service (AKS).

Pour plus d’informations sur les volumes persistants Kubernetes, consultez [Volumes persistants Kubernetes][kubernetes-volumes].

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez un cluster AKS existant. Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec Azure CLI][aks-quickstart-cli] ou [avec le portail Azure][aks-quickstart-portal].

Vous devez également avoir installé et configuré Azure CLI version 2.0.46 ou ultérieure. Exécutez  `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez  [Installation d’Azure CLI 2.0][install-azure-cli].

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

Lorsque vous créez un partage Azure Files de manière dynamique en tant que volume Kubernetes, il est possible d’utiliser un compte de stockage tant que celui-ci figure dans le **nœud** AKS du groupe de ressources. Ce groupe est celui contenant le préfixe *MC_* qui a été créé par le provisionnement des ressources pour le cluster AKS. Obtenez le nom du groupe de ressources avec la commande [az aks show][az-aks-show].

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Utilisez la commande [az storage account create][az-storage-account-create] pour créer le compte de stockage.

Mettez à jour `--resource-group` avec le nom du groupe de ressources collecté à la dernière étape et `--name` avec le nom de votre choix. Fournissez votre propre nom de compte de stockage unique :

```azurecli
az storage account create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name mystorageaccount --sku Standard_LRS
```

> [!NOTE]
> Actuellement, Azure Files ne fonctionne qu’avec le stockage Standard. Si vous utilisez le stockage Premium, le provisionnement du volume échoue.

## <a name="create-a-storage-class"></a>Créer une classe de stockage

Une classe de stockage permet de définir la façon dont un partage de fichiers Azure est créé. Il est possible de spécifier un compte de stockage dans la classe. Si aucun compte de stockage n’est spécifié, un *skuName* et un *emplacement* doivent être spécifiés, et tous les comptes de stockage du groupe de ressources associé sont évalués pour rechercher une correspondance. Pour plus d’informations sur les classes de stockage Kubernetes pour Azure Files, consultez [Classes de stockage Kubernetes][kubernetes-storage-classes].

Créez un fichier nommé `azure-file-sc.yaml` et copiez-le dans l’exemple de manifeste suivant. Mettez à jour la valeur *storageAccount* avec le nom de votre compte de stockage créé à l’étape précédente. Pour plus d’informations sur *mountOptions*, consultez la section [Options de montage][mount-options].

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
  storageAccount: mystorageaccount
```

Créez la classe de stockage avec la commande [kubectl apply][kubectl-apply] :

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-cluster-role-and-binding"></a>Créer un rôle de cluster et une liaison

Les clusters AKS utilisent le contrôle d’accès en fonction du rôle (RBAC) de Kubernetes pour limiter les actions pouvant être effectuées. Les *rôles*définissent les permissions à accorder, et les *liaisons*les appliquent aux utilisateurs souhaités. Ces affectations peuvent s’appliquer à un espace de noms donné ou à l’ensemble du cluster. Pour plus d’informations, consultez [Utilisation de l’autorisation RBAC][kubernetes-rbac].

Pour permettre à la plateforme Azure de créer les ressources de stockage nécessaires, créez un rôle *Clusterrole* et une liaison *Clusterrolebinding*. Créez un fichier nommé `azure-pvc-roles.yaml`, et copiez-y le code YAML suivant :

```yaml
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: system:azure-cloud-provider
rules:
- apiGroups: ['']
  resources: ['secrets']
  verbs:     ['get','create']
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: system:azure-cloud-provider
roleRef:
  kind: ClusterRole
  apiGroup: rbac.authorization.k8s.io
  name: system:azure-cloud-provider
subjects:
- kind: ServiceAccount
  name: persistent-volume-binder
  namespace: kube-system
```

Assignez les permissions avec la commande [kubectl apply][kubectl-apply] :

```console
kubectl apply -f azure-pvc-roles.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Créer une revendication de volume persistant

Une revendication de volume persistant utilise l’objet de classe de stockage pour provisionner dynamiquement un partage de fichiers Azure. Le code YAML suivant permet de créer une revendication de volume persistant d’une taille de *5GB* avec un accès *ReadWriteMany*. Pour plus d’informations sur les modes d’accès, consultez la [documentation Kubernetes sur les volumes persistants][access-modes].

Maintenant, créez un fichier nommé `azure-file-pvc.yaml`, et copiez-y le code YAML suivant. Vérifiez que *storageClassName* correspond à la classe de stockage créée à la dernière étape :

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

Créez la revendication de volume persistant avec la commande [kubectl apply][kubectl-apply] :

```console
kubectl apply -f azure-file-pvc.yaml
```

Une fois que c’est terminé, le partage de fichiers est créé. Un secret Kubernetes incluant des informations d’identification et des informations de connexion est également créé. Vous pouvez utiliser la commande [kubectl get][kubectl-get] pour afficher l’état de la revendication de volume persistant :

```
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Utiliser le volume persistant

Le code YAML ci-après crée un pod qui utilise la revendication de volume persistant *azurefile* pour monter le partage de fichiers Azure dans le chemin */mnt/azure*.

Créez un fichier nommé `azure-pvc-files.yaml` et copiez-y le code YAML suivant. Vérifiez que *claimName* correspond à la revendication de volume persistant créée à la dernière étape.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

Créez le pod avec la commande [kubectl apply][kubectl-apply].

```console
kubectl apply -f azure-pvc-files.yaml
```

Vous disposez maintenant d’un pod en cours d’exécution avec le partage Azure Files monté dans le répertoire */mnt/azure*. Cette configuration peut s’afficher lors de l’inspection de votre pod par le biais de `kubectl describe pod mypod`. La sortie de l’exemple condensé suivant montre le volume monté dans le conteneur :

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          nginx:1.15.5
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Wed, 15 Aug 2018 22:22:27 +0000
    Ready:          True
    Mounts:
      /mnt/azure from volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8rv4z (ro)
[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>Options de montage

Les valeurs par défaut *fileMode* et *dirMode* diffèrent d’une version Kubernetes à l’autre, comme détaillé dans le tableau suivant.

| version | value |
| ---- | ---- |
| V1.6.x, v1.7.x | 0777 |
| V1.8.0-v1.8.5 | 0700 |
| v1.8.6 ou version supérieure | 0755 |
| V1.9.0 | 0700 |
| v1.9.1 ou version supérieure | 0755 |

Si vous utilisez un cluster de la version 1.8.5 ou d’une version ultérieure et que vous créez le volume persistant de manière dynamique avec une classe de stockage, les options de montage peuvent être spécifiées sur l’objet de classe de stockage. L’exemple suivant définit *0777* :

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
```

Si vous utilisez un cluster de la version 1.8.5 ou d’une version ultérieure et que vous créez l’objet de volume persistant de manière statique, les options de montage doivent être spécifiées sur l’objet *PersistentVolume*. Pour plus d’informations sur la création statique d’un volume persistant, consultez la [documentation relative aux volumes persistants statiques][pv-static].

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    shareName: azurefile
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
```

Si vous utilisez un cluster de version 1.8.0 - 1.8.4, un contexte de sécurité peut être spécifié avec la définition de la valeur *runAsUser* sur *0*. Pour plus d’informations sur le contexte de sécurité Pod, consultez [Configurer un contexte de sécurité][kubernetes-security-context].

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur les volumes persistants Kubernetes utilisant Azure Files.

> [!div class="nextstepaction"]
> [Plug-in Kubernetes pour Azure Files][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/aks#az-aks-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
