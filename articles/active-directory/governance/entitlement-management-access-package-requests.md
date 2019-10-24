---
title: Voir et gérer les demandes pour un package d’accès dans la gestion des droits d’utilisation d’Azure Active Directory (préversion) - Azure Active Directory
description: Découvrez comment voir, retraiter et annuler les demandes pour un package d’accès dans la gestion des droits d’utilisation d’Azure Active Directory (préversion).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7211fea4781904b9d97428ecf00b57970e4b57d
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430286"
---
# <a name="view-and-manage-requests-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Voir et gérer les demandes pour un package d’accès dans la gestion des droits d’utilisation d’Azure Active Directory (préversion)

> [!IMPORTANT]
> La gestion des droits d’utilisation Azure Active Directory (Azure AD) est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dans la gestion des droits d’utilisation Azure AD, vous pouvez voir qui a demandé des packages d’accès, ainsi que la stratégie et l’état associés. Cet article explique comment voir, retraiter et annuler les demandes pour un package d’accès.

## <a name="view-requests"></a>Afficher les requêtes

**Rôle prérequis :** Administrateur général, administrateur d’utilisateurs ou gestionnaire de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Cliquez sur **Demandes**.

1. Cliquez sur une requête spécifique pour voir plus de détails.

    ![Liste des demandes pour un package d’accès](./media/entitlement-management-access-package-requests/requests-list.png)

## <a name="view-a-requests-delivery-errors"></a>Afficher les erreurs de remise d’une requête

**Rôle prérequis :** Administrateur général, administrateur d’utilisateurs ou gestionnaire de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Cliquez sur **Demandes**.

1. Sélectionnez la requête à afficher.

    Si la requête contient des erreurs de remise, l’état de la demande sera **Non remis** ou **Partiellement remis**.

    Le cas échéant, le panneau des détails de la requête indique le nombre d’erreurs de livraison.

1. Cliquez sur ce nombre pour voir toutes les erreurs de livraison de la requête.

## <a name="reprocess-a-request"></a>Retraiter une demande

Si une demande rencontre une erreur, vous pouvez la retraiter pour faire une nouvelle tentative. Vous ne pouvez retraiter qu’une demande dont l’état est **Échec de livraison** ou **Partiellement remis** avec une date de fin inférieure à une semaine.

**Rôle prérequis :** Administrateur général, administrateur d’utilisateurs ou gestionnaire de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Cliquez sur **Demandes**.

1. Cliquez sur la demande à retraiter.

1. Dans le volet des détails de la requête, cliquez sur **Retraiter la demande**.

    ![Retraiter une demande ayant échoué](./media/entitlement-management-access-package-requests/reprocess-request.png)

## <a name="cancel-a-pending-request"></a>Annuler une demande en attente

Vous pouvez uniquement annuler une requête en attente qui n’a pas encore été autorisée ou dont la livraison a échoué.

**Rôle prérequis :** Administrateur général, administrateur d’utilisateurs ou gestionnaire de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Cliquez sur **Demandes**.

1. Cliquez sur la requête à annuler.

1. Dans le volet des détails de la requête, cliquez sur **Annuler la demande**.

## <a name="next-steps"></a>Étapes suivantes

- [Changer les paramètres de demande et d’approbation pour un package d’accès](entitlement-management-access-package-request-policy.md)
- [Afficher, ajouter et supprimer les attributions pour un package d’accès](entitlement-management-access-package-assignments.md)