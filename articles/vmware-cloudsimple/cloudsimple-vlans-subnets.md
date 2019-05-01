---
title: Réseaux locaux virtuels et sous-réseaux dans la VMware Solution par CloudSimple - Azure
description: En savoir plus sur les réseaux locaux virtuels et sous-réseaux dans un cloud privé CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 448b2b51ef6c5f7aa0bd660eec580019714caf5d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577929"
---
# <a name="vlans-and-subnets-overview"></a>Vue d’ensemble des réseaux locaux virtuels et sous-réseaux

CloudSimple fournit un réseau par région où votre service CloudSimple est déployé.  Le réseau est un seul espace d’adresse TCP de couche 3 avec routage activé par défaut.  Tous les clouds privés et les sous-réseaux créés dans cette région peuvent communiquer entre eux sans aucune configuration supplémentaire.  Vous pouvez créer des groupes de ports distribué sur le serveur vCenter à l’aide des réseaux locaux virtuels.

## <a name="vlans"></a>Réseaux VLAN

Réseaux locaux virtuels (réseau de couche 2) sont créés par le cloud privé.  Le trafic de couche 2 reste dans les limites d’un cloud privé, ce qui vous permet d’isoler le trafic local dans le cloud privé.  Un réseau local virtuel créé sur le cloud privé peut être utilisé pour créer des groupes de ports distribuées uniquement dans ce nuage privé.  Un réseau local virtuel créé sur un cloud privé est automatiquement configuré sur tous les commutateurs connectés aux hôtes d’un cloud privé.

## <a name="subnets"></a>Sous-réseaux

Vous pouvez créer un sous-réseau lorsque vous créez un réseau local virtuel, en définissant l’espace d’adressage du sous-réseau. Une adresse IP à partir de l’espace d’adressage est attribuée en tant que sous-réseau passerelle. Un seul espace d’adressage privé de couche 3 est affecté par le client et par région. Vous pouvez configurer n’importe quel espace adresse sans chevauchement de RFC 1918, avec votre réseau local ou d’un réseau virtuel Azure, dans la région de votre réseau.

Tous les sous-réseaux peuvent communiquer entre eux par défaut, ce qui réduit la configuration de la surcharge pour le routage entre des clouds privés. Est-ouest des données sur les PC dans la même région restent dans le même réseau de couche 3 et transféré via l’infrastructure de réseau local au sein de la région. Aucune sortie n’est requise pour la communication entre des clouds privés dans une région. Cette approche permet d’éliminer toute altération des performances WAN et de sortie dans le déploiement de différentes charges de travail dans des clouds privés différents.

## <a name="vspherevsan-subnets-cidr-range"></a>plage CIDR vSphere/vSAN sous-réseaux

Un Cloud privé est créé comme une pile VMware isolée (hôtes ESXi vCenter, vSAN et NSX) environnement géré par un serveur vCenter.  Composants de gestion sont déployés dans le réseau sélectionné pour **vSphere/vSAN sous-réseaux CIDR**.  La plage CIDR du réseau est divisé en sous-réseaux différents pendant le déploiement.

Préfixe de plage minimale vSphere/vSAN sous-réseaux CIDR : **/24** sous-réseaux de vSphere/vSAN Maximum CIDR de plage préfixe :   **/21**

### <a name="vspherevsan-subnets-cidr-range-limits"></a>limites de plage de sous-réseaux vSphere/vSAN CIDR

En sélectionnant la taille de la plage CIDR vSphere/vSAN sous-réseaux a un impact sur la taille de votre nuage privé.  Tableau ci-dessous affiche le nombre maximal de nœuds, que vous pouvez avoir basée sur la taille des sous-réseaux vSphere/vSAN CIDR.

| Longueur du préfixe spécifiés vSphere/vSAN sous-réseaux CIDR | Nombre maximal de nœuds |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>Sous-réseaux de gestion créés sur un Cloud privé

Les sous-réseaux de gestion suivants sont créés lorsque vous créez un Cloud privé. 

* **Gestion du système** -VLAN / sous-réseau pour la gestion des hôtes ESXi réseau, serveur DNS, serveur vCenter.
* **VMotion** -réseau local virtuel et sous-réseau pour vMotion réseau des hôtes ESXi.
* **VSAN** -réseau local virtuel et sous-réseau pour vSAN réseau des hôtes ESXi.
* **NsxtEdgeUplink1** -VLAN et le sous-réseau pour les liaisons montantes de réseau local virtuel à un réseau externe.
* **NsxtEdgeUplink2** -VLAN et le sous-réseau pour les liaisons montantes de réseau local virtuel à un réseau externe.
* **NsxtEdgeTransport** -VLAN / sous-réseau pour les zones de transport contrôlent la portée des réseaux de couche 2 dans NSX-T.
* **NsxtHostTransport** -zone de transport de réseau local virtuel et un sous-réseau pour l’hôte.

### <a name="management-network-cidr-range-breakdown"></a>Répartition de plage CIDR gestion réseau

la plage CIDR vSphere/vSAN sous-réseaux spécifiée est divisée en plusieurs sous-réseaux.  Tableau ci-dessous montre un exemple de la répartition des préfixes autorisés.  L’exemple utilise **192.168.0.0** en tant que la plage CIDR.

Exemple :

| Sous-réseaux vSphere/vSAN spécifiés/préfixe CIDR | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| Gestion de systèmes | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| Transport d’hôte NSX-T | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| -T NSX Edge Transport | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| -T NSX Edge Uplink1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| -T NSX Edge uplink2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>Étapes suivantes

* [Créer et gérer des réseaux locaux virtuels et sous-réseaux](https://docs.azure.cloudsimple.com/vlansubnet/)