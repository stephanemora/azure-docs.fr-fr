---
title: Meilleures pratiques en matière de continuité et de reprise d'activité AKS
description: Découvrez les bonnes pratiques d’un opérateur de cluster pour optimiser le temps de fonctionnement des applications, en offrant une haute disponibilité et en étant prêt à faire face aux situations de récupération d’urgence dans AKS (Azure Kubernetes Service).
services: container-service
author: lastcoolnameleft
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: 5bcd30c22132bc53ff28fdefcb73f686e08e34ea
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105083"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Bonnes pratiques pour la continuité d’activité et la reprise d’activité dans AKS (Azure Kubernetes Services)

Quand vous gérez des clusters dans AKS (Azure Kubernetes Service), le temps de fonctionnement des applications s’avère important. Par défaut, AKS fournit une haute disponibilité en utilisant différents nœuds au sein d'un [groupe de machines virtuelles identiques](../virtual-machine-scale-sets/overview.md). Mais ces nœuds multiples ne protègent pas votre système contre une défaillance régionale. Pour optimiser votre temps de fonctionnement, soyez prévoyant pour assurer la continuité de l’activité et faire face à une situation de récupération d’urgence.

Cet article porte essentiellement sur la planification de la continuité d’activité et la récupération d’urgence dans AKS. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Planifier des clusters AKS dans plusieurs régions.
> * Router le trafic sur plusieurs clusters avec Azure Traffic Manager.
> * Utiliser la géoréplication pour les registres d’images conteneur.
> * Planifier l’état de l’application sur plusieurs clusters.
> * Répliquer le stockage dans plusieurs régions.

## <a name="plan-for-multiregion-deployment"></a>Planifier le déploiement multirégion

> **Bonne pratique**
>
> Lorsque vous déployez plusieurs clusters AKS, choisissez des régions où AKS est disponible. Utilisez des régions jumelées.

Un cluster AKS est déployé dans une seule région. Pour protéger votre système contre la défaillance d’une région, déployez votre application sur plusieurs clusters AKS dans différentes régions. Pour savoir à quel emplacement déployer votre cluster AKS, tenez compte des points suivants :

