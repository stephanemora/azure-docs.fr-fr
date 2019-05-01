---
title: Clouds privés dans la VMware Solution par CloudSimple - Azure
description: Découvrez les concepts et les clouds privés CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: dc07b4eea553e6cb3d9b522826e860ddbfbc1513
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577044"
---
# <a name="cloudsimple-private-cloud-overview"></a>Vue d’ensemble du cloud privé CloudSimple

CloudSimple transforme et étend les charges de travail VMware à des clouds publics en quelques minutes. À l’aide du service CloudSimple, vous pouvez déployer VMware en mode natif sur l’infrastructure Azure de métal nu. Votre déploiement se trouve sur les emplacements Azure et s’intègre entièrement avec le reste du cloud Azure.

* La solution CloudSimple fournit la continuité opérationnelle complète VMware. Cette solution vous offre les avantages de cloud public de :
  * Élasticité
  * Innovation
  * Rendement
* Avec CloudSimple, vous bénéficiez d’un modèle de consommation cloud permettant de réduire votre coût total de possession. Il offre également la demande approvisionnement, payer en tant que les extensions et optimisation de la capacité.
* CloudSimple est entièrement compatible avec :
  * Outils existants
  * Compétences
  * Processus
* Cette compatibilité permet à vos équipes gérer des charges de travail sur le cloud Azure, sans interrompre vos stratégies :
  * Réseau
  * Sécurité  
  * Protection des données  
  * Audit
* CloudSimple gère l’infrastructure et tous les services réseau et de gestion nécessaires. Le service CloudSimple permet à vous concentrer sur votre équipe :
  * Valeur commerciale
  * Approvisionnement d’application
  * Continuité de l’activité
  * Support
  * Application de stratégies

## <a name="private-cloud-environment-overview"></a>Vue d’ensemble des environnement de cloud privé

Un cloud privé est une pile VMware isolée, tels que ces environnements :

* Hôtes ESXi
* vCenter
* vSAN
* NSX

Clouds privés sont gérés par un serveur vCenter dans son propre domaine de gestion.

La pile s’exécute sur :

* Nœuds dédiés
* Nœuds de matériel nu isolé

Les utilisateurs consomment la pile via les outils natifs de VMware, y compris :

* vCenter
* NSX Manager

Vous pouvez déployer des nœuds dédiés dans des emplacements Azure. Puis vous pouvez les gérer avec Azure et CloudSimple. Un cloud privé se compose d’un ou plusieurs clusters vSphere, et chaque cluster contient 3 et 16 nœuds.

Vous pouvez créer un cloud privé à l’aide d’acheté des nœuds :

* Nœuds de paiement à l’utilisation
* Nœuds réservés et dédiés

Vous pouvez vous connecter au cloud privé à votre environnement local et le réseau Azure utilise les connexions suivantes :

* Sécuriser
* Privé VPN
* Azure ExpressRoute

L’environnement de cloud privé est conçu pour éviter d’avoir un point de défaillance unique :

* ESXi clusters sont configurés avec une haute disponibilité de vSphere et sont dimensionnés pour avoir au moins un nœud de secours pour assurer la résilience.
* vSAN fournit le stockage principal redondant. vSan nécessite au moins trois nœuds pour fournir une protection contre une défaillance unique. Vous pouvez configurer le réseau vSAN pour fournir une résilience accrue pour les clusters de grande taille.
* Vous pouvez configurer vCenter PSC, NSX Manager machines virtuelles et avec la stratégie de stockage RAID 10 pour protéger contre les défaillances de stockage. Ensuite, qu’elles sont protégées par vSphere HA contre les défaillances de nœud et le réseau.

## <a name="scenarios-for-deploying-a-private-cloud"></a>Scénarios de déploiement d’un cloud privé

* **Mise hors service du centre de données ou la migration**

  * Obtenez une capacité supplémentaire lorsque vous atteignez les limites de votre centre de données ou actualisez le matériel.
  * Ajouter la capacité nécessaire dans le cloud et d’éliminer les soucis liés à la gestion des actualisations de matériel.
  * Réduire les risques et le coût des migrations de cloud, par rapport à des conversions du temps ou de reconcevoir l’architecture.
  * Utiliser des outils familiers de VMware et les compétences pour accélérer les migrations de cloud. Dans le cloud, utilisez les services Azure à moderniser vos applications à votre rythme.

* **Développez à la demande**

  * Développez pour le cloud pour répondre aux besoins imprévues, telles que les nouveaux environnements de développement ou les pics saisonniers capacité.
  * Créer une nouvelle capacité à la demande et conservez-la tant que vous en avez besoin.
  * Réduisez votre investissement initial, accélèrent la vitesse de l’approvisionnement et réduire la complexité avec la même architecture et des stratégies sur en local et le cloud.

* **Récupération d’urgence et les bureaux virtuels dans le cloud Azure**

  * Établir l’accès à distance aux données, des applications et des postes de travail dans le cloud Azure. Les connexions à bande passante élevée, vous charger / télécharger des données rapides à récupérer après des incidents. Donnez des réseaux à latence faible rapide de réponse fois les utilisateurs s’attendent à partir d’une application de bureau.

  * Répliquer toutes vos stratégies et mise en réseau dans le cloud à l’aide de la CloudSimple portail et les outils VMware. Cette réplication permet de réduire les efforts et le risque de créer et gérer des implémentations de récupération d’urgence et l’infrastructure VDI.

* **Bases de données et des applications hautes performances**

  * Exécutez vos charges de travail les plus exigeantes, avec l’architecture de hyperconvergé fournie par CloudSimple.
  * Exécutez Oracle, Microsoft SQL server, systèmes d’intergiciel (middleware) et les bases de données NoSQL hautes performances.

  * Découvrez le cloud en tant que votre propre centre de données avec des connexions de réseau à haut débit 25 Gbits/s. Connexions à haut débit vous permet d’exécuter des applications hybrides qui couvrent en local, VMware sur Azure, activer et privés charges de travail Azure, sans compromettre les performances.

* **True hybride**

  * Unifier DevOps sur VMware et des services Azure.
  * Optimiser l’administration de VMware pour les solutions qui peuvent être appliquées sur toutes vos charges de travail et des services Azure.
  * Accéder aux services de cloud public sans avoir à développer votre centre de données ou réorganisez vos applications.
  * Centraliser les identités, les stratégies de contrôle d’accès, journalisation et de surveillance pour les applications de VMware sur Azure.

## <a name="limits"></a>limites

Tableau ci-dessous indique les limites de nœud sur les ressources d’un cloud privé.

| Ressource | Limite |
|----------|-------|
| Nombre minimal de nœuds pour créer un cloud privé | 3 |
| Nombre maximal de nœuds dans un cluster sur un cloud privé | 16 |
| Nombre maximal de nœuds dans un cloud privé | 64 |
| Nombre minimal de nœuds sur un nouveau cluster | 3 |

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer un cloud privé](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Découvrez comment [configurer un environnement de cloud privé](quickstart-create-private-cloud.md)