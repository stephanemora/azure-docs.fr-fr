---
title: 'Bonnes pratiques pour les opérateurs : haute disponibilité et reprise d’activité dans AKS (Azure Kubernetes Services)'
description: Découvrez les bonnes pratiques destinées aux opérateurs de cluster pour optimiser le temps de fonctionnement des applications afin de fournir une haute disponibilité et de se préparer aux situations de reprise d’activité dans AKS (Azure Kubernetes Services)
services: container-service
author: lastcoolnameleft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: lastcoolnameleft
ms.openlocfilehash: 926f470b8a4dbdb6d6cbfe09ee61349a819600e7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58098625"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Bonnes pratiques pour la continuité d’activité et la reprise d’activité dans AKS (Azure Kubernetes Services)

Quand vous gérez des clusters dans AKS (Azure Kubernetes Service), le temps de fonctionnement des applications s’avère important. AKS offre une haute disponibilité en utilisant plusieurs nœuds dans un groupe à haute disponibilité. Ces nœuds multiples ne vous protègent pas contre la défaillance d’une région. Pour optimiser le temps de fonctionnement, implémentez certaines fonctionnalités de continuité d’activité et de reprise d’activité.

Cet article met l’accent sur certains points qui facilitent la planification de la continuité d’activité et de la reprise d’activité dans AKS. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Planifier des clusters AKS dans plusieurs régions
> * Router le trafic entre plusieurs clusters avec Azure Traffic Manager
> * Utiliser la géoréplication pour les registres d’images conteneur
> * Planifier l’état de l’application sur plusieurs clusters
> * Répliquer le stockage dans plusieurs régions

## <a name="plan-for-multi-region-deployment"></a>Planifier le déploiement multi-région

**Bonne pratique** : quand vous déployez plusieurs clusters AKS, choisissez des régions où AKS est disponible et utilisez des régions appairées.

Un cluster AKS est déployé dans une seule région. Pour vous protéger contre la défaillance d’une région, déployez votre application sur plusieurs clusters AKS dans différentes régions. Quand vous planifiez les régions sur lesquelles déployer votre cluster AKS, tenez compte des points suivants :

