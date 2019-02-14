---
title: Créer une planification pour laboratoire de classe dans Azure Lab Services | Microsoft Docs
description: Découvrez comment créer des planifications pour laboratoires de classe dans Azure Lab Services de sorte que les machines virtuelles utilisées dans les laboratoires démarrent et s’arrêtent à une heure précise.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: spelluru
ms.openlocfilehash: 34bc8263053cd4a701c16ee1832cf1b27340a345
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55501260"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Créer et gérer des planifications pour laboratoires de classe dans Azure Lab Services 
Les planifications vous permettent de configurer un laboratoire de classe de sorte que les machines virtuelles utilisées dans le laboratoire démarrent et s’arrêtent automatiquement à une heure précise. Vous pouvez définir une planification ponctuelle ou périodique. Les procédures suivantes décrivent comment créer et gérer des planifications pour un laboratoire de classe : 

> [!IMPORTANT]
> Le temps d’exécution planifié des machines virtuelles ne compte pas dans le [quota alloué à un utilisateur](how-to-configure-student-usage.md#set-quotas-per-user). Le quota s’applique au temps pendant lequel un étudiant utilise les machines virtuelles en dehors des heures planifiées. 

## <a name="add-a-schedule-once"></a>Ajouter une planification (une fois)

1. Basculez vers la page **Planifications**, puis, dans la barre d’outils, sélectionnez **Ajouter une planification**. 

    ![Bouton Ajouter une planification dans la page Planifications](../media/how-to-create-schedules/add-schedule-button.png)
2. Dans la page **Ajouter une planification**, vérifiez que l’option **Une fois** située en haut est activée. Si elle ne l’est pas, sélectionnez **Une fois**. 
3. Pour **Schedule date (required)** (Planifier une date (obligatoire)), entrez la date ou sélectionnez l’icône de calendrier pour sélectionner une date. 
4. Pour **Start time** (Heure du démarrage), sélectionnez l’heure à laquelle vous souhaitez que les machines virtuelles démarrent. L’heure de démarrage est obligatoire si l’heure d’arrêt n’est pas définie. Sélectionnez **Remove start event** (Supprimer l’événement de démarrage) si vous souhaitez spécifier uniquement l’heure d’arrêt. Si l’option **Start time** (Heure de démarrage) est désactivée, sélectionnez **Add start event** (Ajouter un événement de démarrage) en regard de la liste déroulante pour l’activer. 
5. Pour **Stop time** (Heure de l’arrêt), sélectionnez l’heure à laquelle vous souhaitez que les machines virtuelles s’arrêtent. L’heure d’arrêt est obligatoire si l’heure de démarrage n’est pas définie. Sélectionnez **Remove stop event** (Supprimer l’événement d’arrêt) si vous souhaitez spécifier uniquement l’heure de démarrage. Si l’option **Stop time** (Heure d’arrêt) est désactivée, sélectionnez **Add stop event** (Ajouter un événement d’arrêt) en regard de la liste déroulante pour l’activer.
6. Pour **Time zone (required)** (Fuseau horaire (obligatoire)), sélectionnez le fuseau horaire pour les heures de démarrage et d’arrêt que vous avez spécifiées. 
7. Pour **Notes**, entrez une description ou des notes pour la planification. 
8. Sélectionnez **Enregistrer**. 

    ![Planification ponctuelle](../media/how-to-create-schedules/add-schedule-page.png)

## <a name="add-a-recurring-schedule-weekly"></a>Ajouter une planification périodique (hebdomadaire)

1. Basculez vers la page **Planifications**, puis, dans la barre d’outils, sélectionnez **Ajouter une planification**. 

    ![Bouton Ajouter une planification dans la page Planifications](../media/how-to-create-schedules/add-schedule-button.png)
2. Dans la page **Ajouter une planification**, basculez vers **Hebdomadaire** en haut. 
3. Pour **Schedule days (required)** (Planifier des jours (obligatoire)), sélectionnez les jours auxquels vous voulez appliquer la planification. Dans l’exemple suivant, l’option Monday-Friday (Lundi-vendredi) est sélectionnée. 
4. Dans le champ **De**, entrez la **date de début de la planification** ou choisissez une date en sélectionnant le bouton **Calendrier**. Ce champ est obligatoire. 
5. Pour **Schedule end date** (Planifier une date de fin), entrez ou sélectionnez une date de fin à laquelle les machines virtuelles devront être arrêtées. 
6. Pour **Start time** (Heure de démarrage), sélectionnez l’heure à laquelle vous voulez que les machines virtuelles démarrent. L’heure de démarrage est obligatoire si l’heure d’arrêt n’est pas définie. Sélectionnez **Remove start event** (Supprimer l’événement de démarrage) si vous souhaitez spécifier uniquement l’heure d’arrêt. Si l’option **Start time** (Heure de démarrage) est désactivée, sélectionnez **Add start event** (Ajouter un événement de démarrage) en regard de la liste déroulante pour l’activer. 
7. Pour **Stop time** (Heure de l’arrêt), sélectionnez l’heure à laquelle vous souhaitez que les machines virtuelles s’arrêtent. L’heure d’arrêt est obligatoire si l’heure de démarrage n’est pas définie. Sélectionnez **Remove stop event** (Supprimer l’événement d’arrêt) si vous souhaitez spécifier uniquement l’heure de démarrage. Si l’option **Stop time** (Heure d’arrêt) est désactivée, sélectionnez **Add stop event** (Ajouter un événement d’arrêt) en regard de la liste déroulante pour l’activer.
8. Pour **Time zone (required)** (Fuseau horaire (obligatoire)), sélectionnez le fuseau horaire pour les heures de démarrage et d’arrêt que vous avez spécifiées.  
9. Pour **Notes**, entrez une description ou des notes pour la planification. 
10. Sélectionnez **Enregistrer**. 

    ![Planification hebdomadaire](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Afficher les planifications dans un calendrier
Vous pouvez voir les dates et heures planifiées en surbrillance dans l’affichage calendrier, comme illustré dans l’image suivante :

![Planifications dans l’affichage calendrier](../media/how-to-create-schedules/schedules-in-calendar.png)

Sélectionnez le bouton **Aujourd’hui** en haut à droite pour basculer vers la date actuelle dans le calendrier. Sélectionnez la **flèche gauche** pour basculer vers la semaine précédente, et la **flèche droite** pour basculer vers la semaine suivante dans le calendrier. 

## <a name="edit-a-schedule"></a>Modifier une planification
Lorsque vous double-cliquez sur une planification en surbrillance dans le calendrier ou sélectionnez le bouton **Crayon** dans la barre d’outils, la page **Modifier la planification** s’affiche. La mise à jour des paramètres dans cette page s’effectue de la même manière que dans la page **Ajouter une planification**, comme décrit dans la section [Ajouter une planification périodique](#add-a-recurring-schedule-weekly). 

![Page Modifier la planification](../media/how-to-create-schedules/edit-schedule-page.png)

## <a name="delete-a-schedule"></a>Supprimer une planification

1. Pour supprimer une planification, sélectionnez le bouton Corbeille (Supprimer) dans la barre d’outils, comme illustré dans l’image suivante :

    ![Bouton Supprimer dans la barre d’outils](../media/how-to-create-schedules/delete-schedule-button.png)

    Vous pouvez utiliser le bouton Supprimer pour toutes les dates et heures planifiées dans le calendrier, puis sélectionner **Supprimer**. 
2. Dans la page **Delete schedules** (supprimer des planifications), sélectionnez **Oui**.

    ![Confirmation de la suppression de planifications](../media/how-to-create-schedules/delete-schedules-confirmation.png)




## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants :

- [En tant qu’administrateur, créer et gérer des comptes de labo](how-to-manage-lab-accounts.md)
- [En tant que propriétaire de labo, créer et gérer des labos](how-to-manage-classroom-labs.md)
- [En tant que propriétaire de labo, configurer et contrôler l’utilisation d’un labo](how-to-configure-student-usage.md)
- [En tant qu’utilisateur du laboratoire, accéder aux laboratoires de classe](how-to-use-classroom-lab.md)
