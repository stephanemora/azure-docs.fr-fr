---
title: Déléguer la gouvernance des accès aux créateurs de catalogue dans la gestion des droits d’utilisation Azure AD - Azure Active Directory
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
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: f71007b886d3cc25a7cf9dc23d784144ed4e1fbd
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174381"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Déléguer la gouvernance des accès aux créateurs de catalogue dans la gestion des droits d’utilisation Azure AD

Pour déléguer à des utilisateurs qui ne sont pas administrateurs, pour qu’ils puissent créer leurs propres catalogues, vous pouvez ajouter ces utilisateurs au rôle de créateur de catalogue défini par la gestion des droits d’utilisation Azure AD. Vous pouvez ajouter des utilisateurs individuels ou un groupe, dont les membres sont alors en mesure de créer des catalogues.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>En tant qu’administrateur informatique, déléguer à un créateur de catalogue

Suivez ces étapes pour affecter un utilisateur au rôle de créateur de catalogue.

**Rôle prérequis :** Administrateur général ou Administrateur d’utilisateurs

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, dans la section **Gestion des droits d’utilisation**, cliquez sur **Paramètres**.

1. Cliquez sur **Modifier**.

    ![Paramètres pour ajouter des créateurs de catalogue](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. Dans la section **Déléguer la gestion des droits d’utilisation**, cliquez sur **Ajouter des créateurs de catalogue** pour sélectionner les utilisateurs ou groupes auxquels vous voulez déléguer cette gestion des droits d’utilisation.

1. Cliquez sur **Sélectionner**.

1. Cliquez sur **Enregistrer**.

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>Autoriser les rôles délégués à accéder au Portail Azure

Pour permettre aux rôles délégués, tels que les créateurs de catalogue et les gestionnaires de packages d’accès, d’accéder au Portail Azure afin de gérer les packages d’accès, vous devez vérifier le paramètre du portail d’administration.

**Rôle prérequis :** Administrateur général ou Administrateur d’utilisateurs

1. Dans le Portail Azure, cliquez sur **Azure Active Directory**, puis sur **Utilisateurs**.

1. Dans le menu de gauche, cliquez sur **Paramètres utilisateur**.

1. Vérifiez que **Limiter l’accès au portail d’administration Azure AD** est défini sur **Non**.

    ![Paramètres utilisateur Azure AD - Portail d’administration](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>Étapes suivantes

- [Créer et gérer un catalogue de ressources](entitlement-management-catalog-create.md)
- [Déléguer la gouvernance des accès aux gestionnaires de package d’accès](entitlement-management-delegate-managers.md)

