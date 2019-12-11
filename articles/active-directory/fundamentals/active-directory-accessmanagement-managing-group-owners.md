---
title: Ajouter ou supprimer les propriétaires d’un groupe – Azure Active Directory | Microsoft Docs
description: Ces instructions expliquent comment ajouter ou supprimer les propriétaires de groupes avec Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ee4b452d087bfef0e9f5f7c820870da0df8dc3e
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74683935"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Ajouter ou supprimer les propriétaires de groupes dans Azure Active Directory
Les groupes Azure Active Directory (Azure AD) sont détenus et gérés par des propriétaires de groupes. Les propriétaires de groupe peuvent être des utilisateurs ou des principaux de service et sont en mesure de gérer le groupe, notamment l’appartenance. Seuls les propriétaires de groupe existant ou les administrateurs gérant un groupe peuvent attribuer des propriétaires de groupe. Ils ne sont pas nécessairement membres du groupe.

Lorsqu’un groupe n’a aucun propriétaire, les administrateurs gérant le groupe sont toujours en mesure de gérer le groupe.

## <a name="add-an-owner-to-a-group"></a>Ajouter un propriétaire à un groupe
Vous trouverez ci-dessous des instructions pour l’ajout d’un utilisateur en tant que propriétaire d’un groupe à l’aide du Portail Azure Active Directory. Pour ajouter un principal de service en tant que propriétaire d’un groupe, suivez les instructions pour effectuer cette opération avec [PowerShell](https://docs.microsoft.com/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0).

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
