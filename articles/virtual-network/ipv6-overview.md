---
title: Vue d’ensemble du protocole IPv6 pour le réseau virtuel Azure (version préliminaire)
titlesuffix: Azure Virtual Network
description: Description de IPv6 de chemins de données et les points de terminaison IPv6 dans un réseau virtuel Azure.
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
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130936"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>Quel est le protocole IPv6 pour le réseau virtuel Azure ? (Préversion)

IPv6 pour réseau virtuel Azure (VNET) vous permet d’héberger des applications dans Azure avec une connectivité IPv6 et IPv4 à la fois au sein d’un réseau virtuel et vers et depuis Internet. En raison de l’épuisement des adresses IPv4 publiques, les nouveaux réseaux virtuels pour la mobilité et l’Internet des objets (IoT) reposent souvent sur IPv6. Long même établie de fournisseur de services Internet et les réseaux mobiles sont en cours de transformation pour IPv6. Services IPv4 uniquement peuvent se retrouvent un désavantage réelles dans les marchés existantes et émergentes. Double pile IPv4/IPv6 connectivité permet les services hébergés sur Azure Parcourir ce fossé technologique avec les services disponibles dans le monde entier, double pile immédiatement se connecter avec IPv4 existante et que ces nouveaux appareils IPv6 et les réseaux.

Une connectivité IPv6 d’origine d’Azure facilite la fournir double pile (IPv4/IPv6) à Internet pour les applications hébergées dans Azure. Il permet le déploiement simple des machines virtuelles avec une connectivité IPv6 à charge équilibrée pour les connexions entrantes et sortantes. Cette fonctionnalité est toujours disponible et des informations supplémentaires sont disponibles [ici](../load-balancer/load-balancer-ipv6-overview.md).
IPv6 pour le réseau virtuel Azure est beaucoup plus complète proposée-activation des architectures de solution complets IPv6 à déployer dans Azure.

> [!Important]
> IPv6 pour le réseau virtuel Azure est actuellement en version préliminaire publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le diagramme suivant illustre un déploiement simple double pile (IPv4/IPv6) dans Azure :

![Diagramme de déploiement de réseau IPv6](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Avantages

Avantages d’IPv6 de réseau virtuel Azure :

- Vous aide à développer la portée de vos applications hébergées sur Azure dans la culture mobiles et les marchés de l’Internet des objets.
- Deux machines virtuelles IPv4/IPv6 empilées fournissent la flexibilité de déploiement de service maximales. Une instance de service unique peut se connecter avec les clients IPv4 et IPv6 compatible Internet.
- S’appuie sur depuis longtemps, stable Azure VM à Internet une connectivité IPv6.
- Sécurisation par défaut dans la mesure où une connectivité IPv6 sur Internet est établie uniquement lorsque vous le demandez explicitement dans votre déploiement.

## <a name="capabilities"></a>Fonctionnalités

Prise en charge IPv6 pour les machines virtuelles inclut les fonctionnalités suivantes :

- Les clients Azure peuvent définir leur propre espace d’adressage de réseau virtuel IPv6 pour répondre aux besoins de leurs applications, les clients, ou intégrer en toute transparence dans leur espace d’adresse IP en local.
- Double pile (IPv4 et IPv6) réseaux virtuels avec les sous-réseaux double pile permettent aux applications de se connecter à des ressources à la fois IPv4 et IPv6 dans leur réseau virtuel ou - Internet.
- Protéger vos ressources avec des règles d’IPv6 pour les groupes de sécurité réseau
- Personnaliser le routage du trafic IPv6 sur votre réseau virtuel avec des itinéraires définis par l’utilisateur - en particulier quand vous tirez parti des Appliances virtuelles réseau pour augmenter votre application.
- Équilibreur de charge IPv6 prise en charge pour créer des applications résilientes et évolutives qui inclut la prise en charge de Azure DNS pour les enregistrements AAAA des adresses IP publiques IPv6.
- Ajouter facilement la connectivité IPv6 pour les déploiements existants de IPv4 uniquement avec la mise à niveau sur Place.
- Créer des applications de pile double qui s’adaptent automatiquement à votre charge de machines virtuelles identiques.

## <a name="limitations"></a>Limites
La version préliminaire du protocole IPv6 pour le réseau virtuel Azure présente les limitations suivantes :
- IPv6 pour un réseau virtuel Azure (version préliminaire) est disponible dans toutes les régions Azure globales, mais uniquement dans Global Azure - pas dans les clouds government.   
- Support concernant le portail pour la version préliminaire est limitée pour afficher uniquement pour la configuration IPv6 bien, mais pas tous, mais IPv6 pour le réseau virtuel est prise en charge complète et documentation (avec exemples) pour le déploiement d’IPv6 à l’aide d’Azure Powershell et Interface de ligne de commande (CLI).
- Prise en charge de l’Observateur réseau pour la version préliminaire est limité à des journaux de flux NSG et les captures de paquets réseau.
- Prise en charge de l’équilibrage de charge pour la version préliminaire est initialement limitée à l’équilibreur de charge.
- Adresses IP publiques au niveau de l’instance (adresses IP publiques directement sur une machine virtuelle) ne sont pas pris en charge dans la version préliminaire.  
- Homologation de réseaux virtuels (au niveau régional ou global) n'est pas pris en charge dans la version préliminaire. 

## <a name="pricing"></a>Tarifs

La bande passante et ressources IPv6 Azure sont facturés au tarif de la même IPv4. Il n’existe aucun frais supplémentaires ou différents pour IPv6. Vous trouverez des informations sur la tarification pour [des adresses IP publiques](https://azure.microsoft.com/pricing/details/ip-addresses/), [la bande passante réseau](https://azure.microsoft.com/pricing/details/bandwidth/), ou [équilibreur de charge](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [déployer une application de double pile IPv6 à l’aide d’Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-powershell.md).
- Découvrez comment [déployer une application de double pile IPv6 à l’aide d’Azure CLI](virtual-network-ipv4-ipv6-dual-stack-cli.md).
