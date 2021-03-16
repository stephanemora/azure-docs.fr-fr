---
title: Offres privées dans la place de marché commerciale de Microsoft
description: Offres privées dans la Place de marché commerciale de Microsoft pour les éditeurs d’applications et de services.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: vikrambmsft
ms.author: vikramb
ms.date: 02/22/2021
ms.openlocfilehash: 7c02c41379cfb886fd7e6f84486d815d3cdbfb5b
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097174"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Offres privées dans la Place de marché commerciale de Microsoft

Les offres privées, aussi appelées « plans privés », permettent aux éditeurs de créer des plans que seuls les clients ciblés peuvent voir. Cet article présente les options et les avantages des offres privées.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Élargissez les possibilités de transactions B2B grâce aux offres privées

En créant des offres privées, les éditeurs peuvent offrir de manière privée des solutions personnalisées aux clients ciblés avec les fonctionnalités dont les entreprises ont besoin :

- Les *tarifs négociés* permettent aux éditeurs d’étendre les remises et les prix hors catalogue par rapport aux offres publiques.
- Grâce aux *conditions générales privées*, ils peuvent personnaliser les conditions générales pour chaque client.
- Les *configurations spécialisées* aident les éditeurs à adapter leur solution Machines virtuelles, Azure Application ou SaaS aux besoins de chaque client. Cette option leur permet également de proposer un accès en préversion aux nouvelles fonctionnalités des produits, avant de les lancer pour l’ensemble des clients.

Les offres privées permettent aux éditeurs de tirer parti de l’envergure et de la disponibilité mondiale d’un marketplace public, avec la flexibilité et le contrôle nécessaires pour négocier et proposer des configurations et des offres personnalisées. Les entreprises ont ainsi la liberté de gérer leurs achats/ventes comme elles le souhaitent.

## <a name="create-private-offers-using-plans"></a>Créer des offres privées à l’aide de plans

À partir d’*offres nouvelles ou existantes avec plans*, les éditeurs peuvent ajouter sans peine des variantes privées en créant de nouveaux plans (anciennement les « références SKU ») et en les marquant comme privés. Chaque offre peut avoir jusqu’à 45 plans privés.

<!--- [Private SKUs]() --->

Des plans privés sont disponibles pour les types d’offre suivants :

- Machine virtuelle Azure
- Azure Application (implémenté comme modèle de solution ou application gérée)
- Service managé
- Offres SaaS

Les plans privés sont des composants d’une offre que seuls les clients ciblés peuvent voir et acquérir. Les plans privés ne sont visibles et ne peuvent être achetés que par les clients ciblés. Des plans privés peuvent être mis à la disposition des clients dans Azure Global et Azure Government.

Ils peuvent réutiliser les images de base et offrir des métadonnées déjà publiées pour un plan public. Cette option permet aux éditeurs de créer plusieurs variantes privées d’une offre publique sans avoir à publier plusieurs versions de la même image de base ou à proposer des métadonnées. Dans le cas des offres Machines virtuelles Azure et Azure Application uniquement, quand un plan privé partage une image de base avec un plan public, les modifications apportées à l’image de base de l’offre se propagent sur tous les plans, publics et privés, qui utilisent cette image de base.

Pour les *nouvelles offres qui comportent uniquement des plans privés*, les éditeurs peuvent créer des offres classiques, puis marquer les plans comme privés. Les offres qui ne possèdent que des plans privés ne sont pas détectables ni accessibles dans [Portail Azure](https://azure.microsoft.com/features/azure-portal/) par les clients qui ne sont pas associés à l’offre.

>[!NOTE]
>Une offre qui contient uniquement des plans privés ne sera pas visible dans la Place de marché Azure publique, ni dans AppSource.

## <a name="target-customers-with-private-offers"></a>Cibler des clients avec des offres privées

Avec les offres privées, qu’elles soient anciennes ou nouvelles, les éditeurs ont la possibilité de cibler des clients à l’aide de leurs identificateurs d’abonnement. Pour les offres Machines virtuelles Azure, Azure Application et Services gérés, les éditeurs peuvent limiter la disponibilité d’un plan privé à un ID d’abonnement Azure individuel ou charger un fichier CSV d’un maximum de 10 000 ID d’abonnement Azure. Pour les offres SaaS, les éditeurs peuvent associer un ID de locataire Azure Active Directory pour limiter la disponibilité d’un plan privé, suivant l’approche manuelle ou par chargement CSV.

Une fois qu’une offre a été certifiée et publiée, il devient possible de mettre à jour ou de supprimer des clients du plan avec la fonctionnalité de synchronisation des abonnements privés. Cette capacité permet aux éditeurs de mettre à jour rapidement et facilement la liste des clients auxquels le plan privé est présenté, sans avoir à certifier ou publier de nouveau l’offre.

## <a name="deploying-private-offers"></a>Déployer des offres privées

Une fois connectés au portail Azure, les clients peuvent suivre ces étapes pour sélectionner vos offres privées.

1. Connectez-vous au [Portail Azure](https://ms.portal.azure.com/).
1. Sous **Services Azure**, sélectionnez **Créer une ressource**.
1. Sur la page **Nouveau**, à côté de **Place de marché Azure**, sélectionnez **Afficher tout**. La page Marketplace s’affiche.
1. Dans le volet de navigation de gauche, sélectionnez **Offres privées**.

> [!NOTE]
> Les offres privées sont uniquement détectables dans [Portail Azure](https://azure.microsoft.com/features/azure-portal/). Elles ne sont pas affichées dans [Microsoft AppSource](https://appsource.microsoft.com/) ni dans [Place de marché Azure](https://azuremarketplace.microsoft.com). Pour en savoir plus sur la publication sur les différents magasins en ligne du marketplace commercial, consultez [Présentation des options de référencement](./determine-your-listing-type.md).

Les offres privées apparaissent également dans les résultats de recherche et peuvent être déployées par le biais d’une ligne de commande et de modèles Azure Resource Manager, comme n’importe quelle autre offre.

[![[Offres privées apparaissant dans les résultats de la recherche.]](media/marketplace-publishers-guide/private-offer.png)](media/marketplace-publishers-guide/private-offer.png#lightbox)

Les offres privées apparaissent également dans les résultats de recherche. Recherchez simplement le badge **Privé**.

>[!Note]
>Les offres privées ne sont pas prises en charge avec les abonnements souscrits via un revendeur participant au programme des fournisseurs de solutions cloud (CSP).

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans]() guide.
--->
