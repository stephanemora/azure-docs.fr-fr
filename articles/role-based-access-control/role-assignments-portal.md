---
title: Gérer les accès à l’aide du contrôle d’accès en fonction du rôle et du portail Azure | Microsoft Docs
description: Découvrez comment gérer l’accès pour les utilisateurs, groupes et applications, à l’aide du Contrôle d’accès en fonction du rôle (RBAC) et du portail Azure. Cela comprend comment dresser la liste d’accès, accorder l’accès et supprimer l’accès.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0aec1f50225bea25d5f68289c22ccc7b69703f7a
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317944"
---
# <a name="manage-access-using-rbac-and-the-azure-portal"></a>Gérer les accès à l’aide du contrôle d’accès en fonction du rôle et du portail Azure

Le [Contrôle d’accès en fonction du rôle (RBAC)](overview.md) est la façon dont vous gérez l’accès aux ressources dans Azure. Cet article décrit comment gérer l’accès pour les utilisateurs, groupes et applications à l’aide de RBAC et du portail Azure.

## <a name="list-roles"></a>Répertorier les rôles

Une définition de rôle est une collection d’autorisations que vous utilisez pour les attributions de rôle. Azure dispose de plus de 60 [rôles intégrés](built-in-roles.md).

1. Dans le portail Azure, sélectionnez **Tous les services**, puis **Abonnements**.

1. Choisissez votre abonnement.

1. Cliquez sur **Contrôle d’accès (IAM)**.

   ![Option de rôles](./media/role-assignments-portal/list-subscription-access-control.png)

1. Choisissez **Rôles** pour afficher la liste de tous les rôles intégrés et personnalisés.

   ![Option de rôles](./media/role-assignments-portal/roles-option.png)

   Vous pouvez voir le nombre d’utilisateurs et de groupes attribués à chaque rôle.

   ![Liste de rôles](./media/role-assignments-portal/roles-list.png)

## <a name="list-access"></a>Répertorier les accès

Lorsque vous gérez des accès, vous souhaitez savoir quelles sont les personnes ayant accès, quelles sont ses autorisations et à quel niveau. Pour énumérer les accès, vous répertoriez les attributions de rôles.

### <a name="list-role-assignments-for-a-subscription"></a>Répertorier les attributions de rôles pour un abonnement

1. Dans le portail Azure, sélectionnez **Tous les services**, puis **Abonnements**.

1. Choisissez votre abonnement.

1. Cliquez sur **Contrôle d’accès (IAM)**.

    Dans le panneau Contrôle d’accès (IAM), également nommé gestion des identités et des accès, vous pouvez voir qui a accès à cet abonnement et son rôle.

    ![Panneau Contrôle d’accès (IAM)](./media/role-assignments-portal/subscription-access-control.png)

    Les administrateurs et coadministrateurs d’abonnement classiques sont considérés comme propriétaires de l’abonnement dans le modèle RBAC.


### <a name="list-role-assignments-for-a-resource-group"></a>Lister les attributions de rôles pour un groupe de ressources

1. Dans la liste de navigation, sélectionnez **Groupes de ressources**.

1. Choisissez un groupe de ressources, puis **Contrôle d’accès (IAM)**.

   Dans le panneau Contrôle d’accès (IAM), vous pouvez voir qui a accès à ce groupe de ressources. Notez que certains rôles sont inclus dans l’étendue de **cette ressource**, tandis que d’autres sont **hérités** à partir d’une autre étendue. L’accès est attribué spécifiquement au groupe de ressources ou hérité d’une affectation à l’abonnement parent.

   ![Groupes de ressources](./media/role-assignments-portal/resource-group-access-control.png)

### <a name="list-role-assignments-for-a-user"></a>Répertorier les attributions de rôles pour un utilisateur

1. Dans la liste de navigation, sélectionnez **Azure Active Directory**.

1. Choisissez **Utilisateurs** pour ouvrir **Tous les utilisateurs**.

   ![Panneau Azure Active Directory - Tous les utilisateurs](./media/role-assignments-portal/aad-all-users.png)

1. Choisissez un utilisateur individuel dans la liste.

1. Dans la section **Gérer**, choisissez **Ressources Azure**.

   ![Ressources Azure d’un utilisateur Azure Active Directory](./media/role-assignments-portal/aad-user-azure-resources.png)

   Dans le panneau Ressources Azure, vous pouvez voir les attributions de rôle pour l’utilisateur sélectionné. Cette liste inclut uniquement les attributions de rôles pour les ressources que vous êtes autorisé à lire. Par exemple, si l’utilisateur dispose également d’attributions de rôles dans un autre abonnement que vous ne pouvez pas lire, ces attributions de rôles n’apparaissent pas dans la liste.

## <a name="grant-access"></a>Accorder l'accès

Dans le contrôle d’accès en fonction du rôle, vous créez une attribution de rôle pour accorder l’accès.

### <a name="create-a-role-assignment-at-a-subscription-scope"></a>Créer une attribution de rôle à une étendue d’abonnement

