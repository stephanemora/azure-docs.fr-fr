---
title: Fichier Include
description: Fichier Include
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/08/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: d694a7030f96ef8e652d76cfb6036674aaa71249
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660209"
---
| | Référence SKU standard | Référence SKU De base |
| --- | --- | --- |
| Taille de pool principal | Prend en charge jusqu’à 1 000 instances. | Prend en charge jusqu’à 100 instances. |
| Points de terminaison du pool du serveur principal | Toute machine virtuelle dans un seul réseau virtuel, y compris la combinaison de machines virtuelles, de groupes à haute disponibilité et de groupes de machines virtuelles identiques. | Machines virtuelles dans un groupe à haute disponibilité ou un groupe de machines virtuelles identiques unique. |
| [Sondes d’intégrité](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Comportement en cas de panne de sonde d’intégrité](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | Les connexions TCP restent actives sur une sonde d’instance en panne __et__ sur toutes les sondes en panne. | Les connexions TCP restent actives sur une sonde d’instance en panne. Toutes les connexions TCP s'arrêtent quand toutes les sondes sont en panne. |
| Zones de disponibilité | Serveurs frontaux redondants interzones et zonaux pour le trafic entrant et sortant. Les mappages de flux sortants survivent aux défaillances de zone. Équilibrage de charge entre les zones. | Non disponible |
| Diagnostics | Azure Monitor. Indicateurs de performances multidimensionnels, y compris compteurs d’octets et de paquets. État de la sonde d’intégrité. Tentatives de connexion (TCP SYN). Intégrité de la connexion sortante (flux SNAT réussis et échoués). Mesures de plan de données actives. | Azure Log Analytics pour équilibreur de charge public uniquement. Alerte d’épuisement des ressources SNAT. Mesure de l’intégrité du pool du serveur principal. |
| Ports HA | Équilibreur de charge interne | Non disponible |
| Sécurisé par défaut | Les adresses IP publiques, les points de terminaison d’équilibreurs de charge publics et les points de terminaison d’équilibreurs de charge internes sont fermés aux flux entrants, sauf s’ils sont autorisés par un groupe de sécurité réseau. Notez que le trafic interne du réseau virtuel vers l’équilibreur de charge interne est toujours autorisé. | Ouvertes par défaut. Groupe de sécurité réseau facultatif. |
| [Connexions sortantes](../articles/load-balancer/load-balancer-outbound-connections.md) | Vous pouvez définir explicitement des règles NAT de trafic sortant basées sur un pool avec des [règles de trafic sortant](../articles/load-balancer/load-balancer-outbound-rules-overview.md). Vous pouvez utiliser plusieurs serveurs frontaux avec refus par règle d’équilibrage de charge. Un scénario sortant _doit_ être explicitement créé pour que la machine virtuelle, le groupe à haute disponibilité ou le groupe de machines virtuelles identiques utilise la connectivité sortante. Les points de terminaison de service de réseau virtuel peuvent être atteints sans définir de connectivité sortante et ne sont pas comptabilisés dans les données traitées. Toutes les adresses IP publiques, y compris les services PaaS Azure non disponibles en tant que points de terminaison de service de réseau virtuel, doivent être atteintes en utilisant la connectivité sortante et sont comptabilisées dans les données traitées. Quand seul un équilibreur de charge interne gère une machine virtuelle, un groupe à haute disponibilité ou un groupe de machines virtuelles identiques, les connexions sortantes via les règles SNAT par défaut ne sont pas disponibles. À la place, utilisez des [règles de trafic sortant](../articles/load-balancer/load-balancer-outbound-rules-overview.md). La programmation de SNAT sortante dépend du protocole de transport de la règle d’équilibrage de charge entrante. | Serveur frontal unique, sélectionné de manière aléatoire quand plusieurs serveurs frontaux sont présents. Quand seul un équilibreur de charge interne gère une machine virtuelle, un groupe à haute disponibilité ou un groupe de machines virtuelles identiques, le mode SNAT par défaut est utilisé. |
| [Règles de trafic sortant](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Configuration déclarative de règles NAT de trafic sortant, en utilisant des adresses IP publiques, des préfixes IP publics ou les deux. Délai d'inactivité sortant configurable (4-120 minutes). Allocation de ports SNAT personnalisée | Non disponible |
| [Réinitialisation TCP pendant le délai d’inactivité](../articles/load-balancer/load-balancer-tcp-reset.md) | Activer la réinitialisation TCP (TCP RST) pendant le délai d’inactivité sur n’importe quelle règle | Non disponible |
| [Plusieurs serveurs frontaux](../articles/load-balancer/load-balancer-multivip-overview.md) | Entrant et [sortant](../articles/load-balancer/load-balancer-outbound-connections.md) | Entrant uniquement |
| Opérations de gestion | La plupart des opérations < 30 secondes | Généralement 60 à 90 secondes et plus |
| Contrat SLA | 99,99 % pour le chemin de données avec deux machines virtuelles saines. | Non applicable | 
| Tarifs | Facturation en fonction du nombre de règles configurées et des données associées aux ressources traitées en entrée et en sortie. | Aucuns frais |
|  |  |  |
