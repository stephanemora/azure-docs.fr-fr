---
title: 'Tutoriel : Accorder un accès utilisateur aux ressources Azure à l’aide du portail Azure - RBAC Azure'
description: Découvrez dans ce tutoriel comment accorder un accès utilisateur aux ressources Azure à l’aide du portail Azure et du contrôle d’accès en fonction du rôle Azure (RBAC Azure).
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 06/07/2021
ms.author: rolyon
ms.custom: subject-rbac-steps
ms.openlocfilehash: 5df6b591cc7ba3795104957b9cd11de814e2472a
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2021
ms.locfileid: "111591863"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-the-azure-portal"></a>Tutoriel : Accorder un accès utilisateur aux ressources Azure à l’aide du portail Azure

Le [contrôle d’accès en fonction du rôle Azure (RBAC Azure)](overview.md) vous permet de gérer l’accès aux ressources Azure. Dans ce tutoriel, vous allez autoriser un utilisateur à créer et à gérer des machines virtuelles dans un groupe de ressources.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Accorder l’accès à un utilisateur au niveau du groupe de ressources
> * Suppression d'accès

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

1. Dans la liste de navigation, cliquez sur **Groupes de ressources**.

1. Cliquez sur **Nouveau** pour ouvrir la page **Créer un groupe de ressources**.

   ![Page Créer un groupe de ressources.](./media/quickstart-assign-role-user-portal/resource-group.png)

1. Sélectionnez un abonnement.

1. Pour le nom du **groupe de ressources**, entrez **example-group** ou un autre nom.

1. Cliquez sur **Vérifier + créer**, puis sur **Créer** pour créer le groupe de ressources.

1. Cliquez sur **Actualiser** pour actualiser la liste des groupes de ressources.

   Le nouveau groupe de ressources apparaît dans la liste de vos groupes de ressources.

## <a name="grant-access"></a>Accorder l'accès

Dans le RBAC Azure, vous attribuez un rôle Azure pour accorder l’accès.

1. Dans la liste des **groupes de ressources**, ouvrez le nouveau groupe de ressources **example-group**.

1. Dans le menu de navigation, cliquez sur **Contrôle d’accès (IAM)** .

1. Cliquez sur l’onglet **Attributions de rôles** pour voir la liste actuelle des attributions de rôles.

   ![Page Contrôle d’accès (IAM) pour un groupe de ressources.](./media/shared/rg-role-assignments.png)


1. Cliquez sur **Ajouter** > **Ajouter une attribution de rôle (préversion)** .

   Si vous n’avez pas les autorisations pour attribuer des rôles, l’option Ajouter une attribution de rôle sera désactivée.

   ![Page Contrôle d’accès (IAM) avec le menu Ajouter une attribution de rôle ouvert.](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. Sous l’onglet **Rôle**, sélectionnez le rôle **Contributeur de machines virtuelles**.

    ![Page Ajouter une attribution de rôle avec l’onglet Rôle sélectionné.](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. Sous l’onglet **Membres**, sélectionnez-vous, ou sélectionnez un autre utilisateur.

1. Sous l’onglet **Vérifier + attribuer**, vérifiez les paramètres d’attribution de rôle.

1. Cliquez sur **Vérifier + attribuer** pour attribuer le rôle.

   Après quelques instants, l’utilisateur se voit attribuer le rôle Contributeur de machines virtuelles dans l’étendue du groupe de ressources example-group.

   ![Attribution du rôle Contributeur de machines virtuelles.](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Supprimer l’accès

Dans le RBAC Azure, pour supprimer un accès, vous supprimez une attribution de rôle.

1. Dans la liste des attributions de rôles, ajoutez une coche en regard de l’utilisateur titulaire du rôle Contributeur de machines virtuelles.

1. Cliquez sur **Supprimer**.

   ![Message relatif à la suppression des attributions de rôles.](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. Dans le message d’attribution de rôle qui s’affiche, cliquez sur **Oui**.

## <a name="clean-up"></a>Nettoyer

1. Dans la liste de navigation, cliquez sur **Groupes de ressources**.

1. Cliquez sur **example-group** pour ouvrir le groupe de ressources.

1. Cliquez sur **Supprimer le groupe de ressources** pour supprimer le groupe de ressources.

1. Dans le volet **Voulez-vous vraiment supprimer**, tapez le nom du groupe de ressources, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Accorder un accès utilisateur aux ressources Azure à l’aide d’Azure PowerShell](tutorial-role-assignments-user-powershell.md)
