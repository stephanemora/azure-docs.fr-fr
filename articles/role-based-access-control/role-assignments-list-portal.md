---
title: Lister les attributions de rôles à l’aide du RBAC Azure et du portail Azure
description: Découvrez comment déterminer les ressources, utilisateurs, groupes, principaux de service ou identités managées qui ont accès au contrôle d’accès en fonction du rôle (RBAC) Azure et au portail Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 25e11ae1311df9d0392340b32e0691298f78ee1c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74709889"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Lister les attributions de rôles à l’aide du RBAC Azure et du portail Azure

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] Cet article explique comment lister les attributions de rôles à l’aide du portail Azure.

## <a name="list-role-assignments-for-a-user-or-group"></a>Lister les attributions de rôles pour un utilisateur ou un groupe

La façon la plus simple de voir les rôles attribués à un utilisateur ou à un groupe dans un abonnement consiste à utiliser le volet **Ressources Azure**.

1. Dans le portail Azure, cliquez sur **Tous les services**, puis sélectionnez **Utilisateurs** ou **Groupes**.

1. Cliquez sur l’utilisateur ou le groupe pour lequel vous voulez lister les attributions de rôles.

1. Cliquez sur **Ressources Azure**.

    Vous voyez une liste de rôles attribués à l’utilisateur ou au groupe sélectionné dans différentes étendues, comme un groupe d’administration, un abonnement, un groupe de ressources ou une ressource. Cette liste inclut toutes les attributions de rôles que vous êtes autorisé à lire.

    ![Attributions de rôles pour un utilisateur](./media/role-assignments-list-portal/azure-resources-user.png)    

1. Pour changer d’abonnement, cliquez sur la liste **Abonnements**.

## <a name="list-role-assignments-at-a-scope"></a>Lister les attributions de rôles dans une étendue

1. Dans le portail Azure, cliquez sur **Tous les services**, puis sélectionnez l’étendue. Par exemple, vous pouvez sélectionner **Groupes d’administration**, **Abonnements**, **Groupes de ressources**, ou une ressource.

1. Cliquez sur la ressource spécifique.

1. Cliquez sur **Contrôle d’accès (IAM)** .

1. Cliquez sur l’onglet **Attributions de rôles** afin d’afficher toutes les attributions de rôles pour cette étendue.

   ![Contrôle d’accès - Onglet Attributions de rôles](./media/role-assignments-list-portal/access-control-role-assignments.png)

   L’onglet Attributions de rôles indique les personnes qui ont accès à cette étendue. Notez que certains rôles sont inclus dans l’étendue de **cette ressource**, tandis que d’autres sont **hérités** à partir d’une autre étendue. L’accès est attribué spécifiquement à cette ressource ou hérité d’une affectation à l’étendue parente.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Lister les attributions de rôles pour un utilisateur dans une étendue

Pour lister l’accès pour un utilisateur, un groupe, un principal de service ou une identité managée, vous listez leurs attributions de rôles. Suivez ces étapes pour lister les attributions de rôles pour un utilisateur, un groupe, un principal de service ou une identité managée dans une étendue particulière.

1. Dans le portail Azure, cliquez sur **Tous les services**, puis sélectionnez l’étendue. Par exemple, vous pouvez sélectionner **Groupes d’administration**, **Abonnements**, **Groupes de ressources**, ou une ressource.

1. Cliquez sur la ressource spécifique.

1. Cliquez sur **Contrôle d’accès (IAM)** .

1. Cliquez sur l’onglet **Vérifier l’accès**.

    ![Contrôle d’accès - onglet Vérifier l’accès](./media/role-assignments-list-portal/access-control-check-access.png)

1. Dans la liste **Rechercher**, sélectionnez le type principal de sécurité dont vous souhaitez contrôler l’accès.

1. Dans la zone de recherche, entrez une chaîne afin de rechercher, dans le répertoire, des noms d’affichage, des adresses e-mail ou des identificateurs d’objet.

    ![Liste de sélection Vérifier l’accès](./media/role-assignments-list-portal/check-access-select.png)

1. Cliquez sur le principal de sécurité pour ouvrir le volet **Affectations**.

    ![Volet Affectations](./media/role-assignments-list-portal/check-access-assignments.png)

    Dans ce volet, vous pouvez voir les rôles attribués au principal de sécurité sélectionné et la portée. S’il existe des affectations de refus dans cette étendue, ou héritées par cette étendue, elles sont listées.

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter ou supprimer des attributions de rôles à l’aide du RBAC Azure et du portail Azure](role-assignments-portal.md)
- [Résoudre des problèmes liés au contrôle d'accès en fonction du rôle pour les ressources Azure](troubleshooting.md)
