---
title: À propos d'ExpressRoute Direct | Microsoft Docs
description: Cette page propose une vue d’ensemble d’ExpressRoute Direct (préversion).
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 0415c329364f8df312667e725f3c5376ea13a6f4
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55098096"
---
# <a name="about-expressroute-direct-preview"></a>À propos d’ExpressRoute Direct (préversion)

ExpressRoute Direct vous offre la possibilité de vous connecter directement au réseau mondial Microsoft à partir d’emplacements d’homologation qui sont distribués stratégiquement dans le monde entier. ExpressRoute Direct offre une double connectivité de 100 Gbit/s qui prend en charge la connectivité Active/Active à grande échelle.

Les principales fonctionnalités offertes par ExpressRoute Direct sont les suivantes (sans s’y limiter) :

* Ingestion de données massive dans des services comme le stockage et Cosmos DB
* Isolation physique pour les secteurs qui sont réglementés, pour lesquels la connectivité isolée et dédiée est obligatoire, notamment : banque, secteur public et commerce de détail
* Contrôle précis de la distribution du circuit en fonction de l’unité commerciale

> [!IMPORTANT]
> ExpressRoute Direct est actuellement disponible en préversion.
>
> Cette préversion publique est fournie sans contrat de niveau de service et ne doit pas être utilisée pour les charges de travail de production. Certaines fonctionnalités peuvent ne pas être prises en charge, disposer de capacités limitées ou ne pas être disponibles dans tous les emplacements Azure. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enroll-in-the-preview"></a>S’inscrire à la préversion

Vous devez commencer par inscrire votre abonnement à la préversion pour pouvoir utiliser ExpressRoute Direct. Envoyez pour cela un e-mail à <ExpressRouteDirect@microsoft.com> avec votre ID d’abonnement et les informations suivantes :

* Scénarios que vous souhaitez réaliser avec **ExpressRoute Direct**
* Préférences d’emplacement : consultez l’article [Partenaires et emplacements d’homologation](expressroute-locations-providers.md) pour obtenir la liste complète de tous les emplacements
* Chronologie d'implémentation
* D’autres questions

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute avec fournisseur de services et ExpressRoute Direct

| **ExpressRoute avec fournisseur de services** | **ExpressRoute Direct** | 
| --- | --- |
| Utilisation de fournisseurs de services pour permettre une intégration et une connectivité rapides dans l’infrastructure existante | Infrastructure de 100 Gbit/s et gestion complète de toutes les couches obligatoires
| Intégration avec des centaines de fournisseurs, y compris Ethernet et MPLS | Capacité directe dédiée pour les secteurs réglementés et ingestion massive de données |
| Références SKU de circuits de 50 Mbit/s à 10 Gbit/s | Possibilité pour le client de sélectionner une combinaison entre les références SKU de circuits suivantes : 5 Gbit/s, 10 Gbit/s, 40 Gbit/s, 100 Gbit/s, dans la limite de 200 Gbit/s au total
| Optimisé pour monolocataire | Optimisé pour monolocataire/fournisseurs de services cloud/unités commerciales multiples

## <a name="expressroute-direct-circuits"></a>Circuits ExpressRoute Direct

Microsoft Azure ExpressRoute vous permet d’étendre vos réseaux locaux au cloud de Microsoft via une connexion privée assurée par un fournisseur de connectivité. Grâce à ExpressRoute, vous pouvez établir des connexions aux services de cloud computing Microsoft, comme Microsoft Azure, Office 365 et Dynamics 365.  

Chaque emplacement d’homologation dispose d’un accès au réseau global Microsoft, à n’importe quelle région d’une zone géopolitique par défaut et à toutes les régions du monde à l’aide d’un circuit premium.  

Dans la plupart des scénarios, la fonctionnalité se comporte comme des circuits qui utilisent un fournisseur de services ExpressRoute. Afin d’offrir un contrôle plus précis et de nouvelles fonctionnalités à l’aide d’ExpressRoute Direct, les circuits sont dotés de certaines fonctions clés.

## <a name="circuit-skus"></a>Références SKU de circuit

ExpressRoute Direct prend en charge des scénarios d’ingestion massive de données dans le Stockage Azure et d’autres services de Big Data. Désormais, les circuits ExpressRoute sur ExpressRoute Direct prennent également en charge les références SKU de circuit **40 Gbit/s** et **100 Gbit/s**.

## <a name="vlan-tagging"></a>Balisage VLAN

ExpressRoute Direct prend en charge le balisage VLAN QinQ et Dot1Q.

* Grâce au **balisage VLAN QinQ**, il est possible d’isoler des domaines de routage individuellement pour chaque circuit ExpressRoute. Azure alloue dynamiquement une balise-S (S-Tag) non modifiable lors de la création du circuit. Chaque homologation du circuit (privé et Microsoft) utilise une balise-C (C-Tag) unique en tant que VLAN. La balise-C (C-Tag) n’a pas besoin d’être unique sur les circuits des ports ExpressRoute Direct.

* Grâce au **balisage VLAN Dot1Q**, il est possible d’avoir un VLAN balisé unique pour chaque paire de ports ExpressRoute Direct. Une balise-C (C-Tag) utilisée sur une homologation doit être unique sur tous les circuits et homologations de la paire de ports ExpressRoute Direct.

## <a name="workflow"></a>Workflow

[ ![workflow](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>Contrat SLA

ExpressRoute Direct fournit le même contrat de niveau de service professionnel avec des connexions redondantes Active/Active dans le réseau mondial Microsoft. L’infrastructure ExpressRoute est redondante, et la connectivité au réseau mondial Microsoft est redondante, différente, et s’adapte aux exigences du client. Il n’y a aucun contrat de niveau de service dans la préversion. Cette dernière doit être envisagée uniquement pour les charges de travail hors production.

## <a name="next-steps"></a>Étapes suivantes

[Configurer ExpressRoute Direct](expressroute-howto-erdirect.md)
