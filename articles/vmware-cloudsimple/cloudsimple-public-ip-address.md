---
title: Solution de VMware à CloudSimple - adresse IP publique Azure
description: En savoir plus sur les adresses IP publiques et leurs avantages sur la VMware Solution par CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f57b7397f4a2d288cd2b8b55cf23b2d635aa5f8c
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209563"
---
# <a name="cloudsimple-public-ip-address-overview"></a>CloudSimple : les présentation de l’adresse IP publique

Une adresse IP publique permet de ressources internet communiquer entrant, à des ressources de cloud privé sur une adresse IP privée. L’adresse IP privée est une machine virtuelle ou un équilibreur de charge logiciel. L’adresse IP privée est sur votre vCenter de cloud privé. L’adresse IP publique vous permet d’exposer des services en cours d’exécution sur votre cloud privé à internet.

L’adresse IP publique est dédiée à l’adresse IP privée jusqu'à ce que vous supprimez l’affectation. Une adresse IP publique peut uniquement être affectée à une adresse IP privée.

Une ressource associée à une adresse IP publique toujours utilise l’adresse IP publique pour accéder à internet. Par défaut, uniquement un accès internet sortant est autorisé sur une adresse IP publique.  Le trafic entrant sur l’adresse IP publique est refusé.  Pour autoriser le trafic entrant, créez une règle de pare-feu pour l’adresse IP publique sur le port spécifique.

## <a name="benefits"></a>Avantages

À l’aide d’une adresse IP publique pour communiquer le trafic entrant fournit :

* Distribuée prévention du déni de service (distribué DDoS) attaque. Cette protection est automatiquement activée pour l’adresse IP publique.
* Permanente du trafic en temps réel et surveillance atténuation des attaques courantes au niveau du réseau. Ces défenses sont les mêmes défenses que celles utilisées par Microsoft online services.
* Toute mise à l’échelle du réseau global Azure. Le réseau peut être utilisé pour distribuer et de réduire le trafic d’attaque dans différentes régions.  

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [allouer une adresse IP publique](https://docs.azure.cloudsimple.com/public-ips/)
