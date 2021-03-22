---
title: Qu’est-ce qu’Azure Load Balancer ?
titleSuffix: Azure Load Balancer
description: Présentation des fonctionnalités, de l'architecture et de l'implémentation de l'équilibrage de charge Azure. Découvrez comment fonctionne Load Balancer et comment l’utiliser dans le cloud.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/25/2021
ms.author: allensu
ms.openlocfilehash: 51ceb72d53f78264edcadd2255e20c8fbdac2cae
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181208"
---
# <a name="what-is-azure-load-balancer"></a>Qu’est-ce qu’Azure Load Balancer ?

L’*équilibrage de charge* correspond à une répartition équilibrée de la charge ou du trafic réseau entrant au sein d’un groupe de ressources ou serveurs back-end. 

Azure Load Balancer opère sur la couche 4 du modèle OSI (Open Systems Interconnection). Il s’agit du point de contact unique pour les clients. Load Balancer distribue les flux entrants arrivant sur le serveur frontal de l’équilibreur de charge aux instances de pool back-end. Ces flux dépendent des règles d’équilibrage de charge et des sondes d’intégrité configurées. Les instances de pool de back-ends peuvent être des machines virtuelles Azure ou des instances d’un groupe de machines virtuelles identiques.

Un **[équilibreur de charge public](./components.md#frontend-ip-configurations)** permet des connexions sortantes pour les machines virtuelles de votre réseau virtuel. Ces connexions s’effectuent par la traduction de leurs adresses IP privées en adresses IP publiques. Les équilibreurs de charge publics sont utilisés pour équilibrer la charge du trafic Internet sur vos machines virtuelles.

Un **[équilibreur de charge interne (ou privé)](./components.md#frontend-ip-configurations)** est utilisé lorsque des adresses IP privées sont nécessaires uniquement au niveau du serveur front-end. Les équilibreurs de charge internes sont utilisés pour équilibrer la charge du trafic au sein d’un réseau virtuel. Vous pouvez accéder au front-end d’un équilibreur de charge à partir d’un réseau local dans le cadre d’un scénario hybride.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" alt="Figure depicts both public and internal load balancers directing traffic to port 80 on multiple servers on a Web tier and port 443 on multiple servers on a business tier." width="512" title="Azure Load Balancer">
</p>

*Figure : Équilibrage d’applications multiniveaux à l’aide d’un Load Balancer public et d’un Load Balancer interne*

Pour plus d’informations sur les composants de l’équilibreur de charge, consultez [Composants Azure Load Balancer](./components.md).

>[!NOTE]
> Azure offre une suite de solutions d’équilibrage de charge entièrement managées pour vos scénarios. 
> * Si vous cherchez à effectuer un routage global basé sur DNS et que vous **ne répondez pas** aux exigences concernant la terminaison de protocole TLS (« déchargement SSL ») ou le traitement de la couche Application ou par requête HTTP/HTTPS, passez en revue [Traffic Manager](../traffic-manager/traffic-manager-overview.md). 
> * Si vous souhaitez équilibrer la charge entre vos serveurs dans une région au niveau de la couche Application, consultez [Application Gateway](../application-gateway/overview.md).
> * Si vous avez besoin d’optimiser le routage global de votre trafic web et d’optimiser les performances et la fiabilité des utilisateurs finaux grâce à un basculement global rapide, consultez [Front Door](../frontdoor/front-door-overview.md).
> 
> Vos scénarios de bout en bout peuvent tirer parti de la combinaison de ces solutions en fonction de vos besoins.
> Pour obtenir une comparaison des options d’équilibrage de charge Azure, consultez [Vue d’ensemble des options d’équilibrage de charge dans Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).


## <a name="why-use-azure-load-balancer"></a>Pourquoi utiliser Azure Load Balancer ?
Azure Load Balancer vous permet de mettre à l’échelle vos applications et de créer des services hautement disponibles. Load Balancer prend en charge à la fois les scénarios entrants et les scénarios sortants. Un équilibreur de charge offre une latence faible et un débit élevé, et peut augmenter l’échelle jusqu’à des millions de flux pour toutes les applications TCP et UDP.

Voici les principaux scénarios que vous pouvez gérer avec Azure Standard Load Balancer :

- Équilibrer la charge du trafic **[interne](./quickstart-load-balancer-standard-internal-portal.md)** et **[externe](./quickstart-load-balancer-standard-public-portal.md)** sur les machines virtuelles Azure

- Augmenter la disponibilité en répartissant les ressources **[au sein des zones](./tutorial-load-balancer-standard-public-zonal-portal.md)** et **[entre les zones](./tutorial-load-balancer-standard-public-zone-redundant-portal.md)**

- Configurer la **[connectivité sortante](./load-balancer-outbound-connections.md)** pour les machines virtuelles Azure

- Utiliser des **[sondes d’intégrité](./load-balancer-custom-probe-overview.md)** pour superviser les ressources à charge équilibrée

- Utiliser le **[réacheminement de port](./tutorial-load-balancer-port-forwarding-portal.md)** pour accéder aux machines virtuelles d’un réseau virtuel via une adresse IP publique et un port

- Activer la prise en charge de l’ **[équilibrage de charge](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)** de **[IPv6](../virtual-network/ipv6-overview.md)**

- L’équilibreur de charge Standard fournit des métriques multidimensionnelles via [Azure Monitor](../azure-monitor/overview.md).  Ces métriques peuvent être filtrées, regroupées et réparties pour une dimension donnée.  Elles fournissent des analyses en cours et historiques sur les performances et l’intégrité de votre service. [Insights pour Azure Load Balancer](./load-balancer-insights.md) offre un tableau de bord préconfiguré avec des visualisations utiles pour ces métriques.  Resource Health est également pris en charge. Pour plus d’informations, consultez **[Diagnostics des équilibreurs de charge standard](load-balancer-standard-diagnostics.md)** .

- Équilibrer la charge des services sur **[plusieurs ports, plusieurs adresses IP ou les deux](./load-balancer-multivip-overview.md)**

- Déplacer des ressources d’équilibreur de charge **[internes](./move-across-regions-internal-load-balancer-portal.md)** et **[externes](./move-across-regions-external-load-balancer-portal.md)** entre les différentes régions Azure

- Équilibrer simultanément la charge du flux TCP et UDP sur tous les ports à l’aide des **[ports haute disponibilité](./load-balancer-ha-ports-overview.md)** .

### <a name="secure-by-default"></a><a name="securebydefault"></a>Sécuriser par défaut

* L’équilibreur de charge Standard repose sur le modèle de sécurité réseau Confiance Zéro.

* Standard Load Balancer est sécurisé par défaut et fait partie de votre réseau virtuel. Le réseau virtuel est un réseau privé et isolé.  

* Les équilibreurs de charge Standard et les IP publiques standard sont fermés aux connexions entrantes, sauf si celles-ci sont ouvertes par les groupes de sécurité du réseau. Les groupes de sécurité réseau sont utilisés pour autoriser explicitement le trafic autorisé.  Si vous n’avez pas de groupe de sécurité réseau sur un sous-réseau ou une carte réseau de votre ressource de machine virtuelle, le trafic n’est pas autorisé à atteindre cette ressource. Pour plus d’informations sur les groupes de sécurité réseau et la façon de les appliquer à votre scénario, consultez [Groupes de sécurité réseau](../virtual-network/network-security-groups-overview.md).

* Par défaut, un équilibreur de charge De base est ouvert sur Internet. 

* L’équilibreur de charge ne stocke pas les données des clients.

## <a name="pricing-and-sla"></a>Tarifs et contrat SLA

Pour plus d’informations sur la tarification d’un équilibreur de charge Standard, consultez [Tarification Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).
L’équilibreur de charge De base est proposé gratuitement.
Consultez [Contrat SLA pour équilibreur de charge](https://aka.ms/lbsla). Un équilibreur de charge De base n’a pas de contrat SLA.

## <a name="whats-new"></a>Nouveautés

Abonnez-vous au flux RSS et découvrez les dernières mises à jour des fonctionnalités Azure Load Balancer dans la page [Mises à jour Azure](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer).

## <a name="next-steps"></a>Étapes suivantes

Pour bien démarrer avec les équilibreurs de charge, consultez [Créer un équilibreur de charge standard public](quickstart-load-balancer-standard-public-portal.md).

Pour plus d’informations sur les limitations et les composants d’Azure Load Balancer, consultez [Composants Azure Load Balancer](./components.md) et [Concepts relatifs à Azure Load Balancer](./concepts.md).