---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 71f18cf8448060385ea38be9b2719b1ed545c5d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545297"
---
> [!NOTE] 
> Vous ne pouvez pas modifier les paramètres de chiffrement ni les informations d’identification de compte de stockage associés à un conteneur de volumes après sa création.

#### <a name="to-modify-a-volume-container"></a>Pour modifier un conteneur de volumes

1. Accédez à votre service StorSimple Device Manager, puis accédez à **Gestion > Conteneurs de volumes**.

2. Dans la liste tabulaire des conteneurs de volumes, sélectionnez le conteneur de volumes à modifier. Sur la page **Appareils**, sélectionnez l’appareil, double-cliquez dessus, puis cliquez sur l’onglet **Conteneurs de volumes**.

3. Dans la liste tabulaire des conteneurs de volumes, sélectionnez le conteneur de volumes à modifier. Dans le panneau qui s’ouvre, cliquez sur **Modifier** à partir de la barre de commandes.

    ![Modifier un conteneur de volumes](./media/storsimple-8000-modify-volume-container/modify-volume-container-01.png)

4. Dans le panneau **Modifier le conteneur de volumes**, effectuez les étapes suivantes :
   
   1. Une fois spécifiés, le nom, la clé de chiffrement et le compte de stockage associés au conteneur de volumes ne peuvent plus être modifiés. Modifier le paramètre de bande passante associée.<!--STEPS NEED WORK. Updated screen doesn't show alternative to Unlimited or subsequent steps if they customize bandwidth. Can we talk them through this (briefly)?-->
      
       ![Modifier le paramètre de bande passante](./media/storsimple-8000-modify-volume-container/modify-volume-container-02.png)<!--New graphic based on: modify-volume-container-bw-setting.png-->

   1.  Cliquez sur **OK**.<!--If they choose Custom, do they still click OK, or are there more steps?-->

5. Dans la page suivante de la boîte de dialogue **Modifier le conteneur de volumes** , procédez comme suit :<!--This step happens only if they choose Custom bandwidth? Are the steps similar to those in "Add volume container," step 3f, above?"-->
   
   1. Dans la liste déroulante, sélectionnez un modèle de bande passante.
   1. Examinez les paramètres de planification du modèle de bande passante spécifié.
   1. Cliquez sur **Enregistrer** et confirmez les modifications.
      
       ![Confirmer les modifications](./media/storsimple-8000-modify-volume-container/modify-volume-container-03.png)

      Le panneau **Conteneurs de volumes** est mis à jour pour refléter les modifications.
