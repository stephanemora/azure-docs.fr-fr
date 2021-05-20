---
title: Architecture d’Azure Lighthouse
description: Découvrez la relation qui existe entre les locataires d’Azure Lighthouse et les ressources créées dans le locataire du client qui permettent cette relation.
ms.date: 05/11/2021
ms.topic: conceptual
ms.openlocfilehash: bdcd1057d4ba2adfe8ab312228acd562b384b09d
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109795438"
---
# <a name="azure-lighthouse-architecture"></a>Architecture d’Azure Lighthouse

Azure Lighthouse permet aux fournisseurs de services de simplifier les expériences d’engagement et d’intégration des clients, tout en gérant les ressources déléguées à grande échelle avec agilité et précision. Les utilisateurs, groupes et principaux de service autorisés peuvent travailler directement dans le contexte de l’abonnement d’un client sans avoir de compte dans le locataire Azure Active Directory (Azure AD) de ce client ou être copropriétaire du locataire du client. Le mécanisme utilisé pour prendre en charge cet accès est appelé gestion des ressources déléguées Azure.

:::image type="content" source="../media/delegation.jpg" alt-text="Diagramme illustrant la gestion des ressources déléguées Azure.":::

> [!TIP]
> Azure Lighthouse peut aussi être utilisé [dans une entreprise qui compte plusieurs locataires Azure AD propres](enterprise.md) afin de simplifier la gestion interlocataire.

Cette rubrique traite de la relation qui existe entre les locataires d’Azure Lighthouse et les ressources créées dans le locataire du client qui permettent cette relation.

## <a name="delegation-resources-created-in-the-customer-tenant"></a>Ressources de délégation créées dans le locataire du client

Quand l’abonnement ou le groupe de ressources d’un client est intégré à Azure Lighthouse, deux ressources sont créées : la **définition d’inscription** et l’**attribution d’inscription**. Vous pouvez accéder à ces ressources à l’aide d’[API et d’outils de gestion](cross-tenant-management-experience.md#apis-and-management-tool-support), ou vous pouvez les utiliser sur le [portail Azure](../how-to/view-manage-customers.md).

### <a name="registration-definition"></a>Définition d’inscription

La définition d’inscription contient les détails de l’offre Azure Lighthouse (ID du locataire de gestion et autorisations qui attribuent les rôles intégrés à des utilisateurs, groupes et/ou principaux de service spécifiques au sein du locataire de gestion).

Une définition d’inscription est créée au niveau de l’abonnement pour chaque abonnement délégué ou dans chaque abonnement qui contient un groupe de ressources délégué. Si vous prévoyez de créer une définition d’inscription à l’aide d’API, vous devez travailler au niveau de l’abonnement. Par exemple, si vous utilisez Azure PowerShell, vous devez utiliser New-AzureRmDeployment avant de créer une définition d’inscription ([New-AzManagedServicesDefinition](/powershell/module/az.managedservices/new-azmanagedservicesdefinition)) au lieu d’utiliser New-AzureRmResourceGroupDeployment.

### <a name="registration-assignment"></a>Attribution d’inscription

L’attribution d’inscription attribue la définition d’inscription à une étendue spécifique, c’est-à-dire le ou les abonnements intégrés et/ou le ou les groupes de ressources.

Sachant qu’une attribution d’inscription est créée dans chaque étendue déléguée, elle se situe soit au niveau du groupe d’abonnement, soit au niveau du groupe de ressources, selon ce qui a été intégré.

Chaque attribution d’inscription doit faire référence à une définition d’inscription valide au niveau de l’abonnement, liant les autorisations pour ce fournisseur de services à l’étendue déléguée et accordant ainsi l’accès.

## <a name="logical-projection"></a>Projection logique

Azure Lighthouse crée une projection logique de ressources d’un locataire vers un autre. Cela permet aux utilisateurs autorisés du fournisseur de services de se connecter à leur propre locataire avec l’autorisation de travailler dans les abonnements et les groupes de ressources de clients délégués. Les utilisateurs du locataire du fournisseur de services peuvent ainsi effectuer des opérations de gestion au nom de leurs clients, sans avoir à se connecter au locataire de chaque client individuel.

Chaque fois qu’un utilisateur, un groupe ou un principal de service dans le locataire du fournisseur de services accède aux ressources du locataire d’un client, Azure Resource Manager reçoit une demande. Resource Manager authentifie ces demandes comme il le fait pour les demandes émanant d’utilisateurs du propre locataire du client. Pour Azure Lighthouse, il fait cela en vérifiant que les deux ressources (la définition d’inscription et l’attribution d’inscription) sont présentes dans le locataire du client. Si c’est le cas, Resource Manager autorise l’accès en fonction des informations définies par ces ressources.

:::image type="content" source="../media/logical-projection.jpg" alt-text="Diagramme illustrant la projection logique dans Azure Lighthouse.":::

L’activité des utilisateurs dans le locataire du fournisseur de services est suivie dans le journal d’activité, qui est stocké dans le locataire du client. Le client peut ainsi [voir les modifications qui ont été apportées et identifier leurs auteurs](../how-to/view-service-provider-activity.md).

## <a name="how-azure-lighthouse-works"></a>Fonctionnement d’Azure Lighthouse

Globalement, voici comment Azure Lighthouse fonctionne :

1. Identifiez les [rôles](tenants-users-roles.md#role-support-for-azure-lighthouse) dont vos groupes, principaux de service ou utilisateurs auront besoin pour gérer les ressources Azure du client.
2. Spécifiez cet accès et intégrez le client à Azure Lighthouse en [publiant une offre de service managé sur la Place de marché Azure](../how-to/publish-managed-services-offers.md) ou en [déployant un modèle Azure Resource Manager](../how-to/onboard-customer.md). Ce processus d’intégration crée les deux ressources décrites ci-dessus (définition d’inscription et attribution d’inscription) dans le locataire du client.
3. Une fois le client intégré, les utilisateurs autorisés se connectent à votre locataire de gestion et effectuent des tâches au niveau de l’étendue (abonnement ou groupe de ressources) du client, en fonction de l’accès que vous avez défini. Les clients peuvent examiner toutes les actions effectuées et retirer l’accès à tout moment.

Si, dans la plupart des cas, les ressources spécifiques d’un client sont gérées par un seul et même fournisseur de services, le client a la possibilité de créer plusieurs délégations pour un même abonnement ou groupe de ressources, ce qui permet à plusieurs fournisseurs de services d’y avoir accès. Ce scénario autorise également les scénarios d’ISV qui [projettent des ressources du locataire du fournisseur de services vers plusieurs clients](isv-scenarios.md#saas-based-multi-tenant-offerings).

## <a name="next-steps"></a>Étapes suivantes

- Examinez les commandes [Azure CLI](/cli/azure/managedservices/assignment) et [Azure PowerShell](/powershell/module/az.managedservices/new-azmanagedservicesdefinition) permettant d’utiliser des définitions d’inscription et des attributions d’inscription.
- Découvrez les [services et scénarios améliorés](cross-tenant-management-experience.md#enhanced-services-and-scenarios) pour Azure Lighthouse.
- Découvrez plus en détail le fonctionnement des [locataires, utilisateurs et rôles](tenants-users-roles.md) avec Azure Lighthouse.
