---
title: Ajouter une attribution de rôle pour une identité managée (préversion) - Azure RBAC
description: Découvrez comment ajouter une attribution de rôle en commençant par l’identité managée, puis sélectionnez l’étendue et le rôle à l’aide du portail Azure et du contrôle d’accès en fonction du rôle Azure (RBAC Azure).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: a01246c0cf35653f4d13262183cf9df28b056c69
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121723"
---
# <a name="add-a-role-assignment-for-a-managed-identity-preview"></a>Ajouter une attribution de rôle pour une identité managée (préversion)

Vous pouvez ajouter des attributions de rôles pour une identité managée à l’aide de la page **Contrôle d’accès (IAM)** comme cela est décrit dans [Ajouter ou supprimer des attributions de rôles Azure à l’aide du portail Azure](role-assignments-portal.md). Lorsque vous utilisez la page Contrôle d’accès (IAM), vous démarrez avec l’étendue, puis vous sélectionnez l’identité managée et le rôle. Cet article décrit une autre façon d’ajouter des attributions de rôles pour une identité managée. En suivant ces étapes, vous commencez par l’identité managée, puis vous sélectionnez l’étendue et le rôle.

> [!IMPORTANT]
> L’ajout d’une attribution de rôle pour une identité managée à l’aide en suivant ces autres étapes est actuellement en préversion.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="system-assigned-managed-identity"></a>Identité managée affectée par le système

Procédez comme suit pour attribuer un rôle à une identité managée attribuée par le système en commençant par l’identité managée.

1. Dans le Portail Azure, ouvrez une identité gérée affectée par le système.

1. Dans le menu de gauche, cliquez sur **identité**.

    ![Identité managée affectée par le système](./media/shared/identity-system-assigned.png)

1. Sous **Autorisations**, cliquez sur **Attributions de rôles Azure**.

    Si des rôles sont déjà attribués à l’identité managée attribuée par le système sélectionnée, la liste des attributions de rôles s’affiche. Cette liste inclut toutes les attributions de rôles que vous êtes autorisé à lire.

    ![Attributions de rôles pour une identité gérée affectée par le système](./media/shared/role-assignments-system-assigned.png)

1. Pour changer d’abonnement, cliquez sur la liste **Abonnement**.

1. Cliquez sur **Ajouter une attribution de rôle (préversion)** .

1. Utilisez les listes déroulantes pour sélectionner l’ensemble de ressources auquel s’applique l’attribution de rôle, par exemple **Abonnement**, **Groupe de ressources** ou ressource.

    Si vous n’avez pas d’autorisations d’accès en écriture pour l’étendue sélectionnée, un message inline s’affiche. 

1. Dans la liste déroulante **Rôle**, sélectionnez un rôle, tel que **Contributeur de machines virtuelles**.

   ![Volet Ajouter une attribution de rôle pour une identité managée affectée par le système](./media/role-assignments-portal-managed-identity/add-role-assignment-with-scope.png)

1. Cliquez sur **Enregistrer** pour attribuer le rôle.

   Après quelques instants, le rôle est attribué à l’identité managée dans l’étendue sélectionnée.

## <a name="user-assigned-managed-identity"></a>Identité managée affectée par l’utilisateur

Procédez comme suit pour attribuer un rôle à une identité managée attribuée par l’utilisateur en commençant par l’identité managée.

1. Dans le Portail Azure, ouvrez une identité gérée affectée par l’utilisateur.

1. Dans le menu gauche, cliquez sur **Attributions de rôles Azure**.

    Si des rôles sont déjà attribués à l’identité managée attribuée par l’utilisateur sélectionnée, la liste des attributions de rôles s’affiche. Cette liste inclut toutes les attributions de rôles que vous êtes autorisé à lire.

    ![Attributions de rôles pour une identité managée affectée par l’utilisateur](./media/shared/role-assignments-user-assigned.png)

1. Pour changer d’abonnement, cliquez sur la liste **Abonnement**.

1. Cliquez sur **Ajouter une attribution de rôle (préversion)** .

1. Utilisez les listes déroulantes pour sélectionner l’ensemble de ressources auquel s’applique l’attribution de rôle, par exemple **Abonnement**, **Groupe de ressources** ou ressource.

    Si vous n’avez pas d’autorisations d’accès en écriture pour l’étendue sélectionnée, un message inline s’affiche. 

1. Dans la liste déroulante **Rôle**, sélectionnez un rôle, tel que **Contributeur de machines virtuelles**.

   ![Volet Ajouter une attribution de rôle pour une identité managée affectée par l’utilisateur](./media/role-assignments-portal-managed-identity/add-role-assignment-with-scope.png)

1. Cliquez sur **Enregistrer** pour attribuer le rôle.

   Après quelques instants, le rôle est attribué à l’identité managée dans l’étendue sélectionnée.

## <a name="next-steps"></a>Étapes suivantes

- [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md)
- [Ajouter ou supprimer des attributions de rôles Azure avec le portail Azure](role-assignments-portal.md)
- [Répertorier les attributions de rôle Azure à l’aide du portail Azure](role-assignments-list-portal.md)
