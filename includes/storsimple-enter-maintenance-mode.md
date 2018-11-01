---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9bd2ee708f7d27cff5d07ca7f86d925ca6d2741d
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165422"
---
<!--author=SharS last changed: 12/01/15-->

#### <a name="to-enter-maintenance-mode"></a>Pour passer en mode Maintenance
1. Dans le menu de la console série, sélectionnez l’option 1, **Ouvrir une session avec un accès total**.
2. Saisissez le mot de passe. Le mot de passe par défaut est **Password1**.
3. À l'invite de commandes, tapez
   
     `Enter-HcsMaintenanceMode`
4. Un message d’avertissement s’affiche pour vous indiquer que le mode Maintenance va perturber toutes les demandes d’E/S et annuler la connexion au portail Azure Classic. Vous êtes invité à confirmer l’opération. Tapez **O** pour passer en mode Maintenance.
   
    Les deux contrôleurs redémarrent. Une fois le redémarrage terminé, un autre message s’affiche pour indiquer que l’appareil est en mode Maintenance.

