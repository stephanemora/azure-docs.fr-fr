---
title: Offres privées | Azure
description: Offres privées sur la Place de marché Azure pour les éditeurs d’applications et de services.
services: Azure, Marketplace, Compute
documentationcenter: ''
author: qianw211
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/1/2018
ms.author: qianw211
ms.openlocfilehash: c3294aaa2b6c88e08028c8958d2ab3a7297aea74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60735922"
---
# <a name="private-offers"></a>Offres privées

Les offres privées sur la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/) permettent aux éditeurs de créer des références SKU que seuls les clients ciblés peuvent voir.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Élargissez les possibilités de transactions B2B avec les offres privées

Les entreprises clientes sont de plus en plus nombreuses à utiliser des places de marché en ligne pour trouver, tester et acheter des solutions cloud. Grâce aux offres privées, les éditeurs peuvent à présent partager des solutions personnalisées avec les clients ciblés sur la place de marché, en proposant les fonctionnalités exigées par les entreprises :

- Les *tarifs négociés* permettent aux éditeurs d’étendre les remises et les prix hors catalogue par rapport aux offres publiques.
- Grâce aux *conditions générales privées*, ils peuvent personnaliser les conditions générales pour chaque client.
- Les *configurations spécialisées* aident les éditeurs à adapter leur offre Machines virtuelles, Azure Application ou SaaS aux besoins de chaque client. Cette option leur permet également de proposer un accès en préversion aux nouvelles fonctionnalités des produits, avant le lancement général auprès de l’ensemble des clients.

Les offres privées permettent aux éditeurs de tirer parti de l’envergure et de la disponibilité mondiale d’une place de marché publique, avec la flexibilité et le contrôle nécessaires pour négocier et proposer des configurations et des offres personnalisées. Toutes ces fonctionnalités ouvrent la voie à une adoption massive des places de marché cloud dans les entreprises.  Ces dernières ont ainsi la liberté de gérer leurs achats/ventes comme elles le souhaitent.

Les offres privées sont maintenant disponibles pour les offres Machines virtuelles, Azure Application (implémentées sous forme de modèles de solution ou d’applications managées) et SaaS. Tout comme les offres publiques, elles sont créées et gérées sur le [Portail Cloud Partner](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus).  Quelques minutes suffisent pour accorder ou révoquer l’accès aux offres privées à des clients.

## <a name="creating-private-offers-using-skus-and-plans"></a>Créer des offres privées avec des références SKU et des plans

À partir d’une *offre nouvelle ou existante avec références SKU ou plans publics*, les éditeurs peuvent ajouter en toute simplicité des variantes privées en créant de nouvelles références SKU ou de nouveaux plans et en les marquant comme privés.  Les plans et les [références SKU privés](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus) sont des composants d’une offre que seuls les clients ciblés peuvent voir et acquérir. Ils peuvent réutiliser les images de base et des métadonnées déjà publiées pour une référence SKU ou un plan public. Cette option permet aux éditeurs de créer plusieurs variantes privées d’une offre publique sans avoir à publier plusieurs versions de la même image de base ou à proposer des métadonnées. Dans le cas des offres Machines virtuelles et Azure Application uniquement, quand une référence SKU privée partage une image de base avec une référence SKU publique, les modifications apportées à l’image de base de l’offre se propagent sur toutes les références SKU, publiques et privées, qui utilisent cette image de base.

Pour les *nouvelles offres qui comportent uniquement des références SKU ou des plans privés*, les éditeurs peuvent créer des offres classiques, puis marquer les références SKU ou les plans comme privés. Ces offres ne sont accessibles ni sur la [Place de marché Azure](https://azuremarketplace.microsoft.com) ni sur le [Portail Azure](https://azure.microsoft.com/features/azure-portal/) par les clients qui n’y sont pas associés.

## <a name="targeting-customers-with-private-offers"></a>Cibler des clients avec des offres privées
Avec les offres privées, qu’elles soient anciennes ou nouvelles, les éditeurs ont la possibilité de cibler des clients à l’aide de leurs identificateurs d’abonnement. Les éditeurs qui utilisent une offre Machine virtuelle ou Azure Application peuvent limiter la disponibilité d’une référence SKU privée à un seul ID d’abonnement Azure ou charger un fichier CSV pouvant comporter jusqu’à 20 000 ID d’abonnement Azure. Avec une offre privée d’application SaaS, ils peuvent associer un ID d’abonnement Azure ou un ID de client pour limiter la disponibilité d’un plan privé, suivant l’approche manuelle ou par chargement CSV.

Une fois qu’une offre a été certifiée et publiée, il suffit de quelques minutes pour modifier ou supprimer des clients de la référence SKU ou du plan avec la fonctionnalité de synchronisation des abonnements privés. Cette fonctionnalité permet aux éditeurs de mettre à jour rapidement et facilement la liste des clients à laquelle la référence SKU ou le plan privé est présenté sans avoir à certifier ou publier l’offre de nouveau.

## <a name="deploying-private-offers"></a>Déployer des offres privées

Les offres privées ne sont accessibles que sur le [Portail Azure](https://azure.microsoft.com/features/azure-portal/) et non sur la [Place de marché Azure](https://azuremarketplace.microsoft.com). Une fois connectés au Portail Azure, les clients sélectionnent l’élément de navigation Place de marché pour accéder à leurs offres privées. Les offres privées peuvent être déployées en ligne de commande et avec des modèles Azure Resource Manager, comme toutes les autres offres.

![[Offres privées]](./media/marketplace-publishers-guide/private-offer.png)

Les offres privées apparaissent également dans les résultats de recherche. Recherchez simplement le badge « Private » (« Privé »).

> [!Note]
> Offres privées ne sont pas pris en charge avec les abonnements établis via un revendeur du programme fournisseur de solutions Cloud (CSP).

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez tirer parti de ces nouvelles fonctionnalités, vous pouvez commencer à vendre sur la [Place de marché Azure](https://azuremarketplace.microsoft.com/sell).
