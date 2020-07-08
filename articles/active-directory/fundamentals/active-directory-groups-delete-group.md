---
title: Supprimer un groupe - Azure Active Directory | Microsoft Docs
description: Instructions de suppression d’un groupe avec Azure Active Directory.
services: active-directory
author: msaburnley
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
ms.openlocfilehash: cf309c10dc5924374d8c3a191f6fd11cceb2ac49
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85604282"
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
