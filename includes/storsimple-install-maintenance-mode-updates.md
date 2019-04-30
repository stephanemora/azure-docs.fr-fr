---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 720288aff462b0590bb9da509096a9305b9b6cc7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61409943"
---
#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Pour installer les mises à jour en mode Maintenance via Windows PowerShell pour StorSimple
1. Si vous ne l’avez pas déjà fait, accédez à la console série de l’appareil et sélectionnez l’option 1, **Ouvrir une session avec un accès total**. 
2. Saisissez le mot de passe. Le mot de passe par défaut est **Password1**.
3.  À l’invite de commandes, tapez :
   
     `Get-HcsUpdateAvailability` 
4. Le système vous indique si des mises à jour sont disponibles et si elles risquent ou non de provoquer une interruption de service. Pour appliquer les mises à jour sans interruption, vous devez mettre l’appareil en mode Maintenance. Consultez [Étape 2 : Quitter le mode Maintenance](../articles/storsimple/storsimple-update-device.md#step2) pour obtenir des instructions.
5. Lorsque votre périphérique est en mode Maintenance, à l’invite de commandes, tapez : `Start-HcsUpdate`
6. Vous êtes invité à confirmer l’opération. Une fois que vous les avez confirmées, les mises à jour sont installées sur le contrôleur auquel vous êtes connecté. Après l’installation des mises à jour, le contrôleur redémarre. 
7. Surveillez l’état des mises à jour. Tapez :
   
    `Get-HcsUpdateStatus`
   
    Si le `RunInProgress` est `True`, la mise à jour est toujours en cours. Si le `RunInProgress` est `False`, cela veut dire que la mise à jour est terminée.  
8. Lorsque la mise à jour est installée sur le contrôleur actuel et que celui-ci a redémarré, connectez-vous à l’autre contrôleur et menez les étapes 1 à 6.
9. Après la mise à jour des deux contrôleurs, quittez le mode Maintenance. Consultez [Étape 4 : Quitter le mode Maintenance](../articles/storsimple/storsimple-update-device.md#step4) pour obtenir des instructions.

