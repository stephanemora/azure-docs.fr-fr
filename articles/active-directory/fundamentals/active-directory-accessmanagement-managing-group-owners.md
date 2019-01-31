---
title: Ajouter ou supprimer les propriétaires d’un groupe – Azure Active Directory | Microsoft Docs
description: Ces instructions expliquent comment ajouter ou supprimer les propriétaires de groupes avec Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.openlocfilehash: 887f376eafadfb556ce69611195301b25b24bbd5
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104250"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Ajouter ou supprimer les propriétaires de groupes dans Azure Active Directory
Les groupes Azure Active Directory (Azure AD) sont détenus et gérés par des propriétaires de groupes. Les propriétaires de groupes sont affectés par un propriétaire de ressource (administrateur) pour gérer un groupe et ses membres. Ils ne sont pas nécessairement membres du groupe. Lorsqu’un propriétaire de groupe a été affecté, seul un propriétaire de ressource peut ajouter ou supprimer des propriétaires.

Dans certains cas, en tant qu’administrateur, vous pouvez décider de ne pas affecter un propriétaire de groupe. Le cas échéant, vous devenez le propriétaire de groupe. En outre, des propriétaires peuvent affecter d’autres propriétaires à leur groupe à moins que vous n’ayez limité ce paramètre dans les paramètres de groupe.

## <a name="add-an-owner-to-a-group"></a>Ajouter un propriétaire à un groupe
Ajoutez des propriétaires de groupes supplémentaires à un groupe à l’aide d’Azure AD.

### <a name="to-add-a-group-owner"></a>Pour ajouter un propriétaire de groupe
1. Connectez-vous au [Portail Azure](https://portal.azure.com) à l’aide d’un compte d’administrateur général pour l’annuaire.

2. Sélectionnez successivement **Azure Active Directory**, **Groupes** et le groupe pour lequel vous souhaitez ajouter un propriétaire (pour cet exemple, *MDM policy - West* [Stratégie GPM - Ouest]).

3. Dans la page **MDM policy - West Overview** (Vue d’ensemble de la stratégie GPM - Ouest), sélectionnez **Propriétaires**.

    ![Page MDM policy - West Overview (Vue d’ensemble de la stratégie GPM - Ouest) avec l’option Propriétaires mise en surbrillance](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. Dans la page **MDM policy - West - Owners** (Stratégie GPM - Ouest - Propriétaires), sélectionnez **Ajouter des propriétaires**, recherchez et sélectionnez l’utilisateur qui sera le nouveau propriétaire de groupe, puis choisissez **Sélectionner**.

    ![Page MDM policy - West - Owners (Stratégie GPM - Ouest - Propriétaires) avec l’option Propriétaires mise en surbrillance](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    Une fois que vous avez sélectionné le nouveau propriétaire, vous pouvez actualiser la page **Propriétaires** et observer que le nom a été ajouté à la liste des propriétaires.

## <a name="remove-an-owner-from-a-group"></a>Supprimer un propriétaire d’un groupe
Supprimez un propriétaire d’un groupe à l’aide d’Azure AD.

### <a name="to-remove-an-owner"></a>Pour supprimer un propriétaire
1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte d’administrateur général pour le répertoire.

2. Sélectionnez successivement **Azure Active Directory**, **Groupes** et le groupe pour lequel vous souhaitez supprimer un propriétaire (pour cet exemple, *MDM policy - West* [Stratégie GPM - Ouest]).

3. Dans la page **MDM policy - West Overview** (Vue d’ensemble de la stratégie GPM - Ouest), sélectionnez **Propriétaires**.

    ![Page MDM policy - West Overview (Vue d’ensemble de la stratégie GPM - Ouest) avec l’option Propriétaires mise en surbrillance](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. Dans la page **MDM policy - West - Owners** (Stratégie GPM - Ouest - Propriétaires), sélectionnez l’utilisateur que vous souhaitez supprimer en tant que propriétaire de groupe, choisissez **Supprimer** dans la page d’informations de l’utilisateur, puis sélectionnez **Oui** pour confirmer votre décision.

    ![Page d’informations d’utilisateur avec l’option Supprimer mise en surbrillance](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    Après que vous avez supprimé le propriétaire, vous pouvez revenir à la page **Propriétaires** et observer que le nom a été supprimé de la liste des propriétaires.

## <a name="next-steps"></a>Étapes suivantes
- [Gestion de l’accès aux ressources avec les groupes Azure Active Directory](active-directory-manage-groups.md)

- [Configuration des paramètres de groupe avec les applets de commande Azure Active Directory](../users-groups-roles/groups-settings-cmdlets.md)

- [Utilisation d’un groupe pour gérer l’accès aux applications SaaS](../users-groups-roles/groups-saasapps.md)

- [Intégration des identités locales dans Azure Active Directory](../hybrid/whatis-hybrid-identity.md)

- [Configuration des paramètres de groupe avec les applets de commande Azure Active Directory](../users-groups-roles/groups-settings-v2-cmdlets.md)
