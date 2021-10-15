---
title: Intégrer Azure NetApp Files à Azure Kubernetes Service | Microsoft Docs
description: Découvrez comment approvisionner Azure NetApp Files avec Azure Kubernetes Service.
services: container-service
ms.topic: article
ms.date: 10/04/2021
ms.openlocfilehash: 177526fa98ada37341fadc90e183f224e1aa0830
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129544789"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Intégrer Azure NetApp Files à Azure Kubernetes Service

Un volume persistant représente un élément de stockage provisionné pour une utilisation dans des pods Kubernetes. Un volume persistant peut être utilisé par un ou plusieurs pods, et être provisionné de façon statique ou dynamique. Cet article explique comment créer des volumes [Azure NetApp Files][anf] qui seront utilisés par des pods dans un cluster Azure Kubernetes Service (AKS).

[Azure NetApp Files][anf] est un service de stockage de fichiers limité, hautes performances et de classe entreprise s’exécutant sur Azure. Les utilisateurs de Kubernetes ont deux options lorsqu’il s’agit d’utiliser des volumes Azure NetApp Files pour les charges de travail Kubernetes :

* Créer des volumes Azure NetApp Files **de manière statique** : Dans ce scénario, la création des volumes est réalisée en dehors d’AKS ; les volumes sont créés en utilisant `az` ou l’interface utilisateur Azure et sont ensuite exposés à Kubernetes par la création d’un `PersistentVolume`.
* Créer des volumes Azure NetApp Files **à la demande**, en les orchestrant via Kubernetes : Cette méthode est le mode de fonctionnement **privilégié** pour créer plusieurs volumes directement via Kubernetes et est réalisée à l’aide d’[Astra Trident](https://netapp-trident.readthedocs.io/).

Il est **fortement recommandé** de disposer d’un pilote CSI prêt à l’emploi permettant aux utilisateurs de Kubernetes de consommer directement les volumes Azure NetApp Files via AKS. Cette condition est remplie par Astra Trident, un orchestrateur de stockage dynamique open source pour Kubernetes. Il s’agit d’un orchestrateur de stockage destiné aux entreprises conçu spécialement pour Kubernetes et entièrement pris en charge par NetApp. Il simplifie l’accès au stockage dans les environnements Kubernetes en automatisant l’approvisionnement en stockage. Les consommateurs peuvent tirer parti du pilote CSI d’Astra Trident pour Azure NetApp Files afin de faire abstraction des détails sous-jacents et de créer, d’étendre ou de capturer des volumes à la demande.

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez un cluster AKS existant. Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec Azure CLI][aks-quickstart-cli]ou avec le [Portail Azure][aks-quickstart-portal].

> [!IMPORTANT]
> Votre cluster AKS doit également être situé [dans une région qui prend en charge Azure NetApp Files][anf-regions].

Azure CLI 2.0.59 (ou une version ultérieure) doit également être installé et configuré. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][install-azure-cli].

### <a name="prerequisites"></a>Prérequis

Les considérations suivantes s’appliquent lorsque vous utilisez Azure NetApp Files :

