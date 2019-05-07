---
title: VMware Solution Azure par CloudSimple - composants VMware de cloud privé
description: Décrit comment les composants VMware sont installés sur le cloud privé
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5e6548a5a04e32b374a8a9c29d2ca5f89fd65c78
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160184"
---
# <a name="private-cloud-vmware-components"></a>Composants de VMware de cloud privé

Un cloud privé est une pile VMware isolée (hôtes ESXi vCenter, vSAN et NSX) environnement géré par un serveur vCenter dans un domaine de gestion.  Le service CloudSimple vous permet de déployer VMware en mode natif sur l’infrastructure Azure de métal nu dans les emplacements Azure.  Clouds privés sont intégrées avec le reste du Cloud Azure.  Un cloud privé est déployé avec les composants de pile VMware suivants :

* **VMware ESXi -** hyperviseur sur Azure des nœuds dédiés
* **VMware vCenter -** Appliance pour la gestion centralisée de l’environnement de cloud privé vSphere
* **VMware vSAN -** solution d’infrastructure Hyper-convergée
* **Centre de données VMware NSX -** la virtualisation et les logiciels de sécurité réseau  

## <a name="vmware-component-versions"></a>Versions des composants VMware

Une pile de VMware de cloud privé est déployée avec la version du logiciel suivant.

| Composant | Version | Version sous licence |
|-----------|---------|------------------|
| ESXi | 6.7U1 | Enterprise ainsi que |
| vCenter | 6.7U1 | vCenter Standard |
| vSAN | 6.7 | Entreprise |
| Centre de données NSX | 2.3 | Avancé |

## <a name="esxi"></a>ESXi

VMware ESXi est installé sur les nœuds CloudSimple achetées lorsque vous créez un cloud privé.  ESXi fournit l’hyperviseur pour déployer des machines virtuelles de charge de travail (VM).  Nœuds fournissent une infrastructure Hyper-convergée (de calcul et de stockage) sur votre cloud privé.  Les nœuds sont une partie du cluster vSphere sur le cloud privé.  Chaque nœud possède quatre interfaces de réseaux physiques connectés à underlay réseau.  Deux interfaces réseau physiques sont utilisées pour créer un **vSphere distribuée basculer (VDS)** sur vCenter et deux sont utilisés pour créer un **gérés NSX distribuée commutateur virtuel (VDS-N)**.  Interfaces réseau sont configurés en mode actif-actif pour la haute disponibilité.

En savoir plus sur VMware ESXi

## <a name="vcenter-server-appliance"></a>appliance de serveur vCenter

appliance de serveur vCenter (VCSA) fournit les fonctions d’authentification, la gestion et d’orchestration pour la VMware Solution par CloudSimple. VCSA avec embedded Platform Services Controller (PSC) est déployé lorsque vous créez votre cloud privé.  VCSA est déployé sur le cluster vSphere qui est créé lorsque vous déployez votre cloud privé.  Chaque cloud privé a son propre VCSA.  Expansion d’un cloud privé ajoute les nœuds à le VCSA sur le cloud privé.

### <a name="vcenter-single-sign-on"></a>vCenter l’authentification unique

