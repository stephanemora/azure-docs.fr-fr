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
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd684e1bd48f877a74280b33b4df65d7baaa0fe7
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65507172"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Ajouter ou supprimer les propriétaires de groupes dans Azure Active Directory
Les groupes Azure Active Directory (Azure AD) sont détenus et gérés par des propriétaires de groupes. Propriétaires de groupe peuvent être des utilisateurs ou principaux du service et sont en mesure de gérer le groupe, y compris l’appartenance. Seuls les propriétaires de groupe existant ou de la gestion de groupe peut affecter onwers de groupe. Ils ne sont pas nécessairement membres du groupe.

Lorsqu’un groupe n’a aucun propriétaire, la gestion de groupe des administrateurs sont toujours en mesure de gérer le groupe.

## <a name="add-an-owner-to-a-group"></a>Ajouter un propriétaire à un groupe
Vous trouverez ci-dessous des instructions pour l’ajout d’un utilisateur en tant que propriétaire à un groupe sont utilisant le portail Azure AD. Pour ajouter un principal de service en tant que propriétaire d’un groupe, suivez les instructions pour ce faire, à l’aide de [PowerShell](https://docs.microsoft.com/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0).

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
