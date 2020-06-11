---
title: Gestion des ressources déléguées Azure
description: Les offres de services managés permettent aux fournisseurs de services de vendre des offres de gestion des ressources à des clients sur la Place de marché Azure.
ms.date: 05/28/2020
ms.topic: conceptual
ms.openlocfilehash: e6f741ccbf0d52e0859722aa304aa6c273fb5acf
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2020
ms.locfileid: "84219069"
---
# <a name="azure-delegated-resource-management"></a>Gestion des ressources déléguées Azure

La gestion des ressources déléguées Azure est l’un des principaux composants d’Azure Lighthouse. Grâce à la gestion des ressources déléguées Azure, les fournisseurs de services peuvent simplifier les expériences d’engagement et d’intégration des clients, tout en gérant les ressources déléguées à grande échelle avec agilité et précision.

## <a name="what-is-azure-delegated-resource-management"></a>Qu’est-ce que la gestion des ressources déléguées Azure ?

La gestion des ressources déléguées Azure permet d’opérer une projection logique des ressources d’un locataire sur un autre. Des utilisateurs autorisés dans un locataire Azure Active Directory (Azure AD) peuvent ainsi effectuer des opérations de gestion sur différents locataire Azure AD appartenant à leurs clients. Les fournisseurs de services peuvent se connecter à leur propre locataire Azure AD et être autorisés à travailler dans des abonnements et groupes de ressources délégués de clients. Cela leur permet d’effectuer des opérations de gestion au nom de leurs clients, sans avoir à se connecter à chaque locataire du client.

> [!NOTE]
> La gestion des ressources déléguées Azure peut également être utilisée [au sein d’une entreprise comptant plusieurs locataires Azure AD propres](enterprise.md) afin de simplifier la gestion inter-locataire.

Avec la gestion des ressources déléguées Azure, les utilisateurs autorisés peuvent travailler directement dans le contexte d’un abonnement client sans avoir de compte dans le locataire de ce client ou être copropriétaire du locataire du client. Ils peuvent également [voir et gérer tous les abonnements délégués du client**dans la nouvelle page**Mes clients](../how-to/view-manage-customers.md) du portail Azure.

L’[expérience de gestion inter-locataire](cross-tenant-management-experience.md) vous aide à travailler plus efficacement avec des services de gestion Azure tels qu’Azure Policy, Azure Security Center et bien plus encore. Toute l'activité du fournisseur de services fait l'objet d'un suivi dans le journal d'activité, qui est stocké dans le locataire du client (et peut être consulté par les utilisateurs dans le locataire de gestion). Cela signifie que tant le client que le fournisseur de services peuvent facilement identifier l’utilisateur associé à toute modification.

Lorsque vous intégrez un client à la gestion des ressources déléguées Azure, celui-ci a accès à la nouvelle page **Fournisseurs de services** du portail Azure, où il peut [confirmer et gérer ses offres, fournisseurs de services et ressources déléguées](../how-to/view-manage-service-providers.md). Si le client en vient à vouloir révoquer l’accès d’un fournisseur de services, il peut le faire à tout moment.

Vous pouvez [publier le nouveau type d'offre de service managé sur la Place de marché Azure](../how-to/publish-managed-services-offers.md) pour intégrer facilement des clients à la gestion des ressources déléguées Azure. Vous pouvez également [accomplir le processus d’intégration en déployant des modèles Azure Resource Manager](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Fonctionnement de la gestion des ressources déléguées Azure

De façon générale, voici comment fonctionne la gestion des ressources déléguées Azure :

1. En tant que fournisseur de services, vous identifiez l’accès (les rôles) dont vos groupes, principaux de service ou utilisateurs auront besoin pour gérer les ressources Azure du client. La définition de l’accès contient l’ID de locataire du fournisseur de services, ainsi que l’accès requis pour l’offre, définis à l’aide des identités **principalId** de votre locataire mappées aux [valeurs **roleDefinition** intégrées](../../role-based-access-control/built-in-roles.md) (Contributeur, Contributeur de machines virtuelles, Lecteur, etc.).
2. Vous pouvez spécifier cet accès et intégrer le client à la gestion des ressources déléguées Azure de deux manières :
   - [Publier une offre de service managé (privé ou public) sur la Place de marché Azure](../how-to/publish-managed-services-offers.md), que le client acceptera.
   - [Déployer un modèle Azure Resource Manager sur le locataire du client](../how-to/onboard-customer.md) pour un ou plusieurs abonnements ou groupes de ressources spécifiques.
3. Une fois le client intégré, les utilisateurs autorisés peuvent se connecter au locataire de votre fournisseur de services et effectuer des tâches de gestion dans l’étendue du client, en fonction de l’accès que vous avez défini.

> [!NOTE]
> Vous pouvez gérer des ressources déléguées situées dans différentes [régions](../../availability-zones/az-overview.md#regions). Toutefois, la délégation d’abonnements sur un [cloud national](../../active-directory/develop/authentication-national-cloud.md) et le cloud public Azure, ou sur deux clouds nationaux distincts, n’est pas prise en charge.

## <a name="support-for-azure-delegated-resource-management"></a>Support pour la gestion des ressources déléguées Azure

Si vous avez besoin d’aide en lien avec la gestion des ressources déléguées Azure, vous pouvez ouvrir une demande de support sur le portail Azure. Pour **Type de problème**, choisissez **Technique**. Sélectionnez un abonnement, puis **Lighthouse** (sous **Surveillance et gestion**).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [Expériences de gestion inter-locataire](cross-tenant-management-experience.md).
- Découvrez les [Offres de services managés sur la Place de marché Azure](managed-services-offers.md).