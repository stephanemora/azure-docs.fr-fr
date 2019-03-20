---
author: rayne-wiselman
ms.service: backup
ms.topic: include
ms.date: 11/09/2018
ms.author: raynew
ms.openlocfilehash: 3631d2e9beaa7c0d9ee018a32981a278381a7d86
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58115694"
---
## <a name="defining-a-backup-policy"></a>Définition d’une stratégie de sauvegarde
Une stratégie de sauvegarde définit quand les instantanés de données sont pris ainsi que leur durée de conservation. Lors de la définition de la stratégie de sauvegarde d’une machine virtuelle, vous pouvez déclencher un travail de sauvegarde *une fois par jour*. Lorsque vous créez une stratégie, elle est appliquée à l’archivage. L'interface de la stratégie de sauvegarde ressemble à ceci :

![Stratégie de sauvegarde](./media/backup-create-policy-for-vms/backup-policy.png)

Pour créer une stratégie :

1. Entrez un nom dans **Nom de la stratégie**.
2. Les instantanés de vos données peuvent être pris à intervalles quotidiens ou hebdomadaires. Utilisez le menu déroulant **Fréquence de sauvegarde** pour déterminer la fréquence de prise des instantanés de données (tous les jours ou toutes les semaines).

   * Si vous choisissez un intervalle quotidien, utilisez le contrôle en surbrillance pour sélectionner l'heure du jour à laquelle prendre l'instantané. Pour modifier l'heure, désélectionnez-la et choisissez-en une autre.

     ![Stratégie de sauvegarde quotidienne](./media/backup-create-policy-for-vms/backup-policy-daily.png) <br/>
   * Si vous choisissez un intervalle hebdomadaire, utilisez les contrôles en surbrillance pour sélectionner le(s) jour(s) de la semaine et l’heure auxquels prendre l’instantané. Dans le menu du jour, sélectionnez un ou plusieurs jours. Dans le menu de l'heure, sélectionnez une heure. Pour modifier l'heure, désélectionnez-la et choisissez-en une autre.

     ![Stratégie de sauvegarde hebdomadaire](./media/backup-create-policy-for-vms/backup-policy-weekly.png)
3. Par défaut, toutes les options **Durée de rétention** sont sélectionnées. Désactivez la limite de plage de rétention que vous ne souhaitez pas utiliser. Ensuite, spécifiez le ou les intervalles à utiliser.

    Les durées de rétention mensuelle et annuelle vous permettent de prendre des instantanés selon un intervalle hebdomadaire ou quotidien.

   > [!NOTE]
   > 
   > - Lorsque vous protégez un ordinateur virtuel, un travail de sauvegarde s'exécute une fois par jour. L'heure d'exécution de la sauvegarde est le même pour chaque plage de rétention.
   > - Le point de récupération est généré à la date et à l’heure auxquelles l’instantané de sauvegarde a été effectué, indépendamment de la planification du travail de sauvegarde.
   >   - P. Si la fréquence de sauvegarde est planifiée pour 23 h 30 et si, en raison d’un problème, l’instantané est effectué à 0 h 01, le point de récupération est créé avec la prochaine date et à 0 h 01.
   > - En cas de sauvegarde mensuelle, si la sauvegarde est configurée pour s’exécuter le premier jour de chaque mois et si l’instantané est effectué le lendemain en raison d’un problème, le point de récupération créé pour la sauvegarde mensuelle porte l’indication du jour suivant (c’est-à-dire le second du mois en question).


4. Après avoir défini toutes les options de la stratégie, cliquez sur **Enregistrer**en haut du panneau.

    La nouvelle stratégie est appliquée immédiatement au coffre.