1. Dans le portail Azure, sélectionnez **Tous les services**, puis **Abonnements**.

1. Choisissez votre abonnement.

1. Choisissez **Contrôle d’accès (IAM)** pour afficher la liste actuelle des attributions de rôles au niveau de l’étendue de l’abonnement.

   ![Panneau Contrôle d’accès (IAM) pour le groupe de ressources](./media/role-assignments-portal/grant-subscription-access-control.png)

1. Choisissez **Ajouter** pour ouvrir le volet **Ajouter des autorisations**.

   Si vous n’êtes pas autorisé à attribuer des rôles, l’option **Ajouter** ne s’affiche pas.

   ![Volet Ajouter des autorisations](./media/role-assignments-portal/add-permissions.png)

1. Dans la liste déroulante **Rôle**, sélectionnez un rôle, tel que **Contributeur de machines virtuelles**.

1. Dans la liste **Sélectionner** , sélectionnez un utilisateur, un groupe ou une application. Si vous ne voyez pas le principal de sécurité dans la liste, vous pouvez saisir du texte dans la zone **Sélectionner** pour rechercher des noms d’affichage, des adresses de messagerie et des identificateurs d’objet dans le répertoire.

1. Sélectionnez **Enregistrer** pour créer l’attribution de rôle.

   Après quelques instants, le principal de sécurité est attribué au rôle à l’étendue d’abonnement.

### <a name="create-a-role-assignment-at-a-resource-group-scope"></a>Créer une attribution de rôle à une étendue de groupe de ressources

1. Dans la liste de navigation, sélectionnez **Groupes de ressources**.

1. Choisissez un groupe de ressources.

1. Choisissez **Contrôle d’accès (IAM)** pour afficher la liste actuelle des attributions de rôle à l’étendue de groupe de ressources.

   ![Panneau Contrôle d’accès (IAM) pour le groupe de ressources](./media/role-assignments-portal/grant-resource-group-access-control.png)

1. Choisissez **Ajouter** pour ouvrir le volet **Ajouter des autorisations**.

   Si vous n’êtes pas autorisé à attribuer des rôles, l’option **Ajouter** ne s’affiche pas.

   ![Volet Ajouter des autorisations](./media/role-assignments-portal/add-permissions.png)

1. Dans la liste déroulante **Rôle**, sélectionnez un rôle, tel que **Contributeur de machines virtuelles**.

1. Dans la liste **Sélectionner** , sélectionnez un utilisateur, un groupe ou une application. Si vous ne voyez pas le principal de sécurité dans la liste, vous pouvez saisir du texte dans la zone **Sélectionner** pour rechercher des noms d’affichage, des adresses de messagerie et des identificateurs d’objet dans le répertoire.

1. Sélectionnez **Enregistrer** pour créer l’attribution de rôle.

   Après quelques instants, le principal de sécurité est attribué au rôle à l’étendue de groupe de ressources.

## <a name="remove-access"></a>Suppression d'accès

Dans le RBAC, vous supprimez une attribution de rôle pour supprimer un accès.

### <a name="remove-a-role-assignment"></a>Supprimer une attribution de rôle

1. Ouvrez le panneau **Contrôle d’accès (IAM)** pour l’abonnement, le groupe de ressources ou la ressource qui comporte l’attribution de rôle que vous souhaitez supprimer.

1. Dans la liste des attributions de rôle, ajoutez une coche à côté du principal de sécurité comportant l’attribution de rôle que vous souhaitez supprimer.

   ![Supprimer le message d’attribution de rôle](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Choisissez **Supprimer**.

   ![Supprimer le message d’attribution de rôle](./media/role-assignments-portal/remove-role-assignment.png)

1. Dans le message d’attribution de rôle qui s’affiche, choisissez **Oui**.

Les attributions de rôle héritées ne peuvent pas être supprimées. Si vous avez besoin de supprimer une attribution de rôle héritée, vous devez le faire au niveau de l’étendue où l’affectation de rôle a été créée. Dans la colonne **Étendue**, en regard de **Hérité**, un lien vous dirige vers les ressources auxquelles ce rôle a été affecté. Accédez à l’étendue répertoriée ici pour supprimer l’attribution de rôle.

## <a name="other-tools-to-manage-access"></a>Autres outils pour gérer l’accès

Vous pouvez attribuer des rôles et gérer l’accès avec les commandes RBAC Azure dans des outils autres que le portail Azure. Pour plus d’informations, consultez les liens suivants :

* [Azure PowerShell](role-assignments-powershell.md)
* [interface de ligne de commande Azure](role-assignments-cli.md)
* [API REST](role-assignments-rest.md)

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : accorder l’accès à un utilisateur avec RBAC et le portal Azure](quickstart-assign-role-user-portal.md)
* [Tutoriel : accorder l’accès à un utilisateur avec RBAC et Azure PowerShell](tutorial-role-assignments-user-powershell.md)
* [Rôles intégrés](built-in-roles.md)
