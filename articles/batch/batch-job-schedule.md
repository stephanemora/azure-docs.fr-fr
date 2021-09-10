---
title: Planifier vos travaux
description: Utilisez la planification des travaux pour gérer vos tâches.
ms.topic: how-to
ms.date: 07/16/2021
ms.custom: seodec18
ms.openlocfilehash: a247be1826cd68ef2ac1302c1910eae74b2d65b6
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2021
ms.locfileid: "114405738"
---
# <a name="schedule-jobs-for-efficiency"></a>Planifier des travaux pour gagner en efficacité

La planification des travaux de traitement par lots vous permet de classer par ordre de priorité les travaux que vous souhaitez exécuter en premier, tout en prenant en compte les [tâches qui ont des dépendances avec d’autres tâches](batch-task-dependencies.md). En planifiant vos travaux, vous pouvez vous assurer d’utiliser le moins de ressources possible. Les nœuds peuvent être mis hors service lorsqu’ils ne sont pas nécessaires, et les tâches qui dépendent d’autres tâches sont lancées juste-à-temps pour optimiser les workflows. Vous pouvez également définir l’autocomplétion des travaux, car un seul travail à la fois s’exécute et un nouveau ne démarre pas tant que le précédent n’est pas terminé.

Les tâches que vous planifiez à l’aide de la tâche du gestionnaire de travaux sont associées à une tâche. La tâche du gestionnaire de travaux créera des tâches pour le travail. Pour ce faire, la tâche du gestionnaire de travaux doit s’authentifier auprès du compte Batch. Utilisez le jeton d’accès AZ_BATCH_AUTHENTICATION_TOKEN. Le jeton autorise l’accès au reste du travail.

Pour gérer un travail à l’aide d’Azure CLI, consultez [az batch job-schedule](/cli/azure/batch/job-schedule). Vous pouvez également créer des planifications de travaux dans le Portail Azure.

## <a name="schedule-a-job-in-the-azure-portal"></a>Planifier un travail dans le Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Sélectionnez le compte Batch dans lequel vous souhaitez planifier des travaux.
1. Dans le volet de navigation gauche, sélectionnez **Planifications de travaux**.
1. Sélectionnez **Ajouter** pour créer une nouvelle planification de travaux.
1. Dans le **Formulaire de base**, entrez les informations suivantes :
   - **ID de planification du travail** : un identificateur unique pour la planification de ce travail.
   - **Nom complet** : ce nom est facultatif et ne doit pas nécessairement être unique. Sa longueur maximale est de 1 024 caractères.
   - **Ne pas exécuter jusqu’à** : spécifie l’heure à laquelle le travail est exécuté au plus tôt. Si vous ne la définissez pas, la planification est prête à exécuter les travaux immédiatement.
   - **Ne pas exécuter après** : aucun travail ne sera exécuté après l’heure que vous avez entrée ici. Si vous ne spécifiez pas d’heure, vous créez une planification de travail périodique, qui reste active jusqu’à ce que vous la terminiez explicitement.
   - **Intervalle de périodicité** : sélectionnez **Activé** si vous voulez spécifier le laps de temps entre les travaux. Vous ne pouvez avoir qu’un seul travail planifié à la fois. Par conséquent, s’il est temps de créer un nouveau travail dans le cadre d’une planification de travail, mais que le travail précédent est encore en cours d’exécution, le service Batch ne crée pas le nouveau travail tant que le travail précédent n’est pas terminé.
   - **Fenêtre Démarrer** : sélectionnez **Personnalisé** si vous souhaitez spécifier l’intervalle de temps dans lequel un travail doit être créé. Si un travail n’est pas créé dans cette fenêtre, aucun nouveau travail n’est créé avant la prochaine récurrence de la planification.

     :::image type="content" source="media/batch-job-schedule/add-job-schedule-02.png" alt-text="Capture d’écran des options Ajouter une planification de travail dans le Portail Azure.":::  

1. En bas du formulaire de base, spécifiez le pool sur lequel vous souhaitez que le travail s’exécute. Pour choisir dans une liste de pools dans votre compte Batch, sélectionnez **Mettre à jour**.
1. De pair avec l’**ID de pool**, entrez les informations suivantes :
   - **Tâche de configuration des travaux** : sélectionnez **Mettre à jour** pour nommer et configurer la tâche du gestionnaire de travaux, ainsi que les tâches de préparation et de lancement des travaux, si vous en utilisez.
   - **Nom complet** : ce nom est facultatif et ne doit pas nécessairement être unique. Sa longueur maximale est de 1 024 caractères.
   - **Priorité** : utilisez le curseur pour définir une priorité pour le travail, ou entrez une valeur dans la zone.
   - **Durée maximale de l’horloge** : sélectionnez **Personnalisée** si vous souhaitez définir une durée maximale d’exécution du travail. Dans ce cas, Batch mettra fin à la tâche si elle ne se termine pas dans ce laps de temps.
   - **Nombre maximal de tentatives de tâche** : sélectionnez **Personnalisé** si vous voulez spécifier le nombre de fois qu’une tâche peut être réessayée, ou **Illimité** si vous voulez que la tâche soit essayée autant de fois que nécessaire. Ce n’est pas le même que le nombre de nouvelles tentatives qu’un appel d’API peut avoir.
   - **Lorsque toutes les tâches sont terminées** : la valeur par défaut est NoAction, mais vous pouvez sélectionner **TerminateJob** si vous préférez mettre fin au travail une fois toutes les tâches terminées (ou en l’absence de tâches dans le travail).
   - **Quand une tâche échoue** : une tâche échoue si le nombre de tentatives est épuisé ou si une erreur s’est produite lors du démarrage de la tâche. La valeur par défaut est NoAction, mais vous pouvez sélectionner **PerformExitOptionsJobAction** si vous préférez effectuer l’action associée à la condition de sortie de la tâche en cas d’échec.

     :::image type="content" source="media/batch-job-schedule/add-job-schedule-03.png" alt-text="Capture d’écran des options de spécification de tâche pour une nouvelle planification de travail dans le Portail Azure.":::

1. Sélectionnez **Enregistrer** pour créer votre planification de travail.

Pour suivre l’exécution du travail, revenez à **Planification des travaux** et sélectionnez la planification du travail. Développez **Informations sur l’exécution** pour voir les détails. Vous pouvez également arrêter, supprimer ou désactiver la planification du travail à partir de cet écran.

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur [les travaux et les tâches](jobs-and-tasks.md).
- [Créez des dépendances de tâches](batch-task-dependencies.md) pour exécuter des tâches qui dépendent d’autres tâches.
