---
title: Retraiter les demandes concernant un package d’accès dans la gestion des droits d’utilisation Azure AD – Azure Active Directory
description: Découvrez comment retraiter une demande pour un package d’accès dans la gestion des droits d’utilisation d’Azure Active Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/25/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a85d796f8b66cca16d4d3c01ecbfc5f6c43c79f9
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113129232"
---
# <a name="reprocess-requests-for-an-access-package-in-azure-ad-entitlement-management"></a>Retraiter les demandes pour un package d’accès dans la gestion des droits d’utilisation Azure AD

En tant que gestionnaire de package d’accès, vous pouvez retenter automatiquement la demande d’accès d’un utilisateur pour un package d’accès à l’aide de la fonctionnalité de retraitement, et ce, à tout moment. Le retraitement évite à l’utilisateur de recommencer le processus de demande si son accès aux ressources n’est pas correctement provisionné.

> [!NOTE]
> Vous pouvez retraiter une demande pendant 14 jours à compter de la date où la demande d’origine s’est terminée. Pour les demandes qui se sont terminées il y a plus de 14 jours, les utilisateurs devront annuler et effectuer de nouvelles demandes dans MyAccess.

Cet article explique comment retraiter des demandes pour un package d’accès existant.

## <a name="prerequisites"></a>Prérequis

Pour utiliser la gestion des droits d’utilisation Azure AD et affecter des utilisateurs aux packages d’accès, vous devez disposer d’une des licences suivantes :

- Azure AD Premium P2
- Licence Enterprise Mobility + Security (EMS) E5

## <a name="open-an-existing-access-package-and-reprocess-user-requests"></a>Ouvrir un package d’accès existant et retraiter les demandes utilisateur

**Rôle prérequis** : administrateur général, administrateur de la gouvernance des identités, administrateur d’utilisateurs, propriétaire de catalogue, gestionnaire de package d’accès ou gestionnaire d’attribution de package d’accès

Si vos utilisateurs ont effectué des demandes et que leur état indique qu’elles ont été partiellement remises ou qu’elles ont échoué, vous devrez peut-être retraiter certaines de ces demandes. Effectuez les étapes suivantes pour retraiter les demandes pour un package d’accès existant :

1.  Connectez-vous au [portail Azure](https://portal.azure.com).

1.  Cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1.  Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1.  Sous **Gérer** sur le côté gauche, cliquez sur **Demandes**.

1.  Sélectionnez tous les utilisateurs dont vous souhaitez retraiter les demandes.

1.  Cliquez sur **Retraiter**.

## <a name="next-steps"></a>Étapes suivantes

- [Afficher les requêtes d’un package d’accès](entitlement-management-access-package-requests.md)
- [Approuver ou refuser les demandes d’accès](entitlement-management-request-approve.md)