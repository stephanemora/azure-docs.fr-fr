---
title: Meilleures pratiques relatives à Azure Kubernetes Service (AKS)
description: Ensemble des meilleures pratiques à destination du développeur et de l’opérateur de clusters pour créer et gérer des applications dans Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/09/2021
ms.openlocfilehash: f4bd109ce6827de26b1649380b6f6a2ba7c10e17
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105899"
---
# <a name="cluster-operator-and-developer-best-practices-to-build-and-manage-applications-on-azure-kubernetes-service-aks"></a>Meilleures pratiques à destination du développeur et de l’opérateur de clusters pour créer et gérer des applications sur Azure Kubernetes Service (AKS)

La création et l’exécution d’applications avec succès dans Azure Kubernetes Service (AKS) requièrent la compréhension et l’implémentation de certaines considérations clés, notamment :
* Fonctionnalités d’architecture mutualisée et de planificateur.
* Sécurité des clusters et des pods.
* La continuité d’activité et la reprise d’activité. 


Le groupe de produits AKS, les équipes d’ingénieurs et les équipes sur le terrain (notamment les global black belts [GBB]) ont contribué aux, rédigé et regroupé les meilleures pratiques et les articles conceptuels suivants. Leur but est d’aider les opérateurs et les développeurs de cluster à comprendre les considérations ci-dessus et à implémenter les fonctionnalités appropriées.


## <a name="cluster-operator-best-practices"></a>Meilleures pratiques pour l’opérateur de cluster

En tant qu’opérateur de cluster, travaillez avec les propriétaires d’applications et les développeurs afin de comprendre leurs besoins. Vous pouvez ensuite utiliser les meilleures pratiques suivantes pour configurer vos clusters AKS selon les besoins.

**Mutualisation**

* [Meilleures pratiques relatives à l’isolation de cluster](operator-best-practices-cluster-isolation.md)
    * Inclut les composants clés d’une architecture mutualisée et l’isolation logique des espaces de noms.
* [Meilleures pratiques relatives aux fonctionnalités basiques du planificateur](operator-best-practices-scheduler.md)
    * Inclut l’utilisation des quotas de ressources et des budgets d’interruption de pod.
* [Meilleures pratiques relatives aux fonctionnalités avancées du planificateur](operator-best-practices-advanced-scheduler.md)
    * Inclut l’utilisation de teints et de tolérances, de sélecteurs de nœud et d’affinité, d’affinité interpod et d’anti-affinité.
* [Meilleures pratiques relatives à l’authentification et l’autorisation](operator-best-practices-identity.md)
    * Inclut l’intégration avec Azure Active Directory, à l’aide du contrôle d’accès en fonction du rôle Kubernetes (Kubernetes RBAC), d’Azure RBAC et des identités de pod.

**Sécurité**

* [Meilleures pratiques relatives à la sécurité et aux mises à niveau du cluster](operator-best-practices-cluster-security.md)
    * Inclut la sécurisation de l’accès au serveur d’API, la limitation de l’accès au conteneur et la gestion des mises à niveau et du redémarrage du nœud.
* [Meilleures pratiques relatives à la gestion et à la sécurité des images de conteneur](operator-best-practices-container-image-management.md)
    * Inclut la sécurisation de l’image et des runtimes ainsi que les générations automatisées sur les mises à jour de l’image de base.
* [Bonnes pratiques relatives à la sécurité de pod](developer-best-practices-pod-security.md)
    * Inclut la sécurisation de l’accès aux ressources, la limitation de l’exposition des informations d’identification et l’utilisation d’identités de pod et de coffres de clés numériques.

**Réseau et stockage**

* [Meilleures pratiques relatives à la connectivité réseau](operator-best-practices-network.md)
    * Inclut des modèles de réseau différents (à l’aide de pare-feu d’applications web et d’entrée (WAF)) et la sécurisation de l’accès SSH au nœud.
* [Meilleures pratiques relatives au stockage et aux sauvegardes](operator-best-practices-storage.md)
    * Inclut le choix de la taille de nœud et du type stockage appropriés, l’approvisionnement dynamique de volumes et les sauvegardes de données.

**Exécution de charges de travail prêtes à l’emploi en entreprise**

* [Meilleures pratiques relatives à la continuité et la reprise d’activité](operator-best-practices-multi-region.md)
    * Inclut l’utilisation de paires de régions, de clusters multiples avec Azure Traffic Manager et la géoréplication d’images de conteneur.

## <a name="developer-best-practices"></a>Meilleures pratiques de développement

En tant que développeur ou propriétaire d’application, vous pouvez simplifier votre expérience de développement et définir vos propres besoins quant aux performances de l’application.

* [Meilleures pratiques pour les développeurs d’application qui gèrent les ressources](developer-best-practices-resource-management.md)
    * Inclut la définition des requêtes et des limites des ressources du pod, la configuration des outils de développement et la recherche de problèmes dans l’application.
* [Bonnes pratiques relatives à la sécurité de pod](developer-best-practices-pod-security.md)
    * Inclut la sécurisation de l’accès aux ressources, la limitation de l’exposition des informations d’identification et l’utilisation d’identités de pod et de coffres de clés numériques.

## <a name="kubernetes--aks-concepts"></a>Concepts Kubernetes/AKS

Pour comprendre certains composants et fonctionnalités de ces meilleures pratiques, vous pouvez également consulter les articles conceptuels suivants qui traitent des clusters dans Azure Kubernetes Service (AKS) :

* [Concepts de base de Kubernetes](concepts-clusters-workloads.md)
* [Accès et identité](concepts-identity.md)
* [Concepts de sécurité](concepts-security.md)
* [Concepts de réseau](concepts-network.md)
* [Options de stockage](concepts-storage.md)
* [Options de scaling](concepts-scale.md)

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez faire vos premiers pas avec AKS, suivez l’un des guides de démarrage rapide pour déployer un cluster Azure Kubernetes Service (AKS) à l’aide d’[Azure CLI](kubernetes-walkthrough.md) ou du [Portail Azure](kubernetes-walkthrough-portal.md).
