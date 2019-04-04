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
ms.openlocfilehash: 0467359cd9d6a067e519a62532f00459bc5f68cb
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58890935"
---
| Ressource | Limite par défaut |
| --- | --- |
| Débit de données |30 Gbits/s<sup>1</sup> |
|Règles|10 000, tous les règles des types associés.|
|Taille minimale d’AzureFirewallSubnet |/26|
|Plage de ports dans les règles de réseau et d’application|0-64,000. Nous nous efforçons actuellement d’assouplir cette limitation.|
|Table de routage|Par défaut, AzureFirewallSubnet dispose d’un itinéraire 0.0.0.0/0 avec la valeur de type de tronçon suivant définie sur **Internet**.<br><br>Si vous activez le tunneling forcé au niveau local via ExpressRoute ou passerelle VPN, vous devrez configurer un itinéraire de défini par l’utilisateur (UDR) 0.0.0.0/0 avec l’ensemble de valeurs de NextHopType comme Internet et l’associer à votre AzureFirewallSubnet explicitement. Cela remplace une passerelle par défaut potentiels annonce BGP à votre réseau local. Si votre organisation exige que le tunneling forcé pour le pare-feu Azure diriger le trafic de passerelle par défaut par le biais de votre réseau local, contactez le Support technique. Nous pouvons liste verte de votre abonnement pour vous assurer de la connectivité Internet de pare-feu requis est conservée.|

<sup>1</sup>si vous avez besoin d’augmenter ces limites, contactez le Support Azure.
