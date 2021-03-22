---
title: Clusters managés Service Fabric (préversion)
description: Les clusters managés Service Fabric sont une évolution du modèle de ressource de cluster Azure Service Fabric qui simplifie le déploiement et la gestion de cluster.
ms.topic: overview
ms.date: 02/15/2021
ms.openlocfilehash: 271852214097ee96ba6b10de7a94904981cd8ef8
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041227"
---
# <a name="service-fabric-managed-clusters-preview"></a>Clusters managés Service Fabric (préversion)

Les clusters gérés Service Fabric sont une évolution du modèle de ressource de cluster Azure Service Fabric qui simplifie votre expérience de déploiement et de gestion de cluster.

Le modèle Azure Resource Model (ARM) pour les clusters Service Fabric traditionnels vous oblige à définir une ressource de cluster avec un certain nombre de ressources de support, qui doivent toutes être « câblées » correctement (lors du déploiement et tout au long du cycle de vie du cluster) afin que le cluster et vos services fonctionnent correctement. En revanche, le modèle d’encapsulation pour clusters managés Service Fabric est constitué d’une seule ressource de *cluster managé Service Fabric*. Toutes les ressources sous-jacentes pour le cluster sont extraites et managées par Azure en votre nom.

**Modèle de cluster traditionnel Service Fabric**
![Modèle de cluster traditionnel Service Fabric][sf-composition]

**Modèle de cluster managé Service Fabric**
![Modèle de cluster managé Service Fabric][sf-encapsulation]

En termes de taille et de complexité, le modèle ARM pour un cluster managé Service Fabric est d’environ 100 lignes au format JSON, par opposition à quelques lignes 1 000 requises pour définir un cluster Service Fabric classique :

| Ressources Service Fabric | Ressources de cluster managé Service Fabric |
|----------|-----------|
| Cluster Service Fabric | Cluster managé Service Fabric |
| Groupe(s) de machines virtuelles identiques | |
| Équilibrage de charge | |
| Adresse IP publique | |
| Compte(s) de stockage | |
| Réseau virtuel | |

Les clusters managés Service Fabric offrent un certain nombre d’avantages par rapport aux clusters traditionnels :

**Déploiement et gestion simplifiés des clusters**
- Déployer et gérer une seule ressource Azure
- Gestion et autorotation des certificats
- Opérations de mise à l’échelle simplifiées

**Empêcher les erreurs opérationnelles**
- Empêcher les incompatibilités de configuration avec les ressources sous-jacentes
- Bloquer les opérations non sécurisées (telles que la suppression d’un nœud seed)

**Meilleures pratiques par défaut**
- Paramètres de fiabilité et de durabilité simplifiés

Il n’y a aucun coût supplémentaire pour les clusters managés Service Fabric au-delà du coût des ressources sous-jacentes requises pour le cluster.

## <a name="service-fabric-managed-cluster-skus"></a>SKU de cluster managé Service Fabric

Les clusters managés Service Fabric sont disponibles la fois dans les SKU de base et standard.

| Fonctionnalité | De base | standard |
| ------- | ----- | -------- |
| Ressource réseau (SKU pour [Load Balancer](../load-balancer/skus.md), [adresse IP publique](../virtual-network/public-ip-addresses.md)) | De base | standard |
| Nombre minimal de nœuds (instance de machine virtuelle) | 3 | 5 |
| Nombre maximal de nœuds par type de nœud | 100 | 100 |
| Nombre maximal de types de nœuds | 1 | 20 |
| Ajouter/supprimer des types de nœuds | Non | Oui |
| Redondance de zone | Non | Oui |

## <a name="whats-new-for-service-fabric-managed-clusters"></a>Nouveautés des clusters managés Service Fabric

Les dernières fonctionnalités de la préversion des clusters managés Service Fabric incluent la prise en charge des éléments suivants :

* [Déploiement d’applications à l’aide de modèles ARM](how-to-managed-cluster-app-deployment-template.md)
* [Mises à niveau automatiques des systèmes d’exploitation](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades)
* [Chiffrement de disque](how-to-enable-managed-cluster-disk-encryption.md)
* [Application de règles NSG](how-to-managed-cluster-networking.md)

Les fonctionnalités qui seront ajoutées dans les prochaines versions comprennent :

* Déploiement d’applications à l’aide de Visual Studio
* Prise en charge des identités managées
* Zones de disponibilité
* Proxy inversé
* Mise à l’échelle automatique

## <a name="next-steps"></a>Étapes suivantes

Pour commencer à utiliser des clusters managés Service Fabric, essayez le démarrage rapide :

> [!div class="nextstepaction"]
> [Créer un cluster managé Service Fabric (préversion)](quickstart-managed-cluster-template.md)


[sf-composition]: ./media/overview-managed-cluster/sfrp-composition-resource.png
[sf-encapsulation]: ./media/overview-managed-cluster/sfrp-encapsulated-resource.png