---
title: Kubernetes sur Azure - Autoscaler de cluster
description: Découvrez comment utiliser l’autoscaler de cluster avec Azure Kubernetes Service (AKS) afin de mettre automatiquement à l'échelle votre cluster pour répondre à la demande.
services: container-service
author: sakthivetrivel
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/19/18
ms.author: sakthivetrivel
ms.custom: mvc
ms.openlocfilehash: 3bac6534f43d62e6eb9381b8513025ba9117ed04
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857004"
---
# <a name="cluster-autoscaler-on-azure-kubernetes-service-aks---preview"></a>Autoscaler de cluster sur Azure Kubernetes Service (AKS) - Préversion

Azure Kubernetes Service (AKS) est une solution flexible qui simplifie le déploiement d’un cluster Kubernetes géré dans Azure. À mesure que la demande en ressources augmente, l’autoscaler de cluster permet à votre cluster de se développer pour répondre à cette demande en fonction de contraintes que vous définissez. Pour ce faire, l’autoscaler de cluster (CA) met à l’échelle vos nœuds d’agent en fonction de pods en attente. Il analyse le cluster régulièrement pour rechercher des nœuds vides ou des pods en attente, et augmente la taille si possible. Par défaut, l’autoscaler analyse les pods en attente toutes les 10 secondes, et supprime un nœud s’il n’est pas utilisé pendant plus de 10 minutes. Quand il est utilisé avec [l’autoscaler de pods horizontaux](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) (HPA), ce dernier met à jour les réplicas de pod et les ressources conformément à la demande. S’il n’existe pas suffisamment de nœuds ou de nœuds inutiles après cette mise à l’échelle des pods, l’autoscaler CA répond et planifie les pods sur le nouvel ensemble de nœuds.

Cet article décrit comment déployer l’autoscaler de cluster sur les nœuds d’agent. Toutefois, étant donné que l’autoscaler de cluster est déployé dans l’espace de noms kube-system, il ne fait pas descendre en puissance le nœud qui exécute ce pod.

