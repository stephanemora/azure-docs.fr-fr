---
title: Vue d’ensemble des ports haute disponibilité dans Azure | Microsoft Docs
description: Découvrez plus d’informations sur l’équilibrage de charge des ports haute disponibilité sur un équilibreur de charge interne.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/07/2018
ms.author: kumud
ms.openlocfilehash: 716a3dafe08e896924bd28e44d69141e4c229687
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005501"
---
# <a name="high-availability-ports-overview"></a>Vue d’ensemble des ports haute disponibilité

Azure Standard Load Balancer vous permet d’équilibrer la charge des flux TCP et UDP sur tous les ports simultanément quand vous utilisez un équilibreur de charge interne. 

Une règle de ports haute disponibilité est une variante d’une règle d’équilibrage de charge configurée sur un équilibreur de charge standard interne. Vous pouvez simplifier l’utilisation d’un équilibreur de charge en fournissant une seule règle pour équilibrer la charge de tous les flux TCP et UDP qui arrivent sur tous les ports d’un équilibreur de charge standard interne. La décision d’équilibrage de charge est prise par flux. Cette action est basée sur la connexion à 5 tuples suivante : adresse IP source, port source, adresse IP de destination, port de destination et protocole.

La fonctionnalité des ports haute disponibilité est utile dans les scénarios critiques, comme pour la haute disponibilité et la mise à l’échelle d’appliances virtuelles réseau dans des réseaux virtuels. La fonctionnalité peut également servir quand un grand nombre de ports doit avoir une charge équilibrée. 

La fonctionnalité des ports haute disponibilité est configurée quand vous affectez la valeur **0** aux ports frontend et backend, et la valeur **Tous** au protocole. La ressource d’équilibreur de charge interne équilibre alors tous les flux TCP et UDP, quel que soit le nombre de ports.

## <a name="why-use-ha-ports"></a>Pourquoi utiliser des ports haute disponibilité ?

### <a name="nva"></a>Appliances virtuelles réseau

Vous pouvez utiliser des appliances virtuelles réseau pour sécuriser votre charge de travail Azure contre plusieurs types de menaces de sécurité. Quand vous utilisez des appliances virtuelles réseau dans ces scénarios, elles doivent être fiables, hautement disponibles et s’adapter à la demande.

Vous pouvez atteindre ces objectifs en ajoutant simplement des instances d’appliances virtuelles réseau au pool backend de votre équilibreur de charge interne et en configurant une règle d’équilibreur de charge pour les ports haute disponibilité.

Les ports haute disponibilité présentent les avantages suivants pour les scénarios de haute disponibilité des appliances virtuelles réseau :
- Basculement rapide sur des instances saines, avec des sondes d’intégrité par instance
- Meilleures performances avec scale-out vers des instances en mode *n*-actif
- Scénarios en mode *n*-actif et actif-passif
- Plus besoin d’utiliser des solutions complexes comme les nœuds Apache Zookeeper pour la surveillance des appliances

Le diagramme suivant présente un déploiement de réseau virtuel hub-and-spoke. Les spokes forcent le tunneling de leur trafic vers le réseau virtuel du hub et via l’appliance virtuelle réseau avant de quitter l’espace de confiance. Les appliances virtuelles réseau se trouvent derrière un équilibreur de charge standard interne avec une configuration de ports haute disponibilité. Tout le trafic peut être traité et transféré en conséquence.

