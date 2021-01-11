---
title: Azure Lighthouse dans les scénarios d’ISV
description: Les capacités d’Azure Lighthouse peuvent être utilisées par les éditeurs de logiciels indépendants pour une plus grande flexibilité dans les offres aux clients.
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: d6a12a51d360ad236563b871dbd94cc442ade434
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2020
ms.locfileid: "97696291"
---
# <a name="azure-lighthouse-in-isv-scenarios"></a>Azure Lighthouse dans les scénarios d’ISV

Un scénario courant pour [Azure Lighthouse](../overview.md) met en jeu un fournisseur de services qui gère des ressources dans les locataires Azure Active Directory (Azure AD) de ses clients. Les capacités d’Azure Lighthouse peuvent également être utilisées par les éditeurs de logiciels indépendants (ISV) qui utilisent des offres SaaS avec leurs clients. Azure Lighthouse peut être particulièrement utile pour les ISV qui proposent des services gérés ou une prise en charge qui nécessitent l’accès à l’étendue de l’abonnement.

## <a name="managed-service-offers-in-azure-marketplace"></a>Offres de services managés dans la Place de marché Azure

En tant qu’ISV, vous avez peut-être déjà publié des solutions sur Place de marché Azure. Si vous offrez des services gérés à vos clients, vous pouvez le faire en publiant une offre Service géré. Ces offres rationalisent le processus d’intégration et rendent vos services plus évolutifs pour autant de clients que nécessaire. Azure Lighthouse prend en charge un large éventail de [scénarios et de tâches de gestion](cross-tenant-management-experience.md#enhanced-services-and-scenarios) qui peuvent être utilisés pour offrir une valeur ajoutée à vos clients.

Pour plus d’informations, consultez [Publier une offre Service géré sur Place de marché Azure](../how-to/publish-managed-services-offers.md).

## <a name="using-azure-lighthouse-with-azure-managed-applications"></a>Utilisation d’Azure Lighthouse avec des applications managées Azure

[Les applications managées Azure](../../azure-resource-manager/managed-applications/overview.md) sont un autre moyen pour les ISV de fournir des services à leurs clients. Vous pouvez utiliser Azure Lighthouse avec vos applications managées Azure permettre l’amélioration des scénarios.

Pour plus d’informations, consultez [Azure Lighthouse et applications managées Azure](managed-applications.md).

## <a name="saas-based-multi-tenant-offerings"></a>Offres multilocataires basées sur SaaS

Un autre scénario est celui où l’ISV héberge les ressources dans un abonnement dans son propre locataire, puis utilise Azure Lighthouse pour permettre aux clients d’accéder à ces ressources. Le client peut alors se connecter à son propre locataire et accéder à ces ressources le cas échéant. Les ISV conservent leur adresse IP dans leur propre locataire et peuvent utiliser leurs propres plans de support pour créer des tickets liés à la solution hébergée dans leur locataire, plutôt que d’utiliser le plan du client. Étant donné que les ressources se trouvent dans le locataire de l’ISV, toutes les actions peuvent être effectuées directement par l’ISV, comme la connexion aux machines virtuelles, l’installation d’applications et l’exécution de tâches de maintenance.

Dans ce scénario, les utilisateurs du locataire du client bénéficient essentiellement d’un accès en tant que « locataire gérant », même si le client ne gère pas les ressources de l’ISV. Comme ils accèdent directement au locataire de l’ISV, il est important d’accorder uniquement les autorisations minimales nécessaires pour que les clients ne puissent pas apporter de modifications par inadvertance à la solution ou à d’autres ressources de l’ISV.

Pour permettre cette architecture, l’ISV doit obtenir l’ID d’objet d’un groupe d’utilisateurs dans le locataire Azure AD du client, ainsi que son ID de locataire. L’ISV crée ensuite un modèle ARM accordant à ce groupe d’utilisateurs les autorisations appropriées et [le déploie sur l’abonnement de l’ISV](../how-to/onboard-customer.md) contenant les ressources auxquelles le client accédera.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [Expériences de gestion inter-locataire](cross-tenant-management-experience.md).
- Découvrez [Gestion des ressources déléguées Azure](azure-delegated-resource-management.md).