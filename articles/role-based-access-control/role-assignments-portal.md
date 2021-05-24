---
title: Attribuer des rôles Azure à l’aide du portail Azure - Azure RBAC
description: Découvrez comment accorder l’accès aux ressources Azure pour les utilisateurs, groupes, principaux de service ou identités managées à l’aide du portail Azure et du contrôle d’accès en fonction du rôle Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 05/07/2021
ms.author: rolyon
ms.custom: contperf-fy21q3-portal,subject-rbac-steps
ms.openlocfilehash: 95ac82bfbdef8843f3eb4fc10cea7a0309d9eb89
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109656831"
---
# <a name="assign-azure-roles-using-the-azure-portal"></a>Attribuer des rôles Azure à l’aide du portail Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Cet article explique comment attribuer des rôles à l’aide du portail Azure.

Si vous devez attribuer des rôles d’administrateur dans Azure Active Directory, consultez [Attribuer des rôles Azure AD aux utilisateurs](../active-directory/roles/manage-roles-portal.md).

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="choose-experience"></a>Choisir une expérience

Le contrôle d’accès en fonction du rôle (RBAC) Azure offre une nouvelle expérience pour attribuer des rôles Azure dans le portail Azure qui est actuellement en préversion publique. Si vous voulez essayer cette nouvelle expérience, effectuez les étapes spécifiées sous l’onglet **(Préversion)** .

#### <a name="current"></a>[Current](#tab/current/)

## <a name="step-1-identify-the-needed-scope"></a>Étape 1 : Identifier l’étendue nécessaire

[!INCLUDE [Scope for Azure RBAC introduction](../../includes/role-based-access-control/scope-intro.md)]Pour plus d’informations, consultez [Comprendre l’étendue](scope-overview.md).

![Diagramme montrant les niveaux d’étendue de RBAC Azure.](../../includes/role-based-access-control/media/scope-levels.png)

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans la zone de recherche située en haut, recherchez l’étendue à laquelle vous souhaitez accorder l’accès. Par exemple, recherchez **Groupes d’administration**, **Abonnements**, **Groupes de ressources**, ou une ressource spécifique.

    ![Capture d’écran de la recherche d’un groupe de ressources dans le portail Azure.](./media/shared/rg-portal-search.png)

