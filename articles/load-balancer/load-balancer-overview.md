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
ms.date: 1/14/2019
ms.author: allensu
ms.openlocfilehash: dc986d40d50b93720c87ba36d265ed3044b0abc9
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045396"
---
# <a name="what-is-azure-load-balancer"></a>Qu’est-ce qu’Azure Load Balancer ?

L’*équilibrage de charge* correspond à une répartition équilibrée de la charge ou du trafic réseau entrant au sein d’un groupe de ressources ou serveurs back-end. Azure propose une [variété d’options d’équilibrage de charge](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) que vous pouvez choisir en fonction de vos besoins. Ce document présente Azure Load Balancer.

Azure Load Balancer opère à la couche quatre du modèle OSI (Open Systems Interconnection). Il s’agit du point de contact unique pour les clients. Load Balancer distribue les nouveaux flux entrants arrivant sur le serveur front-end de Load Balancer sur des instances du pool de back-ends. Ces flux dépendent des règles d’équilibrage de charge et des sondes d’intégrité configurées. Les instances de pool de back-ends peuvent être des machines virtuelles Azure ou des instances d’un groupe de machines virtuelles identiques.


Un **[équilibreur de charge public](./concepts-limitations.md#publicloadbalancer)** permet des connexions sortantes pour les machines virtuelles de votre réseau virtuel. Ces connexions s’effectuent par la traduction de leurs adresses IP privées en adresses IP publiques. Les équilibreurs de charge publics sont utilisés pour équilibrer la charge du trafic Internet sur vos machines virtuelles.

Un **[équilibreur de charge interne (ou privé)](./concepts-limitations.md#internalloadbalancer)** est utilisé lorsque des adresses IP privées sont nécessaires uniquement au niveau du serveur front-end. Les équilibreurs de charge internes sont utilisés pour équilibrer la charge du trafic au sein d’un réseau virtuel. Vous pouvez accéder au front-end d’un équilibreur de charge à partir d’un réseau local dans le cadre d’un scénario hybride.

<div align="center">
  <img src='./media/load-balancer-overview/IC744147.png'>
</div>

*Figure : Équilibrage d’applications multiniveaux à l’aide d’un Load Balancer public et d’un Load Balancer interne*

Pour plus d’informations sur les composants de l’équilibreur de charge, consultez [Composants et limitations d’Azure Load Balancer](./concepts-limitations.md).

>[!NOTE]
> Microsoft recommande [Standard Load Balancer](./load-balancer-standard-overview.md).
Les machines virtuelles autonomes, les groupes à haute disponibilité et les groupes de machines virtuelles identiques peuvent uniquement être connectés à une référence SKU, jamais aux deux. Les références SKU de Load Balancer et des adresses IP publiques doivent correspondre lorsque vous les utilisez avec des adresses IP publiques. Les références SKU de Load Balancer et des adresses IP publiques ne sont pas mutables.

## <a name="why-use-azure-load-balancer"></a>Pourquoi utiliser Azure Load Balancer ?
Azure Load Balancer vous permet de mettre à l’échelle vos applications et de créer des services hautement disponibles. Load Balancer prend en charge à la fois les scénarios entrants et les scénarios sortants. Un équilibreur de charge offre une latence faible et un débit élevé, et peut augmenter l’échelle jusqu’à des millions de flux pour toutes les applications TCP et UDP.

Voici les principaux scénarios que vous pouvez gérer avec Azure Load Balancer :

- Équilibrer la charge du trafic **[interne](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-manage-portal)** et **[externe](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-internal-portal)** sur les machines virtuelles Azure

- Augmenter la disponibilité en répartissant les ressources **[au sein des zones](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zonal-portal)** et **[entre les zones](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal)**

- Configurer la **[connectivité sortante](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)** pour les machines virtuelles Azure

- Utiliser des **[sondes d’intégrité](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** pour superviser les ressources à charge équilibrée

- Utiliser le **[réacheminement de port ](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** pour accéder aux machines virtuelles d’un réseau virtuel via une adresse IP publique et un port

- Activer la prise en charge de l’ **[équilibrage de charge](https://docs.microsoft.com/azure/virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell)** de **[IPv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)**

- Tirer parti des **[métriques et des diagnostics](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)** pour Azure Load Balancer avec **[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)**

- Équilibrer la charge des services sur **[plusieurs ports, plusieurs adresses IP ou les deux](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview)**

- Déplacer des ressources d’équilibreur de charge **[internes](https://docs.microsoft.com/azure/load-balancer/move-across-regions-internal-load-balancer-portal)** et **[externes](https://docs.microsoft.com/azure/load-balancer/move-across-regions-external-load-balancer-portal)** entre les différentes régions Azure

- Équilibrer simultanément la charge du flux TCP et UDP sur tous les ports à l’aide des **[ports haute disponibilité](https://docs.microsoft.com/azure/load-balancer/load-balancer-ha-ports-overview)** .

## <a name="pricing"></a>Tarifs

L'utilisation de Standard Load Balancer est facturée.

* Nombre de règles de trafic sortant et d’équilibrage de la charge configurées. Les règles NAT de trafic entrant ne sont pas comptabilisées dans le nombre total de règles.
* Volume entrant et sortant de données traitées indépendamment des règles.

Pour plus d’informations sur la tarification de Load Balancer Standard, consultez [Tarification de Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

La référence SKU De base de Load Balancer est proposée gratuitement.

## <a name="sla"></a>Contrat SLA

Pour plus d’informations sur le contrat de niveau de service (SLA) de Load Balancer Standard, consultez [SLA pour Load Balancer](https://aka.ms/lbsla).

## <a name="next-steps"></a>Étapes suivantes

Pour bien démarrer avec les équilibreurs de charge, consultez [Créer un équilibreur de charge standard public](quickstart-load-balancer-standard-public-portal.md).

Pour plus d’informations sur les limitations et les composants d’Azure Load Balancer, consultez [Concepts et limitations d’Azure Load Balancer](./concepts-limitations.md).
