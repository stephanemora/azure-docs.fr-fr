---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8cc5dbb907c342b766cebe6da36cf580ddac5e2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "67177095"
---
#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>Pour installer des correctifs logiciels périodiques via Windows PowerShell pour StorSimple
1. Connectez-vous à la console série de l’appareil. Pour plus d’informations, consultez [l’Étape 1 : connexion à la console série](../articles/storsimple/storsimple-update-device.md#step1).
2. Dans le menu de la console série, sélectionnez l’option 1, **Ouvrir une session avec un accès total**. Tapez le mot de passe. Le mot de passe par défaut est **Password1**.
3. À l’invite de commandes, tapez :
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > Cette commande s’applique uniquement aux correctifs logiciels périodiques. Vous exécutez cette commande sur un seul contrôleur, mais les deux contrôleurs sont mis à jour.
    > Il est possible qu’un basculement de contrôleur se produise pendant la mise à jour. Celui-ci n’a aucune incidence sur la disponibilité ou le fonctionnement du système.

4. Lorsque vous y êtes invité, indiquez le chemin d’accès au dossier partagé sur le réseau qui contient les fichiers du correctif logiciel.
5. Vous êtes invité à confirmer l’opération. Tapez **Y** pour installer le correctif.

