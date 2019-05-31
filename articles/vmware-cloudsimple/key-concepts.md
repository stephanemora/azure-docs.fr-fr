---
title: Concepts clés pour l’administration de Solution de VMware Azure par CloudSimple
description: Décrit les concepts clés pour l’administration de Solution de VMware Azure par CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0d890553ee145ca6aafed5a34d158c6a34d9af36
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358167"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solution-by-cloudsimple"></a>Concepts clés pour l’administration de Solution de VMware Azure par CloudSimple

Administration de Solution de VMware Azure par CloudSimple, vous devez comprendre les concepts suivants.

* CloudSimple Service (affiché en tant que Solution de VMware Azure par CloudSimple - Service)
* Nœud CloudSimple (affiché en tant que Solution de VMware Azure par CloudSimple - nœud)
* Cloud privé de CloudSimple
* Service réseau
* Machine virtuelle de CloudSimple (affiché en tant que Solution de VMware Azure par CloudSimple - Machine virtuelle)

## <a name="cloudsimple-service"></a>Service de CloudSimple

Le service CloudSimple vous permet de créer et gérer toutes les ressources associées à des Solutions de VMware à partir du portail Azure le CloudSimple. Créer une ressource de service dans chaque région dans laquelle vous souhaitez utiliser le Service. 

En savoir plus sur les [CloudSimple Service](cloudsimple-service.md)

## <a name="cloudsimple-node"></a>Nœud de CloudSimple

Un nœud CloudSimple est un hôte hyperconvergé calcul et de stockage dans lequel est déployé l’hyperviseur VMware ESXi dédié, nu. Ce nœud est ensuite incorporé dans les plateformes NSX, vCenter, de réseau vSAN et VMware vSphere. Services de mise en réseau CloudSimple et services de mise en réseau de périmètre sont également activées. Chaque nœud sert d’une unité de capacité de calcul et de stockage que vous pouvez acheter pour créer [des Clouds privés de CloudSimple](cloudsimple-private-cloud.md). Vous achetez ou réservez de nœuds dans une région où le service CloudSimple est disponible.


En savoir plus sur [CloudSimple nœuds](cloudsimple-node.md)

## <a name="cloudsimple-private-cloud"></a>Cloud privé de CloudSimple

Un Cloud privé de CloudSimple est un environnement isolé de pile VMware géré par un serveur vCenter dans son propre domaine de gestion. Pile VMware inclut des hôtes ESXi, vSphere, vCenter, vSAN et NSX.  Les exécutions de pile sur des nœuds (dédié et isolé matériel nu) dédiés et est consommée par les utilisateurs via les outils VMware natifs qui incluent vCenter et NSX Manager. Nœuds dédiés sont déployés dans des emplacements Azure et sont gérés par Azure. Chaque Cloud privé peut être segmenté et sécurisé à l’aide de mise en réseau des services tels que les réseaux locaux virtuels/sous-réseaux et les tables de pare-feu.  Les connexions à votre environnement local et le réseau Azure sont créées à l’aide de VPN sécurisée et privée et les connexions Azure ExpressRoute.

En savoir plus sur [CloudSimple privé](cloudsimple-private-cloud.md)

## <a name="service-networking"></a>Mise en réseau de service

Le service de CloudSimple fournit un réseau par région où votre service CloudSimple est déployé. Le réseau est un seul espace d’adresse TCP de couche 3 avec routage activé par défaut. Tous les Clouds privés et les sous-réseaux créés dans cette région communiquer entre eux sans aucune configuration supplémentaire. Créez des groupes de ports distribué sur le serveur vCenter à l’aide des réseaux locaux virtuels.  Vous pouvez utiliser les fonctionnalités réseau suivantes pour configurer et de sécuriser vos ressources de la charge de travail de votre Cloud privé.

* [Réseaux locaux virtuels/sous-réseaux](cloudsimple-vlans-subnets.md)
* [Tables de pare-feu](cloudsimple-firewall-tables.md)
* [Passerelles VPN](cloudsimple-vpn-gateways.md)
* [Adresse IP publique](cloudsimple-public-ip-address.md)
* [Connexion de réseau Azure](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>Machine virtuelle de CloudSimple

Le service CloudSimple vous permet de gérer des machines virtuelles VMware à partir du portail Azure. Un ou plusieurs clusters ou des pools de ressources à partir de votre environnement de vSphere peuvent être mappés à l’abonnement sur lequel le service est créé.

Pour en savoir plus :

* [Machines virtuelles CloudSimple](cloudsimple-virtual-machines.md)
* [Mappage de l’abonnement Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
