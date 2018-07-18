---
title: Différences et considérations concernant la mise en réseau Azure Stack | Microsoft Docs
description: Découvrez les différences et les éléments à prendre en compte lors de l’utilisation de la mise en réseau dans Azure Stack.
services: azure-stack
keywords: ''
author: mattbriggs
manager: femila
ms.author: mabrigg
ms.date: 05/21/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: scottnap
ms.openlocfilehash: faff52ba5b5e2f0d573a67633d3a8411b2d7de74
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606424"
---
# <a name="considerations-for-azure-stack-networking"></a>Considérations relatives à la mise en réseau Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

La gestion réseau Azure Stack dispose d’un grand nombre des fonctionnalités fournies par la gestion réseau Azure. Toutefois, il existe des différences fondamentales que vous devez connaître avant de déployer un réseau Azure Stack.

Cet article fournit une vue d’ensemble de considérations propres à la gestion réseau Azure Stack et ses fonctionnalités. Pour connaître les différences majeures entre Azure Stack et Azure, consultez la rubrique [Principales considérations](azure-stack-considerations.md).

## <a name="cheat-sheet-networking-differences"></a>Aide-mémoire : différences de mise en réseau

|de diffusion en continu | Fonctionnalité | Azure (global) | Azure Stack |
| --- | --- | --- | --- |
| DNS | Système DNS multilocataire | Prise en charge| Pas encore pris en charge|
| |Enregistrements AAAA DNS|Prise en charge|Non pris en charge|
| |Zones DNS par abonnement|100 (par défaut)<br>Peut être augmenté à la demande.|100|
| |Jeux d’enregistrements DNS par zone|5000 (par défaut)<br>Peut être augmenté à la demande.|5 000|
||Serveurs de noms pour la délégation de zone|Azure fournit quatre serveurs de noms pour chaque zone utilisateur (locataire) créée.|Azure Stack fournit deux serveurs de noms pour chaque zone utilisateur (locataire) créée.|
| Réseau virtuel|Homologation de réseaux virtuels|Connecter deux réseaux virtuels situés dans la même région par le biais du réseau principal Azure.|Pas encore pris en charge|
| |Adresses IPv6|Vous pouvez affecter une adresse IPv6 dans le cadre de la [Configuration de l’interface réseau](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions).|Seul le protocole IPv4 est pris en charge.|
|Passerelles VPN|Passerelle VPN de point à site|Prise en charge|Pas encore pris en charge|
| |Passerelle de réseau virtuel à réseau virtuel|Prise en charge|Pas encore pris en charge|
| |SKU de passerelle de réseau virtuel|Prise en charge de Basic, GW1, GW2, GW3, Standard High Performance, Ultra-High Performance. |Prise en charge des SKU Basic, Standard et High-Performance.|
|Équilibrage de charge|Adresses IP publiques IPv6|Prise en charge de l’affectation d’une adresse IP publique IPv6 à un équilibreur de charge.|Seul le protocole IPv4 est pris en charge.|
|Network Watcher|Fonctionnalités de surveillance réseau de locataire Network Watcher|Prise en charge|Pas encore pris en charge|
|CDN|Profils de réseau de distribution de contenu (CDN)|Prise en charge|Pas encore pris en charge|
|passerelle d’application|Équilibrage de charge de couche 7|Prise en charge|Pas encore pris en charge|
|Traffic Manager|Routage du trafic entrant pour une fiabilité et des performances d’application optimales.|Prise en charge|Pas encore pris en charge|
|ExpressRoute|Configurer une connexion privée rapide aux services cloud Microsoft à partir de votre infrastructure locale ou d’une installation de colocalisation.|Prise en charge|Prise en charge de la connexion d’Azure Stack à un circuit Express Route.|

## <a name="next-steps"></a>Étapes suivantes

[DNS dans Azure Stack](azure-stack-dns.md)