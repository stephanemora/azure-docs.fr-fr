---
title: Supprimer un groupe - Azure Active Directory | Microsoft Docs
description: Instructions de suppression d’un groupe avec Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/29/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 869def6b3187e10727a170377f2635fb3652f98b
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87797591"
---
# <a name="delete-a-group-using-azure-active-directory"></a>Supprimer un groupe avec Azure Active Directory
Vous pouvez supprimer un groupe Azure Active Directory (Azure AD) pour différentes raisons, généralement parce que :

- Vous avez incorrectement défini le **type de groupe** sur la mauvaise option.

- Vous avez créé le mauvais groupe ou un groupe en doublon par erreur. 

- Vous n’avez plus besoin du groupe.

## <a name="to-delete-a-group"></a>Pour supprimer un groupe
1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte d’administrateur général pour le répertoire.

2. Sélectionnez **Azure Active Directory**, puis **Groupes**.

3. Sur la page **Groupes - Tous les groupes**, recherchez et sélectionnez le groupe que vous souhaitez supprimer. Pour ces étapes, nous allons utiliser **Stratégie GPM - Est**.

    ![Page Groupes - Tous les groupes avec le nom de groupe en surbrillance](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. Sur la page **Vue d’ensemble de la stratégie GPM - Est**, sélectionnez **Supprimer**.

    Le groupe est supprimé de votre locataire Azure Active Directory.

    ![Page Vue d’ensemble de la stratégie GPM - Est, avec l’option de suppression en surbrillance](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>Étapes suivantes

- Si vous supprimez un groupe par erreur, vous pouvez le créer à nouveau. Pour plus d’informations, consultez [Créer un groupe de base et ajouter des membres](active-directory-groups-create-azure-portal.md).

- Si vous supprimez par erreur un groupe Office 365, vous devriez pouvoir le restaurer. Pour plus d’informations, consultez [Restaurer un groupe Office 365 supprimé](../users-groups-roles/groups-restore-deleted.md).
