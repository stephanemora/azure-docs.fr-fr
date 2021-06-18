---
title: Déployer un cluster managé Service Fabric sur plusieurs zones de disponibilité
description: Apprenez à déployer un cluster managé Service Fabric sur plusieurs zones de disponibilité et à le configurer dans un modèle ARM.
ms.topic: how-to
ms.date: 5/10/2021
ms.openlocfilehash: 5278ea170e0a60907813b9a79b151dde44ab4a12
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111956784"
---
# <a name="deploy-a-service-fabric-managed-cluster-across-availability-zones"></a>Déployer un cluster managé Service Fabric sur plusieurs zones de disponibilité

Les zones de disponibilité dans Azure constituent une offre à haute disponibilité qui protège vos applications et données contre les pannes des centres de données. Une zone de disponibilité est un emplacement physique unique équipé d’une alimentation, d’un refroidissement et d’une mise en réseau indépendants dans une région Azure.

Un cluster managé Service Fabric prend en charge les déploiements qui s’étendent sur plusieurs zones de disponibilité pour assurer la résilience des zones. Cette configuration garantit la haute disponibilité des services système critiques et de vos applications afin de les protéger contre les points de défaillance uniques. Les zones de disponibilité Azure sont disponibles uniquement dans les régions sélectionnées. Pour plus d’informations, consultez [Vue d’ensemble des zones de disponibilité Azure](../availability-zones/az-overview.md).

>[!NOTE]
>La répartition sur plusieurs zones de disponibilité est disponible uniquement sur les clusters de niveau tarifaire Standard.

Des exemples de modèles sont disponibles : [Modèle entre zones de disponibilité Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommendations-for-zone-resilient-azure-service-fabric-managed-clusters"></a>Recommandations pour les clusters managés Service Fabric résilient aux zones
Un cluster Service Fabric distribué entre des zones de disponibilité garantit la haute disponibilité de l’état du cluster. 

La topologie recommandée pour un cluster managé nécessite les ressources décrites ci-dessous :

* Le niveau tarifaire du cluster doit être Standard.
* Le type de nœud principal doit avoir au moins neuf nœuds pour une meilleure résilience, mais prend en charge un nombre minimum de six.
* Les types de nœuds secondaires doivent avoir au moins six nœuds pour une meilleure résilience, mais prend en charge un nombre minimum de trois.

>[!NOTE]
>Seuls les déploiements sur trois zones de disponibilité sont pris en charge.

>[!NOTE]
> Il n’est pas possible de modifier sur place un cluster managé sur une zone en cluster managé réparti sur plusieurs zones.

Diagramme illustrant l’architecture des zones de disponibilité Azure Service Fabric ![Architecture des zones de disponibilité Azure Service Fabric][sf-multi-az-arch]

Exemple de liste de nœuds illustrant les formats FD/UD dans les zones étendues d’un groupe de machines virtuelles identiques

 ![Exemple de liste de nœuds illustrant les formats FD/UD dans les zones étendues d’un groupe de machines virtuelles identiques.][sfmc-multi-az-nodes]

**Distribution des réplicas de service entre les zones** : Quand un service est déployé sur les nodeTypes, qui sont des zones étendues, des réplicas sont placés pour s’assurer qu’ils se trouvent dans des zones distinctes. Cette séparation est assurée, car le domaine d’erreur sur les nœuds présents dans chacun de ces nodeTypes est configuré avec les informations de zone (p. ex. : FD = fd:/zone1/1, etc.). Par exemple : pour 5 réplicas ou instances d’un service, la distribution sera 2-2-1 et le runtime essaiera de garantir une distribution égale entre les zones de disponibilité.

**Configuration du réplica de service utilisateur** : Les services d’utilisateur avec état déployés sur la zone de disponibilité croisée nodeTypes doivent être configurés avec cette configuration : nombre de réplicas avec cible = 9, min = 5. Cette configuration permet au service de fonctionner même lorsqu’une zone tombe en panne, car 6 réplicas sont toujours dans les deux autres zones. Une mise à niveau d’une application passera également dans un tel scénario.

**Scénario de zone défaillante** : Lorsqu’une zone est défaillante, tous les nœuds de cette zone apparaissent comme étant en panne. Les réplicas de service sur ces nœuds sont également indisponibles. Étant donné qu’il y a des réplicas dans les autres zones, le service continue à être réactif avec les réplicas principaux qui basculent vers les zones qui fonctionnent. Les services s’affichent dans un état d’avertissement, car le nombre de réplicas cibles n’est pas atteint et le nombre de machines virtuelles est toujours supérieur à la taille minimale de réplica cible définie. En conséquence, un équilibreur de charge Service Fabric affichera les réplicas dans les zones de travail pour qu’elles correspondent au nombre de réplicas cibles configuré. À ce stade, les services apparaissent intègres. Lorsque la zone qui était hors service se remet en marche, l’équilibreur de charge répartit à nouveau tous les réplicas de service de manière égale sur toutes les zones.

## <a name="networking-configuration"></a>Configuration de la mise en réseau
Pour plus d’informations, consultez [Configuration des paramètres réseau pour les clusters managés Service Fabric](./how-to-managed-cluster-networking.md).

## <a name="enabling-a-zone-resilient-azure-service-fabric-managed-cluster"></a>Activation d’un cluster managé Azure Service Fabric résilient aux zones
Pour activer un cluster managé Azure Service Fabric résilient aux zones, vous devez inclure ce qui suit dans la définition de ressource de cluster managé.

* La propriété **ZonalResiliency**, qui spécifie si le cluster est résilient aux zones ou non.

```json
{
    "apiVersion": "2021-05-01",
    "type": "Microsoft.ServiceFabric/managedclusters",
    "ZonalResiliency": "true"
    
}
```
[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-multi-az-arch]: ./media/service-fabric-cross-availability-zones/sf-multi-az-topology.png
[sfmc-multi-az-nodes]: ./media/how-to-managed-cluster-availability-zones/sfmc-multi-az-nodes.png