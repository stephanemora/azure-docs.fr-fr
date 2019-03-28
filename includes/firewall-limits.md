---
title: Fichier Include
description: Fichier Include
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 3/25/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 368a7cf1d372d79f062affe9b206d070afe5c2f7
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58505944"
---
| Ressource | Limite par défaut |
| --- | --- |
| Débit de données |30 Gbits/s<sup>1</sup> |
|Règles|10 000, tous les règles des types associés.|
|Taille minimale d’AzureFirewallSubnet |/26|
|Plage de ports dans les règles de réseau et d’application|0-64,000. Nous nous efforçons actuellement d’assouplir cette limitation.|
|Table de routage|Par défaut, AzureFirewallSubnet dispose d’un itinéraire 0.0.0.0/0 avec la valeur de type de tronçon suivant définie sur **Internet**.<br><br>Si vous activez le tunneling forcé au niveau local via ExpressRoute ou passerelle VPN, vous devrez configurer un itinéraire de défini par l’utilisateur (UDR) 0.0.0.0/0 avec l’ensemble de valeurs de NextHopType comme Internet et l’associer à votre AzureFirewallSubnet explicitement. Cela remplace une passerelle par défaut potentiels annonce BGP à votre réseau local. Si votre organisation exige que le tunneling forcé pour le pare-feu Azure diriger le trafic de passerelle par défaut par le biais de votre réseau local, contactez le Support technique. Nous pouvons liste verte de votre abonnement pour vous assurer de la connectivité Internet de pare-feu requis est conservée.|

<sup>1</sup>si vous avez besoin d’augmenter ces limites, contactez le Support Azure.
