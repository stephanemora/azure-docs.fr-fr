---
title: Déléguer la gouvernance des accès aux créateurs de catalogue dans la gestion des droits d’utilisation Azure AD (préversion) - Azure Active Directory
description: Découvrez comment déléguer la gouvernance des accès des administrateurs informatiques aux créateurs de catalogue et chefs de projet pour qu’ils puissent gérer les accès eux-mêmes.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: da6183599f2dacd1a98d8fe359edda741b903b50
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170879"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management-preview"></a>Déléguer la gouvernance des accès aux créateurs de catalogue dans la gestion des droits d’utilisation Azure AD (préversion)

> [!IMPORTANT]
> La gestion des droits d’utilisation Azure Active Directory (Azure AD) est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pour déléguer à des utilisateurs qui ne sont pas administrateurs, pour qu’ils puissent créer leurs propres catalogues, vous pouvez ajouter ces utilisateurs au rôle de créateur de catalogue défini par la gestion des droits d’utilisation Azure AD. Vous pouvez ajouter des utilisateurs individuels ou un groupe, dont les membres sont alors en mesure de créer des catalogues.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>En tant qu’administrateur informatique, déléguer à un créateur de catalogue

Suivez ces étapes pour affecter un utilisateur au rôle de créateur de catalogue.

**Rôle prérequis :** Administrateur général ou Administrateur d’utilisateurs

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, dans la section **Gestion des droits d’utilisation**, cliquez sur **Paramètres**.

1. Cliquez sur **Modifier**.

    ![Paramètres pour ajouter des créateurs de catalogue](./media/entitlement-management-delegate/settings-delegate.png)

1. Dans la section **Déléguer la gestion des droits d’utilisation**, cliquez sur **Ajouter des créateurs de catalogue** pour sélectionner les utilisateurs ou groupes auxquels vous voulez déléguer cette gestion des droits d’utilisation.

1. Cliquez sur **Sélectionner**.

1. Cliquez sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

- [Créer et gérer un catalogue de ressources](entitlement-management-catalog-create.md)
- [Déléguer la gouvernance des accès aux gestionnaires de package d’accès](entitlement-management-delegate-managers.md)

