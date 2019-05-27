---
title: Migrer à partir d’Azure Container Service (ACS) vers Azure Kubernetes Service (AKS)
description: Migrer à partir d’Azure Container Service (ACS) vers Azure Kubernetes Service (AKS).
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: dcee8da943603fb0978caf9992be76347ca197d6
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65977712"
---
# <a name="migrate-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Migrer à partir d’Azure Container Service (ACS) vers Azure Kubernetes Service (AKS)

Cet article vous aide à planifier et exécuter une migration réussie entre Azure Container Service (ACS) avec Kubernetes et Azure Kubernetes Service (AKS). Pour vous aider à prendre des décisions importantes, ce guide décrit en détail les différences entre ACS et AKS et fournit une vue d’ensemble du processus de migration.

## <a name="differences-between-acs-and-aks"></a>Différences entre ACS et AKS

ACS et AKS diffèrent dans certains domaines clés qui affectent la migration. Avant toute migration, vous devez examiner et planifier résoudre les différences suivantes :

* Utilisent des nœuds AKS [des disques gérés](../virtual-machines/windows/managed-disks-overview.md).
    * Disques non gérés doivent être convertis avant de les joindre aux nœuds AKS.
    * Custom `StorageClass` pour les disques Azure doivent être modifiés à partir des objets `unmanaged` à `managed`.
    * N’importe quel `PersistentVolumes` doivent utiliser `kind: Managed`.
