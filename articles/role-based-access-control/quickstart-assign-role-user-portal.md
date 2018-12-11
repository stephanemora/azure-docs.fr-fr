---
title: 'Tutoriel : Accorder l’accès à un utilisateur avec RBAC et le portail Azure | Microsoft Docs'
description: Utilisez le contrôle d’accès en fonction du rôle (RBAC) pour attribuer un rôle à un utilisateur dans le portail Azure et lui accorder des autorisations.
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
ms.date: 11/30/2018
ms.author: rolyon
ms.openlocfilehash: 8caa5c3b33ac1b483429251e0c1256636c4ece1a
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634470"
---
# <a name="tutorial-grant-access-for-a-user-using-rbac-and-the-azure-portal"></a>Tutoriel : Accorder l’accès à un utilisateur avec RBAC et le portail Azure

Le [contrôle d’accès en fonction du rôle (RBAC)](overview.md) est la façon dont vous gérez l’accès aux ressources dans Azure. Dans ce tutoriel, vous allez autoriser un utilisateur à créer et à gérer des machines virtuelles dans un groupe de ressources.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Accorder l’accès à un utilisateur au niveau du groupe de ressources
> * Suppression d'accès

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur http://portal.azure.com.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

1. Dans la liste de navigation, sélectionnez **Groupes de ressources**.

1. Choisissez **Ajouter** pour ouvrir le panneau **Groupe de ressources**.

   ![Ajouter un nouveau groupe de ressources](./media/quickstart-assign-role-user-portal/resource-group.png)

1. Dans **Nom du groupe de ressources**, entrez **rbac-resource-group**.

1. Sélectionnez un abonnement et un emplacement.

1. Choisissez **Créer** pour créer le groupe de ressources.

1. Choisissez **Actualiser** pour actualiser la liste des groupes de ressources.

   Le nouveau groupe de ressources apparaît dans la liste de vos groupes de ressources.

   ![Liste de groupes de ressources](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>Accorder l'accès

Dans le contrôle d’accès en fonction du rôle, vous créez une attribution de rôle pour accorder l’accès.

1. Dans la liste **Groupes de ressources**, choisissez le nouveau groupe de ressources **rbac-resource-group**.

1. Cliquez sur **Contrôle d’accès (IAM)**.

1. Choisissez l’onglet **Attributions de rôles** pour afficher la liste actuelle des attributions de rôles.

   ![Panneau Contrôle d’accès (IAM) pour le groupe de ressources](./media/quickstart-assign-role-user-portal/access-control.png)

1. Choisissez **Ajouter une attribution de rôle** pour ouvrir le volet Ajouter une attribution de rôle.

   Si vous n’avez pas les autorisations pour attribuer des rôles, l’option Ajouter une attribution de rôle sera désactivée.

   ![Volet Ajouter une attribution de rôle](./media/quickstart-assign-role-user-portal/add-role-assignment.png)

1. Dans la liste déroulante **Rôle**, sélectionnez **Contributeur de machines virtuelles**.

1. Dans la liste **Sélectionner**, sélectionnez un utilisateur : vous-même ou un autre.

1. Sélectionnez **Enregistrer** pour créer l’attribution de rôle.

   Après quelques instants, l’utilisateur se voit attribuer le rôle Contributeur de machines virtuelles dans l’étendue du groupe de ressources rbac-resource-group.

   ![Attribution du rôle Contributeur de machines virtuelles](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Suppression d'accès

Dans le RBAC, vous supprimez une attribution de rôle pour supprimer un accès.

1. Dans la liste des attributions de rôles, ajoutez une coche en regard de l’utilisateur titulaire du rôle Contributeur de machines virtuelles.

1. Choisissez **Supprimer**.

   ![Supprimer le message d’attribution de rôle](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. Dans le message d’attribution de rôle qui s’affiche, choisissez **Oui**.

## <a name="clean-up"></a>Nettoyer

1. Dans la liste de navigation, sélectionnez **Groupes de ressources**.

1. Choisissez **rbac-resource-group** pour ouvrir le groupe de ressources.

1. Choisissez **Supprimez le groupe de ressources** pour supprimer le groupe de ressources.

   ![Supprimer un groupe de ressources](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. Dans le panneau **Voulez-vous vraiment supprimer**, tapez le nom du groupe de ressources : **rbac-resource-group**.

1. Choisissez **Supprimer** pour supprimer le groupe de ressources.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Accorder l’accès à un utilisateur avec RBAC et PowerShell](tutorial-role-assignments-user-powershell.md)

