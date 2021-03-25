---
title: Stratégies de sauvegarde du Gestionnaire d’instantanés StorSimple | Microsoft Docs
description: Explique comment utiliser le composant logiciel enfichable MMC Gestionnaire d’instantanés StorSimple pour créer et gérer les stratégies de sauvegarde qui contrôlent les sauvegardes planifiées.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 745aeb9844f1abad075d34b34735563200ea618b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90054940"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>Utiliser le Gestionnaire d’instantanés StorSimple pour créer et gérer des stratégies de sauvegarde
## <a name="overview"></a>Vue d’ensemble
Une stratégie de sauvegarde permet de créer une planification de sauvegarde des données de volume localement ou dans le cloud. Lorsque vous créez une stratégie de sauvegarde, vous pouvez également spécifier une stratégie de rétention. (Vous pouvez conserver un maximum de 64 instantanés). Pour plus d'informations sur les stratégies de sauvegarde, consultez la page [Types de sauvegarde](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies) dans [Gamme StorSimple 8000 : une solution de cloud hybride](storsimple-overview.md).

Ce didacticiel explique comment :

* Créer une stratégie de sauvegarde
* Modifier une stratégie de sauvegarde
* Supprimer une stratégie de sauvegarde

## <a name="create-a-backup-policy"></a>Créer une stratégie de sauvegarde
Pour créer une stratégie de sauvegarde, procédez comme suit.

#### <a name="to-create-a-backup-policy"></a>Pour créer une stratégie de sauvegarde
1. Cliquez sur l’icône de bureau pour démarrer le Gestionnaire d’instantanés StorSimple.
2. Dans le volet **Étendue**, cliquez avec le bouton droit sur **Stratégies de sauvegarde**, puis cliquez sur **Créer une stratégie de sauvegarde**.

    ![Créer une stratégie de sauvegarde](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    La boîte de dialogue **Créer une stratégie** s'affiche.

    ![Créer une stratégie - onglet Général](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. Sous l'onglet **Général**, complétez les informations suivantes :

   1. Dans la zone de texte **Nom**, entrez un nom pour la stratégie.
   2. Dans la zone de texte **Groupe de volumes**, entrez le nom du groupe de volumes associé à la stratégie.
   3. Sélectionnez **Instantané local** ou **Instantané sur le cloud**.
   4. Sélectionnez le nombre d’instantanés à conserver. Si vous sélectionnez **Tous**, vous pourrez conserver 64 instantanés (maximum).
4. Cliquez sur l'onglet **Planifier**.

    ![Créer une stratégie - onglet Planification](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. Sous l'onglet **Planifier**, complétez les informations suivantes :

   1. Activez la case à cocher **Activer** pour planifier la sauvegarde suivante.
   2. Sous **Paramètres**, sélectionnez **Une fois**, **Quotidien**, **Hebdomadaire** ou **Mensuel**.
   3. Dans la zone de texte **Début**, cliquez sur l'icône de calendrier, puis sélectionnez une date de début.
   4. Sous **Paramètres avancés**, vous pouvez définir des planifications répétées facultatives, ainsi qu'une date de fin.
   5. Cliquez sur **OK**.

Après avoir créé une stratégie de sauvegarde, vous pourrez consulter les informations suivantes dans le volet **Résultats** :

* **Nom** : nom de la stratégie de sauvegarde.
* **Type** : instantané local ou de cloud.
* **Groupe de volumes** : groupe de volume associé à la stratégie.
* **Rétention** : nombre d'instantanés conservés (64 au maximum).
* **Création le** : date à laquelle cette stratégie a été créée.
* **Activée** : indique si la stratégie est en vigueur. La valeur **True** indique qu’elle est en vigueur, la valeur **False** qu’elle ne l’est pas.

## <a name="edit-a-backup-policy"></a>Modifier une stratégie de sauvegarde
Pour modifier une stratégie de sauvegarde existante, procédez comme suit.

#### <a name="to-edit-a-backup-policy"></a>Pour modifier une stratégie de sauvegarde
1. Cliquez sur l’icône de bureau pour démarrer le Gestionnaire d’instantanés StorSimple.
2. Dans le volet **Étendue**, cliquez sur le nœud **Stratégies de sauvegarde**. Toutes les stratégies de sauvegarde s'affichent dans le volet **Résultats**.
3. Cliquez avec le bouton droit sur la stratégie que vous voulez modifier, puis cliquez sur **Modifier**.

    ![Modifier une stratégie de sauvegarde](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. Lorsque la fenêtre **Créer une stratégie** s'affiche, entrez vos modifications, puis cliquez sur **OK**.

## <a name="delete-a-backup-policy"></a>Supprimer une stratégie de sauvegarde
Pour supprimer une stratégie de sauvegarde, procédez comme suit.

#### <a name="to-delete-a-backup-policy"></a>Pour supprimer une stratégie de sauvegarde
1. Cliquez sur l’icône de bureau pour démarrer le Gestionnaire d’instantanés StorSimple.
2. Dans le volet **Étendue**, cliquez sur le nœud **Stratégies de sauvegarde**. Toutes les stratégies de sauvegarde s'affichent dans le volet **Résultats**.
3. Cliquez avec le bouton droit sur la stratégie de sauvegarde que vous voulez supprimer, puis cliquez sur **Supprimer**.
4. Lorsque le message de confirmation s'affiche, cliquez sur **Oui**.

    ![Confirmation de la suppression de la stratégie de sauvegarde](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [utiliser le Gestionnaire d’instantanés StorSimple pour gérer votre solution StorSimple](storsimple-snapshot-manager-admin.md).
* Découvrez comment [utiliser le Gestionnaire d’instantanés StorSimple pour afficher et gérer les tâches de sauvegarde](storsimple-snapshot-manager-manage-backup-jobs.md).
