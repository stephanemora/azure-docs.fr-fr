---
title: Utiliser GitOps avec Helm pour une configuration de cluster compatible avec Azure Arc (préversion)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Utiliser GitOps avec Helm pour une configuration de cluster compatible avec Azure Arc (préversion)
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: 9cd1169c7a622da0e4be3900f94dc31fc99e762d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83662537"
---
# <a name="use-gitops-with-helm-for-an-azure-arc-enabled-cluster-configuration-preview"></a>Utiliser GitOps avec Helm pour une configuration de cluster compatible avec Azure Arc (préversion)

Helm est un outil d’empaquetage open source qui vous aide à installer et à gérer le cycle de vie d’applications Kubernetes. À l’instar de gestionnaires de package Linux tels que APT et Yum, Helm sert à gérer les charts Kubernetes, qui sont des packages de ressources Kubernetes préconfigurées.

Cet article vous montre comment configurer et utiliser Helm à l’aide de Kubernetes avec Azure Arc.

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous disposez d’un cluster connecté Kubernetes avec Azure Arc. Si vous avez besoin d’un cluster connecté, reportez-vous au [guide de démarrage rapide Connecter un cluster](./connect-cluster.md).

Nous allons tout d’abord définir les variables d’environnement à utiliser dans ce didacticiel. Vous aurez besoin du nom du groupe de ressources et de celui du cluster pour votre cluster connecté.

```bash
export RESOURCE_GROUP=<Resource_Group_Name>
export CLUSTER_NAME=<ClusterName>
```

## <a name="verify-your-cluster-is-enabled-with-arc"></a>Vérifier que votre cluster est activé avec Arc

```bash
az connectedk8s list -g $RESOURCE_GROUP -o table
```

Sortie :
```bash
Name           Location    ResourceGroup
-------------  ----------  ---------------
arc-helm-demo  eastus      k8s-clusters
```

## <a name="overview-of-using-helm-with-azure-arc-enabled-kubernetes"></a>Présentation de l’utilisation de Helm avec Kubernetes avec Azure Arc

 L’opérateur Helm fournit une extension à Flux qui automatise les versions Chart Helm. Une version Chart est décrite par le biais d’une ressource personnalisée Kubernetes nommée HelmRelease. Flux synchronise ces ressources de Git au cluster, et l’opérateur Helm vérifie que les charts Helm sont publiés comme spécifié dans les ressources.

 Vous trouverez ci-dessous un exemple de structure de référentiel Git que nous allons utiliser dans ce didacticiel :

```bash
├── charts
│   └── azure-vote
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── prod
        └── azure-vote-app.yaml
```

Dans le référentiel Git, nous avons deux répertoires, l’un contenant un chart Helm et l’autre le fichier config des versions. Dans le répertoire `releases/prod`, le `azure-vote-app.yaml` contient le fichier config HelmRelease indiqué ci-dessous :

```bash
 apiVersion: helm.fluxcd.io/v1
 kind: HelmRelease
 metadata:
   name: azure-vote-app
   namespace: prod
   annotations:
 spec:
   releaseName: azure-vote-app
   chart:
     git: https://github.com/Azure/arc-helm-demo
     path: charts/azure-vote
     ref: master
   values:
     image:
       repository: dstrebel/azurevote
       tag: v1
     replicaCount: 3
```

Le fichier config de la version Helm contient les champs suivants :

- `metadata.name` est obligatoire et doit respecter les conventions d’affectation de noms de Kubernetes
- `metadata.namespace` est facultatif et détermine où la version est créée
- `spec.releaseName` est facultatif et, s’il n’est pas fourni, le nom de la version sera $namespace-$name
- `spec.chart.path` est le répertoire contenant le chart, donné par rapport à la racine de référentiel
- `spec.values` sont des personnalisations par l’utilisateur de valeurs de paramètres par défaut provenant du chart lui-même

Les options spécifiées dans le spec.values HelmRelease remplacent les options spécifiées dans values.yaml de la source du chart.

Vous pouvez en savoir plus sur le HelmRelease dans la [documentation officielle de l’opérateur Helm](https://docs.fluxcd.io/projects/helm-operator/en/1.0.0-rc9/references/helmrelease-custom-resource.html).

## <a name="create-a-configuration"></a>Créer une configuration

À l’aide de l’extension Azure CLI pour `k8sconfiguration`, nous allons lier notre cluster connecté à un exemple de référentiel Git. Nous donnerons à cette configuration le nom `azure-voting-app` et déploierons l’opérateur Flux dans l’espace de noms `prod`.

```bash
az k8sconfiguration create --name azure-voting-app --resource-group  $RESOURCE_GROUP --cluster-name $CLUSTER_NAME --operator-instance-name azure-voting-app --operator-namespace prod --enable-helm-operator --helm-operator-version='0.6.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --operator-params='--git-readonly --git-path=releases/prod' --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Paramètres de configuration

Pour personnaliser la création de la configuration, [découvrez les paramètres supplémentaires que vous pouvez utiliser](./use-gitops-connected-cluster.md#additional-parameters).


## <a name="validate-the-configuration"></a>Valider la configuration

À l’aide d’Azure CLI, validez la création de `sourceControlConfiguration`.

```console
az k8sconfiguration show --resource-group $RESOURCE_GROUP --name azure-voting-app --cluster-name $CLUSTER_NAME --cluster-type connectedClusters
```

Notez que la ressource `sourceControlConfiguration` est mise à jour avec un état de conformité, des messages et des informations de débogage.

**Output:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Compliant",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "...",
    "messageLevel": "3"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "azure-vote-app",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "prod",
  "operatorParams": "--git-readonly --git-path=releases/prod",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>Valider l’application

Nous allons maintenant vérifier que l’application est en cours d’exécution en obtenant l’adresse IP du service.

```bash
kubectl get svc/azure-vote-front -n prod
```

**Output:**

```bash
NAME               TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.14.161   52.186.160.216   80:30372/TCP   4d22h
```

Recherchez l’adresse IP externe à partir de la sortie ci-dessus, puis ouvrez-la dans un navigateur.

## <a name="next-steps"></a>Étapes suivantes

- [Utiliser Azure Policy pour gérer la configuration du cluster](./use-azure-policy.md)
