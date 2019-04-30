---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d35f0ef783a2c48f8211657bc8829635c19495aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61409917"
---
#### <a name="to-enter-maintenance-mode"></a>Pour passer en mode Maintenance
1. Dans le menu de la console série, sélectionnez l’option 1, **Ouvrir une session avec un accès total**.
2. Saisissez le mot de passe. Le mot de passe par défaut est **Password1**.
3. À l'invite de commandes, tapez
   
     `Enter-HcsMaintenanceMode`
4. Un message d’avertissement s’affiche pour vous indiquer que le mode Maintenance va perturber toutes les demandes d’E/S et annuler la connexion au portail Azure Classic. Vous êtes invité à confirmer l’opération. Tapez **O** pour passer en mode Maintenance.
   
    Les deux contrôleurs redémarrent. Une fois le redémarrage terminé, un autre message s’affiche pour indiquer que l’appareil est en mode Maintenance.

