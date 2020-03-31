---
title: 'Azure ExpressRoute : Prérequis'
description: Cette page fournit une liste des exigences à respecter avant de commander un circuit Azure ExpressRoute. Il inclut une liste de vérification.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: cherylmc
ms.openlocfilehash: a72eba9bde0745e66bdf8e7efd8eaec7d6a0b186
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083359"
---
# <a name="expressroute-prerequisites--checklist"></a>Configuration requise pour ExpressRoute et liste de contrôle
Pour vous connecter aux services cloud de Microsoft en utilisant ExpressRoute, vous devez vérifier que les conditions requises des sections suivantes sont remplies.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Compte Azure
* Un compte Microsoft Azure actif et valide. Ce compte est nécessaire pour configurer le circuit ExpressRoute. Les circuits ExpressRoute sont des ressources au sein des abonnements Azure. Un abonnement Azure est nécessaire, même si la connectivité est limitée aux services cloud Microsoft autres qu’Azure, tels qu’Office 365.
* Un abonnement Office 365 actif (si vous utilisez les services Office 365). Pour plus d’informations, consultez la section Conditions requises propres à Office 365 de cet article.

## <a name="connectivity-provider"></a>Fournisseur de connectivité

* Vous pouvez travailler avec un [partenaire de connectivité ExpressRoute](expressroute-locations.md#partners) pour vous connecter à Microsoft Cloud. Vous pouvez établir une connexion entre votre réseau local et Microsoft de [trois façons différentes](expressroute-introduction.md).
* Si votre fournisseur n’est pas un partenaire de connectivité ExpressRoute, vous pouvez toujours vous connecter à Microsoft Cloud par le biais d’un [fournisseur de solutions d’interconnexion de clouds](expressroute-locations.md#connectivity-through-exchange-providers).

## <a name="network-requirements"></a>Configuration requise pour le réseau
* **Redondance à chaque emplacement de peering** : Microsoft exige la configuration de sessions BGP redondantes entre ses routeurs et les routeurs de peering sur chaque circuit ExpressRoute, même quand vous ne disposez que d’[une seule connexion physique à une solution d’interconnexion de clouds](expressroute-faqs.md#onep2plink).
* **Redondance pour la reprise d’activité après sinistre** : Microsoft vous recommande vivement de configurer au moins deux circuits ExpressRoute à des emplacements de peering différents pour éviter d’avoir un point de défaillance unique.
* **Routage** : selon la façon dont vous vous connectez à Microsoft Cloud, vous ou votre fournisseur pouvez avoir besoin de configurer et gérer les sessions BGP pour les [domaines de routage](expressroute-circuit-peerings.md). Certains fournisseurs de connectivité Ethernet ou fournisseurs d’échange cloud peuvent proposer la gestion BGP en tant que service à valeur ajoutée.
* **Traduction d’adresses réseau (NAT)** : Microsoft accepte uniquement les adresses IP publiques par le biais du peering Microsoft. Si votre réseau local comporte des adresses IP privées, vous ou votre fournisseur devez traduire les adresses IP privées en adresses IP publiques [à l’aide de la traduction d’adresses réseau](expressroute-nat.md).
* **Qualité de service (QoS)** : Skype Entreprise comprend différents services (par exemple, voix, vidéo, texte) nécessitant un traitement QoS différencié. Vous et votre fournisseur devez suivre la [configuration requise pour ExpressRoute QoS](expressroute-qos.md).
* **Sécurité réseau** : pensez à la [sécurité réseau](../best-practices-network-security.md) lorsque vous vous connectez à Microsoft Cloud par le biais d’ExpressRoute.

## <a name="office-365"></a>Office 365
Si vous prévoyez d’activer Office 365 sur ExpressRoute, consultez les documents suivants pour plus d’informations sur la configuration requise pour Office 365.

* [Vue d’ensemble d’ExpressRoute pour Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Routage avec ExpressRoute pour Office 365](https://support.office.com/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
* [Haute disponibilité et basculement avec ExpressRoute](https://aka.ms/erhighavailability)
* [URL et plages d’adresses IP Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
* [Planification réseau et optimisation des performances pour Office 365](https://support.office.com/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
* [Outils et calculatrices de bande passante réseau](https://support.office.com/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
* [Intégration d’Office 365 aux environnements locaux](https://support.office.com/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)
* [Vidéos de formation avancée à ExpressRoute sur Office 365](https://channel9.msdn.com/series/aer/)

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d'informations sur ExpressRoute, consultez la [FAQ sur ExpressRoute](expressroute-faqs.md).
* Recherchez un fournisseur de connectivité ExpressRoute. Consultez [Partenaires ExpressRoute et emplacements de peering](expressroute-locations.md).
* Reportez-vous aux conditions requises pour le [routage](expressroute-routing.md), la [traduction d’adresses réseau](expressroute-nat.md) et la [qualité de service](expressroute-qos.md).
* Configurez votre connexion ExpressRoute.
  * [Création d’un circuit ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configuration du routage](expressroute-howto-routing-arm.md)
  * [Liaison d’un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-arm.md)