* [**Disponibilité des régions AKS**](./quotas-skus-regions.md#region-availability)
    * Choisissez des régions proches de vos utilisateurs. 
    * AKS s’étend en permanence à de nouvelles régions.
* [**Régions appairées Azure**](../best-practices-availability-paired-regions.md)
    * Pour votre zone géographique, choisissez deux régions appairées.
    * Les régions appairées coordonnent les mises à jour de la plateforme et hiérarchisent les efforts de récupération si nécessaire.
* **Disponibilité du service**
    * Décidez si vos régions appairées doivent avoir un niveau de disponibilité chaud/chaud, chaud/tiède ou chaud/froid.
    * Souhaitez-vous utiliser les deux régions en même temps, avec une région *prête* à commencer à traiter le trafic ? Ou,
    * Souhaitez-vous donner à une région le temps de se préparer à traiter le trafic ?

La disponibilité des régions AKS et les régions appairées sont des questions qui vont de pair. Déployez vos clusters AKS sur des régions appairées conçues pour gérer ensemble la récupération d'urgence. Par exemple, AKS est disponible dans les régions USA Est et USA Ouest. Ces régions sont appairées. Choisissez ces deux régions si vous créez une stratégie AKS de continuité d’activité/récupération d’urgence (BC/DR).

Quand vous déployez votre application, ajoutez une autre étape à votre pipeline d’intégration continue/déploiement continu (CI/CD) pour effectuer le déploiement sur ces divers clusters AKS. La mise à jour de vos pipelines empêche le déploiement des applications dans une région et un cluster AKS particuliers. Dans ce cas, le trafic client dirigé vers une région secondaire ne recevra pas les dernières mises à jour de code.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Utiliser Azure Traffic Manager pour router le trafic

> **Bonne pratique**
>
> Azure Traffic Manager peut vous diriger vers votre cluster AKS et votre instance d'application les plus proches. Pour optimiser les performances et la redondance, faites transiter l’ensemble du trafic d’application par Traffic Manager avant qu’il parvienne à votre cluster AKS.

Si vous disposez de plusieurs clusters AKS dans différentes régions, utilisez Traffic Manager pour contrôler le flux de trafic vers les applications exécutées dans chaque cluster. [Azure Traffic Manager](../traffic-manager/index.yml) est un équilibreur de charge de trafic DNS qui peut répartir le trafic réseau entre les régions. Utilisez Traffic Manager pour router les utilisateurs selon le temps de réponse des clusters ou en fonction de critères géographiques.

![AKS avec Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Si vous disposez d'un seul cluster AKS, vous vous connectez généralement à l'adresse IP du service ou au nom DNS d'une application donnée. Dans un déploiement impliquant plusieurs clusters, vous devez vous connecter à un nom DNS Traffic Manager qui pointe vers les services de chaque cluster AKS. Définissez ces services en utilisant des points de terminaison Traffic Manager. Chaque point de terminaison est l’*adresse IP d’équilibreur de charge de service*. Cette configuration vous permet de diriger le trafic réseau du point de terminaison Traffic Manager d’une région vers le point de terminaison d’une autre région.

![Routage géographique via Traffic Manager](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Traffic Manager effectue des recherches DNS et renvoie votre point de terminaison le plus approprié. Les profils imbriqués peuvent donner la priorité à un emplacement principal. Par exemple, vous devez vous connecter à sa région géographique la plus proche. Si cette région rencontre un problème, Traffic Manager vous dirige vers une région secondaire. Cette approche garantit que vous pouvez vous connecter à une instance d'application, même si votre région géographique la plus proche n'est pas disponible.

Pour savoir comment configurer des points de terminaison et le routage, consultez [Configurer la méthode de routage du trafic géographique à l’aide de Traffic Manager](../traffic-manager/traffic-manager-configure-geographic-routing-method.md).

### <a name="application-routing-with-azure-front-door-service"></a>Routage d’applications avec Azure Front Door Service

Grâce au protocole anycast basé sur Split TCP, [Azure Front Door Service](../frontdoor/front-door-overview.md) connecte rapidement vos utilisateurs finaux au point de présence (POP) Front Door le plus proche. Autres fonctionnalités d'Azure Front Door Service :
* Arrêt TLS
* Domaine personnalisé
* Pare-feu d’applications web
* Réécriture d’URL
* Affinité de session 

Passez en revue les besoins de trafic de votre application pour comprendre la solution qui est la plus adaptée.

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>Interconnecter des régions avec l’appairage de réseau virtuel global

Connectez les deux réseaux virtuels l'un à l'autre par le biais de l'[appairage de réseaux virtuels](../virtual-network/virtual-network-peering-overview.md) pour permettre la communication entre les clusters. L'appairage de réseaux virtuels interconnecte les réseaux virtuels pour fournir une bande passante élevée à travers le réseau principal de Microsoft, même entre différentes régions géographiques.

Avant d'appairer des réseaux virtuels contenant des clusters AKS en cours d'exécution, utilisez Standard Load Balancer dans votre cluster AKS. Cette condition préalable rend les services Kubernetes accessibles via l'appairage de réseau virtuel.

## <a name="enable-geo-replication-for-container-images"></a>Activer la géoréplication pour les images conteneur

> **Bonne pratique**
> 
> Stockez vos images conteneur dans Azure Container Registry et géorépliquez le registre dans chaque région AKS.

Pour déployer et exécuter vos applications dans AKS, vous avez besoin d’un moyen de stocker et d’extraire les images conteneur. Container Registry s’intègre avec AKS, ce qui lui permet de stocker de manière sécurisée vos images conteneur ou graphiques Helm. Container Registry prend en charge la géoréplication multimaître pour répliquer automatiquement vos images dans les régions Azure à l’échelon mondial. 

Pour améliorer les performances et la disponibilité :
1. Utilisez la géoréplication Container Registry pour créer un registre dans chacune des régions où vous disposez d'un cluster AKS. 
1. Chaque cluster AKS extrait ensuite les images conteneur du registre de conteneurs local dans la même région :

![Géoréplication Container Registry pour les images conteneur](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Quand vous utilisez la géoréplication Container Registry pour extraire les images d’une même région, les résultats sont :

* **Plus rapides** : vous extrayez les images à partir de connexions réseau haut débit et à faible latence dans la même région Azure.
* **Plus fiables** : Si une région n’est pas disponible, votre cluster AKS extrait les image auprès d’un registre de conteneurs disponible.
* **Plus économiques** : pas de frais de sortie de réseau entre les centres de données.

La géoréplication est une fonctionnalité des registres de conteneurs de la référence SKU *Premium*. Pour savoir comment configurer la géoréplication, consultez [Géoréplication dans Container Registry](../container-registry/container-registry-geo-replication.md).

## <a name="remove-service-state-from-inside-containers"></a>Supprimer l’état du service des conteneurs

> **Bonne pratique**
> 
> Évitez de stocker l'état du service à l'intérieur du conteneur. Utilisez plutôt une plateforme Azure en tant que service (PaaS) qui prend en charge la réplication multirégion.

L'*état du service* correspond aux données en mémoire ou sur disque nécessaires au bon fonctionnement du service. L’état comprend des variables membres et des structures de données que le service lit et écrit. Selon l'architecture du service, l'état peut aussi inclure des fichiers ou d'autres ressources stockés sur le disque, par exemple, les fichiers qu’utilise une base de données pour stocker les journaux de données et de transactions.

L'état peut être externalisé ou colocalisé avec le code qui le manipule. En règle générale, l’état est externalisé au moyen d’une base de données ou d’un autre magasin de données qui s’exécute sur différentes machines du réseau ou en dehors du processus sur la même machine.

Les conteneurs et microservices sont plus résilients quand les processus qui s’y exécutent ne conservent pas l’état. Étant donné que les applications contiennent presque toujours un état, utilisez une solution PaaS, telle que :
* Azure Cosmos DB
* Azure Database pour PostgreSQL
* Azure Database pour MySQL
* Azure SQL Database

Pour créer des applications portables, suivez les recommandations suivantes :

* [The 12 -factor app methodology](https://12factor.net/) (méthodologie des applications à 12 facteurs)
* [Exécuter une application web dans plusieurs régions Azure](/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Créer un plan de migration de stockage

> **Bonne pratique**
>
> Si vous utilisez Stockage Azure, préparez et testez la migration de votre stockage de la région principale vers la région de sauvegarde.

Vos applications peuvent utiliser Stockage Azure pour leurs données. Dans ce cas, vos applications sont réparties sur plusieurs clusters AKS de régions différentes. Le stockage doit rester synchronisé. Voici deux méthodes courantes pour répliquer le stockage :

* Réplication asynchrone basée sur l’infrastructure
* Réplication asynchrone basée sur l’application

### <a name="infrastructure-based-asynchronous-replication"></a>Réplication asynchrone basée sur l’infrastructure

Vos applications peuvent nécessiter un stockage persistant même après la suppression d’un pod. Dans Kubernetes, vous pouvez utiliser des volumes persistants pour conserver le stockage de données. Ces volumes persistants sont montés sur une machine virtuelle de nœud, puis exposés aux pods. Les volumes persistants suivent les pods même si ceux-ci sont déplacés vers un autre nœud au sein du même cluster.

La stratégie de réplication que vous utilisez dépend de votre solution de stockage. Les solutions de stockage courantes suivantes fournissent toutes leurs propres recommandations en matière de récupération d'urgence et de réplication.
* [Gluster](https://docs.gluster.org/en/latest/Administrator-Guide/Geo-Replication/)
* [Ceph](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/)
* [Rook](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html)
* [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) 

En général, vous fournissez un point de stockage commun où les applications écrivent leurs données. Ces données sont ensuite répliquées entre les régions et rendues accessibles localement.

![Réplication asynchrone basée sur l’infrastructure](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Si vous utilisez Disques managés Azure, vous pouvez utiliser [Velero sur Azure][velero] et [Kasten][kasten] pour gérer la réplication et la récupération d'urgence. Ces options sont des solutions de sauvegarde natives du système Kubernetes, mais non prises en charge par celui-ci.

### <a name="application-based-asynchronous-replication"></a>Réplication asynchrone basée sur l’application

Kubernetes ne propose actuellement aucune implémentation native pour la réplication asynchrone basée sur l’application. Dans la mesure où les conteneurs et Kubernetes sont faiblement couplés, toute approche traditionnelle basée sur l'application ou le langage doit fonctionner. En règle générale, les applications elles-mêmes répliquent les demandes de stockage, qui sont ensuite écrites dans le stockage de données sous-jacent de chaque cluster.

![Réplication asynchrone basée sur l’application](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Étapes suivantes

Cet article porte essentiellement sur les considérations relatives à la continuité d’activité et à la récupération d’urgence pour les clusters AKS. Pour plus d’informations sur les opérations liées aux clusters dans AKS, consultez ces articles relatifs aux bonnes pratiques :

* [Multilocation et isolation de cluster][aks-best-practices-cluster-isolation]
* [Fonctionnalités de base du planificateur Kubernetes][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md

[velero]: https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md
[kasten]: https://www.kasten.io/