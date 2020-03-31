---
title: Présentation du service NAT de Réseau virtuel Azure
titlesuffix: Azure Virtual Network
description: Vue d’ensemble des fonctionnalités, des ressources, de l’architecture et de l’implémentation du service NAT de Réseau virtuel. Découvrez le fonctionnement du service NAT de Réseau virtuel et comment utiliser des ressources de passerelle NAT dans le cloud.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to learn more about Virtual Network NAT, its NAT gateway resources, and what I can use them for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2020
ms.author: allensu
ms.openlocfilehash: 4b34d4208d8686cdac3f8164d2cf7efb2d881346
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "79409896"
---
# <a name="what-is-virtual-network-nat"></a>Qu’est-ce que le service NAT de Réseau virtuel ?

Le service NAT (traduction d’adresses réseau) de Réseau virtuel simplifie la connectivité Internet sortante uniquement pour les réseaux virtuels. Quand il est configuré sur un sous-réseau, toute la connectivité sortante utilise vos adresses IP publiques statiques spécifiées.  Une connectivité sortante est possible sans équilibreur de charge ni adresses IP publiques directement attachées aux machines virtuelles. NAT est complètement managé et hautement résilient.

<!-- 
<img src="./media/nat-overview/flow-map.svg" width="270" align="center">
![Virtual Network NAT](./media/nat-overview/flow-map.svg)
-->

<p align="center">
  <img src="./media/nat-overview/flow-map.svg" width="256" title="Service NAT de Réseau virtuel">
</p>



*Figure : Service NAT de Réseau virtuel*

## <a name="static-ip-addresses-for-outbound-only"></a>Adresses IP statiques pour le trafic sortant uniquement

Une connectivité sortante peut être définie pour chaque sous-réseau avec NAT.  Plusieurs sous-réseaux au sein du même réseau virtuel peuvent avoir différents services NAT. Un sous-réseau est configuré en spécifiant la [ressource de passerelle NAT](./nat-gateway-resource.md) à utiliser. Tous les flux sortants UDP et TCP en provenance de toute instance de machine virtuelle utilisent la traduction d’adresses réseau (NAT). 

