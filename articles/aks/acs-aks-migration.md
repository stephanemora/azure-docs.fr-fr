---
title: Migration d’Azure Container Service (ACS) vers Azure Kubernetes Service (AKS)
description: Migration d’Azure Container Service (ACS) vers Azure Kubernetes Service (AKS)
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: e42b0e7bd1bce40b7c58d75cb07f5a3f8afa5836
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49385039"
---
# <a name="migrating-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Migration d’Azure Container Service (ACS) vers Azure Kubernetes Service (AKS)

L’objectif de ce document est de vous aider à planifier et à exécuter une migration d’Azure Container Service avec Kubernetes (ACS) vers Azure Kubernetes Service (AKS). Ce guide décrit en détail les différences qui existent entre ACS et AKS et fournit une vue d’ensemble du processus de migration, ce qui devrait vous aider à prendre des décisions importantes.

## <a name="differences-between-acs-and-aks"></a>Différences entre ACS et AKS

ACS et AKS comportent des différences à certains niveaux clés, ce qui impacte la migration. Passez en revue les différences ci-dessous, et établissez un plan pour gérer ces différences avant de passer à la migration.

* Les nœuds AKS utilisent des [disques managés](../virtual-machines/windows/managed-disks-overview.md)
    * Les disques non managés doivent être convertis avant de pouvoir être joints aux nœuds AKS.
    * Les objets `StorageClass` personnalisés des disques Azure doivent passer de la valeur `unmanaged` à la valeur `managed`.
    * Tous les `PersistentVolumes` ont besoin d’utiliser `kind: Managed`.
