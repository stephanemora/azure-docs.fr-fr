---
title: Qu’est-ce qu’Azure Lighthouse ?
description: Azure Lighthouse permet aux fournisseurs de services de fournir des services managés à leurs clients avec une automatisation et une efficacité accrues à grande échelle.
ms.date: 11/11/2019
ms.topic: overview
ms.openlocfilehash: 0bec1f5b727ca3ecd604d2654358ca9a5507abe4
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646192"
---
# <a name="what-is-azure-lighthouse"></a>Qu’est-ce qu’Azure Lighthouse ?

Azure Lighthouse offre aux fournisseurs de services un plan de contrôle unique pour voir et gérer Azure sur tous leurs clients avec une automatisation et une mise à l’échelle supérieures et une gouvernance améliorée. Avec Azure Lighthouse, les fournisseurs de services peuvent fournir des services managés à l’aide des outils de gestion complets et robustes intégrés dans la plateforme Azure. Cette offre peut également aider les organisations informatiques d’entreprise à gérer les ressources de plusieurs locataires.

![Schéma de présentation d’Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Avantages

Azure Lighthouse vous aide à créer et à fournir des services managés de manière rentable et efficace pour vos clients. Voici les avantages :

- **Gestion à grande échelle** : les opérations d’engagement client et de gestion du cycle de vie des ressources des clients sont plus simples et plus évolutives.
- **Visibilité et précision accrues pour les clients** : les clients dont vous gérez les ressources ont une meilleure visibilité de vos actions et peuvent contrôler avec précision l’étendue de la gestion qu’ils délèguent, tandis que votre adresse IP est préservée.
- **Outillage de plateforme complet et unifié** : notre outillage est adapté aux principaux scénarios des fournisseurs de services, notamment aux modèles de licences multiples tels que Contrat Entreprise, Fournisseur de solutions cloud et paiement à l’utilisation. Les nouvelles fonctionnalités opèrent avec des outils et API, des modèles de licence et des programmes de partenaires tels que le [programme Fournisseur de solutions cloud (CSP)](https://docs.microsoft.com/partner-center/csp-overview) existants. Les options Azure Lighthouse que vous choisissez peuvent être intégrées dans vos flux de travail et applications existants, et vous pouvez suivre votre impact sur les engagements client en [liant votre ID partenaire](../billing/billing-partner-admin-link-started.md).

Aucun coût supplémentaire n’est associé à l’utilisation d’Azure Lighthouse pour gérer les ressources Azure de vos clients.

## <a name="capabilities"></a>Fonctionnalités

Azure Lighthouse offre plusieurs moyens de simplifier l’engagement et la gestion des clients :

- **Gestion des ressources déléguées Azure** : gérez les ressources Azure de vos clients en toute sécurité à partir de votre propre locataire, sans avoir à changer de contexte ou de plan de contrôle. Pour plus d’informations, voir [Gestion des ressources déléguées Azure](concepts/azure-delegated-resource-management.md).
- **Nouvelles expériences du portail Azure** : affichez les informations inter-locataires dans la nouvelle page **Mes clients** du [portail Azure](https://portal.azure.com). Un panneau **Fournisseurs de services** correspondant permet à vos clients de voir et gérer l’accès du fournisseur de services. Pour plus d’informations, consultez [Voir et gérer les clients](./how-to/view-manage-customers.md) et [Voir et gérer les fournisseurs de services](how-to/view-manage-service-providers.md).
- **Modèles Microsoft Azure Resource Manager** : effectuez plus facilement des tâches de gestion telles que l’intégration de clients pour la gestion des ressources déléguées Azure. Pour plus d’informations, voir [Référentiel d’exemples](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates) et [Intégrer un client dans la gestion des ressources déléguées Azure](how-to/onboard-customer.md).
- **Offres de services managés sur la Place de marché Azure** : offrez vos services aux clients via des offres privées ou publiques, et faites en sorte qu’ils soient automatiquement intégrés à la gestion des ressources déléguées Azure, en guise d’alternative à l’intégration à l’aide de modèles Azure Resource Manager. Pour plus d’informations, voir [Offres de services managés sur la Place de marché Azure](concepts/managed-services-offers.md).
- **Applications managées Azure** : empaquetez et livrez des applications faciles à déployer et à utiliser par vos clients dans leurs propres abonnements. L’application est déployée dans un groupe de ressources auquel vous accédez à partir de votre locataire, ce qui vous permet de gérer le service dans le cadre de l’expérience Azure Lighthouse globale. Pour plus d’informations, voir [Vue d’ensemble des applications managées Azure](../azure-resource-manager/managed-applications/overview.md).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez [Gestion des ressources déléguées Azure](concepts/azure-delegated-resource-management.md).
- Découvrez les [Expériences de gestion inter-locataire](concepts/cross-tenant-management-experience.md).