* AKS prend en charge [plusieurs pools de nœuds](https://docs.microsoft.com/azure/aks/use-multiple-node-pools) (actuellement en version préliminaire).
* Nœuds basés sur Windows Server est actuellement [Aperçu dans AKS](https://azure.microsoft.com/blog/kubernetes-on-azure/).
* AKS prend en charge un ensemble limité de [régions](https://docs.microsoft.com/azure/aks/quotas-skus-regions).
* AKS est un service managé qui comporte un plan de contrôle Kubernetes hébergé. Vous devrez peut-être modifier vos applications si vous avez modifié précédemment de la configuration de vos principaux de services ACS.

## <a name="differences-between-kubernetes-versions"></a>Différences entre les versions de Kubernetes

Si vous effectuez une migration vers une version plus récente de Kubernetes (par exemple, à partir de 1.7.x à 1.9.x), passez en revue les ressources suivantes pour comprendre quelques modifications à l’API Kubernetes :

* [Migration d’un ThirdPartyResource vers CustomResourceDefinition](https://kubernetes.io/docs/tasks/access-kubernetes-api/migrate-third-party-resource/)
* [Modifications d’API des charges de travail dans les versions 1.8 et 1.9](https://kubernetes.io/docs/reference/workloads-18-19/)

## <a name="migration-considerations"></a>Considérations relatives à la migration

### <a name="agent-pools"></a>Pools d'agents

Bien que AKS gère le plan de contrôle Kubernetes, vous toujours définir la taille et le nombre de nœuds à inclure dans votre nouveau cluster. Par exemple, si vous souhaitez un mappage 1:1 entre ACS et AKS, vous devrez capturer les informations relatives aux nœuds ACS existants. Utilisez ces données lorsque vous créez votre cluster AKS.

Exemple :

| Nom | Nombre | Taille de la machine virtuelle | Système d’exploitation |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standard_D2_v2 | Windows |

Étant donné que des machines virtuelles supplémentaires seront déployées dans votre abonnement pendant la migration, vous devez vérifier que vos quotas et vos limites sont définis sur des valeurs suffisantes pour ces ressources. 

Pour plus d’informations, consultez [abonnement Azure et limites de service](https://docs.microsoft.com/azure/azure-subscription-service-limits). Pour vérifier vos quotas actuels, dans le portail Azure, accédez à la [panneau abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), sélectionnez votre abonnement, puis **utilisation + quotas**.

### <a name="networking"></a>Mise en réseau

Pour les applications complexes, la migration s’effectue généralement en plusieurs fois, et non en une fois. Cela signifie que les anciens et nouveaux environnements devrez communiquer sur le réseau. Les applications qui utilisaient `ClusterIP` services pour communiquer peuvent doivent être exposés en tant que type `LoadBalancer` et être correctement sécurisée.

Pour terminer la migration, vous souhaiterez sans doute pointer les clients vers les nouveaux services qui sont exécutent sur AKS. Nous vous recommandons de vous redirigez le trafic en mettant à jour de DNS pour pointer vers l’équilibreur de charge qui se trouve devant votre cluster AKS.

### <a name="stateless-applications"></a>Applications sans état

La migration des applications sans état est le cas de migration le plus simple. Vous allez appliquer vos définitions de YAML vers le nouveau cluster, assurez-vous que tout fonctionne comme prévu et rediriger le trafic pour activer votre nouveau cluster.

### <a name="stateful-applications"></a>Applications avec état

Planifier soigneusement votre migration d’applications avec état afin d’éviter la perte de données ou temps d’arrêt imprévus.

#### <a name="highly-available-applications"></a>Applications hautement disponibles

Vous pouvez déployer des applications avec état dans une configuration haute disponibilité. Ces applications peuvent copier des données entre les réplicas. Si vous utilisez actuellement ce type de déploiement, vous pourrez peut-être créer un nouveau membre sur le nouveau cluster AKS et ensuite migrer avec un impact minimal sur les appelants en aval. En règle générale, les étapes de migration pour ce scénario sont :

1. Créer un nouveau réplica secondaire sur AKS.
2. Attendez la réplication des données.
3. Échec rendre le réplica secondaire le nouveau réplica principal.
4. Pointer le trafic vers le cluster AKS.

#### <a name="migrating-persistent-volumes"></a>Migration des volumes persistants

Si vous migrez des volumes persistants existants vers AKS, vous allez généralement comme suit :

1. Suspendre écrit à l’application. (Cette étape est facultative et nécessite l’arrêt).
2. Prenez des instantanés des disques.
3. Créer des disques gérés à partir de captures instantanées.
4. Créer des volumes persistants dans ACS.
5. Mettre à jour les spécifications de pod à [utiliser les volumes](https://docs.microsoft.com/azure/aks/azure-disk-volume) plutôt que PersistentVolumeClaims (approvisionnement statique).
6. Déployer l’application sur AKS.
7. Valider.
8. Pointer le trafic vers le cluster AKS.

> [!IMPORTANT]
> Si vous ne souhaitez pas les écritures de mise en veille, vous devez répliquer les données vers le nouveau déploiement. Sinon vous risquez de manquer les données qui a été écrites une fois que vous avez pris les instantanés de disque.

Certains outils open source peuvent vous aider à créer des disques gérés et migrer des volumes entre des clusters Kubernetes :

* [Extension de copie de disque de CLI Azure](https://github.com/noelbundick/azure-cli-disk-copy-extension) copie et convertit les disques entre les régions Azure et de groupes de ressources.
* [Extension Kube CLI Azure](https://github.com/yaron2/azure-kube-cli) énumère les volumes ACS Kubernetes et de les migrer vers un cluster AKS.

#### <a name="azure-files"></a>Azure Files

Contrairement aux disques, Azure Files peut être monté sur plusieurs hôtes en même temps. Dans votre cluster AKS, Azure et Kubernetes ne vous empêcher de créer un pod qui utilise toujours de votre cluster ACS. Pour éviter une perte de données et un comportement inattendu, vérifiez que les clusters ne pas écrire dans les mêmes fichiers en même temps.

Si votre application peut héberger plusieurs réplicas qui pointent vers le même partage de fichiers, suivez les étapes de migration sans état et de déployer vos définitions de YAML vers votre nouveau cluster. Si ce n’est pas le cas, essayez la méthode suivante :

1. Déployer votre application dans AKS avec un nombre de réplicas de 0.
2. Mettre à l’échelle l’application sur ACS à 0. (Cette étape nécessite l’arrêt).
3. Mettre à l’échelle l’application sur AKS jusqu'à 1.
4. Valider.
5. Pointer le trafic vers le cluster AKS.

Si vous souhaitez démarrer avec un partage vide, le rendez une copie de la source de données, vous pouvez utiliser la [ `az storage file copy` ](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) commandes pour migrer vos données.

### <a name="deployment-strategy"></a>Stratégie de déploiement

Nous vous recommandons d’utiliser votre pipeline CI/CD existant pour déployer une configuration correcte connue sur AKS. Cloner vos tâches de déploiement existant et vous assurer que `kubeconfig` pointe vers le nouveau cluster AKS.

Si tel n’est pas possible, exporter des définitions de ressource à partir d’ACS et les appliquer à AKS. Vous pouvez utiliser `kubectl` pour exporter des objets.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

Plusieurs outils open source peuvent aider à en fonction de vos besoins de déploiement :

* [Velero](https://github.com/heptio/ark) (cet outil nécessite Kubernetes 1.7).
* [Extension Kube CLI Azure](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>Étapes de la migration

1. [Créer un cluster AKS](https://docs.microsoft.com/azure/aks/create-cluster) via le portail Azure, Azure CLI ou modèle Azure Resource Manager.

   > [!NOTE]
   > Rechercher des exemples de modèles Azure Resource Manager pour AKS à le [Azure/AKS](https://github.com/Azure/AKS/tree/master/examples/vnet) référentiel sur GitHub.

2. Apportez les modifications nécessaires à vos définitions de YAML. Par exemple, remplacez `apps/v1beta1` avec `apps/v1` pour `Deployments`.

3. [Migrer les volumes](#migrating-persistent-volumes) (facultatif) à partir de votre cluster ACS à votre cluster AKS.

4. Utilisez votre système CI/CD pour déployer des applications sur AKS. Ou utilisez kubectl pour appliquer les définitions YAML.

5. Valider. Assurez-vous que vos applications fonctionnent comme prévu et que toutes les données migrées a été copiées.

6. Rediriger le trafic. Mettez à jour le DNS pour qu’il pointe les clients vers votre déploiement AKS.

7. Terminer les tâches de post-migration. Si vous migré des volumes et choisissez de ne pas suspendre écritures, copiez ces données vers le nouveau cluster.
