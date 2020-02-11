---
title: Azure VMware Solutions (AVS) - Adresse IP publiques
description: En savoir plus sur les adresses IP publiques et leurs avantages sur Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2cb9d0e33da4447760ae0be216c1dd9868c498bd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024974"
---
# <a name="avs-public-ip-address-overview"></a>Présentation de l'adresse IP publique AVS

Une adresse IP publique permet à des ressources Internet de communiquer (communications entrantes) avec des ressources de cloud privé AVS via une adresse IP privée. L'adresse IP privée est une machine virtuelle ou un équilibreur de charge logiciel du vCenter de votre cloud privé AVS. L'adresse IP publique vous permet d'exposer sur Internet des services exécutés sur votre cloud privé AVS.

L’adresse IP publique est dédiée à l’adresse IP privée jusqu’à ce que vous supprimiez l’attribution. Une adresse IP publique ne peut être attribuée qu’à une adresse IP privée.

Une ressource associée à une adresse IP publique toujours utilise l’adresse IP publique pour accéder à Internet. Par défaut, seul un accès Internet sortant est autorisé sur une adresse IP publique.  Le trafic entrant sur l’adresse IP publique est refusé.  Pour autoriser le trafic entrant, créez une règle de pare-feu pour l’adresse IP publique sur le port spécifique.

## <a name="benefits"></a>Avantages

L’utilisation d’une adresse IP publique pour des communications entrantes offre :

* Prévention des attaques par déni de service distribué (DDoS). Cette protection est automatiquement activée pour l’adresse IP publique.
* Surveillance Always On et atténuation en temps réel des attaques courantes au niveau du réseau. Ces défenses sont les mêmes que celles utilisées par les services en ligne Microsoft.
* Mise à l’échelle complète du réseau global Azure. La distribution et l’atténuation du trafic d’attaque peuvent être réalisées entre différentes régions.  

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur l’[allocation d’une adresse IP publique](public-ips.md)