1. Cliquez sur la ressource spécifique pour cette étendue.

    Voici l’illustration d’un exemple de groupe de ressources.

    ![Capture d’écran de la page de vue d’ensemble d’un groupe de ressources.](./media/shared/rg-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane"></a>Étape 2 : Ouvrir le volet Ajouter une attribution de rôle

La page **Contrôle d’accès (IAM)** vous permet généralement d’attribuer des rôles afin d’accorder l’accès aux ressources Azure. Elle est également connue sous le nom de gestion des identités et des accès (IAM) et apparaît à plusieurs endroits du portail Azure.

1. Cliquez sur **Contrôle d’accès (IAM)**.

    Voici un exemple de la page Contrôle d’accès (IAM) pour un groupe de ressources.

    ![Capture d’écran de la page Contrôle d’accès (IAM) d’un groupe de ressources.](./media/shared/rg-access-control.png)

1. Cliquez sur l’onglet **Attributions de rôles** afin d’afficher les attributions de rôles pour cette étendue.

1. Cliquez sur **Ajouter** > **Ajouter une attribution de rôle**.
   Si vous n’avez pas les autorisations pour attribuer des rôles, l’option Ajouter une attribution de rôle sera désactivée.

   ![Capture d’écran du menu Ajouter > Ajouter une attribution de rôle.](./media/shared/add-role-assignment-menu.png)

    Le volet Ajouter une attribution de rôle s’ouvre.

   ![Capture d’écran de la page Ajouter une attribution de rôle avec les options Rôle, Attribuer l’accès à et Sélectionner.](../../includes/role-based-access-control/media/add-role-assignment-page.png)

## <a name="step-3-select-the-appropriate-role"></a>Étape 3 : Sélectionner le rôle approprié

1. Dans la liste **Rôle**, recherchez le rôle que vous souhaitez affecter, ou faites-le défiler.

    Pour vous aider à déterminer le rôle approprié, vous pouvez pointer sur l’icône d’informations afin d’afficher une description du rôle. Pour plus d’informations, consultez l’article [Rôles intégrés Azure](built-in-roles.md).

   ![Capture d’écran de la liste Sélectionner un rôle dans Ajouter une attribution de rôle.](./media/role-assignments-portal/add-role-assignment-role.png)

1. Cliquez ici pour sélectionner le rôle.

## <a name="step-4-select-who-needs-access"></a>Étape 4 : Sélectionner les utilisateurs qui ont besoin d’un accès

1. Dans la liste **Attribuer l’accès à**, sélectionnez le type de principal de sécurité auquel attribuer l’accès.

    | Type | Description |
    | --- | --- |
    | **Utilisateur, groupe ou principal de service** | Si vous souhaitez attribuer le rôle à un utilisateur, un groupe ou un principal de service (application), sélectionnez ce type. |
    | **Identité managée affectée par l’utilisateur** | Si vous souhaitez affecter le rôle à une [identité managée affectée par l’utilisateur](../active-directory/managed-identities-azure-resources/overview.md), sélectionnez ce type. |
    | *Identité managée affectée par le système* | Si vous souhaitez affecter le rôle à une [identité managée affectée par le système](../active-directory/managed-identities-azure-resources/overview.md), sélectionnez l’instance de service Azure où se trouve l’identité managée. |

   ![Capture d’écran de la sélection d’un principal de sécurité dans Ajouter une attribution de rôle.](./media/role-assignments-portal/add-role-assignment-type.png)

1. Si vous avez sélectionné une identité managée affectée par l’utilisateur ou une identité managée affectée par le système, sélectionnez l’**abonnement** où se trouve l’identité managée.

1. Dans la section **Sélectionner**, recherchez le principal de sécurité en entrant une chaîne ou en faisant défiler la liste.

   ![Capture d’écran de la sélection d’un utilisateur dans Ajouter une attribution de rôle.](./media/role-assignments-portal/add-role-assignment-user.png)

1. Cliquez sur le principal de sécurité trouvé pour le sélectionner.

## <a name="step-5-assign-role"></a>Étape 5 : Affecter le rôle

1. Pour attribuer le rôle, cliquez sur **Enregistrer**.

   Après quelques instants, le principal de sécurité est attribué au rôle dans l’étendue sélectionnée.

1. Dans l’onglet **Attributions de rôle**, vérifiez que vous voyez l’attribution de rôle dans la liste.

    ![Capture d’écran de la liste des attributions de rôle après l’attribution d’un rôle.](./media/role-assignments-portal/rg-role-assignments.png)

#### <a name="preview"></a>[(Préversion)](#tab/preview/)

## <a name="step-1-identify-the-needed-scope-preview"></a>Étape 1 : Identifier l’étendue nécessaire (préversion)

[!INCLUDE [Scope for Azure RBAC introduction](../../includes/role-based-access-control/scope-intro.md)]Pour plus d’informations, consultez [Comprendre l’étendue](scope-overview.md).

![Diagramme montrant les niveaux d’étendue de RBAC Azure pour l’expérience en préversion.](../../includes/role-based-access-control/media/scope-levels.png)

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans la zone de recherche située en haut, recherchez l’étendue à laquelle vous souhaitez accorder l’accès. Par exemple, recherchez **Groupes d’administration**, **Abonnements**, **Groupes de ressources**, ou une ressource spécifique.

    ![Capture d’écran de la recherche d’un groupe de ressources dans le portail Azure pour une expérience en préversion.](./media/shared/rg-portal-search.png)

1. Cliquez sur la ressource spécifique pour cette étendue.

    Voici l’illustration d’un exemple de groupe de ressources.

    ![Capture d’écran de la page de vue d’ensemble d’un groupe de ressources pour une expérience en préversion.](./media/shared/rg-overview.png)

## <a name="step-2-open-the-add-role-assignment-page-preview"></a>Étape 2 : Ouvrir la page Ajouter une attribution de rôle (préversion)

La page **Contrôle d’accès (IAM)** vous permet généralement d’attribuer des rôles afin d’accorder l’accès aux ressources Azure. Elle est également connue sous le nom de gestion des identités et des accès (IAM) et apparaît à plusieurs endroits du portail Azure.

1. Cliquez sur **Contrôle d’accès (IAM)**.

    Voici un exemple de la page Contrôle d’accès (IAM) pour un groupe de ressources.

    ![Capture d’écran de la page Contrôle d’accès (IAM) d’un groupe de ressources pour une expérience en préversion.](./media/shared/rg-access-control.png)

1. Cliquez sur l’onglet **Attributions de rôles** afin d’afficher les attributions de rôles pour cette étendue.

1. Cliquez sur **Ajouter** > **Ajouter une attribution de rôle (préversion)** .

    Si vous n’avez pas les autorisations pour attribuer des rôles, l’option Ajouter une attribution de rôle sera désactivée.

    ![Capture d’écran du menu Ajouter > Ajouter une attribution de rôle pour une expérience en préversion.](./media/shared/add-role-assignment-menu-preview.png)

    La page Attribuer un rôle s’ouvre.

## <a name="step-3-select-the-appropriate-role-preview"></a>Étape 3 : Sélectionner le rôle approprié (préversion)

1. Sous l’onglet **Rôles**, sélectionnez un rôle que vous voulez utiliser.

    Vous pouvez rechercher un rôle par nom ou par description. Vous pouvez également filtrer les rôles par type et par catégorie.

   ![Capture d’écran de la page Ajouter une attribution de rôle avec l’onglet Sélectionner un rôle pour une expérience en préversion.](./media/role-assignments-portal/roles.png)

1. Dans la colonne **Détails**, cliquez sur **Afficher** pour obtenir plus de détails sur un rôle.

   ![Capture d’écran du volet Afficher les détails du rôle avec l’onglet Autorisations pour une expérience en préversion.](./media/role-assignments-portal/select-role-permissions.png)

1. Cliquez sur **Suivant**.

## <a name="step-4-select-who-needs-access-preview"></a>Étape 4 : Sélectionner les utilisateurs qui ont besoin d’un accès (préversion)

1. Sous l’onglet **Membres**, sélectionnez **Utilisateur, groupe ou principal de service** pour attribuer le rôle sélectionné à un ou plusieurs utilisateurs, groupes ou principaux de service (applications) Azure AD.

   ![Capture d’écran de la page Ajouter une attribution de rôle avec l’onglet Ajouter des membres pour une expérience en préversion.](./media/role-assignments-portal/members.png)

1. Cliquez sur **Ajouter des membres**.

1. Recherchez et sélectionnez les utilisateurs, les groupes ou les principaux de service.

    Vous pouvez entrer du texte dans la zone **Sélectionner** pour rechercher des noms d’affichage, des adresses e-mail et des identificateurs d’objet dans l’annuaire.

   ![Capture d’écran de l’option Ajouter des membres à l’aide du volet Sélectionner des membres pour une expérience en préversion.](./media/role-assignments-portal/select-principal.png)

1. Cliquez sur **Enregistrer** pour ajouter des utilisateurs, des groupes ou des principaux de service à la liste Membres.

1. Pour attribuer le rôle sélectionné à une ou plusieurs identités managées, sélectionnez **Identité managée**.

1. Cliquez sur **Ajouter des membres**.

1. Dans le volet **Sélectionner des identités gérées**, indiquez si le type est une [identité managée affectée par le système](../active-directory/managed-identities-azure-resources/overview.md) ou une [identité managée affectée par l’utilisateur](../active-directory/managed-identities-azure-resources/overview.md).

1. Recherchez et sélectionnez les identités managées.

    Si vous avez sélectionné une identité managée affectée par le système, vous devez sélectionner l’instance de service Azure où se trouve l’identité managée.

   ![Capture d’écran de l’ajout d’identités managées affectées par le système à l’aide du volet Sélectionner un principal pour une expérience en préversion.](./media/role-assignments-portal/select-managed-identity-system.png)

   ![Capture d’écran de l’ajout d’identités managées affectées par l’utilisateur à l’aide du volet Sélectionner un principal pour une expérience en préversion.](./media/role-assignments-portal/select-managed-identity-user.png)

1. Cliquez sur **Enregistrer** pour ajouter les identités managées à la liste Membres.

1. Dans la zone de texte **Description**, entrez une description facultative pour cette attribution de rôle.

    Par la suite, vous pouvez afficher cette description dans la liste des attributions de rôle.

1. Cliquez sur **Suivant**.

## <a name="step-5-optional-add-condition-preview"></a>Étape 5 : (Facultatif) Ajouter une condition (préversion)

Si vous avez sélectionné un rôle qui prend en charge les conditions, un onglet **Condition** s’affiche et vous avez la possibilité d’ajouter une condition à votre attribution de rôle. Une [condition](conditions-overview.md) est une vérification supplémentaire que vous pouvez éventuellement ajouter à votre attribution de rôle pour fournir un contrôle d’accès plus précis.

Actuellement, il est possible d’ajouter des conditions à des attributions de rôle intégrées ou personnalisées qui ont des [actions de données Blob de stockage](conditions-format.md#actions). Sont inclus les rôles intégrés suivants :


- [Contributeur aux données Blob du stockage](built-in-roles.md#storage-blob-data-contributor)
- [Propriétaire des données Blob du stockage](built-in-roles.md#storage-blob-data-owner)
- [Lecteur des données blob du stockage](built-in-roles.md#storage-blob-data-reader)

1. Cliquez sur **Ajouter une condition** si vous voulez affiner davantage les attributions de rôles en fonction des attributs d’objet blob de stockage. Pour plus d’informations, consultez [Ajouter ou modifier des conditions d’attribution de rôle Azure](conditions-role-assignments-portal.md).

   ![Capture d’écran de la page Ajouter une attribution de rôle avec l’onglet Ajouter une condition pour une expérience en préversion.](./media/shared/condition.png)

1. Cliquez sur **Suivant**.

## <a name="step-6-assign-role-preview"></a>Étape 6 : Attribuer un rôle (préversion)

1. Sous l’onglet **Vérifier + attribuer**, vérifiez les paramètres d’attribution de rôle.

   ![Capture d’écran de la page Attribuer un rôle avec l’onglet Vérifier + attribuer pour une expérience en préversion.](./media/role-assignments-portal/review-assign.png)

1. Cliquez sur **Vérifier + attribuer** pour attribuer le rôle.

   Après quelques instants, le principal de sécurité est attribué au rôle dans l’étendue sélectionnée.

    ![Capture d’écran de la liste d’attribution de rôle après l’attribution d’un rôle pour une expérience en préversion.](./media/role-assignments-portal/rg-role-assignments.png)

1. Si vous ne voyez pas la description de l’attribution de rôle, cliquez sur **Modifier les colonnes** pour ajouter la colonne **Description**.

---

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer à un utilisateur le rôle d’administrateur d’un abonnement Azure](role-assignments-portal-subscription-admin.md)
- [Supprimer des attributions de rôles Azure](role-assignments-remove.md)
- [Résoudre les problèmes liés à Azure RBAC](troubleshooting.md)
