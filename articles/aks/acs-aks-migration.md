---
title: Migrer d’Azure Container Service (ACS) vers Azure Kubernetes Service (AKS)
description: Migrez d’Azure Container Service (ACS) vers Azure Kubernetes Service (AKS).
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: 66f76a8a706f60df786786cbd1ce00b7eafd8d7e
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097883"
---
# <a name="migrate-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Migrer d’Azure Container Service (ACS) vers Azure Kubernetes Service (AKS)

Cet article vous aide à planifier et à exécuter une migration d’Azure Container Service (ACS) avec Kubernetes vers Azure Kubernetes Service (AKS). Pour vous aider à prendre des décisions importantes, ce guide décrit en détail les différences qui existent entre ACS et AKS et fournit une vue d’ensemble du processus de migration.

## <a name="differences-between-acs-and-aks"></a>Différences entre ACS et AKS

ACS et AKS comportent des différences à certains niveaux clés, ce qui impacte la migration. Avant la migration, passez en revue les différences ci-dessous et établissez un plan pour gérer ces différences :

* Les nœuds AKS utilisent des [disques managés](../virtual-machines/windows/managed-disks-overview.md).
    * Les disques non managés doivent être convertis avant que vous ne puissiez les joindre aux nœuds AKS.
    * Les objets `StorageClass` personnalisés des disques Azure doivent passer de la valeur `unmanaged` à la valeur `managed`.
    * Tous les `PersistentVolumes` doivent utiliser `kind: Managed`.