* [Disponibilité des régions AKS](https://docs.microsoft.com/azure/aks/container-service-quotas#region-availability)
  * Choisissez des régions proches de vos utilisateurs. AKS s’étend en permanence à de nouvelles régions.
* [Régions appairées Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
  * Pour votre zone géographique, choisissez deux régions appairées l’une à l’autre. Ces régions coordonnent les mises à jour de la plateforme et hiérarchisent les efforts de récupération si nécessaire.
* Niveau de disponibilité des services (chaud/chaud, chaud/tiède, chaud/froid)
  * Souhaitez-vous utiliser les deux régions en même temps, avec une région *prête* à commencer à assurer le trafic ou une région qui a besoin de temps pour être prête à effectuer cette tâche ?

La disponibilité des régions AKS et les régions appairées sont des considérations qui vont de pair. Déployez vos clusters AKS sur des régions appairées qui sont conçues pour gérer ensemble la reprise d’activité. Par exemple, AKS est disponible dans les régions *USA Est* et *USA Ouest*. Ces régions sont également appairées. Ces deux régions seraient recommandées dans le cadre de la création d’une stratégie AKS de continuité d’activité et de reprise d’activité.

Quand vous déployez votre application, vous devez également ajouter une autre étape à votre pipeline CI/CD pour effectuer le déploiement sur ces clusters AKS multiples. Si vous ne mettez pas à jour vos pipelines de déploiement, les déploiements d’applications risquent de ne s’effectuer que sur une seule région et un seul cluster AKS. Le trafic des clients dirigé vers une région secondaire ne recevrait pas les dernières mises à jour de code.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Utiliser Azure Traffic Manager pour router le trafic

**Bonne pratique** : Azure Traffic Manager peut diriger les clients vers leurs cluster AKS et instance d’application les plus proches. Pour optimiser les performances et la redondance, dirigez tout le trafic des applications par le biais de Traffic Manager avant qu’il n’atteigne votre cluster AKS.

Avec plusieurs clusters AKS dans différentes régions, vous devez contrôler la façon dont le trafic est dirigé vers les applications qui s’exécutent dans chaque cluster. [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) est un équilibreur de charge de trafic DNS qui peut répartir le trafic réseau entre les régions. Vous pouvez router les utilisateurs selon le temps de réponse de cluster ou en fonction de critères géographiques.

![AKS avec Azure Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Dans le cas d’un seul cluster AKS, les clients se connectent généralement à l’*adresse IP de service* ou au nom DNS d’une application donnée. Dans un déploiement impliquant plusieurs clusters, les clients doivent se connecter à un nom DNS Traffic Manager qui pointe vers les services sur chaque cluster AKS. Ces services sont définis à l’aide de points de terminaison Traffic Manager. Chaque point de terminaison est l’*adresse IP d’équilibreur de charge de service*. Cette configuration vous permet de diriger le trafic réseau du point de terminaison Traffic Manager dans une région vers le point de terminaison dans une autre région.

![Routage géographique avec Azure Traffic Manager](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Traffic Manager permet d’effectuer les recherches DNS et de retourner le point de terminaison le plus approprié pour un utilisateur. Des profils imbriqués peuvent être utilisés, la priorité étant accordée à un emplacement principal. Par exemple, un utilisateur doit se connecter principalement à sa région géographique la plus proche. Si cette région rencontre un problème, Traffic Manager le dirige vers une région secondaire. Ainsi, les clients peuvent toujours se connecter à une instance d’application, même si leur région géographique la plus proche n’est pas disponible.

Pour obtenir des instructions sur la façon de configurer ces points de terminaison et le routage, consultez [Configurer la méthode de routage du trafic géographique à l’aide de Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).

### <a name="layer-7-application-routing-with-azure-front-door"></a>Routage d’application au niveau de la couche 7 avec Azure Front Door

Azure Traffic Manager utilise DNS (couche 3) pour former le trafic. [Porte d’Azure (actuellement en version préliminaire)](https://docs.microsoft.com/azure/frontdoor/front-door-overview) fournit une option de routage HTTP/HTTPS (couche 7). Les fonctionnalités supplémentaires de Front Door incluent l’arrêt SSL, le domaine personnalisé, le pare-feu d’applications web, la réécriture d’URL et l’affinité de session.

Passez en revue les besoins de trafic de votre application pour comprendre la solution qui est la plus adaptée.

## <a name="enable-geo-replication-for-container-images"></a>Activer la géoréplication pour les images conteneur

**Bonne pratique** : stockez vos images conteneur dans Azure Container Registry et géorépliquez le registre dans chaque région AKS.

Pour déployer et exécuter vos applications dans AKS, vous avez besoin d’un moyen de stocker et d’extraire les images conteneur. ACR (Azure Container Registry) peut s’intégrer à AKS pour stocker de manière sécurisée vos images conteneur ou charts Helm. ACR prend en charge la géoréplication multimaître pour répliquer automatiquement vos images dans des régions Azure à l’échelle mondiale. Pour améliorer les performances et la disponibilité, utilisez la géoréplication ACR afin de créer un registre dans chaque région où vous avez un cluster AKS. Chaque cluster AKS extrait ensuite les images conteneur du registre ACR local dans la même région :

![Géoréplication Azure Container Registry pour les images conteneur](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Les avantages de l’utilisation de la géoréplication ACR sont les suivants :

* **L’extraction d’images d’une même région est plus rapide.** Vous extrayez les images à partir de connexions réseau haut débit et à faible latence dans la même région Azure.
* **L’extraction d’images d’une même région est plus fiable.** Si une région n’est pas disponible, votre cluster AKS extrait l’image d’un autre registre ACR qui demeure disponible.
* **L’extraction d’images d’une même région est plus économique.** Il n’existe aucun frais de sortie de réseau entre les centres de données.

La géoréplication est une fonctionnalité disponible pour les registres ACR dans la référence (SKU) *Premium*. Pour obtenir des instructions sur la façon de configurer la réplication, consultez [Géoréplication dans Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).

## <a name="remove-service-state-from-inside-containers"></a>Supprimer l’état du service des conteneurs

**Bonne pratique** : dans la mesure du possible, ne stockez pas l’état du service dans le conteneur. Au lieu de cela, utilisez des services PaaS Azure qui prennent en charge la réplication dans plusieurs régions.

L’état du service correspond aux données en mémoire ou sur disque nécessaires au bon fonctionnement du service. L’état comprend des variables membres et des structures de données que le service lit et écrit. Selon l’architecture du service, l’état peut aussi inclure des fichiers ou d’autres ressources stockés sur le disque, par exemple, les fichiers qu’utiliserait une base de données pour stocker les journaux d’activité des transactions et des données.

L’état peut être externalisé ou colocalisé avec le code qui le manipule. L’externalisation d’état s’effectue généralement à l’aide d’une base de données ou autre magasin de données, qui s’exécute sur différentes machines du réseau ou en dehors du processus sur la même machine.

Les conteneurs et microservices sont plus résilients quand les processus qui s’y exécutent ne conservent pas l’état. Vos applications contenant presque toujours un état, utilisez une solution PaaS telle qu’Azure Database pour MySQL/Postgres ou Azure SQL.

Pour plus d’informations sur la façon de générer des applications qui sont plus portables, consultez les instructions suivants :

* [The Twelve-Factor App Methodology](https://12factor.net/) (Méthodologie des applications à 12 facteurs)
* [Exécuter une application web dans plusieurs régions Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Créer un plan de migration de stockage

**Bonne pratique** : si vous utilisez Stockage Azure, préparez et testez la migration de votre stockage depuis la région principale vers la région de sauvegarde.

Vos applications peuvent utiliser Stockage Azure pour leurs données. Vos applications étant réparties sur plusieurs clusters AKS dans différentes régions, le stockage doit demeurer synchronisé. Voici deux méthodes courantes de réplication de stockage :

* Réplication asynchrone basée sur l’application
* Réplication asynchrone basée sur l’infrastructure

### <a name="infrastructure-based-asynchronous-replication"></a>Réplication asynchrone basée sur l’infrastructure

Vos applications peuvent nécessiter un stockage persistant même après la suppression d’un pod. Dans Kubernetes, vous pouvez utiliser des volumes persistants pour conserver le stockage de données. Ces volumes persistants sont montés sur la machine virtuelle d’un nœud, puis exposés aux pods. Les volumes persistants suivent les pods, même si le pod est déplacé vers un autre nœud au sein du même cluster.

Selon l’utilisation de la solution de stockage, les stratégies de réplication peuvent être différentes. Les solutions de stockage courantes telles que [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/), [CEPH](http://docs.ceph.com/docs/master/cephfs/disaster-recovery/), [Rook](https://rook.io/docs/rook/master/disaster-recovery.html) et [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) ont toutes leurs propres instructions.

L’approche centralisée consiste à utiliser un point de stockage commun permettant aux applications d’écrire leurs données. Ces données sont ensuite répliquées entre les régions, puis rendues accessibles localement.

![Réplication asynchrone basée sur l’infrastructure](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Si vous utilisez Azure Disques managés, les solutions de réplication et de reprise d’activité disponibles incluent l’utilisation d’une des approches suivantes :

* [Ark sur Azure](https://github.com/heptio/ark/blob/master/docs/azure-config.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>Réplication asynchrone basée sur l’application

Il n’existe aucune implémentation propre à Kubernetes pour la réplication asynchrone basée sur l’application. En raison de la nature faiblement couplée des conteneurs et de Kubernetes, toute approche traditionnelle basée sur l’application ou le langage doit fonctionner. L’approche centralisée consiste à ce que les applications elles-mêmes répliquent les demandes de stockage, qui sont ensuite écrites dans le stockage de données sous-jacent de chaque cluster.

![Réplication asynchrone basée sur l’application](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Étapes suivantes

Cet article a mis l’accent sur des points relatifs à la continuité d’activité et à la reprise d’activité dans les clusters AKS. Pour plus d’informations sur les opérations liées aux clusters dans AKS, consultez les bonnes pratiques suivantes :

* [Multilocation et isolation de cluster][aks-best-practices-cluster-isolation]
* [Fonctionnalités de base du planificateur Kubernetes][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
