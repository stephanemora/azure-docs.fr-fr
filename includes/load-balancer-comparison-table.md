---
title: Fichier Include
description: Fichier Include
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 9/26/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 74835734a1dd37e07efa96fd3aa76fc0d349e47f
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47454445"
---
| | Référence SKU standard | Référence SKU De base |
| --- | --- | --- |
| Taille de pool de serveur principal | Prend en charge jusqu’à 1000 instances | Prend en charge jusqu’à 100 instances |
| Points de terminaison du pool du serveur principal | Toute machine virtuelle dans un seul réseau virtuel, y compris la combinaison de machines virtuelles, de groupes à haute disponibilité et de groupes de machines virtuelles identiques. | Machines virtuelles dans un groupe à haute disponibilité ou un groupe de machines virtuelles identiques unique. |
| [Sondes d’intégrité](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Comportement en cas de panne de sonde d’intégrité](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | Les connexions TCP restent actives quand la sonde d’instance est en panne __et__ quand toutes les sondes sont en panne. | Les connexions TCP restent actives quand la sonde d’instance est en panne. Toutes les connexions TCP sont arrêtées quand toutes les sondes sont en panne. |
| Zones de disponibilité | Dans la référence (SKU) de base, serveurs frontaux redondants interzone et zonaux pour le trafic entrant et sortant, mappages de flux sortants protégés contre les défaillances de zone, équilibrage de charge entre les zones. | Non disponible |
| Diagnostics | Azure Monitor, métriques à plusieurs dimensions, notamment les compteurs d’octets et de paquets, état de la sonde d’intégrité, tentatives de connexion (TCP SYN), intégrité de la connexion sortante (flux SNAT réussies et échouées), mesures de plan de données actives | Azure Log Analytics pour l’équilibreur de charge public uniquement, alerte d’épuisement des ports SNAT, mesure de l’intégrité du pool du serveur principal. |
| Ports HA | Équilibreur de charge interne | Non disponible |
| Sécurisé par défaut | Par défaut le trafic entrant est fermé pour les points de terminaison d’équilibreur de charge et d’adresse IP publique et un groupe de sécurité réseau doit être utilisé pour mettre explicitement sur liste verte le trafic qui doit circuler. | Ouvert par défaut, groupe de sécurité réseau facultatif. |
| [Connexions sortantes](../articles/load-balancer/load-balancer-outbound-connections.md) | Vous pouvez définir explicitement des règles NAT de trafic sortant basées sur un pool avec des [règles de trafic sortant](../articles/load-balancer/load-balancer-outbound-rules-overview.md). Vous pouvez utiliser plusieurs frontends avec refus par règle d’équilibrage de charge. Un scénario sortant _doit_ être explicitement créé pour que la machine virtuelle puisse utiliser une connectivité sortante.  Les points de terminaison de service du réseau virtuel peuvent être atteints sans connectivité sortante et ne sont pas comptabilisés dans les données traitées.  Toutes les adresses IP publiques, y compris les services PaaS Azure non disponibles en tant que points de terminaison de service du réseau virtuel, doivent être atteintes via la connectivité sortante et sont comptabilisées dans les données traitées. Quand seul un équilibreur de charge interne est utilisé par une machine virtuelle, les connexions sortantes via les règles SNAT par défaut ne sont pas disponibles. Utilisez plutôt des [règles de trafic sortant](../articles/load-balancer/load-balancer-outbound-rules-overview.md). La programmation de SNAT sortante est un protocole de transport spécifique basé sur le protocole de la règle d’équilibrage de charge entrant. | Serveur frontal unique, sélectionné de manière aléatoire quand plusieurs serveurs frontaux sont présents.  Quand seul un équilibreur de charge interne gère une machine virtuelle, le mode SNAT par défaut est utilisé. |
| [Règles de trafic sortant](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Configuration déclarative de règles NAT de trafic sortant, notamment la ou les adresses IP publiques ou préfixes d’adresse IP publique, le délai d’inactivité du trafic sortant configurable, l’allocation de port SNAT personnalisé | Non disponible |
|  [Réinitialisation TCP pendant le délai d’inactivité](../articles/load-balancer/load-balancer-tcp-reset.md) | Activer la réinitialisation TCP (TCP RST) pendant le délai d’inactivité sur n’importe quelle règle | Non disponible |
| [Plusieurs serveurs frontaux](../articles/load-balancer/load-balancer-multivip-overview.md) | Entrant et [sortant](../articles/load-balancer/load-balancer-outbound-connections.md) | Entrant uniquement |
| Opérations de gestion | La plupart des opérations < 30 secondes | Généralement 60 à 90 secondes et plus. |
| Contrat SLA | 99,99 % pour le chemin de données avec deux machines virtuelles saines. | [Implicite dans le SLA de la machine virtuelle](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/). | 
| Tarifs | Facturation en fonction du nombre de règles configurées et des données associées aux ressources traitées en entrée ou en sortie.  | Aucuns frais |
|  |  |  |
