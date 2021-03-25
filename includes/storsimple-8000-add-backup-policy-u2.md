---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 23ce17844a0113f63931c6ece7d36bfefedc2de5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100552673"
---
#### <a name="to-add-a-storsimple-backup-policy"></a>Pour ajouter une stratégie de sauvegarde StorSimple

1. Accédez à votre appareil StorSimple et cliquez sur **Stratégie de sauvegarde**.

2. Dans le panneau **Stratégie de sauvegarde**, cliquez sur **+ Ajouter une stratégie** dans la barre de commandes.
   
    ![Ajout d’une stratégie de sauvegarde](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-01.png)

3. Dans le panneau **Créer la stratégie de sauvegarde**, procédez comme suit :
   
   1. **Sélectionner un appareil** est rempli automatiquement en fonction de l’appareil que vous avez sélectionné.
   
   2. Spécifiez un **Nom de stratégie** de sauvegarde comprenant entre 3 et 150 caractères. Une fois la stratégie créée, vous ne pourrez plus la renommer.
       
   3. Pour affecter des volumes à cette stratégie de sauvegarde, sélectionnez **Ajouter des volumes** puis, dans la liste tabulaire des volumes, cliquez sur les cases à cocher pour affecter un ou plusieurs volumes à cette stratégie de sauvegarde.

       ![Ajouter une stratégie de sauvegarde 2](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-02.png)<!--Replacement screen source: create-backup-policy-addvolumes.png-->

   4. Pour définir une planification pour cette stratégie de sauvegarde, cliquez sur **Première planification**, puis modifiez les paramètres suivants :<!--Do the substeps remain the same? Can they follow without a screenshot?-->

       ![Ajout d’une stratégie de sauvegarde 3](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-03.png)<!--Replacement screen source: create-backup-policy-first-schedule.png-->

       1. Pour **Type d’instantané**, sélectionnez **Cloud** ou **Local**.

       2. Indiquez la fréquence des sauvegardes (spécifiez un nombre et choisissez **jours** ou **semaines** dans la liste déroulante).

       3. Entrez une planification de rétention.

       4. Entrez l’heure et la date de début de la stratégie de sauvegarde.

       5. Cliquez sur **OK** pour définir la planification.

   5. Cliquez sur **Créer** pour créer une stratégie de sauvegarde.
   
   6. Un message s’affiche une fois la stratégie de sauvegarde créée. La stratégie ajoutée s’affiche dans le tableau du panneau **Stratégies de sauvegarde**.

       ![Ajout d’une stratégie de sauvegarde 5](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-07.png)