* AKS ne peut prendre en charge qu’un seul pool d’agents.
* Les nœuds Windows Server sont actuellement en [préversion privée](https://azure.microsoft.com/blog/kubernetes-on-azure/).
* Consultez la liste des [régions prises en charge](https://docs.microsoft.com/azure/aks/container-service-quotas) pour AKS.
* AKS est un service managé qui comporte un plan de contrôle Kubernetes hébergé. Si vous avez déjà modifié la configuration de vos principaux ACS, vous devrez peut-être modifier vos applications.

### <a name="differences-between-kubernetes-versions"></a>Différences entre les versions de Kubernetes

Si vous effectuez une migration vers une version plus récente de Kubernetes (par ex., de 1.7.x vers 1.9.x), vous devez prendre en compte certains changements concernant l’API k8s.

* [Migrate a ThirdPartyResource to CustomResourceDefinition](https://kubernetes.io/docs/tasks/access-kubernetes-api/migrate-third-party-resource/)
* [Workloads API changes in versions 1.8 and 1.9](https://kubernetes.io/docs/reference/workloads-18-19/).

## <a name="migration-considerations"></a>Considérations relatives à la migration

### <a name="agent-pools"></a>Pools d’agents

Même si AKS gère le plan de contrôle Kubernetes, vous devez définir la taille et le nombre de nœuds que vous souhaitez inclure dans votre nouveau cluster. Par exemple, si vous souhaitez un mappage 1:1 entre ACS et AKS, vous devrez capturer les informations relatives aux nœuds ACS existants. Vous utiliserez ces données au moment de créer votre cluster AKS.

Exemple :

| NOM | Count | Taille de la machine virtuelle | Système d’exploitation |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standard_D2_v2 | Windows |

Étant donné que des machines virtuelles supplémentaires seront déployées dans votre abonnement pendant la migration, vous devez vérifier que vos quotas et vos limites sont définis sur des valeurs suffisantes pour ces ressources. Pour plus d’informations, consultez [Limites du service et de l’abonnement Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits). Pour vérifier vos quotas actuels, accédez au [panneau Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) dans le portail Azure, sélectionnez votre abonnement, puis sélectionnez `Usage + quotas`.

### <a name="networking"></a>Mise en réseau

Pour les applications complexes, la migration s’effectue généralement en plusieurs fois, et non en une fois. Cela signifie donc que l’ancien environnement et le nouvel environnement auront besoin de communiquer via le réseau. Les applications qui pouvaient utiliser les services `ClusterIP` pour communiquer devront être exposées en tant que type `LoadBalancer` et sécurisées.

Pour effectuer la migration, vous devez pointer les clients vers les nouveaux services qui exécutent AKS. La méthode recommandée pour rediriger le trafic est de mettre à jour le DNS pour qu’il pointe vers l’équilibreur de charge qui se trouve devant votre cluster AKS.

### <a name="stateless-applications"></a>Applications sans état

La migration des applications sans état est le cas de migration le plus simple. Vous appliquez vos définitions YAML au nouveau cluster, vous vérifiez que tout fonctionne comme prévu et vous redirigez le trafic pour activer votre nouveau cluster.

### <a name="stateful-applications"></a>Applications avec état

La migration des applications avec état nécessite que certaines précautions soient prises lors de la planification, afin d’éviter la perte de données ou un temps d’arrêt imprévu.

#### <a name="highly-available-applications"></a>Applications hautement disponibles

Certaines applications avec état peuvent être déployées dans une configuration à haute disponibilité, et peuvent copier des données d’un réplica à l’autre. Si c’est le cas de votre déploiement actuel, vous pouvez créer un membre dans le nouveau cluster AKS, et effectuer une migration avec impact minimal vers les appelants en aval. En général, les étapes de migration de ce scénario sont les suivantes :

1. Créer un réplica secondaire sur AKS
2. Attendre la réplication des données
3. Faire basculer le réplica secondaire pour qu’il devienne le réplica principal
4. Diriger le trafic vers le cluster AKS

#### <a name="migrating-persistent-volumes"></a>Migration des volumes persistants

Plusieurs facteurs sont à prendre en compte lorsque vous migrez des volumes persistants existant vers AKS. En règle générale, les étapes impliquées sont les suivantes :

1. (Facultatif) Suspendre l’écriture dans l’application (nécessite un temps d’arrêt)
2. Créer des captures instantanées de disques
3. Créer des disques managés à partir d’instantanés
4. Créer des volumes persistants dans AKS
5. Modifier les spécifications du pod pour qu’il [utilise les volumes existants](https://docs.microsoft.com/azure/aks/azure-disk-volume) plutôt que PersistentVolumeClaims (provisionnement statique)
6. Déployer l’application sur AKS
7. Valider
8. Diriger le trafic vers le cluster AKS

> **Important** : Si vous ne souhaitez pas suspendre l’écriture, vous devez répliquer les données sur le nouveau déploiement, puisqu’il vous manquera les données qui ont été écrites depuis la capture instantanée des disques.

Il existe des outils open source permettant de créer des disques managés et d’effectuer la migration de volumes d’un cluster Kubernetes à l’autre.

* [noelbundick/azure-cli-disk-extension](https://github.com/noelbundick/azure-cli-disk-copy-extension) : pour copier et convertir des disques dans les groupes de ressources et les régions Azure
* [yaron2/azure-kube-cli](https://github.com/yaron2/azure-kube-cli) : pour énumérer les volumes Kubernetes ACS et effectuer leur migration vers un cluster AKS

#### <a name="azure-files"></a>Azure Files

Contrairement aux disques, Azure Files peut être monté sur plusieurs hôtes en même temps. Ni Azure ni Kubernetes ne vous empêchent de créer un pod dans votre cluster AKS qui soit toujours utilisé par votre cluster ACS. Pour éviter une perte de données et un comportement inattendu, vérifiez que les deux clusters n’écrivent pas dans les mêmes fichiers en même temps.

Si votre application peut héberger plusieurs réplicas pointant vers le même partage de fichiers, vous pouvez suivre les étapes de migration sans état, et déployer vos définitions YAML sur votre nouveau cluster.

Si ce n’est pas le cas, essayez la méthode suivante :

1. Déployez votre application sur AKS avec un nombre de réplicas égal à 0.
2. Effectuez une mise à l’échelle de l’application ACS vers la valeur 0 (nécessite un temps d’arrêt)
3. Effectuez une mise à l’échelle de l’application AKS vers la valeur 1 (maximum)
4. Valider
5. Diriger le trafic vers le cluster AKS

Si vous souhaitez commencer avec un partage vide, copiez les données sources. Vous pouvez utiliser les commandes [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) pour migrer vos données.

### <a name="deployment-strategy"></a>Stratégie de déploiement

La méthode recommandée consiste à utiliser votre pipeline CI/CD existant pour déployer une configuration connue comme étant valide sur AKS. Vous allez cloner vos tâches de déploiement existantes et vérifier que votre `kubeconfig` pointe vers le nouveau cluster AKS.

Lorsque ce n’est pas possible, vous devez exporter les définitions de ressources à partir d’ACS, puis les appliquer à AKS. Vous pouvez utiliser `kubectl` pour exporter des objets.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

Il existe également plusieurs outils open source qui peuvent vous être utiles, en fonction de vos besoins :

* [heptio/ark](https://github.com/heptio/ark) : nécessite k8s 1.7
* [yaron2/azure-kube-cli](https://github.com/yaron2/azure-kube-cli)
* [mhausenblas/reshifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>Étapes de la migration

### <a name="1-create-an-aks-cluster"></a>1. Créer un cluster AKS

Vous pouvez suivre les instructions des documents qui expliquent comment [créer des clusters AKS](https://docs.microsoft.com/azure/aks/create-cluster) via le portail Azure, Azure CLI ou les modèles Resource Manager.

> Vous trouverez des exemples de modèles Azure Resource Manager pour AKS dans le dépôt [Azure/AKS](https://github.com/Azure/AKS/tree/master/examples/vnet) sur GitHub.

### <a name="2-modify-applications"></a>2. Modifier les applications

Apportez les modifications nécessaires à vos définitions YAML. Par ex. : en remplaçant `apps/v1beta1` par `apps/v1` pour `Deployments`

### <a name="3-optional-migrate-volumes"></a>3. (Facultatif) Effectuer la migration de volumes

Effectuez la migration de volumes de votre cluster ACS vers votre cluster AKS. Pour plus d’informations, consultez la section [Migration des volumes persistants](#Migrating-Persistent-Volumes).

### <a name="4-deploy-applications"></a>4. Déployer des applications

Utilisez votre système CI/CD pour déployer des applications sur AKS, ou utilisez kubectl pour appliquer les définitions YAML.

### <a name="5-validate"></a>5. Valider

Vérifiez que vos applications fonctionnent comme prévu et que toutes les données migrées ont été copiées.

### <a name="6-redirect-traffic"></a>6. Rediriger le trafic

Mettez à jour le DNS pour qu’il pointe les clients vers votre déploiement AKS.

### <a name="7-post-migration-tasks"></a>7. Tâches post-migration

Si vous avez effectué la migration de volumes et avez choisi de ne pas suspendre l’écriture, vous devez copier ces données vers le nouveau cluster.
