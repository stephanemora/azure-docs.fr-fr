---
title: Résoudre les problèmes courants liés à Kubernetes avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Résolution des problèmes courants liés aux clusters Kubernetes avec Arc.
keywords: Kubernetes, Arc, Azure, containers
ms.openlocfilehash: e1f4e84f16c6b584f1ffbd918a86c251f47efcca
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653998"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting"></a>Résolution des problèmes liés à Kubernetes avec Azure Arc

Ce document fournit des guides pour la résolution des problèmes liés à la connectivité, aux autorisations et aux agents.

## <a name="general-troubleshooting"></a>Résolution générale des problèmes

### <a name="azure-cli"></a>Azure CLI

Avant d’utiliser les commandes CLI `az connectedk8s` ou `az k8s-configuration`, vérifiez qu’Azure CLI est configuré pour fonctionner avec l’abonnement Azure approprié.

```azurecli
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>Agents Azure Arc

Tous les agents pour Kubernetes avec Azure Arc sont déployés en tant que pods dans l’espace de noms `azure-arc`. Tous les pods doivent être en cours d’exécution et réussir leurs contrôles d’intégrité.

Tout d’abord, vérifiez la version de Helm pour Azure Arc :

```console
$ helm --namespace default status azure-arc
NAME: azure-arc
LAST DEPLOYED: Fri Apr  3 11:13:10 2020
NAMESPACE: default
STATUS: deployed
REVISION: 5
TEST SUITE: None
```

Si la version de Helm est introuvable ou manquante, essayez de [reconnecter le cluster à Azure Arc](./connect-cluster.md).

Si la version de Helm est présente avec `STATUS: deployed`, vérifiez l’état des agents à l’aide de `kubectl` :

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                       READY  UP-TO-DATE  AVAILABLE  AGE
deployment.apps/clusteridentityoperator     1/1       1          1       16h
deployment.apps/config-agent                1/1       1          1       16h
deployment.apps/cluster-metadata-operator   1/1       1          1       16h
deployment.apps/controller-manager          1/1       1          1       16h
deployment.apps/flux-logs-agent             1/1       1          1       16h
deployment.apps/metrics-agent               1/1       1          1       16h
deployment.apps/resource-sync-agent         1/1       1          1       16h

NAME                                            READY   STATUS  RESTART  AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

Tous les pods doivent indiquer que le `STATUS` est `Running` avec `3/3` ou `2/2` sous la colonne `READY`. Récupérez les journaux et décrivez les pods qui retournent `Error` ou `CrashLoopBackOff`. Si des pods sont bloqués à l’état `Pending`, cela est peut-être dû à un manque de ressources sur les nœuds de cluster. [Effectuez un scale-up de votre cluster](https://kubernetes.io/docs/tasks/administer-cluster/) pour permettre à ces pods de passer à l’état `Running`.

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>Connexion de clusters Kubernetes à Azure Arc

La connexion de clusters à Azure nécessite à la fois l’accès à un abonnement Azure et l’accès `cluster-admin` à un cluster cible. Si vous ne pouvez pas joindre le cluster ou si vous disposez d’autorisations insuffisantes, la connexion du cluster à Azure Arc ne pourra pas s’effectuer.

### <a name="insufficient-cluster-permissions"></a>Autorisations du cluster insuffisantes

Si le fichier kubeconfig fourni ne dispose pas des autorisations suffisantes pour installer les agents Azure Arc, la commande Azure CLI retourne une erreur.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

L’utilisateur qui connecte le cluster à Azure Arc doit se voir attribuer le rôle `cluster-admin` sur le cluster.

### <a name="installation-timeouts"></a>Délai d’installation

La connexion d’un cluster Kubernetes à Kubernetes avec Azure Arc nécessite l’installation d’agents Azure Arc sur le cluster. Si le cluster s’exécute sur une connexion Internet lente, l’extraction de l’image conteneur pour les agents peut dépasser le délai d’expiration d’Azure CLI.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

### <a name="helm-issue"></a>Problème Helm

La version `v3.3.0-rc.1` de Helm présente un [problème](https://github.com/helm/helm/pull/8527) : l’installation/la mise à niveau de Helm (utilisée par l’extension CLI `connectedk8s`) entraîne l’exécution de tous les hooks, ce qui donne lieu à l’erreur suivante :

```console
$ az connectedk8s connect -n shasbakstest -g shasbakstest
Ensure that you have the latest helm version installed before proceeding.
This operation might take a while...

