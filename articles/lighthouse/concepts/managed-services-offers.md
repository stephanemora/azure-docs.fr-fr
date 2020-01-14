---
title: Offres de services managés dans la Place de marché Azure
description: Les offres de services managés permettent aux fournisseurs de services de vendre des offres de gestion des ressources à des clients sur la Place de marché Azure.
ms.date: 12/16/2019
ms.topic: conceptual
ms.openlocfilehash: 1b4f0d7457a74afe710a48f429cfe47535a9b122
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453581"
---
# <a name="managed-services-offers-in-azure-marketplace"></a>Offres de services managés dans la Place de marché Azure

Cet article décrit le nouveau type d’offre **Services managés** sur la [Place de marché Azure](https://azuremarketplace.microsoft.com). Les offres de services managés vous permettent d’offrir des services de gestion des ressources à des clients avec la gestion des ressources déléguées Azure. Vous pouvez mettre ces offres à la disposition de tous les clients potentiels ou uniquement d’un ou plusieurs clients spécifiques. Dans la mesure où vous facturez directement les clients pour les coûts liés à ces services managés, Microsoft ne facture aucuns frais.

## <a name="understand-managed-services-offers"></a>Comprendre les offres de services managés

Les offres de services managés simplifient le processus d’intégration des clients pour la gestion des ressources déléguées Azure. Quand un client achète une offre dans la Place de marché Azure, il peut spécifier les abonnements et/ou les groupes de ressources à intégrer. Notez que l’intégration de chaque abonnement doit d’abord être autorisée par le biais d’une inscription manuelle du fournisseur de ressources **Microsoft.ManagedServices**.

Après cela, les utilisateurs de votre organisation seront en mesure d’effectuer des tâches d’administration pour ces ressources à partir du locataire de votre organisation, en fonction de l’accès que vous avez défini lors de la création de l’offre dans le [portail Cloud Partner](https://cloudpartner.azure.com/). Cette opération s’effectue par le biais d’un manifeste spécifiant les utilisateurs, groupes et principaux de service Azure AD qui auront accès aux ressources du client à l’aide de la gestion des ressources déléguées Azure, ainsi que les rôles déterminant leur niveau d’accès. En affectant des autorisations à un groupe Azure AD plutôt qu’à une série de comptes d’utilisateur ou d’application individuels, vous pouvez ajouter ou supprimer des utilisateurs lorsque vos besoins d’accès changent.

## <a name="public-and-private-offers"></a>Offres publiques et privées

Chaque offre de services managés comprend un ou plusieurs plans. Les plans peuvent être privés ou publics. 

Si vous souhaitez limiter votre offre à des clients spécifiques, vous pouvez publier un plan privé. Dans ce cas, le plan ne peut être acheté que pour les ID d’abonnement que vous spécifiez. Pour plus d’informations, voir [Offres privées](../../marketplace/private-offers.md).

Les plans publics vous permettent de promouvoir vos services auprès de nouveaux clients. Ils sont généralement plus appropriés lorsque vous n’avez besoin que d’un accès limité au locataire du client. Une fois que vous avez établi une relation avec un client, si celui-ci décide d’accorder un accès supplémentaire à votre organisation, vous pouvez soit publier un nouveau plan privé exclusivement pour ce client, soit [intégrer celui-ci pour un accès supplémentaire à l’aide de modèles Resource Manager](../how-to/onboard-customer.md).

Le cas échéant, vous pouvez inclure des plans publics et privés dans la même offre.

> [!IMPORTANT]
> Une fois que vous avez publié un plan public, vous ne pouvez plus le changer en plan privé. Pour contrôler les clients qui peuvent accepter votre offre et déléguer des ressources, utilisez un plan privé. Avec un plan public, vous ne pouvez pas limiter la disponibilité à des clients spécifiques ou à un certain nombre de clients (en revanche, vous pouvez arrêter complètement la vente du plan si vous le souhaitez). Il n’existe actuellement aucun mécanisme permettant de rejeter ou supprimer des délégations une fois qu’un client accepte une offre, bien que vous puissiez toujours contacter un client et lui demander de [supprimer votre accès](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

## <a name="publish-managed-service-offers"></a>Publier des offres de services managés

Pour savoir comment publier une offre de services managés, voir [Publier une offre de services managés sur la Place de marché Azure](../how-to/publish-managed-services-offers.md). Pour obtenir des informations générales concernant la publication sur la Place de marché Azure à l’aide du portail Cloud Partner, voir [Guide de publication de la Place de marché Azure et AppSource](../../marketplace/marketplace-publishers-guide.md) et [Gérer les offres sur les places de marché Azure et AppSource](../../marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers.md).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez la [Gestion des ressources déléguées Azure](azure-delegated-resource-management.md) et les [Expériences de gestion inter-locataire](cross-tenant-management-experience.md).
- [Publier des offres de services managés sur la Place de marché Azure](../how-to/publish-managed-services-offers.md).
