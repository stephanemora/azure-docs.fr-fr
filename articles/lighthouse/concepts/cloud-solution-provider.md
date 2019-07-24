---
title: Azure Lighthouse et le programme Fournisseur de solutions cloud
description: Lorsque vous utilisez la gestion des ressources déléguées Azure, il est important de prendre en compte la sécurité et le contrôle d’accès.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 09552c66d2dc841cff8b5cb52e26dc657568e08f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810993"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure Lighthouse et le programme Fournisseur de solutions cloud

Si vous êtes un partenaire [Fournisseur de solutions cloud (CSP)](https://docs.microsoft.com/partner-center/csp-overview), vous pouvez accéder aux abonnements Azure créés pour vos clients par le biais du programme Fournisseur de solutions cloud en utilisant la fonctionnalité [Administrer au nom de](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO). Cet accès vous permet de prendre en charge, de configurer et de gérer directement les abonnements de vos clients.

Le mécanisme Administrer au nom de accorde un accès complet à l’environnement du client. L’utilisation de la gestion des ressources déléguées Azure avec le mécanisme Administrer au nom de permet d’améliorer la sécurité et de réduire les accès inutiles en octroyant des autorisations plus spécifiques à vos utilisateurs. 

## <a name="administer-on-behalf-of-aobo"></a>Administrer au nom de

Avec le mécanisme Administrer au nom de, tout utilisateur auquel le rôle [Agent d’administration](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) est attribué dans votre locataire dispose d’un accès Administrer au nom de aux abonnements Azure que vous créez via le programme Fournisseur de solutions cloud. Tous les utilisateurs qui ont besoin d’accéder aux abonnements de tous les clients doivent être membres de ce groupe. Le mécanisme Administrer au nom de ne permet pas de créer des groupes distincts fonctionnant avec différents clients, ou d’activer des rôles différents à des groupes ou à des utilisateurs.

![Gestion des locataires à l’aide du mécanisme Administrer au nom de](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Gestion des ressources déléguées Azure

La gestion des ressources déléguées Azure vous permet d’affecter des groupes différents à différents clients ou rôles, comme illustré dans le diagramme suivant. Étant donné que les utilisateurs disposent du niveau d’accès approprié attribué par le biais de la gestion des ressources déléguées Azure, vous pouvez réduire le nombre d’utilisateurs ayant le rôle Agent d’administration (et disposant ainsi d’un accès Administrer au nom de complet). Cela contribue à renforcer la sécurité en limitant les accès inutiles aux ressources de vos clients. Cela vous offre également une plus grande flexibilité pour gérer plusieurs clients à grande échelle.

L’intégration d’un abonnement que vous avez créé par le biais du programme Fournisseur de solutions cloud suit les étapes décrites dans [Intégrer un abonnement à la gestion des ressources déléguées Azure](../how-to/onboard-customer.md). Tout utilisateur auquel le rôle Agent d’administration est attribué dans votre locataire peut effectuer cette intégration.

Notez que, pour les abonnements créés via le programme Fournisseur de solutions cloud, des demandes de support ne peuvent être générées que par les utilisateurs disposant du rôle Agent d’administration dans le locataire du fournisseur de services. Les utilisateurs ajoutés via la gestion des ressources déléguées Azure ne peuvent pas ouvrir de demandes de support pour les ressources déléguées dans ces abonnements.

![Gestion des locataires à l’aide du mécanisme Administrer au nom de et de la gestion des ressources déléguées Azure](../media/csp-2.jpg)

## <a name="partner-admin-link"></a>Partner Admin Link (PAL)

Vous pouvez associer votre ID Microsoft Partner Network (MPN) avec vos abonnements intégrés pour suivre votre impact sur les engagements client.

Si vous [publiez une offre de services managés sur la Place de marché Azure](../how-to/publish-managed-services-offers.md), votre ID MPN est associé à votre profil d’éditeur et est automatiquement associé à l’offre. Les revenus générés par les ressources Azure via cette offre sont ensuite attribuées à votre organisation. Dans les systèmes signalant des partenaires tels que l’Espace partenaires ou MPN, l’attribution apparaît en tant que Partner Admin Link (PAL).

Si vous [intégrez des clients pour la gestion des ressources déléguées Azure à l’aide de modèles Azure Resource Manager](../how-to/onboard-customer.md), vous pouvez toujours associer votre ID MPN afin de bénéficier d’une reconnaissance de votre impact sur les engagement client, mais vous devez le faire manuellement. Pour plus d’informations, voir [Lier un ID partenaire à vos comptes Azure](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started). 

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [Expériences de gestion inter-locataire](cross-tenant-management-experience.md).
- Découvrez le [Programme Fournisseur de solutions cloud](https://docs.microsoft.com/partner-center/csp-overview).
