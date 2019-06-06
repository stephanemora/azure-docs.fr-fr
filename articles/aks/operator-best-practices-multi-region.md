---
title: Haute disponibilité et récupération d’urgence dans Azure Kubernetes Service (ACS)
description: Découvrez les meilleures pratiques de l’opérateur d’un cluster pour atteindre une disponibilité maximale pour vos applications, offrant une haute disponibilité et la préparation de la récupération d’urgence dans Azure Kubernetes Service (ACS).
services: container-service
author: lastcoolnameleft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: lastcoolnameleft
ms.openlocfilehash: 4afc1231e6c9fa49c04c7bf6dfe26ee5eb87cc31
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475171"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Bonnes pratiques pour la continuité d’activité et la reprise d’activité dans AKS (Azure Kubernetes Services)

Quand vous gérez des clusters dans AKS (Azure Kubernetes Service), le temps de fonctionnement des applications s’avère important. AKS offre une haute disponibilité en utilisant plusieurs nœuds dans un groupe à haute disponibilité. Mais ces plusieurs nœuds ne protège pas votre système contre une défaillance de région. Pour optimiser votre temps de fonctionnement, prévoyez à l’avance maintenir la continuité d’activité et le préparer pour la récupération d’urgence.

Cet article se concentre sur la planification de la continuité des activités et récupération d’urgence dans ACS. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Planifier pour les clusters AKS dans plusieurs régions.
> * Router le trafic entre plusieurs clusters à l’aide d’Azure Traffic Manager.
> * Utilisez la géo-réplication pour les registres de votre image de conteneur.
> * Plan de l’état de l’application sur plusieurs clusters.
> * Répliquer le stockage dans plusieurs régions.

## <a name="plan-for-multiregion-deployment"></a>Plan pour le déploiement multirégion

**Bonne pratique** : Lorsque vous déployez plusieurs clusters AKS, choisissez des régions où ACS est disponible et utiliser les régions jumelées.

Un cluster AKS est déployé dans une seule région. Pour protéger votre système de défaillance de région, déployez votre application dans plusieurs clusters ACS dans différentes régions. Lorsque vous planifiez l’emplacement déployer votre cluster AKS, prenez en compte :

