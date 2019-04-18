---
title: Accorder l’accès à d’autres administrateurs à gérer PIM - Azure Active Directory | Microsoft Docs
description: Découvrez comment accorder l’accès à d’autres administrateurs pour gérer Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb22e3cc93baebac023c0148812c6a4c6c95be60
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59489208"
---
# <a name="grant-access-to-other-administrators-to-manage-pim"></a>Accorder l’accès à d’autres administrateurs pour la gestion de PIM

L’administrateur Global qui permet à Azure Active Directory (Azure AD) Privileged Identity Management (PIM) pour une organisation automatiquement obtenir attributions de rôles et d’accès à PIM. Nulle autre personne ne dispose d’un accès en écriture par défaut, pas même les autres administrateurs généraux. Les autres administrateurs généraux, les administrateurs de la sécurité et les lecteurs Sécurité ont un accès en lecture seule à PIM. Pour donner accès à PIM, le premier utilisateur peut affecter d’autres personnes au rôle **Administrateur de rôle privilégié**.

> [!NOTE]
> La gestion de PIM nécessite de disposer d’Azure MFA. Les comptes Microsoft ne pouvant pas s’inscrire pour l’authentification Azure MFA, un utilisateur qui se connecte avec un compte Microsoft ne peut pas accéder à PIM.

Assurez-vous qu’il y a toujours au moins deux utilisateurs auxquels est affecté le rôle Administrateur de rôle privilégié, au cas où un utilisateur serait verrouillé ou son compte supprimé.

## <a name="grant-access-to-manage-pim"></a>Accorder l’accès pour gérer PIM

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Cliquez sur **Rôles Azure AD**.

1. Cliquez sur **Rôles**.

    ![Rôles PIM Azure AD - rôles](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Cliquez sur le rôle **Administrateur de rôle privilégié** pour ouvrir la page des membres.

    ![Administrateur de rôle privilégié - Membres](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Cliquez sur **Ajouter un membre** pour ouvrir le volet Ajouter des membres managés.

1. Cliquez sur **Sélectionner des membres** pour ouvrir le volet Sélectionner des membres.

    ![Administrateur de rôle privilégié - Sélectionner des membres](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Sélectionnez un membre, puis cliquez sur **Sélectionner**.

1. Cliquez sur **OK** afin de rendre le membre éligible pour le rôle **Administrateur de rôle privilégié**.

    Lorsque vous affectez un nouveau rôle à un utilisateur dans PIM, celui-ci est automatiquement configuré comme **Éligible** pour activer le rôle.

1. Pour rendre le membre permanent, cliquez sur l’utilisateur dans la liste de membres Administrateur de rôle privilégié.

1. Cliquez sur **Plus**, puis sur **Désigner comme permanent** afin de rendre l’affectation permanente.

    ![Administrateur de rôle privilégié - Désigner comme permanent](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Envoyez à l’utilisateur un lien vers [Commencer à utiliser PIM](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Supprimer l’accès pour gérer PIM

Avant de supprimer quelqu’un du rôle Administrateur de rôle privilégié, assurez-vous toujours qu’au moins deux utilisateurs y sont affectés.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Cliquez sur **Rôles Azure AD**.

1. Cliquez sur **Rôles**.

1. Cliquez sur le rôle **Administrateur de rôle privilégié** pour ouvrir la page des membres.

1. Ajoutez une coche en regard de l’utilisateur à supprimer, puis cliquez sur **Supprimer un membre**.

    ![Administrateur de rôle privilégié - Supprimer un membre](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Dans le message qui s’affiche pour vous demander si vous souhaitez supprimer le membre du rôle, cliquez sur **Oui**.

## <a name="next-steps"></a>Étapes suivantes

- [Commencer à utiliser PIM](pim-getting-started.md)