![Diagramme d’un réseau virtuel hub-and-spoke avec des appliances virtuelles réseau déployées en mode de haute disponibilité](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Si vous utilisez des appliances virtuelles réseau, renseignez-vous auprès de leurs fournisseurs sur l’utilisation optimale des ports haute disponibilité et les scénarios pris en charge.

### <a name="load-balancing-large-numbers-of-ports"></a>Équilibrage de charge d’un grand nombre de ports

Vous pouvez aussi utiliser des ports haute disponibilité pour les applications qui nécessitent l’équilibrage de charge d’un grand nombre de ports. Vous pouvez simplifier ces scénarios à l’aide d’un [équilibreur de charge standard](load-balancer-standard-overview.md) interne avec des ports haute disponibilité. Une seule règle d’équilibrage de charge remplace plusieurs règles individuelles d’équilibrage de charge, une pour chaque port.

## <a name="region-availability"></a>Disponibilité des régions

La fonctionnalité de ports haute disponibilité est disponible dans toutes les régions Azure globales.

## <a name="supported-configurations"></a>Configurations prises en charge

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Une seule configuration de ports haute disponibilité d’adresse IP non flottante (pas de retour direct du serveur) sur un équilibreur de charge standard interne

Il s’agit d’une configuration de ports haute disponibilité de base. Vous pouvez configurer une règle d’équilibrage de charge de ports haute disponibilité sur une seule adresse IP frontend en effectuant les opérations suivantes :
1. Lors de la configuration de l’équilibreur de charge standard, cochez la case **Ports HA** dans la configuration de règle d’équilibreur de charge.
2. Pour **IP flottante**, sélectionnez **Désactivée**.

Cette configuration n’autorise pas d’autre configuration de règle d’équilibrage de charge sur la ressource d’équilibreur de charge actuelle, ni d’autre configuration de ressource d’équilibreur de charge interne pour l’ensemble donné d’instances backend.

Vous pouvez toutefois configurer un équilibreur de charge standard public pour les instances backend en plus de cette règle de ports haute disponibilité.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Une seule configuration de ports haute disponibilité d’adresse IP flottante (retour direct du serveur) sur un équilibreur de charge standard interne

De la même façon, vous pouvez configurer votre équilibreur de charge pour utiliser une règle d’équilibrage de charge avec **Port HA** avec un seul frontend en affectant à l’option **IP flottante** la valeur **Activée**. 

Cette configuration vous permet d’ajouter d’autres règles d’équilibrage de charge d’adresse IP flottante et/ou un équilibreur de charge public. Toutefois, vous ne pouvez pas utiliser de configuration d’équilibrage de charge de ports haute disponibilité d’adresse IP non flottante en plus de cette configuration.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Plusieurs configurations de ports haute disponibilité sur un équilibreur de charge standard interne

Si votre scénario nécessite la configuration de plusieurs frontends de ports haute disponibilité pour le même pool backend, vous pouvez y parvenir en : 
- configurant plusieurs adresses IP privées frontend pour une ressource d’équilibreur de charge standard interne ;
- configurant plusieurs règles d’équilibrage de charge, où chaque règle a une adresse IP frontend unique sélectionnée ;
- sélectionnant l’option **Ports HA** et en définissant l’option **IP flottante** sur **Activée** pour toutes les règles d’équilibrage de charge.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>Équilibreur de charge interne avec ports haute disponibilité et équilibreur de charge public sur la même instance backend

Vous pouvez configurer *une* ressource d’équilibreur de charge standard public pour les ressources backend ainsi qu’un seul équilibreur de charge standard interne avec des ports haute disponibilité.

>[!NOTE]
>Cette fonctionnalité est actuellement disponible via les modèles Azure Resource Manager, mais pas via le portail Azure.

## <a name="limitations"></a>Limitations

- La configuration des ports haute disponibilité est disponible uniquement pour les équilibreurs de charge internes. Elle n’est pas disponible pour les équilibreurs de charge publics.

- La combinaison d’une règle d’équilibrage de charge de ports haute disponibilité et d’une règle d’équilibrage de charge sans ports haute disponibilité n’est pas prise en charge.

- La fonctionnalité des ports haute disponibilité n’est pas disponible pour IPv6.

- La symétrie des flux dans les scénarios d’appliances virtuelles réseau est uniquement prise en charge avec une seule carte réseau. Consultez la description et le diagramme des [appliances virtuelles réseau](#nva). Toutefois, si une NAT de destination peut fonctionner dans votre scénario, vous pouvez l’utiliser pour vous assurer que l’équilibreur de charge interne envoie le trafic de retour à la même appliance virtuelle réseau.


## <a name="next-steps"></a>Étapes suivantes

- [Configurer des ports haute disponibilité sur un équilibreur de charge standard interne](load-balancer-configure-ha-ports.md)
- [Présentation de la référence Standard d’Azure Load Balancer (préversion)](load-balancer-standard-overview.md)
