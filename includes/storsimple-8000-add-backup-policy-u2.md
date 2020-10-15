---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 02274bacb66a33ef54e07bc8113d7db46d4d5296
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "67177253"
---
#### <a name="to-add-a-storsimple-backup-policy"></a>Pour ajouter une stratégie de sauvegarde StorSimple

1. Accédez à votre appareil StorSimple et cliquez sur **Stratégie de sauvegarde**.

2. Dans le panneau **Stratégie de sauvegarde**, cliquez sur **+ Ajouter une stratégie** dans la barre de commandes.
   
    ![Ajout d’une stratégie de sauvegarde](./media/storsimple-8000-add-backup-policy-u2/addbupol1.png)

3. Dans le panneau **Créer la stratégie de sauvegarde**, procédez comme suit :
   
   1. **Sélectionner un appareil** est rempli automatiquement en fonction de l’appareil que vous avez sélectionné.
   
   2. Spécifiez un **Nom de stratégie** de sauvegarde (entre 3 et 150 caractères). Une fois la stratégie créée, vous ne pourrez plus la renommer.
       
   3. Pour affecter des volumes à cette stratégie de sauvegarde, sélectionnez **Ajouter des volumes** puis, dans la liste tabulaire des volumes, cliquez sur les cases à cocher pour affecter un ou plusieurs volumes à cette stratégie de sauvegarde.

       ![Ajout d’une stratégie de sauvegarde](./media/storsimple-8000-add-backup-policy-u2/addbupol2.png)

   4. Pour définir une planification pour cette stratégie de sauvegarde, cliquez sur **Première planification**, puis modifiez les paramètres suivants :

       ![Ajout d’une stratégie de sauvegarde](./media/storsimple-8000-add-backup-policy-u2/addbupol3.png)

       1. Pour **Type d’instantané**, sélectionnez **Cloud** ou **Local**.

       2. Indiquez la fréquence des sauvegardes (spécifiez un nombre et choisissez **jours** ou **semaines** dans la liste déroulante).

       3. Entrez une planification de rétention.

       4. Entrez l’heure et la date de début de la stratégie de sauvegarde.

       5. Cliquez sur **OK** pour définir la planification.

   5. Cliquez sur **Créer** pour créer une stratégie de sauvegarde.

       ![Ajout d’une stratégie de sauvegarde](./media/storsimple-8000-add-backup-policy-u2/addbupol4.png)
   
   6. Un message s’affiche une fois la stratégie de sauvegarde créée. La stratégie ajoutée s’affiche dans le tableau du panneau **Stratégies de sauvegarde**.

       ![Ajout d’une stratégie de sauvegarde](./media/storsimple-8000-add-backup-policy-u2/addbupol7.png)

