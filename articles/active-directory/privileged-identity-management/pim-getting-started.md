---
title: Commencer à utiliser PIM - Azure Active Directory | Microsoft Docs
description: Découvrez comment activer et bien démarrer avec Azure AD Privileged Identity Management (PIM) dans le portail Azure.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 03/13/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cbb9b4340a7cdb9be5039722a8f75e09288ec48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472858"
---
# <a name="start-using-privileged-identity-management"></a>Commencer à utiliser Privileged Identity Management

Avec Privileged Identity Management (PIM), vous pouvez gérer, contrôler et analyser des accès au sein de votre organisation Azure Active Directory (Azure AD). Cette étendue inclut l’accès aux ressources Azure, à Azure AD et à d’autres services en ligne Microsoft comme Office 365 ou Microsoft Intune.

Cet article décrit comment activer et bien démarrer avec Privileged Identity Management.

## <a name="prerequisites"></a>Prérequis

Pour utiliser Privileged Identity Management, vous devez disposer de l’une des licences suivantes :

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Pour plus d’informations, consultez [Exigences relatives aux licences pour l’utilisation de Privileged Identity Management](subscription-requirements.md).

## <a name="sign-up-pim-for-azure-ad-roles"></a>Inscrire PIM pour les rôles Azure AD

Une fois que vous avez activé Privileged Identity Management pour votre annuaire, vous devez vous inscrire à Privileged Identity Management pour gérer des rôles Azure AD.

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Sélectionnez des **rôles Azure AD**.

    ![S’inscrire à Privileged Identity Management pour les rôles Azure AD](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Sélectionnez **S’inscrire**.

1. Dans le message qui s’affiche, cliquez sur **Oui** afin vous inscrire à Privileged Identity Management pour gérer des rôles Azure AD.

    ![S’inscrire à Privileged Identity Management pour le message des rôles Azure AD](./media/pim-getting-started/sign-up-pim-message.png)

    Une fois l’inscription effectuée, les options d’Azure AD sont activées. Il peut être nécessaire d’actualiser le portail.

    Pour plus d’informations sur la façon de découvrir et de sélectionner les ressources Azure à protéger avec Privileged Identity Management, consultez [Découvrir les ressources Azure à gérer dans Privileged Identity Management](pim-resource-roles-discover-resources.md).

## <a name="navigate-to-your-tasks"></a>Accédez à vos tâches

Une fois Privileged Identity Management configuré, vous pouvez démarrer vos tâches de gestion des identités.

![Fenêtre de navigation dans Privileged Identity Management présentant les tâches et les options de gestion](./media/pim-getting-started/pim-quickstart-tasks.png)

| Tâche + Gérer | Description |
| --- | --- |
| **Mes rôles**  | Affiche la liste des rôles éligibles et actifs qui vous sont attribués. C’est là où vous pouvez activer tout rôle éligible attribué. |
| **Mes demandes** | Affiche toutes vos requêtes en attente pour activer les attributions de rôles éligibles. |
| **Approuver des requêtes** | Affiche la liste des demandes d’activation des rôles éligibles par utilisateur dans votre annuaire, dont l’approbation vous revient. |
| **Révision d’accès** | Répertorie les révisions d’accès que vous devez terminer, que vous les effectuiez pour vous-même ou pour quelqu’un d’autre. |
| **Rôles Azure AD** | Affiche un tableau de bord et des paramètres pour les administrateurs de rôle privilégié qui doivent gérer les attributions de rôles Azure AD. Ce tableau de bord est désactivée pour toute personnes n’étant pas un administrateur de rôle privilégié. Ces utilisateurs ont accès à un tableau de bord spécial intitulé Mon affichage. Le tableau de bord Mon affichage présente uniquement des informations sur l’utilisateur qui accède au tableau de bord (et non sur l’ensemble du locataire). |
| **Ressources Azure** | Affiche un tableau de bord et des paramètres pour les administrateurs de rôle privilégié qui doivent gérer les attributions de rôles de ressources Azure. Ce tableau de bord est désactivée pour toute personnes n’étant pas un administrateur de rôle privilégié. Ces utilisateurs ont accès à un tableau de bord spécial intitulé Mon affichage. Le tableau de bord Mon affichage présente uniquement des informations sur l’utilisateur qui accède au tableau de bord (et non sur l’ensemble du locataire). |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Ajouter une vignette PIM au tableau de bord

Pour faciliter l’ouverture de Privileged Identity Management, ajoutez une vignette Privileged Identity Management à votre tableau de bord du portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Cliquez sur **Tous les services**, puis recherchez le service **Azure AD Privileged Identity Management**.

    ![Azure AD Privileged Identity Management dans Tous les services](./media/pim-getting-started/pim-all-services-find.png)

1. Sélectionnez le démarrage rapide Privileged Identity Management.

1. Cochez la case **Épingler le panneau au tableau de bord** pour épingler le panneau de démarrage rapide de Privileged Identity Management au tableau de bord.

    ![Icône en punaise pour épingler le panneau Privileged Identity Management au tableau de bord](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Dans le tableau de bord Azure, vous verrez une vignette comme celle-ci :

    ![Mosaïque de démarrage rapide Privileged Identity Management sur le tableau de bord](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer des rôles dans Azure AD dans Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Découvrir des ressources Azure à gérer dans Privileged Identity Management](pim-resource-roles-discover-resources.md)
