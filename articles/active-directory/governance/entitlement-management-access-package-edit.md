---
title: Masquer ou supprimer un package d’accès dans la gestion des droits d’utilisation - Azure AD
description: Découvrez comment masquer ou supprimer un package d’accès dans la gestion des droits d’utilisation Azure Active Directory.
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
ms.openlocfilehash: cf410db5a483fc4f5d9efc1a6bd5c6202694ef4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79230397"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>Masquer ou supprimer un package d’accès dans la gestion des droits d’utilisation Azure AD

Par défaut, les packages d'accès sont détectables. Cela signifie que si une stratégie permet à un utilisateur de demander le package d'accès, il verra automatiquement ce package d’accès dans son portail Mon Accès. Toutefois, vous pouvez modifier le paramètre **Masqué** pour que le package d’accès ne soit pas répertorié dans le portail Mon Accès de l’utilisateur.

Cet article décrit comment masquer ou supprimer un package d’accès.

## <a name="change-the-hidden-setting"></a>Modifier le paramètre Hidden (Masqué)

Procédez comme suit pour modifier le paramètre **Masqué** d’un package d’accès.

**Rôle prérequis :** Administrateur général, administrateur d’utilisateurs ou gestionnaire de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Sur la page Vue d’ensemble, cliquez sur **Modifier**.

1. Définissez le paramètre **Hidden** (Masqué).

    Si ce paramètre est défini sur **No** (Non), le package d'accès apparaîtra dans le portail Mon Accès de l'utilisateur.

    S’il est défini sur **Yes** (Oui), le package d'accès n’apparaîtra pas dans le portail Mon Accès de l'utilisateur. Un utilisateur peut uniquement voir le package d'accès s’il dispose du **lien du portail Mon Accès** vers ce package d'accès. Pour plus d’informations, consultez [Partager le lien pour demander un package d’accès](entitlement-management-access-package-settings.md).

## <a name="delete-an-access-package"></a>Supprimer un package d’accès

Un package d’accès ne peut être supprimé que s’il n’est affecté à aucun utilisateur. Pour supprimer un package d’accès, procédez comme suit.

**Rôle prérequis :** Administrateur général, administrateur d’utilisateurs ou gestionnaire de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Dans le menu de gauche, cliquez sur **Affectations** et supprimez l’accès pour tous les utilisateurs.

1. Dans le menu de gauche, cliquez sur **Vue d’ensemble**, puis sur **Supprimer**.

1. Dans le message de suppression qui apparaît, cliquez sur **Yes** (Oui).

## <a name="next-steps"></a>Étapes suivantes

- [Afficher, ajouter et supprimer les attributions pour un package d’accès](entitlement-management-access-package-assignments.md)
- [Afficher les rapports et les journaux](entitlement-management-reports.md)
