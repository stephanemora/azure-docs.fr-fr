---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/28/2019
ms.author: alkohli
ms.openlocfilehash: f5b60d862be0d71f71f770c47d88ad39f2fc6ac7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "66419957"
---
La dernière étape consiste à préparer l’appareil pour l’expédition. Pour cela, tous les partages de l’appareil sont mis hors connexion. Une fois que ce processus aura débuté, vous ne pourrez plus accéder aux partages.

> [!IMPORTANT]
> La préparation de l’expédition est nécessaire, car elle signale les données qui ne sont pas conformes aux conventions de nommage Azure. Si vous ignorez cette étape, vous risquez de ne pas pouvoir charger vos données en raison de leur non-conformité.

1. Accédez à la page **Prepare to ship** (Préparer l’expédition) et cliquez sur **Démarrer la préparation**. Par défaut, les sommes de contrôle sont calculées pendant la copie des données. La préparation de l’expédition procède au calcul des sommes de contrôle et crée la liste des fichiers (également appelés *fichiers BOM* ou manifeste). Le calcul des sommes de contrôle peut prendre plusieurs heures, selon la taille de vos données.
   
    ![Préparer l’expédition 1](media/data-box-heavy-prepare-to-ship/prepare-to-ship1.png)

    Si pour une raison ou une autre, vous souhaitez arrêter la préparation de l’appareil, cliquez sur **Arrêter la préparation**. Vous pourrez reprendre la préparation à l’expédition plus tard.
        
    ![Préparer l’expédition 2](media/data-box-heavy-prepare-to-ship/prepare-to-ship2.png)
    
2. La préparation à l’expédition démarre et les partages de l’appareil sont mis hors ligne. Un message s’affiche pour vous rappeler de télécharger l’étiquette d’expédition une fois que l’appareil est prêt.

    ![Rappel Télécharger une étiquette d’expédition](media/data-box-heavy-prepare-to-ship/download-shipping-label-reminder.png)

3. Une fois la préparation terminée, l’appareil passe à l’état *Ready to ship* (Prêt pour expédition), puis est verrouillé.
        
    ![Préparer l’expédition 3](media/data-box-heavy-prepare-to-ship/prepare-to-ship3.png)

    Si vous souhaitez copier davantage de données sur l’appareil, vous pouvez déverrouiller l’appareil, copier d’autres données, puis exécuter une nouvelle préparation à l’expédition.

    Si des erreurs se produisent lors de cette étape, téléchargez le journal des erreurs et corrigez les erreurs. Une fois les erreurs résolues, exécutez **Préparer l’expédition**.

4. Lorsque la préparation de l’expédition s’est terminée correctement (sans erreurs), téléchargez la liste des fichiers (*fichiers BOM*ou manifeste) qui ont été copiés lors de ce processus. 

    ![Télécharger la liste des fichiers ou fichiers BOM](media/data-box-heavy-prepare-to-ship/download-list-of-files.png)

   Vous pourrez utiliser cette liste ultérieurement pour vérifier les fichiers téléchargés vers Azure. Pour plus d’informations, consultez [Inspecter les fichiers BOM pendant la préparation de l’expédition](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship).
        
    ![Exemple de fichier BOM](media/data-box-heavy-prepare-to-ship/sample-bom-file.png)

5. Arrêtez l’appareil. Accédez à la page **Shut down or restart** (Arrêter ou redémarrer) et cliquez sur **Arrêter**. Lorsque vous êtes invité à confirmer l’opération, cliquez sur **OK** pour continuer.

    ![Arrêter le premier nœud de l’appareil](media/data-box-heavy-prepare-to-ship/shut-down-device-node.png)

6. Répétez toutes les étapes ci-dessus pour l’autre nœud de l’appareil.
7. Une fois que l’appareil est complètement arrêté, tous les voyants situés à l’arrière de l’appareil sont éteints. L’étape suivante consiste à retirer tous les câbles et à expédier l’appareil à Microsoft.
