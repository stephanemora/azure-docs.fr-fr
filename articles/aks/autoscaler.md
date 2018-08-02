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
ms.openlocfilehash: 4f8df8e7004ca3cee832b6230dc153b21e2a6c18
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186711"
---
# <a name="cluster-autoscaler-on-azure-kubernetes-service-aks---preview"></a>Autoscaler de cluster sur Azure Kubernetes Service (AKS) - Préversion

Azure Kubernetes Service (AKS) est une solution flexible qui simplifie le déploiement d’un cluster Kubernetes géré dans Azure. À mesure que la demande en ressources augmente, l’autoscaler de cluster permet à votre cluster de se développer pour répondre à cette demande en fonction de contraintes que vous définissez. Pour ce faire, l’autoscaler de cluster (CA) met à l’échelle vos nœuds d’agent en fonction de pods en attente. Il analyse le cluster régulièrement pour rechercher des nœuds vides ou des pods en attente, et augmente la taille si possible. Par défaut, l’autoscaler analyse les pods en attente toutes les 10 secondes, et supprime un nœud s’il n’est pas utilisé pendant plus de 10 minutes. Lorsqu’il est utilisé avec l’autoscaler de pods horizontaux (HPA), ce dernier met à jour les réplicas de pod et les ressources conformément à la demande. S’il n’existe pas suffisamment de nœuds ou de nœuds inutiles après cette mise à l'échelle des pods, l’autoscaler CA répond et planifie les pods sur le nouvel ensemble de nœuds.