Please check if the azure-arc namespace was deployed and run 'kubectl get pods -n azure-arc' to check if all the pods are in running state. A possible cause for pods stuck in pending state could be insufficientresources on the kubernetes cluster to onboard to arc.
ValidationError: Unable to install helm release: Error: customresourcedefinitions.apiextensions.k8s.io "connectedclusters.arc.azure.com" not found
```

Pour résoudre ce problème, procédez comme suit :

1. Supprimez la ressource Kubernetes avec Azure Arc dans le portail Azure.
2. Exécutez les commandes suivantes sur votre ordinateur :
    
    ```console
    kubectl delete ns azure-arc
    kubectl delete clusterrolebinding azure-arc-operator
    kubectl delete secret sh.helm.release.v1.azure-arc.v1
    ```

3. [Installez une version stable](https://helm.sh/docs/intro/install/) de Helm 3 sur votre ordinateur à la place de la version Release Candidate.
4. Exécutez la commande `az connectedk8s connect` avec les valeurs appropriées pour connecter le cluster à Azure Arc.

## <a name="configuration-management"></a>Gestion des configurations

### <a name="general"></a>Général
Pour permettre la résolution des problèmes liés à la ressource de configuration, exécutez les commandes az avec le paramètre `--debug` spécifié.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8s-configuration create <parameters> --debug
```

### <a name="create-configurations"></a>Créer des configurations

Les autorisations d’accès en écriture sur la ressource Kubernetes avec Azure Arc (`Microsoft.Kubernetes/connectedClusters/Write`) sont nécessaires et suffisantes pour créer des configurations sur ce cluster.

### <a name="configuration-remains-pending"></a>La configuration reste à l’état `Pending`

```console
kubectl -n azure-arc logs -l app.kubernetes.io/component=config-agent -c config-agent
$ k -n pending get gitconfigs.clusterconfig.azure.com  -o yaml
apiVersion: v1
items:
- apiVersion: clusterconfig.azure.com/v1beta1
  kind: GitConfig
  metadata:
    creationTimestamp: "2020-04-13T20:37:25Z"
    generation: 1
    name: pending
    namespace: pending
    resourceVersion: "10088301"
    selfLink: /apis/clusterconfig.azure.com/v1beta1/namespaces/pending/gitconfigs/pending
    uid: d9452407-ff53-4c02-9b5a-51d55e62f704
  spec:
    correlationId: ""
    deleteOperator: false
    enableHelmOperator: false
    giturl: git@github.com:slack/cluster-config.git
    helmOperatorProperties: null
    operatorClientLocation: azurearcfork8s.azurecr.io/arc-preview/fluxctl:0.1.3
    operatorInstanceName: pending
    operatorParams: '"--disable-registry-scanning"'
    operatorScope: cluster
    operatorType: flux
  status:
    configAppliedTime: "2020-04-13T20:38:43.081Z"
    isSyncedWithAzure: true
    lastPolledStatusTime: ""
    message: 'Error: {exit status 1} occurred while doing the operation : {Installing
      the operator} on the config'
    operatorPropertiesHashed: ""
    publicKey: ""
    retryCountPublicKey: 0
    status: Installing the operator
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```
## <a name="monitoring"></a>Surveillance

Azure Monitor pour conteneurs exige que son DaemonSet soit exécuté en mode privilégié. Pour configurer correctement un cluster Kubernetes Charmed Canonical pour la supervision, exécutez la commande suivante :

```console
juju config kubernetes-worker allow-privileged=true
```