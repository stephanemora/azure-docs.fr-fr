---
title: Retraiter les affectations pour un package d’accès dans la gestion des droits d’utilisation d’Azure Active Directory - Azure Active Directory
description: Découvrez comment retraiter les affectations pour un package d’accès dans la gestion des droits d’utilisation d’Azure Active Directory.
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
ms.openlocfilehash: b1b488f8c9331932b82ab0ab55db9c7dcb652add
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113129233"
---
# <a name="reprocess-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Retraiter les affectations pour un package d’accès dans la gestion des droits d’utilisation d’Azure Active Directory

En tant que gestionnaire de package d’accès, vous pouvez réévaluer et appliquer automatiquement les affectations d’origine des utilisateurs dans un package d’accès à l’aide de la fonctionnalité de retraitement. Le retraitement élimine la nécessité pour les utilisateurs de répéter le processus de requête de package d’accès si leur accès aux ressources a été affecté par des modifications en dehors de la gestion des droits d’utilisation.

Par exemple, un utilisateur a peut-être été supprimé manuellement d’un groupe, provoquant ainsi la perte de l’accès aux ressources nécessaires. 

La gestion des droits d’utilisation ne bloque pas les mises à jour externes aux ressources du package d’accès. Par conséquent, l’interface utilisateur de gestion des droits d’utilisation n’affiche pas cette modification avec précision. Ainsi, l’état de l’affectation de l’utilisateur est indiqué comme « Livré », même si l’utilisateur n’a plus accès aux ressources. Toutefois, si l’affectation de l’utilisateur est retraitée, elle est à nouveau ajoutée aux ressources du package d’accès. Le retraitement garantit que les attributions de package d’accès sont à jour, que les utilisateurs ont accès aux ressources nécessaires et que les affectations sont reflétées avec précision dans l’interface utilisateur.

Cet article explique comment retraiter des affectations dans un package d’accès existant.

## <a name="prerequisites"></a>Prérequis

Pour utiliser la gestion des droits d’utilisation Azure AD et affecter des utilisateurs aux packages d’accès, vous devez disposer d’une des licences suivantes :

- Azure AD Premium P2
- Licence Enterprise Mobility + Security (EMS) E5

## <a name="open-an-existing-access-package-and-reprocess-user-assignments"></a>Ouvrir un package d’accès existant et retraiter les affectations d’utilisateurs

**Rôle prérequis :** administrateur général, administrateur de la gouvernance des identités, administrateur d’utilisateurs, propriétaire de catalogue, gestionnaire de package d’accès ou gestionnaire d’attribution de package d’accès

Si vous avez des utilisateurs qui sont dans l’état « Livré » mais n’ont pas accès aux ressources qui font partie du package d’accès, vous devrez probablement retraiter les attributions pour réassigner ces utilisateurs aux ressources du package d’accès. Procédez comme suit pour retraiter les affectations pour un package d’accès existant :

1.  Connectez-vous au [portail Azure](https://portal.azure.com).

1.  Cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1.  Dans le menu de gauche, cliquez sur **Packages d’accès** et ouvrez ensuite le package d’accès avec l’affectation d’utilisateur que vous voulez retraiter.

1.  Sous **Gérer** sur le côté gauche, cliquez sur **Affectations**.

    ![Gestion des droits d’utilisation dans le portail Azure](./media/entitlement-management-reprocess-access-package-assignments/reprocess-access-package-assignment.png)

1.  Sélectionnez tous les utilisateurs dont vous souhaitez retraiter les affectations.

1.  Cliquez sur **Retraiter**.

## <a name="next-steps"></a>Étapes suivantes

- [Afficher, ajouter et supprimer les attributions pour un package d’accès](entitlement-management-access-package-assignments.md)
- [Afficher les rapports et les journaux](entitlement-management-reports.md)
