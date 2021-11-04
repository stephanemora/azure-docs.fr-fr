---
title: Ajout ou modification des administrateurs d’abonnements Azure
description: Explique comment ajouter ou modifier un administrateur d’abonnement Azure à l’aide du contrôle d’accès en fonction du rôle (Azure RBAC).
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: banders
ms.openlocfilehash: ae4a48e62f3e61e6c3d56cfa9083a2a95b4b1a69
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473297"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Ajout ou modification des administrateurs d’abonnements Azure


Pour gérer l’accès aux ressources Azure, vous devez disposer du rôle d’administrateur approprié. Azure dispose d’un système d’autorisation appelé [contrôle d’accès en fonction du rôle Azure](../../role-based-access-control/overview.md) (Azure RBAC) avec plusieurs rôles intégrés parmi lesquels vous pouvez choisir. Vous pouvez attribuer ces rôles à différents niveaux (ou étendues) : groupe de gestion, abonnement, groupe de ressources, etc. Par défaut, la personne qui crée un abonnement Azure peut attribuer à d’autres utilisateurs un accès d’administration à un abonnement.

Cet article explique comment ajouter ou modifier le rôle d’administrateur pour un utilisateur qui a recours à Azure RBAC au niveau de l’abonnement.

Microsoft vous recommande de gérer l’accès aux ressources à l’aide d’Azure RBAC. Toutefois, si vous utilisez encore le modèle de déploiement classique et que vous gérez les ressources classiques à l'aide du [module PowerShell d'Azure Service Management](/powershell/module/servicemanagement/azure.service), vous devez utiliser un administrateur classique.

> [!TIP]
> Si vous utilisez le portail Azure seulement pour gérer les ressources classiques, vous n’avez pas besoin d’utiliser l’administrateur classique.

Pour plus d'informations, consultez [Déploiement Azure Resource Manager et déploiement classique](../../azure-resource-manager/management/deployment-models.md) et [Administrateurs d'abonnements classiques Azure](../../role-based-access-control/classic-administrators.md).

## <a name="determine-account-billing-administrator"></a>Déterminer l’administrateur de facturation d’un compte

<a name="whoisaa"></a>

L’administrateur de facturation est la personne autorisée à gérer la facturation d’un compte. Il est autorisé à accéder à la facturation sur le [portail Azure](https://portal.azure.com) et à effectuer différentes tâches liées à la facturation, comme la création d’abonnements, la consultation et le paiement des factures, ou la mise à jour des modes de paiement.

Pour identifier les comptes dont vous êtes l’administrateur de facturation, consultez la page [Gestion des coûts + facturation dans le portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview). Dans le volet de gauche, sélectionnez **Toutes les étendues de facturation**. La page des abonnements affiche tous les abonnements dont vous êtes l’administrateur de facturation.

Si vous n’êtes pas certain de l’identité de l’administrateur de compte d’un abonnement, accédez à la [page Abonnements dans le portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Sélectionnez ensuite l’abonnement que vous souhaitez consulter, puis regardez sous **Paramètres**. Sélectionnez **Propriétés**. L’administrateur de compte de l’abonnement s’affiche dans la zone **Administrateur de compte**.


## <a name="assign-a-subscription-administrator"></a>Attribuer un administrateur d’abonnement

<a name="add-an-admin-for-a-subscription"></a>

Pour qu’un utilisateur devienne administrateur d’un abonnement Azure, un administrateur de facturation existant lui attribue le rôle de [Propriétaire](../../role-based-access-control/built-in-roles.md#owner) (un rôle Azure) au niveau de l’abonnement. Le rôle Propriétaire donne à l’utilisateur un accès total à toutes les ressources de l’abonnement, ainsi que le droit de déléguer l’accès à d’autres personnes. Ces étapes sont les mêmes que celles de n’importe quelle autre attribution de rôle.

Si vous ne savez pas qui est l’administrateur de facturation de compte d’un abonnement, procédez comme suit pour le découvrir.

1. Ouvrez la [page Abonnements dans le portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Sélectionnez l’abonnement que vous souhaitez vérifier, puis regardez sous **Paramètres**.
1. Sélectionner **Propriétés**. L’administrateur de facturation de compte de l’abonnement s’affiche dans la zone **Administrateur de compte**.

### <a name="to-assign-a-user-as-an-administrator"></a>Pour attribuer un utilisateur en tant qu’administrateur

- Attribuez le rôle de propriétaire à l’utilisateur dans l’étendue de l’abonnement.  
     Pour connaître les étapes détaillées, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

## <a name="next-steps"></a>Étapes suivantes

* [Qu’est-ce que le contrôle d’accès en fonction du rôle Azure (RBAC Azure) ?](../../role-based-access-control/overview.md)
* [Comprendre les différents rôles dans Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Associer ou ajouter un abonnement Azure à votre locataire Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Autorisations des rôles d'administrateur dans Azure Active Directory](../../active-directory/roles/permissions-reference.md)