> [!IMPORTANT]
> L’intégration de l’autoscaler de cluster Azure Kubernetes Service (AKS) est actuellement en **préversion**. Les préversions sont à votre disposition, à condition que vous acceptiez les [conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale.
>

## <a name="prerequisites"></a>Prérequis

Ce document suppose que vous disposez d’un cluster AKS compatible avec RBAC. Si vous avez besoin d’un cluster AKS, consultez le [Guide de démarrage rapide : déployer un cluster Azure Kubernetes Service (AKS)][aks-quick-start].

 Pour utiliser l’autoscaler de cluster, votre cluster doit exploiter Kubernetes v1.10.X ou une version ultérieure, et doit être compatible avec RBAC. Pour mettre à niveau votre cluster, consultez l’article sur [la mise à niveau d’un cluster AKS][aks-upgrade].

## <a name="gather-information"></a>Collecter des informations

Pour générer les autorisations nécessaires pour que votre autoscaler de cluster s’exécute dans votre cluster, exécutez ce script bash :

```sh
#! /bin/bash
ID=`az account show --query id -o json`
SUBSCRIPTION_ID=`echo $ID | tr -d '"' `

TENANT=`az account show --query tenantId -o json`
TENANT_ID=`echo $TENANT | tr -d '"' | base64`

read -p "What's your cluster name? " cluster_name
read -p "Resource group name? " resource_group

CLUSTER_NAME=`echo $cluster_name | base64`
RESOURCE_GROUP=`echo $resource_group | base64`

PERMISSIONS=`az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/$SUBSCRIPTION_ID" -o json`
CLIENT_ID=`echo $PERMISSIONS | sed -e 's/^.*"appId"[ ]*:[ ]*"//' -e 's/".*//' | base64`
CLIENT_SECRET=`echo $PERMISSIONS | sed -e 's/^.*"password"[ ]*:[ ]*"//' -e 's/".*//' | base64`

SUBSCRIPTION_ID=`echo $ID | tr -d '"' | base64 `

NODE_RESOURCE_GROUP=`az aks show --name $cluster_name  --resource-group $resource_group -o tsv --query 'nodeResourceGroup' | base64`

echo "---
apiVersion: v1
kind: Secret
metadata:
    name: cluster-autoscaler-azure
    namespace: kube-system
data:
    ClientID: $CLIENT_ID
    ClientSecret: $CLIENT_SECRET
    ResourceGroup: $RESOURCE_GROUP
    SubscriptionID: $SUBSCRIPTION_ID
    TenantID: $TENANT_ID
    VMType: QUtTCg==
    ClusterName: $CLUSTER_NAME
    NodeResourceGroup: $NODE_RESOURCE_GROUP
---"
```

Une fois les étapes du script effectuées, celui-ci génère vos détails sous la forme d’un secret, comme suit :

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: cluster-autoscaler-azure
  namespace: kube-system
data:
  ClientID: <base64-encoded-client-id>
  ClientSecret: <base64-encoded-client-secret>$
  ResourceGroup: <base64-encoded-resource-group>  SubscriptionID: <base64-encode-subscription-id>
  TenantID: <base64-encoded-tenant-id>
  VMType: QUtTCg==
  ClusterName: <base64-encoded-clustername>
  NodeResourceGroup: <base64-encoded-node-resource-group>
---
```

Ensuite, obtenez le nom de votre pool de nœuds en exécutant la commande suivante. 

```console
$ kubectl get nodes --show-labels
```

Sortie :

```console
NAME                       STATUS    ROLES     AGE       VERSION   LABELS
aks-nodepool1-37756013-0   Ready     agent     1h        v1.10.3   agentpool=nodepool1,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=Standard_DS1_v2,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=eastus,failure-domain.beta.kubernetes.io/zone=0,kubernetes.azure.com/cluster=MC_[resource-group]\_[cluster-name]_[location],kubernetes.io/hostname=aks-nodepool1-37756013-0,kubernetes.io/role=agent,storageprofile=managed,storagetier=Premium_LRS
 ```

Ensuite, extrayez la valeur de l’étiquette **agentpool**. Le nom par défaut pour le pool de nœuds d’un cluster est « nodepool1 ».

Vous pouvez à présent créer un graphique de déploiement à l’aide du secret et du pool de nœuds.

## <a name="create-a-deployment-chart"></a>Créer un graphique de déploiement

Créez un fichier nommé `aks-cluster-autoscaler.yaml`, et copiez dans ce fichier le code YAML suivant.

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
  name: cluster-autoscaler
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRole
metadata:
  name: cluster-autoscaler
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
rules:
- apiGroups: [""]
  resources: ["events","endpoints"]
  verbs: ["create", "patch"]
- apiGroups: [""]
  resources: ["pods/eviction"]
  verbs: ["create"]
- apiGroups: [""]
  resources: ["pods/status"]
  verbs: ["update"]
- apiGroups: [""]
  resources: ["endpoints"]
  resourceNames: ["cluster-autoscaler"]
  verbs: ["get","update"]
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["watch","list","get","update"]
- apiGroups: [""]
  resources: ["pods","services","replicationcontrollers","persistentvolumeclaims","persistentvolumes"]
  verbs: ["watch","list","get"]
- apiGroups: ["extensions"]
  resources: ["replicasets","daemonsets"]
  verbs: ["watch","list","get"]
- apiGroups: ["policy"]
  resources: ["poddisruptionbudgets"]
  verbs: ["watch","list"]
- apiGroups: ["apps"]
  resources: ["statefulsets"]
  verbs: ["watch","list","get"]
- apiGroups: ["storage.k8s.io"]
  resources: ["storageclasses"]
  verbs: ["get", "list", "watch"]

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: Role
metadata:
  name: cluster-autoscaler
  namespace: kube-system
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
rules:
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["create"]
- apiGroups: [""]
  resources: ["configmaps"]
  resourceNames: ["cluster-autoscaler-status"]
  verbs: ["delete","get","update"]

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: cluster-autoscaler
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-autoscaler
subjects:
  - kind: ServiceAccount
    name: cluster-autoscaler
    namespace: kube-system

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: RoleBinding
metadata:
  name: cluster-autoscaler
  namespace: kube-system
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: cluster-autoscaler
subjects:
  - kind: ServiceAccount
    name: cluster-autoscaler
    namespace: kube-system

---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  labels:
    app: cluster-autoscaler
  name: cluster-autoscaler
  namespace: kube-system
spec:
  replicas: 1
  selector:
    matchLabels:
      app: cluster-autoscaler
  template:
    metadata:
      labels:
        app: cluster-autoscaler
    spec:
      serviceAccountName: cluster-autoscaler
      containers:
      - image: gcr.io/google-containers/cluster-autoscaler:v1.2.2
        imagePullPolicy: Always
        name: cluster-autoscaler
        resources:
          limits:
            cpu: 100m
            memory: 300Mi
          requests:
            cpu: 100m
            memory: 300Mi
        command:
        - ./cluster-autoscaler
        - --v=3
        - --logtostderr=true
        - --cloud-provider=azure
        - --skip-nodes-with-local-storage=false
        - --nodes=1:10:nodepool1
        env:
        - name: ARM_SUBSCRIPTION_ID
          valueFrom:
            secretKeyRef:
              key: SubscriptionID
              name: cluster-autoscaler-azure
        - name: ARM_RESOURCE_GROUP
          valueFrom:
            secretKeyRef:
              key: ResourceGroup
              name: cluster-autoscaler-azure
        - name: ARM_TENANT_ID
          valueFrom:
            secretKeyRef:
              key: TenantID
              name: cluster-autoscaler-azure
        - name: ARM_CLIENT_ID
          valueFrom:
            secretKeyRef:
              key: ClientID
              name: cluster-autoscaler-azure
        - name: ARM_CLIENT_SECRET
          valueFrom:
            secretKeyRef:
              key: ClientSecret
              name: cluster-autoscaler-azure
        - name: ARM_VM_TYPE
          valueFrom:
            secretKeyRef:
              key: VMType
              name: cluster-autoscaler-azure
        - name: AZURE_CLUSTER_NAME
          valueFrom:
            secretKeyRef:
              key: ClusterName
              name: cluster-autoscaler-azure
        - name: AZURE_NODE_RESOURCE_GROUP
          valueFrom:
            secretKeyRef:
              key: NodeResourceGroup
              name: cluster-autoscaler-azure
      restartPolicy: Always
```

Copiez et collez le secret créé à l’étape précédente, puis insérez-le au début du fichier.

Ensuite, pour définir la plage de nœuds, renseignez l’argument pour `--nodes` sous `command` au format MIN:MAX:NOM_POOL_NOEUDS. Par exemple : `--nodes=3:10:nodepool1` définit le nombre minimum de nœuds sur 3, le nombre maximum de nœuds sur 10, et le nom du pool de nœuds par nodepool1.

Ensuite, renseignez le champ d’image sous **conteneurs** en indiquant la version de l’autoscaler de cluster que vous souhaitez utiliser. AKS nécessite v1.2.2 ou une version ultérieure. Ici, l’exemple utilise v1.2.2.

## <a name="deployment"></a>Déploiement

Déploiement de l’autoscaler de cluster par l’exécution

```console
kubectl create -f aks-cluster-autoscaler.yaml
```

Pour vérifier si l’autoscaler de cluster est en cours d’exécution, utilisez la commande suivante, puis vérifiez la liste des pods. Un pool avec le préfixe « cluster-autoscaler » doit s’exécuter. Si c’est bien le cas, votre autoscaler de cluster a été déployé.

```console
kubectl -n kube-system get pods
```

Pour afficher l’état de l’autoscaler de cluster, exécutez

```console
kubectl -n kube-system describe configmap cluster-autoscaler-status
```

## <a name="interpreting-the-cluster-autoscaler-status"></a>Interprétation de l’état de l’autoscaler de cluster

```console
$ kubectl -n kube-system describe configmap cluster-autoscaler-status
Name:         cluster-autoscaler-status
Namespace:    kube-system
Labels:       <none>
Annotations:  cluster-autoscaler.kubernetes.io/last-updated=2018-07-25 22:59:22.661669494 +0000 UTC

Data
====
status:
----
Cluster-autoscaler status at 2018-07-25 22:59:22.661669494 +0000 UTC:
Cluster-wide:
  Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleUp:     NoActivity (ready=1 registered=1)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleDown:   NoCandidates (candidates=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC

NodeGroups:
  Name:        nodepool1
  Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0 cloudProviderTarget=1 (minSize=1, maxSize=5))
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleUp:     NoActivity (ready=1 cloudProviderTarget=1)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleDown:   NoCandidates (candidates=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC


Events:  <none>
```

L’état de l’autoscaler de cluster vous donne des informations à deux niveaux : à l’échelle du cluster et au sein de chaque groupe de nœuds. Dans la mesure où AKS ne prend actuellement en charge qu’un seul pool de nœuds, ces métriques sont les mêmes.

* Health indique l’intégrité globale des nœuds. Si l’autoscaler de cluster a des difficultés à créer ou supprime des nœuds dans le cluster, cet état passe à « Unhealthy ». Vous obtenez également une répartition de l’état des différents nœuds :
    * « Ready » signifie qu’un nœud est prêt à accueillir des pods planifiés.
    * « Unready » signifie qu’un nœud est tombé en panne après son démarrage.
    * « NotStarted » signifie qu’un nœud n’est pas encore entièrement démarré.
    * « LongNotStarted » signifie qu’un nœud n’a pas pu démarrer dans un délai raisonnable.
    * « Registered » signifie qu’un nœud est inscrit dans le groupe.
    * « Unregistered » signifie qu’un nœud est présent côté fournisseur de cluster, mais qu’il n’a pas pu s’inscrire auprès de Kubernetes.
  
* ScaleUp vous permet de savoir quand le cluster détermine qu’une montée en puissance doit se produire dans votre cluster.
    * Une « transition » indique un changement au niveau du nombre de nœuds du cluster ou de l’état d’un nœud.
    * Le nombre de nœuds « ready » indique le nombre de nœuds disponibles et prêts dans le cluster. 
    * Déterminé par l’autoscaler de cluster, « cloudProviderTarget » indique le nombre de nœuds dont le cluster a besoin pour gérer sa charge de travail.

* ScaleDown vous permet de savoir si des nœuds sont candidats à la descente en puissance. 
    * Un candidat à la descente en puissance est un nœud qui, selon l’autoscaler de cluster, peut être supprimé sans affecter la capacité du cluster à gérer sa charge de travail. 
    * Les heures fournies indiquent la dernière évaluation du cluster pour déterminer s’il contenait des candidats à la descente en puissance et l’heure de la dernière transition.

Enfin, sous Events, vous pouvez voir les événements de montée ou de descente en puissance effectués par l’autoscaler de cluster, ayant échoué ou réussi, ainsi que les heures correspondantes.

## <a name="next-steps"></a>Étapes suivantes

Pour utiliser l’autoscaler de cluster avec l’autoscaler de pods horizontaux, consultez la section traitant de la [mise à l’échelle de l’application et de l’infrastructure Kubernetes][aks-tutorial-scale].

Apprenez-en davantage sur le déploiement et la gestion d’ACS avec les didacticiels ACS.

> [!div class="nextstepaction"]
> [Didacticiel ACS][aks-tutorial-prepare-app]

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md

<!-- LINKS - external -->
[cluster-autoscale]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md
