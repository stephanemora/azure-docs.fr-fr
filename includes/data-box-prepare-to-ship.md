---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/07/2019
ms.author: alkohli
ms.openlocfilehash: c7e5231650ec1afb97a72ec0cf26cb8f80088b63
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54440428"
---
La dernière étape consiste à préparer l’appareil pour l’expédition. Pour cela, tous les partages de l’appareil sont mis hors connexion. Une fois que ce processus aura débuté, vous ne pourrez plus accéder aux partages.

> [!IMPORTANT]
> La préparation de l’expédition est nécessaire, car elle signale les données qui ne sont pas conformes aux conventions de nommage Azure. Si vous ignorez cette étape, vous risquez de ne pas pouvoir charger vos données en raison de leur non conformité.

1. Accédez à la page **Prepare to ship** (Préparer l’expédition) et cliquez sur **Démarrer la préparation**. Par défaut, les sommes de contrôle sont calculées par le biais d’une fonction inline pendant la préparation de l’expédition. Le calcul des sommes de contrôle peut prendre plusieurs heures, selon la taille de vos données. 
   
    ![Préparer l’expédition 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

    Si pour une raison ou une autre, vous souhaitez arrêter la préparation de l’appareil, cliquez sur **Arrêter la préparation**. Vous pourrez reprendre la préparation à l’expédition plus tard.
        
    ![Préparer l’expédition 2](media/data-box-prepare-to-ship/prepare-to-ship2.png)
    
2. La préparation à l’expédition démarre et les partages de l’appareil sont mis hors ligne. Un message s’affiche pour vous rappeler de télécharger l’étiquette d’expédition une fois que l’appareil est prêt.

    ![Rappel Télécharger une étiquette d’expédition](media/data-box-prepare-to-ship/download-shipping-label-reminder.png)

3. Une fois la préparation terminée, l’appareil passe à l’état *Ready to ship* (Prêt pour expédition), puis est verrouillé.
        
    ![Préparer l’expédition 3](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    Si vous souhaitez copier davantage de données sur l’appareil, vous pouvez déverrouiller l’appareil, copier d’autres données, puis exécuter une nouvelle préparation à l’expédition.

    Si des erreurs se produisent lors de cette étape, vous devrez télécharger le journal des erreurs et résoudre les problèmes. Une fois les erreurs résolues, exécutez **Préparer l’expédition**.

4. Lorsque la préparation de l’expédition s’est terminée correctement (sans erreur), téléchargez la liste des fichiers (ou « manifeste ») qui ont été copiés lors de ce processus. Vous pourrez utiliser cette liste ultérieurement pour vérifier les fichiers téléchargés vers Azure.
        
    ![Préparer l’expédition 1](media/data-box-prepare-to-ship/prepare-to-ship4.png)

5. Arrêtez l’appareil. Accédez à la page **Shut down or restart** (Arrêter ou redémarrer) et cliquez sur **Arrêter**. Lorsque vous êtes invité à confirmer l’opération, cliquez sur **OK** pour continuer.

6. Débranchez les câbles. L’étape suivante consiste à expédier l’appareil à Microsoft.