* [**Disponibilité des régions AKS**](https://docs.microsoft.com/azure/aks/quotas-skus-regions#region-availability): Choisissez des régions proches de vos utilisateurs. AKS se développe en permanence dans de nouvelles régions.
* [**Régions jumelées Azure**](https://docs.microsoft.com/azure/best-practices-availability-paired-regions): Pour votre zone géographique, choisissez deux régions appairées l’une à l’autre. Les régions jumelées coordonner les mises à jour de la plateforme et hiérarchiser les efforts de récupération si nécessaire.
* **Disponibilité des services**: Décidez si votre régions jumelées doivent être « à chaud » / « à chaud », à chaud/à chaud ou à chaud/froid. Vous souhaitez exécuter les deux régions en même temps, avec une région *prêt* pour commencer à traiter le trafic ? Ou une région à avoir le temps de se préparer à servir le trafic ?

Disponibilité des régions ACS et les régions jumelées sont une considération mixte. Déployez vos clusters AKS sur des régions appairées qui sont conçues pour gérer ensemble la reprise d’activité. Par exemple, AKS est disponible dans la région est des États-Unis et ouest des États-Unis. Ces régions sont associées. Choisissez ces deux régions lorsque vous créez une stratégie AKS BC/DR.

Lorsque vous déployez votre application, ajoutez une autre étape à votre pipeline CI/CD pour déployer sur ces clusters AKS plusieurs. Si vous ne mettez à jour vos pipelines de déploiement, les applications pourraient être déployées dans une seule de vos régions et les clusters AKS. Le trafic de client qui est dirigé vers une région secondaire ne recevront pas les dernières mises à jour du code.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Utiliser Azure Traffic Manager pour router le trafic

**Bonne pratique** : Azure Traffic Manager peut diriger les clients vers leur instance de cluster et des applications AKS le plus proche. Pour les meilleures performances et la redondance, diriger tout trafic d’application par le biais de Traffic Manager avant de passer à votre cluster AKS.

Si vous avez plusieurs clusters ACS dans différentes régions, utilisez Traffic Manager pour contrôler le flux du trafic pour les applications qui s’exécutent dans chaque cluster. [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) est un équilibreur de charge de trafic DNS qui peut répartir le trafic réseau entre les régions. Utilisez Traffic Manager pour diriger les utilisateurs selon le temps de réponse de cluster ou sur la géographie.

![ACS avec Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Les clients qui ont un seul cluster AKS généralement se connecter au nom du service DNS ou adresse IP d’une application donnée. Dans un déploiement multicluster, les clients doivent se connecter à un nom DNS Traffic Manager qui pointe vers les services sur chaque cluster AKS. Définissez ces services à l’aide de points de terminaison Traffic Manager. Chaque point de terminaison est la *IP équilibreur de charge de service*. Cette configuration permet de diriger le trafic réseau du point de terminaison Traffic Manager dans une région pour le point de terminaison dans une autre région.

![Géographique routage via Traffic Manager](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Traffic Manager effectue des recherches DNS et retourne point de terminaison approprié d’un utilisateur. Profils imbriqués pouvant hiérarchiser un emplacement principal. Par exemple, les utilisateurs doivent se connecter généralement dans leur région géographique le plus proche. Si cette région rencontre un problème, Traffic Manager dirige à la place les utilisateurs vers une région secondaire. Cette approche garantit que les clients peuvent se connecter à une instance d’application même si leur région géographique la plus proche n’est pas disponible.

Pour plus d’informations sur la façon de configurer des points de terminaison et le routage, consultez [configurer la méthode de routage du trafic géographique à l’aide de Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).

### <a name="layer-7-application-routing-with-azure-front-door-service"></a>Routage de couche 7 application avec le Service Azure porte d’entrée

Traffic Manager utilise DNS (couche 3) pour le trafic de forme. [Azure Service porte](https://docs.microsoft.com/azure/frontdoor/front-door-overview) fournit une option de routage HTTP/HTTPS (couche 7). Les fonctionnalités supplémentaires du Service de porte d’entrée Azure comprennent SSL arrêt, domaine personnalisé, pare-feu d’applications web, réécriture d’URL et l’affinité de session. Passez en revue les besoins de trafic de votre application pour comprendre la solution qui est la plus adaptée.

## <a name="enable-geo-replication-for-container-images"></a>Activer la géoréplication pour les images conteneur

**Bonne pratique** : Store vos images de conteneur dans Azure Container Registry et géo-répliquer le Registre pour chaque région AKS.

Pour déployer et exécuter vos applications dans AKS, vous avez besoin d’un moyen de stocker et d’extraire les images conteneur. Container Registry s’intègre avec AKS, donc il peut stocker en toute sécurité vos images de conteneur ou les graphiques Helm. Container Registry prend en charge la géo-réplication multimaître pour répliquer automatiquement vos images dans des régions Azure dans le monde entier. 

Pour améliorer les performances et la disponibilité, utilisez la géo-réplication du Registre de conteneur pour créer un Registre dans chaque région où vous avez un cluster AKS. Chaque cluster AKS extrait ensuite les images de conteneur à partir du Registre de conteneur local dans la même région :

![Conteneur géo-réplication du Registre pour les images de conteneur](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Lorsque vous utilisez la géo-réplication du Registre de conteneur pour extraire des images à partir de la même région, les résultats sont :

* **Plus rapidement**: Vous extrayez des images à partir de connexions de réseau à haut débit et à faible latence dans la même région Azure.
* **Plus fiable**: Si une région n’est pas disponible, votre cluster AKS extrait les images à partir d’un Registre de conteneurs disponibles.
* **Moins cher**: Il n’existe aucun frais de sortie de réseau entre les centres de données.

Géo-réplication est une fonctionnalité de *Premium* registres de conteneurs de référence (SKU). Pour plus d’informations sur la façon de configurer la géo-réplication, consultez [géo-réplication du Registre de conteneur](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).

## <a name="remove-service-state-from-inside-containers"></a>Supprimer l’état du service des conteneurs

**Bonne pratique** : Si possible, ne stockez pas état de service à l’intérieur du conteneur. Au lieu de cela, utilisez une plateforme Azure en tant que service (PaaS) qui prend en charge de la réplication multirégion.

*État du service* fait référence aux données en mémoire ou sur le disque un service nécessaires au bon fonctionnement. L’état comprend des variables membres et des structures de données que le service lit et écrit. Selon la façon dont l’architecture du service, l’état peut également inclure des fichiers ou autres ressources qui sont stockés sur le disque. Par exemple, l’état peut inclure les fichiers de qu'une base de données utilise pour stocker les données et journaux des transactions.

État peut être externalisé ou colocalisé avec le code qui manipule l’état. En règle générale, vous externaliser état à l’aide d’une base de données ou autre magasin de données qui s’exécute sur des ordinateurs différents sur le réseau ou qui s’exécute en dehors du processus sur le même ordinateur.

Conteneurs et microservices sont plus résilients lorsque ne conservent pas les processus qui s’exécutent dans leur état. Étant donné que les applications contiennent presque toujours un état, destiné à une solution PaaS comme Azure Database pour MySQL, base de données Azure PostgreSQL, ou base de données SQL Azure.

Pour générer des applications portables, consultez les instructions suivantes :

* [La méthodologie d’applications 12 facteurs.](https://12factor.net/)
* [Exécuter une application web dans plusieurs régions Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Créer un plan de migration de stockage

**Bonne pratique** : Si vous utilisez le stockage Azure, préparer et tester la migration de votre stockage à partir de la région primaire vers la région de sauvegarde.

Vos applications peuvent utiliser le stockage Azure pour leurs données. Étant donné que vos applications sont réparties sur plusieurs clusters ACS dans différentes régions, vous devez conserver le stockage synchronisé. Voici deux méthodes courantes pour répliquer le stockage :

* Réplication asynchrone basée sur l’infrastructure
* Réplication asynchrone basée sur l’application

### <a name="infrastructure-based-asynchronous-replication"></a>Réplication asynchrone basée sur l’infrastructure

Vos applications peuvent nécessiter un stockage persistant même après la suppression d’un pod. Dans Kubernetes, vous pouvez utiliser des volumes persistants pour conserver le stockage de données. Volumes persistants sont montés sur un machine virtuelle du nœud et ensuite exposées aux pods. Volumes persistants suivent pods même si les pods sont déplacés vers un autre nœud à l’intérieur du même cluster.

La stratégie de réplication que vous utilisez dépend de votre solution de stockage. Les solutions de stockage courantes telles que [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/), [Ceph](http://docs.ceph.com/docs/master/cephfs/disaster-recovery/), [tour](https://rook.io/docs/rook/master/disaster-recovery.html), et [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) fournissent leurs propres des conseils sur la récupération d’urgence et réplication.

La stratégie typique consiste à fournir un point commun de stockage où les applications peuvent écrire leurs données. Ces données sont ensuite répliquées entre les régions, puis rendues accessibles localement.

![Réplication asynchrone basée sur l’infrastructure](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Si vous utilisez Azure Managed Disks, vous pouvez choisir la réplication et les solutions de récupération d’urgence telles que celles-ci :

* [Velero sur Azure](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>Réplication asynchrone basée sur l’application

Kubernetes ne fournit actuellement aucune implémentation native pour la réplication asynchrone basée sur l’application. Étant donné que les conteneurs et Kubernetes sont faiblement couplés, toute approche traditionnelle de l’application ou la langue doit fonctionner. En règle générale, les applications elles-mêmes répliquent les demandes de stockage, qui sont ensuite écrits dans le stockage de données sous-jacent de chaque cluster.

![Réplication asynchrone basée sur l’application](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Étapes suivantes

Cet article se concentre sur la continuité d’activité et les considérations relatives à la récupération d’urgence pour les clusters AKS. Pour plus d’informations sur les opérations de cluster dans ACS, consultez les articles sur les meilleures pratiques :

* [Isolation de l’architecture mutualisée et de cluster][aks-best-practices-cluster-isolation]
* [Fonctionnalités de base du planificateur Kubernetes][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