Contrôleur de Services de plateforme intégré sur VCSA est associé un **vCenter Single Sign-On domaine**.  Le nom de domaine est **cloudsimple.local**.  Un utilisateur par défaut **CloudOwner@cloudsimple.com** est créé pour vous permettre d’accéder à vCenter.  Vous pouvez ajouter à votre annuaire local/Azure active directory [sources d’identité pour vCenter](https://docs.azure.cloudsimple.com/set-vcenter-identity/).

## <a name="vsan-storage"></a>stockage réseau vSAN

Clouds privés sont créés avec le stockage entièrement configuré exclusivement flash vSAN, local pour le cluster.  Nombre minimal de trois nœuds de la même référence SKU est requis pour créer un cluster vSphere avec magasin de données vSAN.  Déduplication et compression sont activés par défaut sur la banque de données vSAN.  Deux groupes de disques sont créés sur chaque nœud du cluster vSphere. Chaque groupe de disque contient un disque de cache et trois disques de capacité.

Une stratégie de stockage vSAN par défaut est créée sur le cluster vSphere et appliquée à la banque de données vSAN.  Cette stratégie détermine comment les objets de stockage de machine virtuelle sont mis en service et alloués au sein de la banque de données pour garantir le niveau de service requis.  La stratégie de stockage définit la **échecs à tolérer (FTT)** et le **méthode de tolérance de panne**.  Vous pouvez créer des stratégies de stockage et les appliquer aux machines virtuelles. Pour conserver le contrat SLA, capacité de rechange de 25 % doit être conservée sur la banque de données vSAN.  

### <a name="default-vsan-storage-policy"></a>Stratégie de stockage par défaut vSAN

Tableau ci-dessous présente la valeur par défaut des paramètres de stratégie de stockage vSAN.

| Nombre de nœuds de Cluster de vSphere | FTT | Méthode de tolérance de panne |
|------------------------------------|-----|--------------------------|
| 3 et 4 nœuds | 1 | RAID 1 (mise en miroir) : crée des 2 copies |
| 5 à 16 nœuds | 2 | RAID 1 (mise en miroir) : crée des 3 copies |

## <a name="nsx-data-center"></a>Centre de données NSX

Centre de données NSX fournit la virtualisation de réseau, de segmentation micro et de fonctionnalités de sécurité réseau sur votre cloud privé.  Vous pouvez configurer tous les services pris en charge par le centre de données NSX sur votre cloud privé via NSX.  Lorsque vous créez un cloud privé, les composants NSX suivants sont installés et configurés.

* NSXT Manager
* Zones de transport
* Hôte et le profil de liaison montante Edge
* Commutateur logique pour le Transport Edge et Ext2 Ext1
* Pool d’adresses IP pour le nœud de Transport d’ESXi
* Pool d’adresses IP pour le nœud de Transport Edge
* Nœuds de périmètre
* Règle de DRS affinity pour le contrôleur et les machines virtuelles Edge
* Routeur de niveau 0
* Activer BGP sur Tier0 routeur

## <a name="vsphere-cluster"></a>cluster de vSphere

Hôtes ESXi sont configurés en tant que cluster pour garantir une haute disponibilité du cloud privé.  Lorsque vous créez un cloud privé, les composants de gestion de vSphere sont déployés sur le premier cluster.  Un pool de ressources est créé pour les composants de gestion et toutes les machines virtuelles de gestion sont déployés dans ce pool de ressources. Le premier cluster ne peut pas être supprimé pour réduire le cloud privé.  vSphere cluster offre une haute disponibilité pour les machines virtuelles à l’aide de **vSphere haute disponibilité**.  Échecs à tolérer sont basées sur le nombre de nœuds disponibles dans le cluster.  Vous pouvez utiliser la formule ```Number of nodes = 2N+1``` où ```N``` est le nombre d’échecs à tolérer.

### <a name="vsphere-cluster-limits"></a>limites de cluster vSphere

| Ressource | Limite |
|----------|-------|
| Nombre minimal de nœuds pour créer un cloud privé (premier cluster vSphere) | 3 |
| Nombre maximal de nœuds dans un vSphere Cluster sur un cloud privé | 16 |
| Nombre maximal de nœuds dans un cloud privé | 64 |
| Nombre maximal de vSphere Clusters dans un cloud privé | 21 |
| Nombre minimal de nœuds sur un Cluster de vSphere nouveau | 3 |

## <a name="vmware-infrastructure-maintenance"></a>Maintenance de l’infrastructure VMware

Il est parfois nécessaire apporter des modifications à la configuration de l’infrastructure VMware. Actuellement, ces intervalles peuvent se produire à tous les mois 1-2, mais la fréquence est censée refuser au fil du temps. Ce type de maintenance généralement faire sans interrompre la consommation des services CloudSimple normale. Pendant un intervalle de maintenance de VMware, les services suivants continuent à fonctionner sans aucun impact :

* Applications et le plan de gestion de VMware
* accès au vCenter
* Mise en réseau et stockage
* Tout le trafic Azure

## <a name="updates-and-upgrades"></a>Mises à jour et mises à niveau

CloudSimple est responsable de la gestion du cycle de vie des logiciels de VMware (ESXi vCenter, PSC et NSX) dans le cloud privé.

Mises à jour logicielles incluent :

* **Correctifs**. Correctifs de sécurité ou correctifs de bogues publiés par VMware.
* **Mises à jour**. Changement de version mineure d’un composant de la pile VMware.
* **Mises à niveau**. Changement de version majeure d’un composant de la pile VMware.

CloudSimple teste un correctif de sécurité critique dès qu’il est disponible à partir de VMware. Par le contrat SLA, CloudSimple déploie le correctif de sécurité pour les environnements de cloud privé au sein d’une semaine.

CloudSimple fournit des mises à jour de maintenance de tous les trimestres pour des composants logiciels VMware. Lorsqu’une nouvelle version majeure du logiciel de VMware est disponible, CloudSimple fonctionne avec les clients pour coordonner une fenêtre de maintenance appropriée pour la mise à niveau.  

## <a name="next-steps"></a>Étapes suivantes

* [Mises à jour et maintenance de CloudSimple](cloudsimple-maintenance-updates.md)