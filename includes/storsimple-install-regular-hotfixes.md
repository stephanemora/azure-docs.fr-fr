---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 77fc95c53ba89c3905ebd1aec785e22f42339369
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164303"
---
<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>Pour installer des correctifs logiciels périodiques via Windows PowerShell pour StorSimple
1. Connectez-vous à la console série de l’appareil. Pour plus d’informations, consultez [l’Étape 1 : connexion à la console série](../articles/storsimple/storsimple-update-device.md#step1).
2. Dans le menu de la console série, sélectionnez l’option 1, **Ouvrir une session avec un accès total**. Saisissez le mot de passe. Le mot de passe par défaut est **Password1**.
3.  À l’invite de commandes, tapez :
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > Cette commande s’applique uniquement aux correctifs logiciels périodiques. Vous exécutez cette commande sur un seul contrôleur, mais les deux contrôleurs sont mis à jour.
    > Il est possible qu’un basculement de contrôleur se produise pendant la mise à jour. Celui-ci n’a aucune incidence sur la disponibilité ou le fonctionnement du système.

4. Lorsque vous y êtes invité, indiquez le chemin d’accès au dossier partagé sur le réseau qui contient les fichiers du correctif logiciel.
5. Vous êtes invité à confirmer l’opération. Saisissez **O** pour poursuivre l’installation du correctif logiciel.