NAT est compatible avec les [ressources d’adresses IP publiques](./virtual-network-ip-addresses-overview-arm.md#standard) ou les [ressources de préfixes d’adresses IP publiques](./public-ip-address-prefix.md) de la référence SKU standard, ou avec une combinaison des deux.  Vous pouvez utiliser directement un préfixe d’adresse IP publique ou distribuer les adresses IP publiques du préfixe entre plusieurs ressources de passerelle NAT. NAT nettoie tout le trafic vers la plage d’adresses IP du préfixe.  Toute mise en liste verte des adresses IP de vos déploiements est à présent très simple.

Tout le trafic sortant du sous-réseau est traité par NAT automatiquement sans aucune configuration par le client.  Les routes définies par l’utilisateur ne sont pas nécessaires. NAT est prioritaire sur les autres [scénarios de trafic sortant](../load-balancer/load-balancer-outbound-connections.md) et remplace la destination Internet par défaut d’un sous-réseau.

## <a name="on-demand-snat-with-multiple-ip-addresses-for-scale"></a>SNAT à la demande avec plusieurs adresses IP à mettre à l’échelle

NAT utilise la « traduction d’adresses réseau de port » (PNAT ou PAT) et est recommandé pour la plupart des charges de travail. Les charges de travail dynamiques ou divergentes peuvent être facilement adaptées à l’allocation de flux de trafic sortant à la demande. Sont ainsi évités une planification préalable, une préallocation et un surprovisionnement des ressources sortantes. Les ressources de port SNAT sont partagées et disponibles sur tous les sous-réseaux à l’aide d’une ressource de passerelle NAT spécifique. Elles sont fournies en cas de besoin.

Une adresse IP publique attaché à NAT fournit jusqu’à 64 000 flux simultanés pour UDP et TCP. Vous pouvez commencer avec une seule adresse IP et augmenter jusqu’à 16 adresses IP publiques.

NAT autorise la création de flux depuis le réseau virtuel vers Internet. Le trafic de retour provenant d’Internet est uniquement autorisé en réponse à un flux actif.

Contrairement au service SNAT de flux sortant d’équilibreur de charge, NAT ne présente aucune restriction quant à l’adresse IP privée d’une instance de machine virtuelle pouvant établir des connexions sortantes.  Les configurations IP secondaires peuvent créer une connexion Internet sortante avec NAT.

## <a name="coexistence-of-inbound-and-outbound"></a>Coexistence des trafics entrant et sortant

NAT est compatible avec les ressources SKU standard suivantes :

- [Équilibreur de charge](../load-balancer/load-balancer-overview.md)
- [Adresse IP publique](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- [Préfixe d’adresse IP publique](../virtual-network/public-ip-address-prefix.md)

Utilisées avec NAT, ces ressources fournissent une connectivité Internet entrante à vos sous-réseaux. NAT fournit toute la connectivité Internet sortante à partir de vos sous-réseaux.

NAT et les fonctionnalités de la référence SKU standard compatibles connaissent la direction dans laquelle le flux a démarré. Des scénarios de trafic entrant et sortant peuvent coexister. Ces scénarios reçoivent les traductions d’adresses réseau appropriées, car ces fonctionnalités connaissent la direction du flux. 

<!-- 
<img src="./media/nat-overview/flow-direction4.svg" width="500" align="center">
![Virtual Network NAT flow direction](./media/nat-overview/flow-direction4.svg)
-->
<p align="center">
  <img src="./media/nat-overview/flow-direction4.svg" width="512" title="Direction du flux NAT de Réseau virtuel">
</p>

*Figure : Direction du flux NAT de Réseau virtuel*

## <a name="fully-managed-highly-resilient"></a>Complètement managé et hautement résilient

NAT a déjà fait l’objet d’un scale-out complet dès le départ. Aucune opération de montée en puissance ni de scale-out n’est nécessaire.  Azure gère le fonctionnement de NAT pour vous.  NAT a toujours plusieurs domaines d’erreur et peut supporter plusieurs défaillances sans interruption de service.

## <a name="tcp-reset-for-unrecognized-flows"></a>Réinitialisation TCP pour les flux non reconnus

Le côté privé de NAT envoie des paquets de réinitialisation TCP pour les tentatives de communication sur une connexion TCP qui n’existe pas. Par exemple, des connexions ont atteint le délai d’inactivité. Le paquet reçu suivant retourne une réinitialisation TCP à l’adresse IP privée pour signaler et forcer la fermeture de la connexion.

Le côté public de NAT ne génère pas de paquets de réinitialisation TCP ni tout autre trafic.  Seul le trafic produit par le réseau virtuel du client est émis.

## <a name="configurable-tcp-idle-timeout"></a>Délai d’inactivité TCP configurable

Un délai d’inactivité TCP par défaut de 4 minutes est utilisé et peut être augmenté à 120 minutes maximum. Toute activité sur un flux peut également réinitialiser le délai d’inactivité, y compris les conservations de connexion active TCP.

## <a name="regional-or-zone-isolation-with-availability-zones"></a>Isolement régional ou zonal avec des zones de disponibilité

Par défaut, le service NAT est régional. Quand vous créez des scénarios de [zones de disponibilité](../availability-zones/az-overview.md), NAT peut être isolé dans une zone spécifique (déploiement zonal).

<!-- 
<img src="./media/nat-overview/az-directions.svg" width="500" align="center">
![Virtual Network NAT with availability zones](./media/nat-overview/az-directions.svg)
-->

<p align="center">
  <img src="./media/nat-overview/az-directions.svg" width="512" title="Service NAT de Réseau virtuel avec des zones de disponibilité">
</p>

*Figure : Service NAT de Réseau virtuel avec des zones de disponibilité*

## <a name="multi-dimensional-metrics-for-observability"></a>Métriques multidimensionnelles pour l’observabilité

Vous pouvez superviser le fonctionnement de votre NAT par le biais de métriques multidimensionnelles exposées dans Azure Monitor. Ces métriques peuvent servir à observer l’utilisation et à résoudre les problèmes.  Les ressources de passerelle NAT exposent les métriques suivantes :
- Octets
- Paquets
- Paquets ignorés
- Nombre total de connexions SNAT
- Transitions d’état de connexion SNAT par intervalle.

<!-- "ADD when PM is done" Learn more about [NAT gateway metrics](./nat-gateway-metrics.md) -->

## <a name="sla"></a>Contrat SLA

En disponibilité générale, le chemin de données NAT est au moins disponible à 99,9 %.


## <a name="pricing"></a>Tarifs

La passerelle NAT est facturée avec deux compteurs distincts :

| Compteur | Tarif |
| --- | --- |
| Heures de ressource | 0,045 dollars/heure |
| Données traitées | 0,045 dollar/Go |

Les heures de ressource tiennent compte de la durée pendant laquelle une ressource de passerelle NAT existe.
Les données traitées tiennent compte de tout le trafic traité par une ressource de passerelle NAT.

## <a name="availability"></a>Disponibilité

Le service NAT de réseau virtuel et la ressource de passerelle NAT sont disponibles dans toutes les [régions](https://azure.microsoft.com/global-infrastructure/regions/) du cloud public Azure.

## <a name="support"></a>Support

NAT est pris en charge par le biais des canaux normaux.

## <a name="feedback"></a>Commentaires

Nous aimerions savoir comment nous pouvons améliorer le service. Proposez-nous de nouvelles fonctionnalités et votez pour celles que vous préférez en nous contactant sur [UserVoice for NAT](https://aka.ms/natuservoice).


## <a name="limitations"></a>Limites

* NAT est compatible avec des ressources d’adresses IP publiques, de préfixes d’adresses IP publiques et d’équilibreur de charge de la référence SKU standard. Les ressources de base (par exemple, un équilibreur de charge de base) et tous les produits qui en dérivent ne sont pas compatibles avec NAT.  Les ressources de base doivent être placées sur un sous-réseau non configuré avec NAT.
* La famille d’adresses IPv4 est prise en charge.  NAT n’interagit pas avec la famille d’adresses IPv6.  NAT ne peut pas être déployé sur un sous-réseau avec un préfixe IPv6.
* La journalisation des flux de groupe de sécurité réseau n’est pas prise en charge lors de l’utilisation de NAT.
* NAT ne peut pas s’étendre sur plusieurs réseaux virtuels.

## <a name="next-steps"></a>Étapes suivantes

* Apprenez-en davantage sur la [ressource de passerelle NAT](./nat-gateway-resource.md).
* [Utilisez UserVoice pour nous faire part des prochains développements dont vous aimeriez bénéficier concernant le service NAT de réseau virtuel](https://aka.ms/natuservoice).

