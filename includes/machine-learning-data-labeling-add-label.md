---
author: sgilley
ms.service: machine-learning
ms.topic: include
ms.date: 09/23/2021
ms.author: sdgilley
ms.openlocfilehash: 1c85888c04859769d82b9c5ebe4691797e404adf
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368278"
---
Pendant le processus d’étiquetage des données, vous souhaiterez peut-être ajouter d’autres étiquettes pour classer vos éléments.  Par exemple, vous pouvez ajouter une étiquette « inconnu » ou « autre » pour indiquer des images pouvant prêter à confusion.

Pour ajouter une ou plusieurs étiquettes à un projet, effectuez les étapes suivantes :

1. Sélectionnez le projet dans la page **Étiquetage des données** principale.
1. En haut à droite de la page, basculez **En cours d’exécution** sur **Mis en pause** pour arrêter les étiqueteurs dans leur activité.
1. Sélectionnez l’onglet **Détails**.
1. Dans la liste à gauche, sélectionnez **Classes d’étiquette**.
1. En haut de la liste, sélectionnez **+ Ajouter des étiquettes** ![Ajouter une étiquette](../articles/machine-learning/media/how-to-create-labeling-projects/add-label.png).
1. Dans le formulaire, ajoutez votre nouvelle étiquette. Choisissez ensuite comment continuer le projet.  Étant donné que vous avez modifié les étiquettes disponibles, choisissez comment traiter les données déjà étiquetées :
    * Recommencer en supprimant toutes les étiquettes existantes.  Choisissez cette option si vous souhaitez commencer l’étiquetage depuis le début avec le nouvel ensemble complet d’étiquettes. 
    * Recommencer en conservant toutes les étiquettes existantes.  Choisissez cette option pour marquer toutes les données comme non étiquetées, mais en conservant les étiquettes existantes comme étiquettes par défaut pour les images précédemment étiquetées.
    * Continuer en conservant toutes les étiquettes existantes. Choisissez cette option pour laisser toutes les données déjà étiquetées telles quelles et commencer à utiliser la nouvelle étiquette pour les données qui ne sont pas encore étiquetées.
1. Modifiez votre page d’instructions au besoin pour la ou les nouvelles étiquettes.
1. Une fois que vous avez ajouté toutes les nouvelles étiquettes, en haut à droite de la page, basculez **Mis en pause** sur **En cours d’exécution** pour redémarrer le projet. 