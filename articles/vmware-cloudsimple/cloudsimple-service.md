---
title: Solutions VMware par CloudSimple - Service
description: Décrit la vue d’ensemble des concepts et CloudSimple service.
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f7b4be0ff3997e27dd5b5321dd44b5006ae52102
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358107"
---
# <a name="cloudsimple-service-overview"></a>Présentation du Service de CloudSimple

Le service CloudSimple vous permet de vous permettent de consommer la Solution de VMware Azure par CloudSimple.  Création du service vous permet d’acheter des nœuds, réserve de nœuds et créer des Clouds privés.  Vous créez le service CloudSimple dans chaque région Azure où le service CloudSimple est disponible.  Le service définit le réseau de périmètre de la Solution de VMware Azure par CloudSimple.  Le réseau de périmètre prend en charge les services qui incluent des VPN ExpressRoute et une connectivité internet pour vos Clouds privés.

## <a name="gateway-subnet"></a>Sous-réseau de passerelle

Un sous-réseau de passerelle est requis par le service de CloudSimple et est propre à la région dans laquelle elle est créée. Le sous-réseau de passerelle est utilisé lors de la création du réseau de périmètre et nécessite un sous-réseau/28 bloc CIDR.  L’espace d’adressage de sous-réseau passerelle doit être unique. Il ne doit pas se chevaucher avec n’importe quel réseau qui communique avec l’environnement CloudSimple. Les réseaux qui communiquent avec CloudSimple incluent des réseaux locaux et réseau virtuel Azure.  Un sous-réseau de passerelle ne peut pas être supprimé une fois qu’il est créé.  Le sous-réseau de passerelle est supprimé lorsque le service est supprimé.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer un service CloudSimple sur Azure](quickstart-create-cloudsimple-service.md)