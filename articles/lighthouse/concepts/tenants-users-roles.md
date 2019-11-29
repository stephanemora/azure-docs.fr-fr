---
title: Locataires, rôles et utilisateurs dans les scénarios Azure Lighthouse
description: Découvrez les concepts d’Azure Active Directory pour les locataires, les utilisateurs et les rôles, ainsi que la façon dont ils peuvent être utilisés dans les scénarios Azure Lighthouse.
ms.date: 11/05/2019
ms.topic: conceptual
ms.openlocfilehash: a1ad004c79f90f4642640405da9e8876b9202e98
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463930"
---
# <a name="tenants-roles-and-users-in-azure-lighthouse-scenarios"></a>Locataires, rôles et utilisateurs dans les scénarios Azure Lighthouse

Avant d’intégrer des clients pour la [gestion des ressources déléguées Azure](azure-delegated-resource-management.md), il est important de comprendre comment les locataires, les utilisateurs et les rôles fonctionnent dans Azure Active Directory (Azure AD), ainsi que la façon dont ils peuvent être utilisés dans les scénarios Azure Lighthouse.

Un *locataire* est une instance dédiée et approuvée d’Azure AD. En général, chaque locataire représente une seule organisation. La gestion des ressources déléguées Azure permet d’opérer une projection logique des ressources d’un locataire sur un autre. Cela permet aux utilisateurs du locataire de gestion (appartenant par exemple à un fournisseur de services) d’accéder à des ressources déléguées dans le locataire d’un client, ou permet aux [entreprises avec plusieurs locataires de centraliser leurs opérations de gestion](enterprise.md).

Pour atteindre cette projection logique, un abonnement (ou un ou plusieurs groupes de ressources au sein d’un abonnement) dans le locataire client doit être *intégré* pour la gestion des ressources déléguées Azure. Ce processus d’intégration peut être effectué [par le biais de modèles Azure Resource Manager](../how-to/onboard-customer.md) ou par [la publication d’une offre publique ou privée sur la Place de marché Azure](../how-to/publish-managed-services-offers.md).

Quelle que soit la méthode d’intégration choisie, vous devez définir des *autorisations*. Chaque autorisation spécifie un compte d’utilisateur dans le locataire gérant qui aura accès aux ressources déléguées, et un rôle intégré qui définit les autorisations dont chacun de ces utilisateurs aura besoin pour ces ressources.

## <a name="role-support-for-azure-delegated-resource-management"></a>Prise en charge des rôles pour la gestion des ressources déléguées Azure

Lors de la définition d’une autorisation, chaque compte utilisateur doit recevoir un des [rôles de contrôle d'accès en fonction du rôle intégrés](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles). Les rôles personnalisés et les [Rôles Administrateur classique de l’abonnement](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) ne sont pas pris en charge.

Tous les [rôles intégrés](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) sont actuellement pris en charge avec la gestion des ressources déléguées Azure, avec les exceptions suivantes :

- Le rôle [propriétaire](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) n’est pas pris en charge.
- Les rôles intégrés disposant d’une autorisation [DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) ne sont pas pris en charge.
- Le rôle intégré [Administrateur de l’accès utilisateur](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) est pris en charge, mais uniquement pour les besoins limités [d’affectation de rôles à une identité gérée dans le locataire client](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant). Aucune autre autorisation généralement accordée par ce rôle ne s’applique. Si vous définissez un utilisateur avec ce rôle, vous devez également spécifier le ou les rôles intégrés que cet utilisateur peut affecter aux identités gérées.

## <a name="best-practices-for-defining-users-and-roles"></a>Meilleures pratiques pour la définition des utilisateurs et des rôles

Lorsque vous créez vos autorisations, nous vous recommandons de suivre ces meilleures pratiques :

- Dans la plupart des cas, vous affectez des autorisations à un groupe d’utilisateurs ou à un principal de service Azure AD, plutôt qu’à une série de comptes d’utilisateur individuels. Cela vous permet d’ajouter ou de supprimer l’accès d’utilisateurs individuels sans devoir mettre à jour et republier le plan lorsque vos conditions d’accès changent.
- Veillez à suivre le principe du privilège minimum afin que les utilisateurs disposent uniquement des autorisations nécessaires pour accomplir leur travail, ce qui contribue à réduire le risque d’erreurs accidentelles. Pour plus d’informations, voir [Pratiques de sécurité recommandées](../concepts/recommended-security-practices.md).
- Incluez un utilisateur avec le [rôle Supprimer l’attribution de l’inscription des services gérés](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-services-registration-assignment-delete-role) afin de pouvoir [supprimer l’accès à la délégation](../how-to/onboard-customer.md#remove-access-to-a-delegation) ultérieurement si nécessaire. Si ce rôle n’est pas attribué, les ressources déléguées ne peuvent être supprimées que par un utilisateur dans le locataire du client.
- Assurez-vous que tous les utilisateurs qui ont besoin [d’afficher la page Mes clients dans le portail Azure](../how-to/view-manage-customers.md) possèdent le rôle de [Lecteur](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) (ou un autre rôle intégré qui inclut l’accès Lecteur).

## <a name="next-steps"></a>Étapes suivantes

- Consultez les [pratiques de sécurité recommandées pour la gestion des ressources déléguées Azure](recommended-security-practices.md).
- Intégrez vos clients à la gestion des ressources déléguées Azure [en utilisant des modèles Azure Resource Manager](../how-to/onboard-customer.md) ou [en publiant une offre de services managés privés ou publics sur la Place de marché Azure](../how-to/publish-managed-services-offers.md).
