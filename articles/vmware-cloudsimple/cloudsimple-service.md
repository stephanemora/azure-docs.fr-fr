---
title: Azure VMware Solutions (AVS) - Service
description: Fournit une vue d’ensemble des concepts et du service AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d09c8c34093e7d33122f934138ff9fdf4842508e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024949"
---
# <a name="avs-service-overview"></a>Vue d’ensemble du service AVS

Le service AVS vous permet de consommer Azure VMware Solution by AVS. En créant ce service, vous pouvez acheter, réserver des nœuds et créer des clouds privés AVS. Vous créez le service AVS dans chaque région Azure où le service AVS est disponible. Le service définit le réseau de périmètre d’Azure VMware Solution by AVS. Le réseau de périmètre prend en charge les services comprenant une connectivité VPN, ExpressRoute et Internet vers vos clouds privés AVS.

## <a name="gateway-subnet"></a>Sous-réseau de passerelle

Un sous-réseau de passerelle est requis par le service AVS et est propre à la région dans laquelle il est créé. Le sous-réseau de passerelle est utilisé lors de la création du réseau de périmètre et requiert un bloc CIDR /28. L’espace d’adressage du sous-réseau de passerelle doit être unique. Il ne doit chevaucher aucun réseau qui communique avec l’environnement AVS. Les réseaux qui communiquent avec AVS incluent les réseaux locaux et le réseau virtuel Azure. Un sous-réseau de passerelle ne peut pas être supprimé après sa création. Le sous-réseau de passerelle est supprimé lorsque le service est supprimé.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer un service AVS sur Azure](quickstart-create-cloudsimple-service.md).
