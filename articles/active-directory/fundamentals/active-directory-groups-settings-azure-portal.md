---
title: Modifier les informations de votre groupe avec Azure Active Directory | Microsoft Docs
description: Découvrez comment modifier les informations d’un groupe avec Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: a02987fdce3a15cd5d416234e3717df6d33622ec
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45731340"
---
# <a name="how-to-edit-your-group-information-using-azure-active-directory"></a>Guide pour modifier les informations d’un groupe avec Azure Active Directory

Avec Azure Active Directory, vous pouvez modifier les paramètres d’un groupe, y compris son nom, sa description ou son type d’appartenance.

## <a name="to-edit-your-group-settings"></a>Modifier vos paramètres de groupe
1. Connectez-vous au [Portail Azure](https://portal.azure.com) à l’aide d’un compte d’administrateur général pour l’annuaire.

2. Sélectionnez **Azure Active Directory**, puis **Groupes**.

    La page **Groups - All groups** (Groupes - Tous les groupes) s’affiche, présentant tous vos groupes actifs.

3. Depuis la page **Groupes - Tous les groupes**, saisissez autant que possible le nom du groupe dans la zone **Recherche**. Dans le cadre de cet article, nous recherchons le groupe **MDM policy - West**.

    Les résultats de la recherche apparaissent dans la zone **Recherche**, qui se met à jour au fur et à mesure que vous tapez des caractères.

    ![Page Tous les groupes, avec texte de recherche dans la zone Recherche](media/active-directory-groups-settings-azure-portal/search-for-specific-group.png)

4. Sélectionnez le groupe **MDM policy - West**, puis **Propriétés** depuis la zone **Gérer**.

    ![Page Présentation du groupe avec nombre, membres, et options de membre en surbrillance](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. En cas de besoin, mettez à jour les informations dans **Paramètres généraux**, y compris :

    ![Les paramètres des propriétés d’un groupe](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **Nom du groupe**. Modifiez le nom du groupe existant.
    
    - **Description du groupe**. Modifiez la description du groupe existant.

    - **Type du groupe**. Vous ne pouvez pas changer le type du groupe après sa création. Pour changer le **Type du groupe**, vous devez le supprimer et en recréer un.
    
    - **Type d’appartenance.** Changez le type d’appartenance. Pour en savoir plus sur les différents types d’appartenance disponibles, consulter [Guide pour créer un groupe de base et ajouter des membres avec le portail Azure Active Directory](active-directory-groups-create-azure-portal.md)
    
    - **ID d’objet**. Vous ne pouvez pas changer l’ID d’objet, mais vous pouvez le copier pour vous en servir dans vos commandes PowerShell pour le groupe. Pour plus d’informations sur l’utilisation des cmdlets PowerShell, consultez [Configuration des paramètres de groupe avec les cmdlets Azure Active Directory](../users-groups-roles/groups-settings-v2-cmdlets.md).

## <a name="next-steps"></a>Étapes suivantes
Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

- [Afficher vos groupes et vos membres](active-directory-groups-view-azure-portal.md)

- [Créer un groupe de base et ajouter des membres](active-directory-groups-create-azure-portal.md)

- [Ajouter ou supprimer des membres d’un groupe](active-directory-groups-members-azure-portal.md)

- [Gérer les règles dynamiques pour les utilisateurs dans un groupe](../users-groups-roles/groups-create-rule.md)

- [Gérer l’appartenance à un groupe](active-directory-groups-membership-azure-portal.md)

- [Gérer l’accès aux ressources en utilisant des groupes](active-directory-manage-groups.md)

- [Associer ou ajouter un abonnement Azure à Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
