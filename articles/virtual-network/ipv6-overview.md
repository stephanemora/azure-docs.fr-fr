---
title: Vue d’ensemble du protocole IPv6 pour réseau virtuel Azure (préversion)
titlesuffix: Azure Virtual Network
description: Description des points de terminaison et des chemins de données IPv6 dans un réseau virtuel Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 0ec650880a45f6383b24b5ac810fc2ee745806b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "62130936"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>Qu’est-ce que le protocole IPv6 pour réseau virtuel Azure ? (Préversion)

Le protocole IPv6 pour réseau virtuel Azure vous permet d’héberger des applications dans Azure avec une connectivité IPv6 et IPv4 tant au sein d’un réseau virtuel que vers et depuis Internet. En raison de l’épuisement des adresses IPv4 publiques, les nouveaux réseaux pour la mobilité et l’Internet des objets (IoT) reposent souvent sur le protocole IPv6. Même des fournisseurs de services Internet et réseaux mobiles établis de longue date sont en cours d’évolution vers le protocole IPv6. Des services uniquement IPv4 peuvent se retrouver réellement désavantagés dans les marchés existants et émergents. La connectivité double pile IPv4/IPv6 permet aux services hébergés dans Azure de combler ce fossé technologique avec des services à double pile disponibles dans le monde entier, qui se connectent sans difficulté tant aux appareils et réseaux IPv4 existants qu’aux nouveaux appareils et réseaux IPv6.

La connectivité IPv6 d’origine d’Azure facilite la fourniture d’une connectivité Internet double pile (IPv4/IPv6) pour les applications hébergées dans Azure. Cela permet un déploiement simple des machines virtuelles avec une connectivité IPv6 à charge équilibrée pour les connexions tant entrantes que sortantes. Cette fonctionnalité est toujours disponible et des informations supplémentaires sont accessibles [ici](../load-balancer/load-balancer-ipv6-overview.md).
Le protocole IPv6 pour réseau virtuel Azure est beaucoup plus complet et permet le déploiement d’architectures de solution IPv6 complètes dans Azure.

> [!Important]
> Le protocole IPv6 pour réseau virtuel Azure est actuellement en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le diagramme suivant illustre un déploiement double pile (IPv4/IPv6) dans Azure :

![Diagramme de déploiement de réseau IPv6](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Avantages

Avantages du protocole IPv6 pour réseau virtuel Azure :

- Vous aide à étendre vos applications hébergées dans Azure aux marchés en pleine expansion des solutions mobiles et de l’Internet des objets.
- Les machines virtuelles double pile IPv4/IPv6 offrent une flexibilité de déploiement de service maximale. Une simple instance de service peut se connecter indifféremment à des clients Internet compatibles IPv4 et IPv6.
- S’appuie sur la connectivité IPv6 à Internet stable et établie de longue date des machines virtuelles Azure.
- Sécurisation par défaut dans la mesure où la connectivité IPv6 à Internet n’est établie que lorsque vous le demandez explicitement dans votre déploiement.

## <a name="capabilities"></a>Fonctionnalités

La prise en charge du protocole IPv6 pour les machines virtuelles présente les caractéristiques suivantes :

- Les clients Azure peuvent définir leur propre espace d’adressage de réseau virtuel IPv6 pour répondre aux besoins de leurs applications et clients, ou intégrer sans difficulté dans leur propre espace d’adressage IP local.
- Des réseaux virtuels double pile (IPv4 et IPv6) avec des sous-réseaux double pile permettent aux applications de se connecter à des ressources tant IPv4 que IPv6 au sein de leur réseau virtuel ou sur Internet.
- Possibilité de protéger vos ressources avec des règles de protocole IPv6 pour les groupes de sécurité réseau.
- Possibilité de personnaliser le routage du trafic IPv6 dans votre réseau virtuel avec des itinéraires définis par l’utilisateur, en particulier lorsque vous utilisez des appliances virtuelles réseau pour augmenter votre application.
- Prise en charge de l’équilibreur de charge IPv6 pour créer des applications résilientes et évolutives, incluant la prise en charge d’Azure DNS pour les enregistrements AAAA pour les adresses IP publiques IPv6.
- Facilité d’ajout de la connectivité IPv6 à des déploiements exclusivement IPv4 existants, avec mise à niveau sur place.
- Possibilité de créer des applications double pile dont l’échelle s’adapte automatiquement à votre charge à l’aide de groupes de machines virtuelles identiques.

## <a name="limitations"></a>Limites
La préversion du protocole IPv6 pour réseau virtuel Azure présente les limitations suivantes :
- Le protocole IPv6 pour réseau virtuel Azure (préversion) est disponible dans toutes les régions Azure, mais uniquement dans Azure international, pas dans les clouds pour le secteur public.   
- La prise en charge du portail pour la préversion est limitée à l’affichage d’une grande partie de la configuration IPv6, mais pas toute. Cependant, le protocole IPv6 pour réseau virtuel offre un support et une documentation complets (avec des exemples) pour le déploiement d’IPv6 à l’aide d’Azure Powershell et de l’interface de ligne de commande.
- La prise en charge de Network Watcher pour la préversion est limitée aux journaux de flux NSG et aux captures de paquet réseau.
- La prise en charge de l’équilibrage de charge pour la préversion est initialement limitée à l’équilibreur de charge de base.
- Les adresses IP publiques au niveau de l’instance (adresses IP publiques définies directement sur une machine virtuelle) ne sont pas prises en charge dans la préversion.  
- Le peering de réseau virtuel (au niveau régional ou global) n’est pas pris en charge dans la préversion. 

## <a name="pricing"></a>Tarifs

Les ressources et la bande passante du protocole IPv6 Azure sont facturées au même tarif que le protocole IPv4. Le protocole IPv6 n’entraîne pas de faire supplémentaires ou autres. Des informations sont disponibles sur la tarification des [adresses IP publiques](https://azure.microsoft.com/pricing/details/ip-addresses/), de la [bande passante réseau](https://azure.microsoft.com/pricing/details/bandwidth/) ou de l’[équilibreur de charge](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [déployer une application double pile IPv6 dans Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-powershell.md).
- Découvrez comment [déployer une application double pile IPv6 à l’aide d’Azure CLI](virtual-network-ipv4-ipv6-dual-stack-cli.md).
