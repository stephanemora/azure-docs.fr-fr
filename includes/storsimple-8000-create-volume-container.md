---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 07/16/2021
ms.author: alkohli
ms.openlocfilehash: 4ad58078207a90966134ec4d6cf8815f614e4341
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449874"
---
#### <a name="to-create-a-volume-container"></a>Pour créer un conteneur de volumes

1. Accédez à votre service StorSimple Device Manager et cliquez sur **Appareils**. Dans la liste tabulaire des appareils, sélectionnez un appareil et cliquez dessus. 

    ![Panneau du conteneur de volumes](./media/storsimple-8000-create-volume-container/create-volume-container-01.png)

2. Dans le tableau de bord de l’appareil, cliquez sur **+ Ajouter un conteneur de volumes**.

    ![Panneau du conteneur de volumes 2](./media/storsimple-8000-create-volume-container/create-volume-container-02.png)

3. Dans le panneau **Ajouter un conteneur de volumes** :
   
   1. L’appareil est automatiquement sélectionné.
   2. Saisissez un **Nom** pour votre conteneur de volume. Le nom doit contenir de 3 à 32 caractères. Vous ne pouvez pas renommer un conteneur de volumes une fois qu’il a été créé.
   3. Sélectionnez **Activer le chiffrement de stockage cloud** pour activer le chiffrement des données envoyées à partir de l’appareil vers le cloud.
   4. Indiquez et confirmez une **Clé de chiffrement de stockage cloud** contenant de 8 à 32 caractères. Cette clé est utilisée par l’appareil pour accéder aux données chiffrées.
   5. Sélectionnez un **Compte de stockage** à associer à ce conteneur de volume. Vous pouvez choisir un compte de stockage existant ou un compte par défaut qui est généré au moment de la création du service. Vous pouvez également utiliser l’option **Ajouter nouveau** pour spécifier un compte de stockage qui n’est pas lié à cet abonnement au service.
   6. Sélectionnez **Illimitée** dans la liste déroulante **Spécifier la bande passante** si vous souhaitez consommer toute la bande passante disponible.
   
      Si vos informations d’utilisation de la bande passante sont disponibles, vous pouvez allouer de la bande passante selon une planification en spécifiant **Sélectionner un modèle de bande passante**. Pour une procédure pas à pas, consultez la page [Ajouter un modèle de bande passante](../articles/storsimple/storsimple-8000-manage-bandwidth-templates.md#add-a-bandwidth-template).

      ![Panneau du conteneur de volumes 3](./media/storsimple-8000-create-volume-container/create-volume-container-06-b.png)<!--New graphic. Source: add-volume-container-bw-setting.-->

   7. Cliquez sur **Créer**.

        <!--![Volume container blade 4](./media/storsimple-8000-create-volume-container/create-volume-container-06.png)-->
   
       Un message s’affiche une fois le conteneur de volumes créé.

       ![Notification de la création du conteneur de volumes](./media/storsimple-8000-create-volume-container/create-volume-container-08.png)

   Le conteneur de volumes nouvellement créé est répertorié dans la liste des conteneurs de volumes pour votre appareil.

   ![Panneau Ajouter un conteneur de volumes](./media/storsimple-8000-create-volume-container/create-volume-container-09.png)
