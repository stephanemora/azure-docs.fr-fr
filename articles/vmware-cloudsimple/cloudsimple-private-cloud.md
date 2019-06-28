---
title: Clouds privés dans la solution VMware de CloudSimple - Azure
description: Découvrez les clouds privés et concepts CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: dc07b4eea553e6cb3d9b522826e860ddbfbc1513
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64577044"
---
# <a name="cloudsimple-private-cloud-overview"></a>Vue d’ensemble des clouds privés CloudSimple

CloudSimple transforme et étend les charges de travail VMware aux clouds publics en quelques minutes. Le service CloudSimple vous permet de déployer VMware en mode natif sur une infrastructure nue Azure. Votre déploiement réside aux emplacements Azure et s’intègre complètement au reste du cloud Azure.

* La solution CloudSimple assure une continuité opérationnelle VMware complète. Cette solution vous offre les avantages du cloud :
  * Élasticité
  * Innovation
  * Efficacité
* Avec CloudSimple, vous bénéficiez d’un modèle de consommation cloud qui réduit votre coût total de possession. Cette solution offre également des fonctionnalités d’approvisionnement à la demande, de paiement en fonction de votre croissance et d’optimisation de la capacité.
* CloudSimple est totalement compatible avec les éléments suivants :
  * Outils existants
  * Compétences
  * Processus
* Cette compatibilité permet à vos équipes de gérer les charges de travail sur le cloud Azure, sans interrompre vos stratégies :
  * Réseau
  * Sécurité  
  * Protection des données  
  * Audit
* CloudSimple gère l’infrastructure, ainsi que tous les services de mise en réseau et de gestion nécessaires. Le service CloudSimple permet à vos équipes de se concentrer sur les aspects suivants :
  * Valeur métier
  * Approvisionnement d’applications
  * Continuité de l’activité
  * Support
  * Application de stratégies

## <a name="private-cloud-environment-overview"></a>Vue d’ensemble d’un environnement de cloud privé

Un cloud privé est une pile VMware isolée, telle que les environnements suivants :

* Hôtes ESXi
* vCenter
* vSAN
* NSX

Les clouds privés sont gérés par un serveur vCenter dans son propre domaine de gestion.

La pile s’exécute sur les éléments suivants :

* Nœuds dédiés
* Nœuds matériels nus isolés

Les utilisateurs consomment la pile par le biais des outils VMware natifs, tels que :

* vCenter
* NSX Manager

Vous pouvez déployer des nœuds dédiés aux emplacements Azure. Vous pouvez ensuite les gérer avec Azure et CloudSimple. Un cloud privé se compose d’un ou de plusieurs clusters vSphere, qui contiennent chacun entre 3 et 16 nœuds.

Vous pouvez créer un cloud privé à l’aide de nœuds achetés :

* Nœuds avec paiement à l’utilisation
* Nœuds dédiés réservés

Vous pouvez connecter le cloud privé à votre environnement local, et le réseau Azure utilise les connexions suivantes :

* Sécuriser
* VPN privé
* Azure ExpressRoute

L’environnement de cloud privé est conçu pour éliminer tout risque de point de défaillance unique :

* Les clusters ESXi sont configurés avec la haute disponibilité vSphere et sont dimensionnés de manière à disposer d’au moins un nœud de réserve à des fins de résilience.
* vSAN fournit le stockage principal redondant. vSAN nécessite au moins trois nœuds afin d’offrir une protection contre une défaillance unique. Vous pouvez configurer vSAN pour qu’il fournisse une résilience accrue pour les clusters de grande taille.
* Vous pouvez configurer les machines virtuelles vCenter, PSC et NSX Manager avec la stratégie de stockage RAID 10 pour les protéger contre les défaillances de stockage. Ces machines sont également protégées par vSphere HA contre les défaillances de nœud et de réseau.

## <a name="scenarios-for-deploying-a-private-cloud"></a>Scénarios de déploiement d’un cloud privé

* **Mise hors service ou migration des centres de données**

  * Bénéficiez d’une capacité supplémentaire lorsque vous atteignez les limites de votre centre de données existant ou actualisez le matériel.
  * Ajoutez la capacité requise dans le cloud et éliminez les problèmes liés à la gestion des actualisations du matériel.
  * Réduisez les risques et les coûts des migrations vers le cloud, comparativement aux longues tâches de conversion ou de remaniement de l’architecture.
  * Appuyez-vous sur les compétences et outils VMware familiers pour accélérer les migrations vers le cloud. Dans le cloud, modernisez vos applications à votre rythme à l’aide des services Azure.

* **Extension à la demande**

  * Étendez-vous au cloud pour faire face aux imprévus, tels que les nouveaux environnements de développement ou les pics de capacité saisonniers.
  * Créez de la capacité à la demande et ne la conservez que le temps nécessaire.
  * Réduisez votre investissement initial, accélérez la vitesse d’approvisionnement et réduisez la complexité en utilisant la même architecture et les mêmes stratégies en local que dans le cloud.

* **Récupération d’urgence et bureaux virtuels dans le cloud Azure**

  * Établissez un accès à distance aux données, aux applications et aux postes de travail dans le cloud Azure. Grâce aux connexions à large bande passante, vous chargez/téléchargez rapidement des données pour accélérer la récupération en cas d’incident. Les réseaux à faible latence offrent la rapidité de réponse que les utilisateurs attendent d’une application de bureau.

  * Répliquez toutes vos stratégies et mises en réseau dans le cloud à l’aide du portail CloudSimple et des outils VMware familiers. Cette réplication réduit les efforts et les risques liés à la création et à la gestion des implémentations de récupération d’urgence et d’infrastructure VDI (Virtual Desktop Infrastructure).

* **Applications et bases de donnés hautes performances**

  * Exécutez vos charges de travail les plus exigeantes avec l’architecture hyperconvergée fournie par CloudSimple.
  * Exécutez Oracle, Microsoft SQL Server, les systèmes intergiciels et les bases de données NoSQL hautes performances.

  * Testez le cloud comme s’il s’agissait de votre propre centre de données avec des connexions réseau à haut débit de 25 Gbits/s. Les connexions à haut débit vous permettent d’exécuter des applications hybrides qui couvrent les charges de travail locales, les charges de travail VMware sur Azure et les charges de travail privées Azure, sans compromettre les performances.

* **Expérience véritablement hybride**

  * Unifiez les opérations DevOps dans l’ensemble des services VMware et Azure.
  * Optimisez l’administration VMware des solutions et services Azure qui sont applicables à toutes vos charges de travail.
  * Accédez aux services cloud publics sans avoir à étendre votre centre de données ni à remanier l’architecture de vos applications.
  * Centralisez les identités, les stratégies de contrôle d’accès, la journalisation et la surveillance des applications VMware sur Azure.

## <a name="limits"></a>limites

Le tableau ci-après indique les limites de nœud sur les ressources d’un cloud privé.

| Ressource | Limite |
|----------|-------|
| Nombre minimal de nœuds pour la création d’un cloud privé | 3 |
| Nombre maximal de nœuds dans un cluster sur un cloud privé | 16 |
| Nombre maximal de nœuds dans un cloud privé | 64 |
| Nombre minimal de nœuds sur un nouveau cluster | 3 |

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer un cloud privé](https://docs.azure.cloudsimple.com/create-private-cloud/).
* Apprenez à [configurer un environnement de cloud privé](quickstart-create-private-cloud.md).