* Azure NetApp Files est uniquement disponible [dans les régions Azure sélectionnées][anf-regions].
* Après le déploiement initial d’un cluster AKS, vous pouvez choisir d’approvisionner les volumes Azure NetApp Files de manière statique ou dynamique.
* Pour utiliser l’approvisionnement dynamique avec Azure NetApp Files, installez et configurez [Astra Trident](https://netapp-trident.readthedocs.io/) version 19.07 ou ultérieure.

## <a name="configure-azure-netapp-files"></a>Configurer Azure NetApp Files

Inscrivez le fournisseur de ressources *Microsoft.NetApp* :

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Cela peut prendre du temps.

Quand vous créez un compte Azure NetApp pour une utilisation avec AKS, vous devez créer le compte dans le groupe de ressources de **nœud**. Tout d’abord, obtenez le nom du groupe de ressources avec la commande [az aks show][az-aks-show] et ajoutez le paramètre de requête `--query nodeResourceGroup`. L’exemple suivant obtient le groupe de ressources de nœud pour le cluster AKS nommé *myAKSCluster* dans le groupe de ressources nommé *myResourceGroup* :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

Créez un compte Azure NetApp Files dans le groupe de ressources de **nœud** et dans la même région que votre cluster AKS en utilisant [az netappfiles account create][az-netappfiles-account-create]. L’exemple suivant crée un compte nommé *myaccount1* dans le groupe de ressources *MC_myResourceGroup_myAKSCluster_eastus* et la région *eastus* :

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Créez un pool de capacités à l’aide de la commande [az netappfiles pool create][az-netappfiles-pool-create]. L’exemple suivant crée un pool de capacités nommé *mypool1* avec une taille de 4 To et un niveau de service *Premium* :

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Créez un sous-réseau à [déléguer à Azure NetApp Files][anf-delegate-subnet] en utilisant [az network vnet subnet create][az-network-vnet-subnet-create]. *Ce sous-réseau doit se trouver dans le même réseau virtuel que votre cluster AKS.*

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --delegations "Microsoft.NetApp/volumes" \
    --address-prefixes 10.0.0.0/28
```

Les volumes peuvent être approvisionnés de manière statique ou dynamique. Les deux options sont présentées en détail ci-dessous.

## <a name="provision-azure-netapp-files-volumes-statically"></a>Approvisionner des volumes Azure NetApp Files de manière statique

Créez un volume en utilisant [az netappfiles volume create][az-netappfiles-volume-create].

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
LOCATION=eastus
ANF_ACCOUNT_NAME=myaccount1
POOL_NAME=mypool1
SERVICE_LEVEL=Premium
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
VOLUME_SIZE_GiB=100 # 100 GiB
UNIQUE_FILE_PATH="myfilepath2" # Note that file path needs to be unique within all ANF Accounts

az netappfiles volume create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --account-name $ANF_ACCOUNT_NAME \
    --pool-name $POOL_NAME \
    --name "myvol1" \
    --service-level $SERVICE_LEVEL \
    --vnet $VNET_ID \
    --subnet $SUBNET_ID \
    --usage-threshold $VOLUME_SIZE_GiB \
    --file-path $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

### <a name="create-the-persistentvolume"></a>Créer la ressource PersistentVolume

Listez les détails de votre volume en utilisant [az netappfiles volume show][az-netappfiles-volume-show]

```azurecli
az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"
```

```output
{
  ...
  "creationToken": "myfilepath2",
  ...
  "mountTargets": [
    {
      ...
      "ipAddress": "10.0.0.4",
      ...
    }
  ],
  ...
}
```

Créez un fichier `pv-nfs.yaml` définissant une ressource PersistentVolume. Remplacez `path` par *creationToken* et `server` par *ipAddress* à partir de la commande précédente. Par exemple :

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  mountOptions:
    - vers=3
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

Mettez à jour *server* et *path* avec les valeurs de votre volume NFS (Network File System) que vous avez créé à l’étape précédente. Créez la ressource PersistentVolume avec la commande [kubectl apply][kubectl-apply] :

```console
kubectl apply -f pv-nfs.yaml
```

Vérifiez que l’*état* de la ressource PersistentVolume est *Disponible* à l’aide de la commande [kubectl describe][kubectl-describe] :

```console
kubectl describe pv pv-nfs
```

### <a name="create-the-persistentvolumeclaim"></a>Créer la ressource PersistentVolumeClaim

Créez un fichier `pvc-nfs.yaml` définissant une ressource PersistentVolume. Par exemple :

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
```

Créez la ressource PersistentVolumeClaim avec la commande [kubectl apply][kubectl-apply] :

```console
kubectl apply -f pvc-nfs.yaml
```

Vérifiez que l’*état* de la ressource PersistentVolumeClaim est *Bound* (Lié) à l’aide de la commande [kubectl describe][kubectl-describe] :

```console
kubectl describe pvc pvc-nfs
```

### <a name="mount-with-a-pod"></a>Monter avec un pod

Créez un fichier `nginx-nfs.yaml` définissant un pod qui utilise la ressource PersistentVolumeClaim. Par exemple :

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

Créez le pod avec la commande [kubectl apply][kubectl-apply] :

```console
kubectl apply -f nginx-nfs.yaml
```

Vérifiez que le pod est *En cours d’exécution* à l’aide de la commande [kubectl describe][kubectl-describe] :

```console
kubectl describe pod nginx-nfs
```

Vérifiez que votre volume a été monté dans le pod en utilisant [kubectl exec][kubectl-exec] pour vous connecter au pod, puis `df -h` pour vérifier si le volume est monté.

```console
$ kubectl exec -it nginx-nfs -- sh
```

```output
/ # df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="provision-azure-netapp-files-volumes-dynamically"></a>Approvisionner des volumes Azure NetApp Files de manière dynamique

### <a name="install-and-configure-astra-trident"></a>Installer et configurer Astra Trident

Pour approvisionner des volumes de manière dynamique, vous devez installer Astra Trident. Astra Trident est l’approvisionneur de stockage dynamique de NetApp spécialement conçu pour Kubernetes. Simplifiez la consommation de stockage pour les applications Kubernetes en utilisant les pilotes [Container Storage Interface (CSI)](https://kubernetes-csi.github.io/docs/) d’Astra Trident, conformes aux normes du secteur d’activité. Astra Trident se déploie dans les clusters Kubernetes sous forme de pods et fournit des services d’orchestration de stockage dynamique pour vos charges de travail Kubernetes.

Vous pouvez en savoir plus en consultant la [documentation](https://netapp-trident.readthedocs.io/en/latest/index.html).

Avant de passer à l’étape suivante, vous devez :

1. **Installer Astra Trident**. Trident peut être installé en utilisant l’opérateur, le chart Helm ou `tridentctl`. Les instructions fournies ci-dessous expliquent comment installer Astra Trident à l’aide de l’opérateur. Pour connaître le fonctionnement des autres méthodes d’installation, consultez le [guide d’installation](https://netapp-trident.readthedocs.io/en/latest/kubernetes/deploying/deploying.html).

2. **Créer un serveur principal.** Pour indiquer à Astra Trident l’abonnement Azure NetApp Files et l’emplacement où il doit créer des volumes, un serveur principal est créé. Cette étape nécessite des détails concernant le compte créé à l’étape précédente.

#### <a name="install-astra-trident-using-the-operator"></a>Installer Astra Trident à l’aide de l’opérateur

Cette section vous guide tout au long de l’installation d’Astra Trident à l’aide de l’opérateur. Vous pouvez également choisir d’utiliser l’une de ses autres méthodes d’installation :

* [Chart Helm](https://netapp-trident.readthedocs.io/en/latest/kubernetes/deploying/operator-deploy.html#deploy-trident-operator-by-using-helm).
* [tridentctl](https://netapp-trident.readthedocs.io/en/latest/kubernetes/deploying/tridentctl-deploy.html).

Consultez la page de [déploiement de Trident](https://netapp-trident.readthedocs.io/en/latest/kubernetes/deploying/deploying.html) pour comprendre le fonctionnement de chaque option et identifier celle qui vous convient le mieux.

Téléchargez Astra Trident à partir de son [référentiel GitHub](https://github.com/NetApp/trident/releases). Choisissez la version souhaitée et téléchargez le pack d’installation.

```console
#Download Astra Trident

$  wget https://github.com/NetApp/trident/releases/download/v21.07.1/trident-installer-21.07.1.tar.gz
$  tar xzvf trident-installer-21.07.1.tar.gz
```
Déployez l’opérateur en utilisant `deploy/bundle.yaml`.

```console
$  kubectl create ns trident

namespace/trident created

$  kubectl apply -f trident-installer/deploy/bundle.yaml -n trident

serviceaccount/trident-operator created
clusterrole.rbac.authorization.k8s.io/trident-operator created
clusterrolebinding.rbac.authorization.k8s.io/trident-operator created
deployment.apps/trident-operator created
podsecuritypolicy.policy/tridentoperatorpods created
```

Créez un `TridentOrchestrator` pour installer Astra Trident.

```console
$ kubectl apply -f trident-installer/deploy/crds/tridentorchestrator_cr.yaml

tridentorchestrator.trident.netapp.io/trident created 
```

L’opérateur effectue l’installation en utilisant les paramètres fournis dans la spec de `TridentOrchestrator`. Vous pouvez vous renseigner sur les paramètres de configuration et les exemples de serveurs principaux dans les [guides d’installation](https://netapp-trident.readthedocs.io/en/latest/kubernetes/deploying/deploying.html) et [de serveurs principaux](https://netapp-trident.readthedocs.io/en/latest/kubernetes/operations/tasks/backends/index.html).

Confirmez l’installation d’Astra Trident. 

```console
$  kubectl describe torc trident
Name:         trident
Namespace:
Labels:       <none>
Annotations:  <none>
API Version:  trident.netapp.io/v1
Kind:         TridentOrchestrator
...
Spec:
  Debug:      true
  Namespace:  trident
Status:
  Current Installation Params:
    IPv6:                       false
    Autosupport Hostname:
    Autosupport Image:          netapp/trident-autosupport:21.01
    Autosupport Proxy:
    Autosupport Serial Number:
    Debug:                      true
    Enable Node Prep:           false
    Image Pull Secrets:
    Image Registry:
    k8sTimeout:           30
    Kubelet Dir:          /var/lib/kubelet
    Log Format:           text
    Silence Autosupport:  false
    Trident Image:        netapp/trident:21.07.1
  Message:                Trident installed
  Namespace:              trident
  Status:                 Installed
  Version:                v21.07.1
Events:
  Type    Reason      Age   From                        Message
  ----    ------      ----  ----                        -------
  Normal  Installing  74s   trident-operator.netapp.io  Installing Trident
  Normal  Installed   67s   trident-operator.netapp.io  Trident installed
```

### <a name="create-a-backend"></a>Création d'un serveur principal

Une fois Astra Trident installé, créez un serveur principal qui pointe vers votre abonnement à Azure NetApp Files.

```console
$  kubectl apply -f trident-installer/sample-input/backends-samples/azure-netapp-files/backend-anf.yaml -n trident

secret/backend-tbc-anf-secret created
tridentbackendconfig.trident.netapp.io/backend-tbc-anf created
```

Avant d’exécuter la commande, vous devrez mettre à jour `backend-anf.yaml` afin d’y inclure les détails relatifs à l’abonnement Azure NetApp Files, par exemple :

* `subscriptionID` pour l’abonnement Azure avec Azure NetApp Files activé. The 
* `tenantID`, `clientID` et `clientSecret` d’une [inscription d’application](../active-directory/develop/howto-create-service-principal-portal.md) dans Azure Active Directory (AD) avec des autorisations suffisantes pour le service Azure NetApp Files. L’inscription d’application doit porter le rôle `Owner` ou `Contributor` prédéfini par Azure.
* Emplacement Azure qui contient au moins un sous-réseau délégué.

En outre, vous pouvez choisir de fournir un niveau de service différent. Azure NetApp Files propose trois [niveaux de service](../azure-netapp-files/azure-netapp-files-service-levels.md) : Standard, Premium et Ultra.

### <a name="create-a-storageclass"></a>Créer une StorageClass

Une classe de stockage permet de définir la création dynamique d’une unité de stockage avec un volume persistant. Pour consommer des volumes Azure NetApp Files, une classe de stockage doit être créée. Créez un fichier nommé `anf-storageclass.yaml` et copiez-y le manifeste fourni ci-dessous.

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azure-netapp-files
provisioner: csi.trident.netapp.io
parameters:
  backendType: "azure-netapp-files"
  fsType: "nfs"
```

Créez la classe de stockage à l’aide de la commande [kubectl apply][kubectl-apply] :

```console
$  kubectl apply -f anf-storageclass.yaml

storageclass/azure-netapp-files created

$  kubectl get sc
NAME                 PROVISIONER             RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
azure-netapp-files   csi.trident.netapp.io   Delete          Immediate           false                  3s
```

### <a name="create-a-persistentvolumeclaim"></a>Créer une ressource PersistentVolumeClaim

Une PersistentVolumeClaim (PVC) est une demande de stockage effectuée par un utilisateur. Lors de la création d’une PersistentVolumeClaim, Astra Trident crée automatiquement un volume Azure NetApp Files et le met à la disposition des charges de travail Kubernetes.

Créez un fichier nommé `anf-pvc.yaml` et fournissez le manifeste suivant. Dans cet exemple, un volume de 1 Tio est créé avec la fonction *ReadWriteMany*.

```yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: anf-pvc
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 1Ti
  storageClassName: azure-netapp-files
```

Créez la revendication de volume persistant avec la commande [kubectl apply][kubectl-apply] :

```console
$  kubectl apply -f anf-pvc.yaml

persistentvolumeclaim/anf-pvc created

$  kubectl get pvc
kubectl get pvc -n trident
NAME      STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS         AGE
anf-pvc   Bound    pvc-bffa315d-3f44-4770-86eb-c922f567a075   1Ti        RWO            azure-netapp-files   62s
```

### <a name="use-the-persistent-volume"></a>Utiliser le volume persistant

Une fois la PVC créée, un pod peut être lancé pour accéder au volume Azure NetApp Files. Le manifeste ci-dessous peut être utilisé pour définir un pod NGINX qui monte le volume Azure NetApp Files créé à l’étape précédente. Dans cet exemple, le volume est monté sur `/mnt/data`.

Créez un fichier nommé `anf-nginx-pod.yaml`, qui contient le manifeste suivant :

```yml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: mcr.microsoft.com/oss/nginx/nginx:latest1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/data"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: anf-pvc
```

Créez le pod avec la commande [kubectl apply][kubectl-apply] :

```console
$  kubectl apply -f anf-nginx-pod.yaml

pod/nginx-pod created
```

Kubernetes a maintenant créé un pod avec le volume monté et accessible dans le conteneur `nginx` sur `/mnt/data`. Confirmez en vérifiant les journaux des événements pour le pod à l’aide de `kubectl describe` :

```console
$  kubectl describe pod nginx-pod

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  anf-pvc
    ReadOnly:   false
  default-token-k7952:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-k7952
    Optional:    false
[...]
Events:
  Type    Reason                  Age   From                     Message
  ----    ------                  ----  ----                     -------
  Normal  Scheduled               15s   default-scheduler        Successfully assigned trident/nginx-pod to brameshb-non-root-test
  Normal  SuccessfulAttachVolume  15s   attachdetach-controller  AttachVolume.Attach succeeded for volume "pvc-bffa315d-3f44-4770-86eb-c922f567a075"
  Normal  Pulled                  12s   kubelet                  Container image "mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine" already present on machine
  Normal  Created                 11s   kubelet                  Created container nginx
  Normal  Started                 10s   kubelet                  Started container nginx
```

Astra Trident prend en charge de nombreuses fonctionnalités avec Azure NetApp Files, telles que :

* [Expansion des volumes](https://netapp-trident.readthedocs.io/en/latest/kubernetes/operations/tasks/volumes/vol-expansion.html)
* [Instantanés de volume à la demande](https://netapp-trident.readthedocs.io/en/latest/kubernetes/operations/tasks/volumes/snapshots.html)
* [Importation de volumes](https://netapp-trident.readthedocs.io/en/latest/kubernetes/operations/tasks/volumes/import.html)

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur Azure NetApp Files, consultez [Présentation d’Azure NetApp Files][anf].

[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account#az_netappfiles_account_create
[az-netapp-files-dynamic]: azure-netapp-files-dynamic.md
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool#az_netappfiles_pool_create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume#az_netappfiles_volume_create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume#az_netappfiles_volume_show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
