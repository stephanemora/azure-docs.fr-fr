---
title: Lister les affectations de refus pour les ressources Azure avec le portail Azure
description: Découvrez comment répertorier les utilisateurs, groupes, principaux de service et identités managées auxquels l’accès à des actions de ressource Azure spécifiques a été refusé dans des étendues particulières à l’aide du portail Azure.
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
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 4db76e5c6191457346ca1f95678cf73843334d3b
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137421"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-azure-portal"></a>Répertorier les affectations de refus pour les ressources Azure à l’aide du portail Azure

Les [affectations de refus](deny-assignments.md) empêchent les utilisateurs d'effectuer des actions particulières sur les ressources Azure, même si une attribution de rôle leur confère un accès. Cet article décrit comment lister les affectations de refus existantes à l’aide du portail Azure.

> [!NOTE]
> Vous ne pouvez pas directement créer vos propres affectations de refus. Pour en savoir sur la création des affectations de refus, consultez [Affectations de refus](deny-assignments.md).

## <a name="prerequisites"></a>Conditions préalables requises

Pour obtenir des informations sur une affectation de refus, vous devez disposer de :

- l’autorisation `Microsoft.Authorization/denyAssignments/read`, qui est incluse dans la plupart des [rôles intégrés pour les ressources Azure](built-in-roles.md).

## <a name="list-deny-assignments"></a>Répertorier les affectations de refus

Effectuez ces étapes pour répertorier les affectations de refus au niveau de l’abonnement ou de l’étendue du groupe d’administration.

1. Dans le portail Azure, cliquez sur **Tous les services**, puis sur **Groupes d’administration** ou sur **Abonnements**.

1. Cliquez sur le groupe d’administration ou sur l’abonnement que vous souhaitez répertorier.

1. Cliquez sur **Contrôle d’accès (IAM)** .

1. Cliquez sur l’onglet **Affectations de refus** (ou cliquez sur le bouton **Afficher** dans la mosaïque Afficher les affectations de refus).

    S’il existe des affectations de refus dans cette étendue, ou héritées par cette étendue, elles sont listées.

    ![Contrôle d’accès - Onglet Affectations de refus](./media/deny-assignments-portal/access-control-deny-assignments.png)

1. Pour afficher des colonnes supplémentaires, cliquez sur **Modifier les colonnes**.

    ![Affectations de refus - Colonnes](./media/deny-assignments-portal/deny-assignments-columns.png)

    |  |  |
    | --- | --- |
    | **Nom** | Nom de l’affectation de refus. |
    | **Type de principal** | Utilisateur, groupe, groupe défini par le système ou principal du service. |
    | **Refusé**  | Nom du principal de sécurité qui est inclus dans l’affectation de refus. |
    | **Id** | Identificateur unique pour l’affectation de refus. |
    | **Principaux exclus** | Indique s’il existe des principaux de sécurité qui sont exclus de l’affectation de refus. |
    | **Ne s’applique pas aux enfants** | Indique si l’affectation de refus est transmise à des étendues secondaires. |
    | **Système protégé** | Indique si l’affectation de refus est managée par Azure. Actuellement, toujours Oui. |
    | **Portée** | Groupe d’administration, abonnement, groupe de ressources ou ressource. |

1. Ajoutez une coche aux éléments activés de votre choix, puis cliquez sur **OK** pour afficher les colonnes sélectionnées.

## <a name="list-details-about-a-deny-assignment"></a>Répertorier les détails d’une affectation de refus

Suivez ces étapes pour répertorier des détails supplémentaires sur une affectation de refus.

1. Ouvrez le volet **Affectations de refus**, comme décrit à la section précédente.

1. Cliquez sur le nom de l’affectation de refus pour ouvrir le panneau **Utilisateurs**.

    ![Affectation de refus - Utilisateurs](./media/deny-assignments-portal/deny-assignment-users.png)

    Le panneau **Utilisateurs** comprend les deux sections suivantes.

    |  |  |
    | --- | --- |
    | **L’affectation de refus s’applique à**  | Principaux de sécurité auxquels l’affectation de refus s’applique. |
    | **L’affectation de refus exclut** | Principaux de sécurité qui sont exclus de l’affectation de refus. |

    Le **principal défini par le système** représente tous les utilisateurs, groupes, principaux de service et identités managées figurant dans un annuaire Azure AD.

1. Pour afficher la liste des autorisations qui sont refusées, cliquez sur **Autorisations refusées**.

    ![Affectation de refus - Autorisations refusées](./media/deny-assignments-portal/deny-assignment-denied-permissions.png)

    | Type d'action | Description |
    | --- | --- |
    | **Actions**  | Opérations de gestion refusées. |
    | **NotActions** | Opérations de gestion exclues de l’opération de gestion refusée. |
    | **DataActions**  | Opérations refusées sur des données. |
    | **NotDataActions** | Opérations sur des données, exclues de l’opération refusée sur des données. |

    Pour l’exemple illustré dans la capture d’écran précédente, les éléments suivants sont les autorisations effectives :

    - Toutes les opérations de stockage sur le plan de données sont refusées à l’exception des opérations de calcul.

1. Pour voir les propriétés d’une affectation de refus, cliquez sur **Propriétés**.

    ![Affectation de refus - Propriétés](./media/deny-assignments-portal/deny-assignment-properties.png)

    Dans le panneau **Propriétés**, vous pouvez voir le nom, l’ID, la description et l’étendue de l’affectation de refus. Le commutateur **Ne s’applique pas aux enfants** indique si l’affectation de refus est transmise à des étendues secondaires. Le commutateur **Système protégé** indique si cette affectation de refus est managée par Azure. Actuellement, c’est **Oui** dans tous les cas.

## <a name="next-steps"></a>Étapes suivantes

* [Comprendre les affectations de refus relatives aux ressources Azure](deny-assignments.md)
* [Répertorier les affectations de refus pour les ressources Azure à l’aide d’Azure PowerShell](deny-assignments-powershell.md)
