---
title: Commencer à utiliser PIM - Azure | Microsoft Docs
description: Découvrez comment utiliser Azure AD Privileged Identity Management (PIM) dans le portail Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 08/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 5b3bff27821964648713b02589c941c99e3eb03d
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190087"
---
# <a name="start-using-pim"></a>Commencer à utiliser PIM

Avec Azure Active Directory (Azure AD) Privileged Identity Management (PIM), vous pouvez gérer, contrôler et superviser les accès au sein de votre organisation. Cette étendue inclut l’accès aux ressources Azure, à Azure AD et à d’autres services en ligne Microsoft comme Office 365 ou Microsoft Intune.

Cet article vous indique comment ajouter l'application Azure AD PIM à votre tableau de bord du portail Azure.

## <a name="first-person-to-use-pim"></a>Première personne à utiliser PIM

Si vous êtes la première personne à utiliser PIM dans votre annuaire, les rôles [Administrateur de sécurité](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) et [Administrateur de rôle privilégié](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) vous sont automatiquement attribués pour cet annuaire. Seuls les administrateurs de rôle privilégié peuvent gérer les attributions de rôles d’annuaire Azure AD d’utilisateurs. En outre, vous pouvez choisir d’exécuter [l’Assistant Sécurité.](pim-security-wizard.md) qui vous guide tout au long du processus de découverte et d’attribution.

## <a name="add-pim-tile-to-the-dashboard"></a>Ajouter une vignette PIM au tableau de bord

Pour faciliter l’ouverture de PIM, vous pouvez ajouter une vignette PIM à votre tableau de bord, dans le portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre annuaire.

1. Cliquez sur **Tous les services**, puis recherchez le service **Azure AD Privileged Identity Management**.

    ![Azure AD Privileged Identity Management dans Tous les services](./media/pim-getting-started/pim-all-services-find.png)

1. Cliquez pour ouvrir le guide de démarrage rapide de PIM.

1. Cochez la case **Épingler le panneau au tableau de bord** pour épingler le panneau du démarrage rapide de PIM au tableau de bord.

    ![Épingler le panneau au tableau de bord](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Dans le tableau de bord Azure, vous verrez une vignette comme celle-ci :

    ![Vignette du démarrage rapide de PIM](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="navigate-to-your-tasks"></a>Accédez à vos tâches

Une fois PIM configuré, vous pouvez utiliser ce panneau pour accomplir vos tâches de gestion des identités.

![Tâches de niveau supérieur pour PIM - capture d’écran](./media/pim-getting-started/pim-quickstart-tasks.png)

| Tâche + Gérer | Description |
| --- | --- |
| **Mes rôles**  | Affiche la liste des rôles éligibles et actifs qui vous sont attribués. C’est là où vous pouvez activer tout rôle éligible attribué. |
| **Mes demandes** | Affiche toutes vos requêtes en attente pour activer les attributions de rôles éligibles. |
| **Accès aux applications** | Permet de réduire les délais potentiels et d’utiliser un rôle immédiatement après son activation. |
| **Approuver des requêtes** | Affiche la liste des demandes d’activation des rôles éligibles par utilisateur dans votre annuaire, dont l’approbation vous revient. |
| **Révision d’accès** | Répertorie les révisions d’accès que vous devez terminer, que vous les effectuiez pour vous-même ou pour quelqu’un d’autre. |
| **Rôles d’annuaire Azure AD** | Affiche un tableau de bord et des paramètres pour les administrateurs de rôle privilégié qui doivent gérer les attributions de rôles d’annuaire Azure AD. Ce tableau de bord est désactivée pour toute personnes n’étant pas un administrateur de rôle privilégié. Ces utilisateurs ont accès à un tableau de bord spécial intitulé Mon affichage. Le tableau de bord Mon affichage présente uniquement des informations sur l’utilisateur qui accède au tableau de bord (et non sur l’ensemble du locataire). |
| **Ressources Azure** | Affiche un tableau de bord et des paramètres pour les administrateurs de rôle privilégié qui doivent gérer les attributions de rôles de ressources Azure. Ce tableau de bord est désactivée pour toute personnes n’étant pas un administrateur de rôle privilégié. Ces utilisateurs ont accès à un tableau de bord spécial intitulé Mon affichage. Le tableau de bord Mon affichage présente uniquement des informations sur l’utilisateur qui accède au tableau de bord (et non sur l’ensemble du locataire). |

## <a name="next-steps"></a>Étapes suivantes

- [Activer des rôles d’annuaire Azure AD dans PIM](pim-how-to-activate-role.md)
- [Activer des rôles de ressources Azure dans PIM](pim-resource-roles-activate-your-roles.md)
