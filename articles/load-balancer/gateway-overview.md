---
title: Équilibreur de charge de passerelle (préversion)
titleSuffix: Azure Load Balancer
description: Vue d’ensemble de la référence (SKU) d’équilibreur de charge de passerelle pour Azure Load Balancer.
ms.service: load-balancer
author: asudbring
ms.author: allensu
ms.date: 11/02/2021
ms.topic: conceptual
ms.custom: ignite-fall-2021
ms.openlocfilehash: 22548d5bb75bb70c20296ddd68fde5d232213403
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097915"
---
# <a name="gateway-load-balancer-preview"></a>Équilibreur de charge de passerelle (préversion)

Un équilibreur de charge de passerelle est une référence (SKU) du portefeuille Azure Load Balancer pour des scénarios de haute performance et de haute disponibilité avec des appliances virtuelles réseau (NVA) tierces. Avec les fonctionnalités d’équilibreur de charge de passerelle, vous pouvez facilement déployer, mettre à l’échelle et gérer des appliances virtuelles réseau. Le chaînage d’un équilibreur de charge de passerelle à votre point de terminaison public ne nécessite qu’un seul clic. 

Vous pouvez insérer des appliances de manière transparente pour différents types de scénarios, tels que les suivants :

* Pare-feux
* Analyse de paquets avancée
* Systèmes de détection et de prévention des intrusions
* Mise en miroir du trafic
* DDoS inclus
* Appliances personnalisées

L’équilibreur de charge de passerelle vous permet de facilement ajouter ou supprimer des fonctionnalités réseau avancées sans occasionner de surcharge de gestion. Il fournit la technologie dont vous avez besoin pour vous assurer que tout le trafic vers un point de terminaison public est envoyé à l’appliance avant votre application. Dans les scénarios avec appliances virtuelles réseau, il est particulièrement important que les flux soient symétriques. L’équilibreur de charge de passerelle maintient l’adhérence du flux à une instance spécifique dans le pool principal, en même temps que la symétrie du flux. Par conséquent, un itinéraire cohérent vers votre appliance virtuelle réseau est assuré, sans qu’une configuration manuelle supplémentaire soit nécessaire. Ainsi, les paquets parcourent le même chemin d’accès réseau dans les deux directions, et les appliances qui ont besoin de cette fonctionnalité clé peuvent fonctionner sans problème.

La sonde d’intégrité écoute tous les ports et achemine le trafic vers les instances principales à l’aide de la règle des ports haute disponibilité. Le trafic échangé avec l’équilibreur de charge de passerelle utilise le protocole VXLAN. 

> [!IMPORTANT]
> L’équilibreur de charge de passerelle est actuellement en préversion.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="benefits"></a>Avantages

Un équilibreur de charge de passerelle présente les avantages suivants :

* Intégration d’appliances virtuelles en toute transparence dans le chemin d’accès réseau.

* Ajout ou suppression aisés d’appliances virtuelles réseau dans le chemin d’accès réseau. 

* Facilité de mise à l’échelle tout en gérant les coûts.

* Amélioration de la disponibilité des appliances virtuelles réseau.

* Chaîner des applications entre régions et abonnements

Un équilibreur de charge public standard ou la configuration IP d’une machine virtuelle peuvent être chaînés à un équilibreur de charge de passerelle. Une fois chaînés à un serveur frontal équilibreur de charge public standard ou à une configuration IP sur une machine virtuelle, aucune configuration supplémentaire n’est nécessaire pour garantir que le trafic échangé avec le point de terminaison de l’application est envoyé à l’équilibreur de charge de passerelle.

Le trafic passe du réseau virtuel du consommateur au réseau virtuel du fournisseur. Le trafic retourne ensuite au réseau virtuel du consommateur. Le réseau virtuel du consommateur et le réseau virtuel du fournisseur peuvent se trouver dans différents abonnements, locataires ou régions, ce qui a pour effet de supprimer la surcharge de gestion.

:::image type="content" source="./media/gateway-overview/gateway-load-balancer-diagram.png" alt-text="Diagramme d’équilibreur de charge de passerelle":::

*Figure : diagramme d’équilibreur de charge de passerelle.*

## <a name="components"></a>Composants

Un équilibreur de charge de passerelle est constitué des composants suivants :

* **Configuration IP frontale** : adresse IP de votre équilibreur de charge de passerelle. Cette adresse IP est privée uniquement. 

* **Règles d’équilibrage de charge** : une règle d’équilibreur de charge sert à définir la manière dont le trafic entrant est distribué à toutes les instances du pool principal. Une règle d’équilibrage de charge fait correspondre une configuration d’adresse IP frontale et un port donnés à plusieurs adresses IP frontales et ports. 

    * Les règles d’équilibreur de charge de passerelle peuvent uniquement être des règles de port haute disponibilité. 

    * Une règle d’équilibreur de charge de passerelle peut être associée à deux pools principaux. 

* **Pool principal** : groupe de machines virtuelles ou d’instances dans un groupe de machines virtuelles identiques qui servent la demande entrante. Pour une mise à l’échelle économique visant à répondre à des volumes élevés de trafic entrant, il est généralement recommandé d’ajouter davantage d’instances au pool de back-ends. Load Balancer se reconfigure instantanément par le biais d’une reconfiguration automatique quand vous effectuez un scale-up ou un scale-down des instances. L’ajout ou la suppression de machines virtuelles du pool principal reconfigure l’équilibreur de charge sans opérations supplémentaires. L’étendue du pool de back-ends est toute machine virtuelle d’un réseau virtuel unique. 

* **Interfaces de tunnel** : les pools principaux d’équilibreur de charge de passerelle comprennent un autre composant appelé interfaces de tunnel. L’interface de tunnel permet aux appliances du serveur principal de s’assurer que les flux réseau sont traités comme prévu. Chaque pool principal peut avoir jusqu’à 2 interfaces de tunnel. Les interfaces de tunnel peuvent être internes ou externes. Pour le trafic entrant en provenance de votre pool principal, vous devez utiliser le type externe. Pour le trafic sortant de votre appliance vers l’application, vous devez utiliser le type interne.

* **Chaîne** : un équilibreur de charge de passerelle peut être référencé par un serveur frontal équilibreur de charge public standard ou une configuration IP publique sur une machine virtuelle. L’ajout de fonctionnalités réseau avancées dans une séquence spécifique est appelé chaînage de services. Par conséquent, cette référence est appelée une chaîne.

## <a name="pricing"></a>Tarifs

L’équilibreur de charge de passerelle n’est pas facturé pendant la période de préversion. 

Pour connaître la tarification applicable lors de la mise en production pour la disponibilité générale, consultez [Tarification d’Azure Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="limitations"></a>Limitations

* L’équilibreur de charge de passerelle ne fonctionne pas avec le niveau Équilibreur de charge global.
* La prise en charge du portail de l’équilibreur de charge de passerelle est actuellement indisponible dans les régions cloud Chine et Government. Vous pouvez utiliser CLI, PowerShell, des modèles ou des alternatives.
* Le chaînage entre locataires n’est pas pris en charge via le portail Azure.

## <a name="next-steps"></a>Étapes suivantes

- Pour créer un équilibreur de charge de passerelle, consultez [Créer un équilibreur de charge de passerelle à l’aide du portail Azure](tutorial-gateway-portal.md).
- En savoir plus sur [Azure Load Balancer](load-balancer-overview.md).
