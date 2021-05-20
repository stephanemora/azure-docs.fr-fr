---
title: Qu’est-ce qu’Azure Lighthouse ?
description: Azure Lighthouse permet aux fournisseurs de services de fournir des services managés à leurs clients avec une automatisation et une efficacité accrues à grande échelle.
ms.date: 05/11/2021
ms.topic: overview
ms.openlocfilehash: 3f8b09584a5f025e22543a65eefd50dbfa7bb894
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109785904"
---
# <a name="what-is-azure-lighthouse"></a>Qu’est-ce qu’Azure Lighthouse ?

Azure Lighthouse offre une gestion inter- et multi-locataire, ce qui permet d’améliorer l’automatisation, la scalabilité et la gouvernance entre les ressources et les locataires.

Avec Azure Lighthouse, les fournisseurs de services peuvent fournir des services managés à l’aide des outils de gestion complets et robustes intégrés dans la plateforme Azure. Les clients conservent le contrôle des personnes autorisées à accéder à leur locataire, des ressources auxquelles elles peuvent accéder et des actions qu’elles peuvent entreprendre. Cette offre peut également aider les [organisations informatiques d’entreprise](concepts/enterprise.md) à gérer les ressources sur plusieurs locataires.

L’[expérience de gestion inter-locataire](concepts/cross-tenant-management-experience.md) vous permet de travailler plus efficacement avec des services de gestion Azure tels qu’Azure Policy, Azure Security Center et bien plus encore. Toute l'activité du fournisseur de services fait l'objet d'un suivi dans le journal d'activité, qui est stocké dans le locataire du client (et peut être consulté par les utilisateurs dans le locataire de gestion). Les utilisateurs peuvent voir quelles modifications ont été apportées et par qui.

![Schéma de présentation d’Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Avantages

Azure Lighthouse aide les fournisseurs de services à créer et à fournir des services managés de manière efficace. Voici les avantages :

- **Gestion à grande échelle** : les opérations d’engagement client et de gestion du cycle de vie des ressources des clients sont plus simples et plus évolutives. Les API, les outils de gestion et les workflows existants peuvent être utilisés avec les ressources déléguées, y compris des machines hébergées en dehors d’Azure, quelles que soient les régions où ils se trouvent.
- **Visibilité et contrôle accrus pour les clients** : Les clients ont un contrôle précis des étendues qu’ils délèguent pour la gestion et des autorisations qui sont autorisées. Ils peuvent auditer les actions des fournisseurs de services et supprimer complètement l’accès s’ils le souhaitent.
- **Outillage de plateforme complet et unifié** : notre outillage est adapté aux principaux scénarios des fournisseurs de services, notamment aux modèles de licences multiples tels que Contrat Entreprise, Fournisseur de solutions cloud et paiement à l’utilisation. Azure Lighthouse opère avec des outils et API, des modèles de licence, des [applications managées Azure](concepts/managed-applications.md) et des programmes de partenaires tels que le programme [Fournisseur de solutions cloud (CSP)](/partner-center/csp-overview) existants. Vous pouvez intégrer Azure Lighthouse à vos workflows et applications existants, et suivre votre impact sur les engagements client en [liant votre ID partenaire](./how-to/partner-earned-credit.md).

## <a name="capabilities"></a>Fonctionnalités

Azure Lighthouse offre plusieurs moyens de simplifier l’engagement et la gestion :

- **Gestion des ressources déléguées Azure** : [gérez les ressources Azure de vos clients de façon sécurisée à partir de votre propre locataire](concepts/architecture.md), sans avoir à changer de contexte ou de plan de contrôle. Les abonnements et les groupes de ressources des clients peuvent être délégués à des utilisateurs et des rôles spécifiés dans le locataire gérant, avec la possibilité de supprimer l’accès si besoin.
- **Nouvelles expériences du portail Azure** : Affichez les informations multilocataires dans la page [**Mes clients** du](how-to/view-manage-customers.md) portail Azure. Une page [**Fournisseurs de services** correspondante](how-to/view-manage-service-providers.md) permet à vos clients de voir et gérer l’accès du fournisseur de services.
- **Modèles Microsoft Azure Resource Manager** : Utilisez les modèles ARM pour [intégrer des ressources client déléguées](how-to/onboard-customer.md) et [effectuer des tâches de gestion multilocataires](samples/index.md).
- **Offres de services managés de la Place de marché Azure** : [Offrez vos services aux clients](concepts/managed-services-offers.md) via des offres privées ou publiques et intégrez-les automatiquement à Azure Lighthouse.

> [!TIP]
> Une offre similaire, [Microsoft 365 Lighthouse](https://techcommunity.microsoft.com/t5/small-and-medium-business-blog/announcing-microsoft-365-lighthouse-for-managed-service/ba-p/1698181), permettra aux partenaires informatiques d’intégrer, de superviser et de gérer leurs clients Microsoft 365 à grande échelle. Microsoft 365 Lighthouse est actuellement en préversion privée.

## <a name="pricing-and-availability"></a>Tarification et disponibilité

Aucun coût supplémentaire n’est associé à l’utilisation d’Azure Lighthouse pour gérer les ressources Azure. Tout client ou partenaire Azure peut utiliser Azure Lighthouse.

## <a name="cross-region-and-cloud-considerations"></a>Considérations liées au cloud et interrégions

Azure Lighthouse n’est pas un service régional. Vous pouvez gérer des ressources déléguées situées dans différentes [régions](../availability-zones/az-overview.md#regions). Toutefois, la délégation d’abonnements sur un [cloud national](../active-directory/develop/authentication-national-cloud.md) et le cloud public Azure, ou sur deux clouds nationaux distincts, n’est pas prise en charge.

## <a name="support-for-azure-lighthouse"></a>Prise en charge d’Azure Lighthouse

Pour obtenir de l’aide sur l’utilisation d’Azure Lighthouse, ouvrez une demande de support dans le portail Azure. Pour **Type de problème**, choisissez **Technique**. Sélectionnez un abonnement, puis **Lighthouse** (sous **Surveillance et gestion**).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez-en plus sur le [fonctionnement technique d’Azure Lighthouse](concepts/architecture.md).
- Explorez les [expériences de la gestion multilocataire](concepts/cross-tenant-management-experience.md).
- Découvrez comment [utiliser Azure Lighthouse au sein d’une d’entreprise](concepts/enterprise.md).
- Affichez les détails de la [disponibilité](https://azure.microsoft.com/global-infrastructure/services/?products=azure-lighthouse&regions=all) et de [l’étendue d’audit du FedRAMP et du DoD SRG](../azure-government/compliance/azure-services-in-fedramp-auditscope.md) pour Azure Lighthouse.
