---
title: Offres de services gérés sur la Place de marché Azure
description: Les offres de services gérés permettent aux fournisseurs de services de vendre des offres de gestion des ressources à des clients sur la Place de marché Azure.
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: a0670bd74971132dcf243736bdf882a00154a942
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672417"
---
# <a name="managed-service-offers-in-azure-marketplace"></a>Offres de services gérés sur la Place de marché Azure

Cet article décrit le type d’offre **Services gérés** sur la [Place de marché Azure](https://azuremarketplace.microsoft.com). Les offres de services gérés vous permettent d’offrir des services de gestion des ressources à des clients avec la [gestion des ressources déléguées Azure](azure-delegated-resource-management.md). Vous pouvez mettre ces offres à la disposition de tous les clients potentiels ou uniquement d’un ou plusieurs clients spécifiques. Dans la mesure où vous facturez directement les clients pour les coûts liés à ces services managés, Microsoft ne facture aucuns frais.

## <a name="understand-managed-service-offers"></a>Comprendre les offres de services gérés

Les offres de services gérés simplifient le processus d’intégration des clients pour la gestion des ressources déléguées Azure. Quand un client achète une offre dans la Place de marché Azure, il peut spécifier les abonnements et/ou les groupes de ressources à intégrer.

Après cela, les utilisateurs de votre organisation seront en mesure de travailler sur ces ressources à partir du locataire de votre organisation, en fonction de l’accès que vous avez défini lors de la création de l’offre dans le [portail Cloud Partner](https://cloudpartner.azure.com/). Cette opération s’effectue par le biais d’un manifeste spécifiant les utilisateurs, groupes et principaux de service Azure AD qui auront accès aux ressources du client à l’aide de la gestion des ressources déléguées Azure, ainsi que les rôles déterminant leur niveau d’accès. En affectant des autorisations à un groupe Azure AD plutôt qu’à une série de comptes d’utilisateur ou d’application individuels, vous pouvez ajouter ou supprimer des utilisateurs lorsque vos besoins d’accès changent.

## <a name="public-and-private-offers"></a>Offres publiques et privées

Chaque offre de services managés comprend un ou plusieurs plans. Les plans peuvent être privés ou publics.

Si vous souhaitez limiter votre offre à des clients spécifiques, vous pouvez publier un plan privé. Dans ce cas, le plan ne peut être acheté que pour les ID d’abonnement que vous spécifiez. Pour plus d’informations, voir [Offres privées](../../marketplace/private-offers.md).

Les plans publics vous permettent de promouvoir vos services auprès de nouveaux clients. Ils sont généralement plus appropriés lorsque vous n’avez besoin que d’un accès limité au locataire du client. Une fois que vous avez établi une relation avec un client, si celui-ci décide d’accorder un accès supplémentaire à votre organisation, vous pouvez soit publier un nouveau plan privé exclusivement pour ce client, soit [intégrer celui-ci pour un accès supplémentaire à l’aide de modèles Resource Manager](../how-to/onboard-customer.md).

Le cas échéant, vous pouvez inclure des plans publics et privés dans la même offre.

> [!IMPORTANT]
> Une fois que vous avez publié un plan public, vous ne pouvez plus le changer en plan privé. Pour contrôler les clients qui peuvent accepter votre offre et déléguer des ressources, utilisez un plan privé. Avec un plan public, vous ne pouvez pas limiter la disponibilité à des clients spécifiques ou à un certain nombre de clients (en revanche, vous pouvez arrêter complètement la vente du plan si vous le souhaitez). Vous pouvez [supprimer l’accès à une délégation](../how-to/onboard-customer.md#remove-access-to-a-delegation) après qu’un client a accepté une offre uniquement si vous avez inclus une **Autorisation** avec la **Définition de rôle** définie sur [Inscription des services managés, attribution Supprimer le rôle](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) lors de la publication de l’offre. Vous pouvez également contacter le client et lui demander de [supprimer votre accès](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

## <a name="publish-managed-service-offers"></a>Publier des offres de services managés

Pour savoir comment publier une offre de services managés, voir [Publier une offre de services managés sur la Place de marché Azure](../how-to/publish-managed-services-offers.md). Pour obtenir des informations générales concernant la publication sur la Place de marché Azure à l’aide du portail Cloud Partner, consultez [Guide de publication de la Place de marché Azure et AppSource](../../marketplace/marketplace-publishers-guide.md).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez la [Gestion des ressources déléguées Azure](azure-delegated-resource-management.md) et les [Expériences de gestion inter-locataire](cross-tenant-management-experience.md).
- [Publier des offres de services managés sur la Place de marché Azure](../how-to/publish-managed-services-offers.md).
