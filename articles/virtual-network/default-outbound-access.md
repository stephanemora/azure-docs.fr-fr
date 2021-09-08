---
title: Accès sortant par défaut dans Azure
titleSuffix: Azure Virtual Network
description: En savoir plus sur l’accès sortant par défaut dans Azure.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: allensu
ms.openlocfilehash: f4c06fb4c97ea38c494f18e5bf72e9eb015299a8
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122535390"
---
# <a name="default-outbound-access-in-azure"></a>Accès sortant par défaut dans Azure

Dans Azure, les machines virtuelles créées dans un réseau virtuel sans connectivité sortante explicite définie se voient attribuer une adresse IP publique sortante par défaut. Cette adresse IP active la connectivité sortante à partir des ressources sur Internet. Cet accès est appelé accès sortant par défaut. 

Les ordinateurs virtuels sont des exemples de connectivité sortante explicite :

* Créé dans un sous-réseau associé à une Passerelle NAT.
* Dans le pool principal d’un équilibreur de charge standard avec des règles sortantes définies.
* Dans le pool principal de l’équilibreur de charge public de base.
* Des machines virtuelles avec des adresses IP publiques explicitement associées.

## <a name="how-is-default-outbound-access-provided"></a>Comment l’accès sortant par défaut est-il fourni ?

L’adresse IPv4 publique utilisée pour l’accès est appelée adresse IP d’accès sortant par défaut. Cette adresse IP est implicite et appartient à Microsoft. Cette adresse IP est sujette à modification et il n’est pas recommandé d’en dépendre pour les charges de travail de production.

## <a name="when-is-default-outbound-access-provided"></a>Quand l’accès sortant par défaut est-il fourni ?

Si vous déployez une machine virtuelle dans Azure et que celle-ci ne dispose pas d’une connectivité sortante explicite, une adresse IP d’accès sortant par défaut lui est affectée.
## <a name="why-is-disabling-default-outbound-access-recommended"></a>Pourquoi la désactivation de l’accès sortant par défaut est-elle recommandée ?

* Sécurisé par défaut
    
    * Il n’est pas recommandé d’ouvrir un réseau virtuel sur Internet par défaut à l’aide du principe de sécurité réseau de niveau de confiance zéro.

* Explicite versus implicite

    * Il est recommandé d’avoir des méthodes de connectivité explicites plutôt qu’implicites lorsque vous autorisez l’accès aux ressources de votre réseau virtuel.

* Perte d’adresse IP

    * L’adresse IP d’accès sortant par défaut n’appartient pas aux clients. Les Protocoles Internet (IP) sont susceptibles d’être modifiés.  Toute dépendance sur cette adresse IP peut entraîner des problèmes à l’avenir.

## <a name="how-can-i-disable-default-outbound-access"></a>Comment puis-je désactiver l’accès sortant par défaut ?

Il existe plusieurs façons de désactiver l’accès sortant par défaut :

1.  Ajouter une méthode de connectivité sortante explicite

    * Associer une passerelle NAT au sous-réseau de votre machine virtuelle.

    * Associer un équilibreur de charge standard à des règles de trafic sortant configurées.

    * Associer une adresse IP publique à l’interface réseau de la machine virtuelle.

2.  Utiliser le mode d’orchestration flexible pour les paramètres de mise à l’échelle des machines virtuelles.

    * Les paramètres de mise à l’échelle flexibles sont sécurisés par défaut. Aucune instance créée via des paramètres de mise à l’échelle flexibles ne sera associée à l’adresse IP d’accès sortant par défaut. Pour plus d’informations, veuillez vous référer au [mode d’orchestration flexible pour les paramètres de mise à l’échelle des machines virtuelles](https://aka.ms/vmssflex/docs)

## <a name="if-i-need-outbound-access-what-is-the-recommended-way"></a>Si j’ai besoin d’un accès sortant, quelle est la méthode recommandée ?

La passerelle NAT est l’approche recommandée pour disposer d’une connectivité sortante explicite. Un pare-feu peut également être utilisé pour fournir cet accès.

## <a name="limitations"></a>Limites

* La connectivité peut être nécessaire pour les Mises à jour Windows.
* L’adresse IP d’accès sortant par défaut ne prend pas en charge les paquets fragmentés. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les connexions sortantes dans Azure et la traduction d’adresses réseau (NAT) de réseau virtuel Azure, consultez :

* [SNAT (Source Network Address Translation) pour les connexions sortantes](../load-balancer/load-balancer-outbound-connections.md).

* [Présentation du service NAT de Réseau virtuel Azure](./nat-gateway/nat-overview.md)
