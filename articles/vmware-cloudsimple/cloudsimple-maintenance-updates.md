---
title: VMware Solution Azure par CloudSimple - CloudSimple maintenance et mises à jour
description: Décrit le processus de service CloudSimple pour les mises à jour et maintenance planifiée
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4dde358f10e9ac5054297ff68a0971404c0dc135
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160244"
---
# <a name="cloudsimple-maintenance-and-updates"></a>Mises à jour et maintenance de CloudSimple

L’environnement de cloud privé est conçu pour ne disposer d’aucun point de défaillance unique :

* ESXi clusters sont configurés avec une haute disponibilité de vSphere. Les clusters sont dimensionnés pour avoir au moins un nœud de secours pour assurer la résilience.
* Stockage principal redondant est fourni par vSAN, ce qui nécessite au moins trois nœuds pour fournir une protection contre une défaillance unique. réseau vSAN peut être configuré pour fournir une résilience accrue pour les clusters de grande taille.
* vCenter PSC, NSX Manager machines virtuelles et sont configurés avec la stratégie de stockage RAID 10 pour protéger contre les défaillances de stockage. Les machines virtuelles sont protégées contre les défaillances de nœud/réseau par vSphere à haute disponibilité.
* Hôtes ESXi ont des cartes réseau et des ventilateurs redondants.
* Les commutateurs TOR et l’épine dorsale sont configurés dans les paires haute disponibilité pour assurer la résilience.

CloudSimple surveille les machines virtuelles suivantes de fonctionnement et de disponibilité en continu et fournit des contrats SLA de disponibilité :

* Hôtes ESXi
* vCenter
* PSC
* NSX Manager

CloudSimple surveille également ce qui suit en permanence pour les échecs :

* Disques durs
* Ports de carte réseau physiques
* Serveurs
* Ventilateurs
* Puissance
* Commutateurs
* Ports de commutateur

Si un disque ou un nœud échoue, un nouveau nœud est automatiquement ajouté au cluster VMware affecté pour ramener immédiatement au contrôle d’intégrité.

CloudSimple sauvegarde gère et met à jour ces éléments VMware dans les clouds privés :

* ESXi
* Services de plateforme de vCenter
* Controller
* vSAN
* NSX

## <a name="back-up-and-restore"></a>Sauvegarder et restaurer

La sauvegarde CloudSimple inclut :

* Les sauvegardes incrémentielles nocturnes de vCenter, PSC et DVS règles.
* Utilisation de vCenter API natives pour sauvegarder des composants de la couche application.
* Sauvegarde automatique avant toute mise à jour ou la mise à niveau du logiciel de gestion de VMware.
* Chiffrement des données à la source, par vCenter, avant le transfert de données via un canal chiffré TLS 1.2 pour Azure. Les données sont stockées dans un objet blob Azure, où elle est répliquée dans différentes régions.

Vous pouvez demander une restauration en ouvrant un [demande de Support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="maintenance"></a>Maintenance 

CloudSimple effectue plusieurs types de maintenance planifiée.

### <a name="backendinternal-maintenance"></a>Maintenance de serveur principal/interne

Cette maintenance implique généralement la reconfiguration des biens physiques ou de l’installation de correctifs logiciels. Cela n’affecte pas la consommation des ressources en cours de maintenance normale. Avec les cartes réseau redondantes accédant à chaque rack physique, le trafic réseau normal et les opérations de cloud privé ne sont pas affectées. Vous pouvez remarquer un impact sur les performances uniquement si votre organisation s’attend à utiliser la bande passante complète redondante pendant l’intervalle de maintenance.

### <a name="cloudsimple-portal-maintenance"></a>Maintenance d’un portail CloudSimple

Un temps d’arrêt de service limité est requis lors de l’infrastructure ou du plan de contrôle CloudSimple est mis à jour. Actuellement, les intervalles de maintenance peuvent être aussi fréquentes qu’une fois par mois. La fréquence est censée refuser au fil du temps. CloudSimple fournit une notification pour la maintenance d’un portail et conserve l’intervalle aussi courte que possible. Pendant un intervalle de maintenance d’un portail, les services suivants continuent à fonctionner sans aucun impact :

* Applications et le plan de gestion de VMware
* accès au vCenter
* Mise en réseau et stockage
* Tout le trafic Azure

### <a name="vmware-infrastructure-maintenance"></a>Maintenance de l’infrastructure VMware

Il est parfois nécessaire apporter des modifications à la configuration de l’infrastructure VMware.  Actuellement, ces intervalles peuvent se produire à tous les mois 1-2, mais la fréquence est censée refuser au fil du temps. Ce type de maintenance généralement faire sans interrompre la consommation des services CloudSimple normale. Pendant un intervalle de maintenance de VMware, les services suivants continuent à fonctionner sans aucun impact :

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

[Sauvegarder des machines virtuelles de charge de travail à l’aide de Veeam](https://docs.azure.cloudsimple.com/backup-workloads-veeam/).