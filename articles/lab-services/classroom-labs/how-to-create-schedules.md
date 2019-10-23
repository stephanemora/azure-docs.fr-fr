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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 4e3cf302437c3e4954ac977ac3f4ff6b2021a760
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330543"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Créer et gérer des planifications pour laboratoires de classe dans Azure Lab Services 
Les planifications vous permettent de configurer un laboratoire de classe de sorte que les machines virtuelles utilisées dans le laboratoire démarrent et s’arrêtent automatiquement à une heure précise. Vous pouvez définir une planification ponctuelle ou périodique. Les procédures suivantes décrivent comment créer et gérer des planifications pour un laboratoire de classe : 

> [!IMPORTANT]
> Le temps d’exécution planifié des machines virtuelles ne compte pas dans le [quota alloué à un utilisateur](how-to-configure-student-usage.md#set-quotas-for-users). Le quota s’applique au temps pendant lequel un étudiant utilise les machines virtuelles en dehors des heures planifiées. 

## <a name="set-a-schedule-for-the-lab"></a>Définir un calendrier pour le laboratoire
Créez un événement planifié pour le labo afin que les machines virtuelles du labo soient automatiquement démarrées ou arrêtées à des moments précis. Le quota utilisateur que vous avez spécifié précédemment est le temps supplémentaire affecté à chaque utilisateur en dehors de ces heures planifiées. 

1. Basculez vers la page **Planifications**, puis, dans la barre d’outils, sélectionnez **Ajouter un événement planifié**. 

    ![Bouton Ajouter une planification dans la page Planifications](../media/how-to-create-schedules/add-schedule-button.png)
2. Vérifiez que **Standard** est sélectionné pour le **Type d’événement**. Vous sélectionnez **Démarrage uniquement** pour spécifier uniquement l’heure de démarrage des machines virtuelles. Vous sélectionnez **Arrêt uniquement** pour spécifier uniquement l’heure d’arrêt des machines virtuelles. 
7. Dans la section **Répéter**, sélectionnez la planification actuelle. 

    ![Bouton Ajouter une planification dans la page Planifications](../media/how-to-create-schedules/select-current-schedule.png)
5. Dans la boîte de dialogue **Répéter**, effectuez ce qui suit :
    1. Vérifiez que **Chaque semaine** est défini pour le champ **Répéter**. 
    3. Spécifiez la **date de démarrage**.
    4. Sélectionnez **l’heure de démarrage** à laquelle vous voulez que les machines virtuelles démarrent.
    5. Spécifiez **l’heure d’arrêt** à laquelle les machines virtuelles doivent être arrêtées. 
    6. Spécifiez le **fuseau horaire** pour les heures de démarrage et d’arrêt que vous avez spécifiées. 
    2. Sélectionnez les jours auxquels vous voulez appliquer la planification. Dans l’exemple suivant, l’option Monday-Thursday (Lundi-jeudi) est sélectionnée. 
    8. Sélectionnez **Enregistrer**. 

        ![Définir la répétition de la planification](../media/how-to-create-schedules/set-repeat-schedule.png)

3. Maintenant, dans la page **Ajouter des événements planifiés**, pour **Notes (facultatif)** , entrez une description ou des remarques pour la planification. 
4. Sur la page **Ajouter des événements planifiés**, sélectionnez **Enregistrer**. 

    ![Planification hebdomadaire](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Afficher les planifications dans un calendrier
Vous pouvez voir les dates et heures planifiées en surbrillance dans l’affichage calendrier, comme illustré dans l’image suivante :

![Planifications dans l’affichage calendrier](../media/how-to-create-schedules/schedules-calendar.png)

Sélectionnez le bouton **Aujourd’hui** en haut à droite pour basculer vers la date actuelle dans le calendrier. Sélectionnez la **flèche gauche** pour basculer vers la semaine précédente, et la **flèche droite** pour basculer vers la semaine suivante dans le calendrier. 

## <a name="edit-a-schedule"></a>Modifier une planification
Lorsque vous sélectionnez une planification en surbrillance dans le calendrier, vous voyez s’afficher des boutons pour **Modifier** ou **Supprimer** la planification. 

![Page Modifier la planification](../media/how-to-create-schedules/schedule-edit-button.png)

Dans la page **Modifier l’événement planifié**, vous pouvez mettre à jour la planification et sélectionner **Enregistrer**. 

## <a name="delete-a-schedule"></a>Supprimer une planification

1. Pour supprimer une planification, sélectionnez une planification en surbrillance dans le calendrier, puis sélectionnez le bouton de corbeille (Supprimer) :

    ![Bouton Supprimer dans la barre d’outils](../media/how-to-create-schedules/schedule-delete-button.png)
2. Dans la boîte de dialogue **Supprimer l’événement planifié**, sélectionnez **Oui** pour confirmer la suppression. 



## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants :

- [En tant qu’administrateur, créer et gérer des comptes de labo](how-to-manage-lab-accounts.md)
- [En tant que propriétaire de labo, créer et gérer des labos](how-to-manage-classroom-labs.md)
- [En tant que propriétaire de labo, configurer et contrôler l’utilisation d’un labo](how-to-configure-student-usage.md)
- [En tant qu’utilisateur du laboratoire, accéder aux laboratoires de classe](how-to-use-classroom-lab.md)
