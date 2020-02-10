---
title: VMware Solutions (AVS) – Connexions réseau Azure
description: En savoir plus sur la connexion de votre réseau virtuel Azure à votre réseau régional AVS
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bf11d4e2676179e8b71d3a03f8ed3cbcb4cfba9d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025076"
---
# <a name="azure-network-connections-overview"></a>Vue d’ensemble des connexions réseau Azure

Lorsque vous créez un service AVS dans une région et créez des nœuds, vous pouvez :

* Demander un circuit Azure ExpressRoute et le joindre au réseau AVS de cette région.
* Connecter votre réseau régional AVS à votre réseau virtuel Azure ou à votre réseau local à l’aide d’Azure ExpressRoute.
* Fournir l’accès aux services s’exécutant dans votre abonnement Azure ou dans votre réseau local à partir de votre environnement de cloud privé.

La connexion ExpressRoute est une bande passante élevée à faible latence.

## <a name="benefits"></a>Avantages

Une connexion réseau Azure vous permet d’effectuer les tâches suivantes :

* Utiliser Azure comme cible de sauvegarde pour les machines virtuelles sur votre cloud privé.
* Déployer des serveurs KMS dans votre abonnement Azure pour chiffrer la banque de données vSAN de votre cloud privé.
* Utiliser des applications hybrides où la couche web de l’application s’exécute dans le cloud public AVS tandis que les couches application et base de données s’exécutent dans votre cloud privé.

## <a name="azure-virtual-network-connection"></a>Connexion au réseau virtuel Azure

Les clouds privés AVS peuvent être connectés à vos ressources Azure à l’aide d’ExpressRoute. La connexion ExpressRoute vous permet d’accéder aux ressources s’exécutant dans votre abonnement Azure à partir de votre cloud privé AVS. Cette connexion vous permet d’étendre votre réseau cloud privé AVS à votre réseau virtuel Azure. Les itinéraires à partir d’un réseau AVS seront échangés avec votre réseau virtuel Azure via BGP. Si le peering de réseau virtuel est configuré, tous les réseaux virtuels appairés sont accessibles à partir de votre réseau AVS.

![Connexion Azure ExpressRoute au réseau virtuel](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>Connexion ExpressRoute au réseau local

Vous pouvez connecter votre circuit Azure ExpressRoute existant à votre région AVS. La fonctionnalité Global Reach d’ExpressRoute permet de connecter les deux circuits entre eux. Une connexion est établie entre les circuits local et ExpressRoute et AVS. Cette connexion vous permet d’étendre vos réseaux locaux à un réseau de cloud privé AVS. Les itinéraires de votre réseau AVS seront échangés avec votre réseau local via BGP.

![Connexion ExpressRoute locale - Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>Connexion à un réseau local et à un réseau virtuel Azure

Des connexions à un réseau local et à un réseau virtuel Azure peuvent coexister à partir de votre réseau AVS. La connexion utilise le BGP pour échanger des itinéraires entre un réseau local, un réseau virtuel Azure et un réseau AVS. Lorsque vous connectez votre réseau AVS à votre réseau virtuel Azure en présence d’une connexion Global Reach, les itinéraires de réseau virtuel Azure sont visibles sur votre réseau local. L’échange d’itinéraire s’effectue dans Azure entre les routeurs de périphérie.

![Connexion ExpressRoute locale avec connexion réseau virtuel Azure](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>Points importants à prendre en compte

La connexion à un réseau AVS à partir d’un réseau local et/ou d’un réseau virtuel Azure permet l’échange d’itinéraires entre tous les réseaux.

* Un réseau virtuel Azure sera visible à partir tant d’un réseau local que d’un réseau AVS.
* Si vous vous êtes connecté à votre réseau virtuel Azure à partir d’un réseau local, la connexion à un réseau AVS à l’aide de Global Reach autorise l’accès aux réseaux virtuels à partir du réseau AVS.
* Les adresses de sous-réseau **ne doivent pas** se chevaucher entre les réseaux connectés.
* AVS ne publiera **pas** l’itinéraire par défaut vers les connexions ExpressRoute
* Si votre routeur local publie l’itinéraire par défaut, le trafic à partir du réseau AVS et du réseau virtuel Azure utilisera l’itinéraire par défaut publié. Par conséquent, il est impossible d’accéder aux machines virtuelles Azure à l’aide d’adresses IP publiques.

## <a name="next-steps"></a>Étapes suivantes

* [Connecter un réseau virtuel Azure à AVS à l’aide d’ExpressRoute](virtual-network-connection.md)
* [Connecter un réseau local à AVS à l’aide d’ExpressRoute](on-premises-connection.md)
