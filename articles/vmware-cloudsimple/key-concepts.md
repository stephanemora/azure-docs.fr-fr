---
title: Concepts clés pour administrer Azure VMware Solutions (AVS)
description: Décrit les concepts clés pour administrer Azure VMware Solutions (AVS)
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c581ffbba7fbaefc8fbd0ffc7e92350bd69802b8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025229"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-avs"></a>Concepts clés pour administrer Azure VMware Solutions (AVS)

Pour administrer Azure VMware Solutions (AVS), vous devez comprendre les concepts suivants :

* Service AVS, présenté comme Azure VMware Solutions (AVS) - Service
* Nœud AVS, présenté comme Azure VMware Solutions (AVS) - Nœud
* Cloud privé AVS
* Mise en réseau du service
* Machine virtuelle AVS, présentée comme Azure VMware Solutions (AVS) - Machine virtuelle

## <a name="avs-service"></a>Service AVS

Le service AVS vous permet de créer et de gérer toutes les ressources associées à Azure VMware Solutions (AVS) à partir du portail Azure. Créez une ressource de service dans chaque région dans laquelle vous souhaitez utiliser le service.

Découvrez plus en détail le [service AVS](cloudsimple-service.md).

## <a name="avs-node"></a>Nœud AVS

Un nœud AVS est un hôte de calcul et de stockage hyperconvergé complet dédié sur lequel l’hyperviseur VMware ESXi est déployé. Ce nœud est ensuite intégré aux plateformes VMware vSphere, vCenter, vSAN, et NSX. Les services réseau AVS et Edge sont également activés. Chaque nœud sert d’unité de capacité de calcul et de stockage que vous pouvez provisionner pour créer des [clouds privés AVS](cloudsimple-private-cloud.md). Vous provisionnez ou réservez des nœuds dans une région où le service AVS est disponible.

Découvrez plus en détail les [nœuds AVS](cloudsimple-node.md).

## <a name="avs-private-cloud"></a>Cloud privé AVS

Un cloud privé AVS est un environnement isolé de la pile VMware géré par un serveur vCenter dans son propre domaine de gestion. La pile VMware inclut des hôtes ESXi, vSphere, vCenter, vSAN et NSX. Les exécutions de pile sur des nœuds dédiés (matériel complet isolé et dédié) et utilisé par les utilisateurs via les outils VMware natifs qui incluent vCenter et NSX Manager. Les nœuds dédiés sont déployés dans des emplacements Azure et sont gérés par Azure. Chaque cloud privé AVS peut être segmenté et sécurisé à l’aide des services réseau, tels que les réseaux locaux virtuels, les sous-réseaux et les tables de pare-feu. Les connexions à votre environnement local et au réseau Azure sont créées à l’aide de connexions VPN sécurisées, privées et Azure ExpressRoute.

Découvrez plus en détail le [cloud privé AVS](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Mise en réseau du service

Le service AVS fournit un réseau par région où votre service AVS est déployé. Le réseau est un seul espace d’adressage TCP de couche 3 avec routage activé par défaut. Tous les clouds privés et sous-réseaux AVS créés dans cette région peuvent communiquer entre eux sans configuration supplémentaire. Vous créez des groupes de ports distribués sur le serveur vCenter en utilisant les réseaux locaux virtuels. Vous pouvez utiliser les fonctionnalités réseau suivantes pour configurer et sécuriser les ressources de charge de travail de votre cloud privé AVS :

* [Réseaux locaux virtuels et sous-réseaux](cloudsimple-vlans-subnets.md)
* [Tables de pare-feu](cloudsimple-firewall-tables.md)
* [Passerelles VPN](cloudsimple-vpn-gateways.md)
* [Adresse IP publique](cloudsimple-public-ip-address.md)
* [Connexion réseau Azure](cloudsimple-azure-network-connection.md)

## <a name="avs-virtual-machine"></a>Machine virtuelle AVS

Avec le service AVS, vous pouvez gérer des machines virtuelles VMware à partir du portail Azure. Un ou plusieurs clusters ou pools de ressources de votre environnement vSphere peuvent être mappés à l’abonnement sur lequel le service est créé.

Pour en savoir plus :

* [Machines virtuelles AVS](cloudsimple-virtual-machines.md)
* [Mappage de l’abonnement Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
