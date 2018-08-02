---
title: Modifier le nom ou le logo d’une application d’entreprise dans Azure Active Directory | Microsoft Docs
description: Comment modifier le nom ou le logo d’une application d’entreprise personnalisée dans Azure Active Directory
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2017
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 47a53adb583ede0618321d9146362e4f663b0066
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39368757"
---
# <a name="change-the-name-or-logo-of-an-enterprise-app-in-azure-active-directory"></a>Modifier le nom ou le logo d’une application d’entreprise dans Azure Active Directory
Il est facile de modifier le nom ou le logo d’une application d’entreprise personnalisée dans Azure Active Directory (Azure AD). Vous devez disposer des autorisations appropriées pour effectuer ces modifications, et vous devez être le créateur de l’application personnalisée.

## <a name="how-do-i-change-an-enterprise-apps-name-or-logo"></a>Comment modifier le nom ou le logo d’une application d’entreprise ?
1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Tous les services**, entrez **Azure Active Directory** dans la zone de texte, puis sélectionnez **Entrée**.
3. Dans le volet **Azure Active Directory -*NomRépertoire*** (autrement dit, le volet Azure AD du répertoire que vous gérez), sélectionnez **Applications d’entreprise**.

    ![Ouverture des applications d’entreprise](./media/change-name-or-logo-portal/open-enterprise-apps.png)
4. Dans le volet **Applications d’entreprise**, sélectionnez **Toutes les applications**. Vous verrez une liste des applications que vous pouvez gérer.
5. Dans le volet **Applications d’entreprise - Toutes les applications**, sélectionnez une application.
6. Dans le volet ***NomApplication*** (autrement dit, le volet intitulé avec le nom de l’application sélectionnée), sélectionnez **Propriétés**.

    ![Sélection de la commande Propriétés](./media/change-name-or-logo-portal/select-app.png)
7. Dans le volet ***NomApplication*** **- Propriétés**, accédez à un fichier à utiliser en tant que nouveau logo et/ou modifiez le nom de l’application.

    ![Modification du logo de l’application ou de la commande PropriétésNom](./media/change-name-or-logo-portal/change-logo.png)
8. Sélectionnez la commande **Enregistrer** .

## <a name="next-steps"></a>Étapes suivantes
* [Voir tous mes groupes](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Affecter un utilisateur ou un groupe à une application d’entreprise](assign-user-or-group-access-portal.md)
* [Supprimer l’affectation d’un utilisateur ou d’un groupe à une application d’entreprise dans la version préliminaire d’Azure Active Directory](remove-user-or-group-access-portal.md)
* [Désactiver les connexions utilisateur pour une application d’entreprise](disable-user-sign-in-portal.md)