* AKS prend en charge [plusieurs pools de nœuds](https://docs.microsoft.com/azure/aks/use-multiple-node-pools) (actuellement en préversion).
* Les nœuds basés sur Windows Server sont actuellement en [préversion dans AKS](https://azure.microsoft.com/blog/kubernetes-on-azure/).
* AKS prend en charge un ensemble limité de [régions](https://docs.microsoft.com/azure/aks/quotas-skus-regions).
* AKS est un service managé qui comporte un plan de contrôle Kubernetes hébergé. Si vous avez déjà modifié la configuration de vos principaux ACS, vous devrez peut-être modifier vos applications.

## <a name="differences-between-kubernetes-versions"></a>Différences entre les versions de Kubernetes

Si vous effectuez une migration vers une version plus récente de Kubernetes, passez en revue les ressources suivantes pour comprendre les stratégies de contrôle de version Kubernetes :

* [Stratégie de prise en charge des versions et des différences de version Kubernetes](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)

## <a name="migration-considerations"></a>Considérations relatives à la migration

### <a name="agent-pools"></a>Pools d’agents

Bien qu’AKS gère le plan de contrôle Kubernetes, vous devez définir la taille et le nombre de nœuds à inclure dans votre nouveau cluster. Par exemple, si vous souhaitez un mappage 1:1 entre ACS et AKS, vous devrez capturer les informations relatives aux nœuds ACS existants. Utilisez ces données au moment de créer votre cluster AKS.

Exemple :

| Nom | Count | Taille de la machine virtuelle | Système d’exploitation |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standard_D2_v2 | Windows |

Étant donné que des machines virtuelles supplémentaires seront déployées dans votre abonnement pendant la migration, vous devez vérifier que vos quotas et vos limites sont définis sur des valeurs suffisantes pour ces ressources. 

Pour plus d’informations, consultez [Azure subscription and service limits (Limites de service et d’abonnement Azure)](https://docs.microsoft.com/azure/azure-subscription-service-limits). Pour vérifier vos quotas actuels, dans le Portail Azure, accédez au [panneau Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), sélectionnez votre abonnement, puis sélectionnez **Utilisation + quotas**.

### <a name="networking"></a>Mise en réseau

Pour les applications complexes, la migration s’effectue généralement en plusieurs fois, et non en une fois. Cela signifie donc que l’ancien environnement et le nouvel environnement auront besoin de communiquer par le biais du réseau. Les applications qui utilisaient les services `ClusterIP` pour communiquer devront peut-être être exposées en tant que type `LoadBalancer` et sécurisées de manière adéquate.

Pour effectuer la migration, vous devez rediriger les clients vers les nouveaux services qui exécutent AKS. Nous vous recommandons de rediriger le trafic en mettant à jour le DNS pour qu’il pointe vers l’équilibreur de charge se trouvant devant votre cluster AKS.

### <a name="stateless-applications"></a>Applications sans état

La migration des applications sans état est le cas de migration le plus simple. Vous appliquez vos définitions YAML au nouveau cluster, vous vérifiez que tout fonctionne comme prévu et vous redirigez le trafic pour activer votre nouveau cluster.

### <a name="stateful-applications"></a>Applications avec état

Planifiez soigneusement la migration des applications avec afin d’éviter la perte de données ou un temps d’arrêt imprévu.

#### <a name="highly-available-applications"></a>Applications hautement disponibles

Vous pouvez déployer des applications avec état dans une configuration haute disponibilité. Ces applications peuvent copier des données entre les réplicas. Si vous utilisez actuellement ce type de déploiement, vous pourrez peut-être créer un membre sur le nouveau cluster AKS et ensuite procéder à une migration avec un impact minimal sur les appelants en aval. En général, les étapes de migration de ce scénario sont les suivantes :

1. Créer un réplica secondaire sur AKS.
2. Attendre la réplication des données.
3. Faire basculer le réplica secondaire pour qu’il devienne le réplica principal.
4. Diriger le trafic vers le cluster AKS.

#### <a name="migrating-persistent-volumes"></a>Migration des volumes persistants

Si vous migrez des volumes persistants existants vers AKS, vous suivez généralement les étapes ci-après :

1. Suspendre l’écriture dans l’application. (Cette étape est facultative et nécessite un temps d’arrêt.)
2. Prendre des instantanés des disques.
3. Créer des disques managés à partir d’instantanés.
4. Créer des volumes persistants dans AKS.
5. Modifier les spécifications du pod pour qu’il [utilise les volumes existants](https://docs.microsoft.com/azure/aks/azure-disk-volume) plutôt que PersistentVolumeClaims (provisionnement statique).
6. Déployer l’application vers AKS.
7. Procéder à la validation.
8. Diriger le trafic vers le cluster AKS.

> [!IMPORTANT]
> Si vous ne souhaitez pas suspendre les écritures, vous devez répliquer les données vers le nouveau déploiement. Dans le cas contraire, vous risquez de manquer les données écrites après que vous avez pris les instantanés de disque.

Des outils open source vous aident à créer des disques managés et à migrer des volumes d’un cluster Kubernetes à l’autre :

* L’[extension Azure CLI Disk Copy](https://github.com/noelbundick/azure-cli-disk-copy-extension) copie et convertit des disques dans les groupes de ressources et les régions Azure.
* L’[extension Azure Kube CLI](https://github.com/yaron2/azure-kube-cli) énumère les volumes Kubernetes ACS et effectue leur migration vers un cluster AKS.

#### <a name="azure-files"></a>Azure Files

Contrairement aux disques, Azure Files peut être monté sur plusieurs hôtes en même temps. Dans votre cluster AKS, Azure et Kubernetes ne vous empêchent pas de créer un pod toujours utilisé par votre cluster ACS. Pour éviter une perte de données et un comportement inattendu, vérifiez que les clusters n’écrivent pas dans les mêmes fichiers en même temps.

Si votre application peut héberger plusieurs réplicas pointant vers le même partage de fichiers, suivez les étapes de migration sans état et déployez vos définitions YAML sur votre nouveau cluster. Si ce n’est pas le cas, essayez la méthode suivante :

1. Déployer votre application sur AKS avec un nombre de réplicas égal à 0.
2. Effectuer une mise à l’échelle de l’application ACS vers la valeur 0. (Cette étape requiert un temps d’arrêt.)
3. Effectuer une mise à l’échelle de l’application AKS vers la valeur 1.
4. Procéder à la validation.
5. Diriger le trafic vers le cluster AKS.

Si vous souhaitez commencer avec un partage vide et copier les données sources, vous pouvez utiliser les commandes [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) pour migrer vos données.

### <a name="deployment-strategy"></a>Stratégie de déploiement

Nous vous recommandons d’utiliser votre pipeline CI/CD existant pour déployer une configuration correcte sur AKS. Clonez vos tâches de déploiement existantes et vérifiez que `kubeconfig` pointe vers le nouveau cluster AKS.

Si ce n’est pas possible, exportez les définitions de ressources à partir d’ACS, puis appliquez-les à AKS. Vous pouvez utiliser `kubectl` pour exporter des objets.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

Plusieurs outils open source peuvent vous être utiles, selon vos besoins de déploiement :

* [Velero](https://github.com/heptio/ark) (Cet outil nécessite Kubernetes 1.7.)
* [Extension Azure Kube CLI](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>Étapes de la migration

1. [Créez un cluster AKS](https://docs.microsoft.com/azure/aks/create-cluster) par le biais du Portail Azure, d’Azure CLI ou d’un modèle Azure Resource Manager.

   > [!NOTE]
   > Recherchez des exemples de modèles Azure Resource Manager pour AKS dans le référentiel [Azure/AKS](https://github.com/Azure/AKS/tree/master/examples/vnet) sur GitHub.

2. Apportez les modifications nécessaires à vos définitions YAML. Par exemple, remplacez `apps/v1beta1` par `apps/v1` pour `Deployments`.

3. [Effectuez la migration de volumes](#migrating-persistent-volumes) (facultatif) de votre cluster ACS vers votre cluster AKS.

4. Utilisez votre système CI/CD pour déployer des applications vers AKS. Ou utilisez kubectl pour appliquer les définitions YAML.

5. Procédez à la validation. Vérifiez que vos applications fonctionnent comme prévu et que toutes les données migrées ont été copiées.

6. Redirigez le trafic. Mettez à jour le DNS pour qu’il pointe les clients vers votre déploiement AKS.

7. Terminez les tâches post-migration. Si vous avez effectué la migration de volumes et choisi de ne pas suspendre l’écriture, copiez ces données vers le nouveau cluster.