> [!IMPORTANT]
> L’intégration de l’autoscaler de cluster Azure Kubernetes Service (AKS) est actuellement en **préversion**. Les préversions sont à votre disposition, à condition que vous acceptiez les [conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale.
>

## <a name="prerequisites"></a>Prérequis

Ce document suppose que vous disposez d’un cluster AKS compatible avec RBAC. Si vous avez besoin d’un cluster AKS, consultez le [Guide de démarrage rapide : déployer un cluster Azure Kubernetes Service (AKS)][aks-quick-start].

 Pour utiliser l’autoscaler de cluster, votre cluster doit exploiter Kubernetes v1.10.X ou une version ultérieure, et doit être compatible avec RBAC. Pour mettre à niveau votre cluster, consultez l’article sur [la mise à niveau d’un cluster AKS][aks-upgrade].

## <a name="gather-information"></a>Collecter des informations

La liste suivante répertorie toutes les informations que vous devez fournir pour définir l’autoscaler.

- *ID d’abonnement* : ID correspondant à l’abonnement utilisé pour ce cluster
- *Nom du groupe de ressources* : nom du groupe de ressources auquel appartient le cluster 
- *Nom du cluster* : nom du cluster
- *ID client*: ID d’application fourni lors de l’étape de génération d’autorisations
- *Clé secrète client* : clé secrète d’application fournie lors de l’étape de génération d’autorisations
- *ID de locataire* : ID du locataire (propriétaire du compte)
- *Groupe de ressources de nœuds* : nom du groupe de ressources contenant les nœuds d’agent dans le cluster
- *Nom du pool de nœuds* : nom du pool de nœuds que vous souhaitez mettre à l’échelle
- *Nombre minimum de nœuds* : nombre minimum de nœuds qui peuvent exister dans le cluster
- *Nombre maximum de nœuds* : nombre maximum de nœuds qui peuvent exister dans le cluster
- *Type de machine virtuelle* : service utilisé pour générer le cluster Kubernetes

Obtenez votre ID d’abonnement comme suit : 

``` azurecli
az account show --query id
```

Générez un ensemble d’informations d’identification Azure en exécutant la commande suivante :

```console
$ az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscription-id>" --output json

"appId": <app-id>,
"displayName": <display-name>,
"name": <name>,
"password": <app-password>,
"tenant": <tenant-id>
```

L’ID d’application, le mot de passe et l’ID de locataire correspondent à clientID, clientSecret et tenantID dans les étapes suivantes.

Obtenez le nom de votre pool de nœuds en exécutant la commande suivante. 

```console
$ kubectl get nodes --show-labels
```

Sortie :

```console
NAME                       STATUS    ROLES     AGE       VERSION   LABELS
aks-nodepool1-37756013-0   Ready     agent     1h        v1.10.3   agentpool=nodepool1,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=Standard_DS1_v2,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=eastus,failure-domain.beta.kubernetes.io/zone=0,kubernetes.azure.com/cluster=MC_[resource-group]\_[cluster-name]_[location],kubernetes.io/hostname=aks-nodepool1-37756013-0,kubernetes.io/role=agent,storageprofile=managed,storagetier=Premium_LRS
 ```

Ensuite, extrayez la valeur de l’étiquette **agentpool**. Le nom par défaut pour le pool de nœuds d’un cluster est « nodepool1 ».

Pour obtenir le nom de votre groupe de ressources de nœuds, extrayez la valeur de l’étiquette **kubernetes.azure.com<span></span>/cluster**. Le nom du groupe de ressources de nœuds est généralement au format MC_ [groupe de ressources]\_[nom-cluster] _ [emplacement].

Le paramètre vmType fait référence au service en cours d’utilisation. Ici, il s’agit d’AKS.

Vous devez désormais disposer des informations suivantes :

- SubscriptionID
- ResourceGroup
- ClusterName
- ClientID
- ClientSecret
- ID de locataire
- NodeResourceGroup
- VMType

Ensuite, encodez l’ensemble de ces valeurs avec base64. Par exemple, pour encoder la valeur VMType avec base64 :

```console
$ echo AKS | base64
QUtTCg==
```

## <a name="create-secret"></a>Créer un secret
À l’aide de ces données, créez un secret pour le déploiement en utilisant les valeurs trouvées dans les étapes précédentes au format suivant :

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: cluster-autoscaler-azure
  namespace: kube-system
data:
  ClientID: <base64-encoded-client-id>
  ClientSecret: <base64-encoded-client-secret>
  ResourceGroup: <base64-encoded-resource-group>
  SubscriptionID: <base64-encode-subscription-id>
  TenantID: <base64-encoded-tenant-id>
  VMType: QUtTCg==
  ClusterName: <base64-encoded-clustername>
  NodeResourceGroup: <base64-encoded-node-resource-group>
---
```

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
      - image: k8s.gcr.io/cluster-autoscaler:{{ ca_version }}
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

Copiez et collez le secret créé à l’étape précédente, et insérez-le au début du fichier.

Ensuite, pour définir la plage de nœuds, renseignez l’argument pour `--nodes` sous `command` au format MIN:MAX:NOM_POOL_NOEUDS. Par exemple : `--nodes=3:10:nodepool1` définit le nombre minimum de nœuds sur 3, le nombre maximum de nœuds sur 10, et le nom du pool de nœuds par nodepool1.

Ensuite, renseignez le champ d’image sous **conteneurs** en indiquant la version de l’autoscaler de cluster que vous souhaitez utiliser. AKS nécessite v1.2.2 ou une version ultérieure. Ici, l’exemple utilise v1.2.2.

## <a name="deployment"></a>Déploiement

Déploiement de l’autoscaler de cluster par l’exécution

```console
kubectl create -f cluster-autoscaler-containerservice.yaml
```

Pour vérifier si l’autoscaler de cluster est en cours d’exécution, utilisez la commande suivante, puis vérifiez la liste des pods. S’il existe un pod avec le préfixe « cluster-autoscaler » en cours d’exécution, votre autoscaler de cluster a été déployé.

```console
kubectl -n kube-system get pods
```

Pour afficher l’état de l’autoscaler de cluster, exécutez

```console
kubectl -n kube-system describe configmap cluster-autoscaler-status
```

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
