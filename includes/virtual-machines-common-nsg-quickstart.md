---
title: Fichier Include
description: Fichier Include
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/17/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d9c8a0e6a3bd6d79a11ee0d0dab0500a209e5571
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38943533"
---
Pour ouvrir un port ou créer un point de terminaison sur une machine virtuelle dans Azure, créez un filtre réseau sur un sous-réseau ou une interface réseau de machine virtuelle. Vous placez ces filtres, qui contrôlent le trafic entrant et sortant, dans un groupe de sécurité réseau associé à la ressource qui reçoit le trafic.

Nous allons utiliser un exemple courant de trafic web sur le port 80. Une fois que vous disposez d’une machine virtuelle configurée pour traiter les requêtes web sur le port TCP 80 standard (n’oubliez pas de démarrer les services appropriés et d’ouvrir toutes les règles de pare-feu du système d’exploitation sur la machine virtuelle), vous pouvez :

1. créer un groupe de sécurité réseau ;
2. créer une règle de trafic entrant autorisant le trafic avec :
   * la plage de ports de destination « 80 » ;
   * la plage de ports source « * » (autorisant n’importe quel port source) ;
   * une valeur de priorité inférieure à 65 500 (de sorte que la priorité soit supérieure à celle de la règle fourre-tout de refus de trafic entrant par défaut) ;
3. associer le groupe de sécurité réseau à l’interface réseau de machine virtuelle ou au sous-réseau.

Vous pouvez créer des configurations réseau complexes pour sécuriser votre environnement à l’aide des règles et groupes de sécurité réseau. Notre exemple utilise seulement une ou deux règles qui autorisent le trafic HTTP ou la gestion à distance. Pour plus d’informations, reportez-vous à la section suivante [Informations complémentaires](#more-information-on-network-security-groups) ou à l’article [Présentation du groupe de sécurité réseau](../articles/virtual-network/security-overview.md).

