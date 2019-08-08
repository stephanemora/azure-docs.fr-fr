---
title: Solution VMware du service CloudSimple
description: Décrit la vue d’ensemble des concepts et du service CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a43fbebb21be82fd751778d6fec95e0ee9c346ad
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812514"
---
# <a name="cloudsimple-service-overview"></a>Vue d’ensemble du service CloudSimple

Le service CloudSimple vous permet d’utiliser la Solution Azure VMware de CloudSimple. Après avoir créé le service, vous pouvez approvisionner des nœuds, réserver des nœuds et créer des clouds privés. Vous créez le service de CloudSimple à chaque région Azure où ce service est disponible. Le service définit le réseau de périmètre de la Solution Azure VMware de CloudSimple. Le réseau de périmètre prend en charge les services comprenant une connectivité VPN, Azure ExpressRoute et Internet vers vos clouds privés.

## <a name="gateway-subnet"></a>Sous-réseau de passerelle

Un sous-réseau de passerelle est requis par le service CloudSimple et est propre à la région dans laquelle il est créé. Le sous-réseau de passerelle est utilisé lors de la création du réseau de périmètre et requiert un bloc CIDR /28. L’espace d’adressage du sous-réseau de passerelle doit être unique. Il ne doit chevaucher aucun réseau qui communique avec l’environnement CloudSimple. Les réseaux qui communiquent avec CloudSimple incluent les réseaux locaux et les réseaux virtuels Azure. Un sous-réseau de passerelle ne peut pas être supprimé après sa création. Le sous-réseau de passerelle est supprimé lorsque le service est supprimé.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer un service CloudSimple sur Azure](quickstart-create-cloudsimple-service.md).
