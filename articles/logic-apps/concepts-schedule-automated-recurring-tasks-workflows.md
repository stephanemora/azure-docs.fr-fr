---
title: Planification des tâches récurrentes et les flux de travail dans Azure Logic Apps
description: Une vue d’ensemble sur la planification périodique de tâches automatisées, les processus et les flux de travail avec Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 7f15dc5b28a44dc8405e2f0524913e6012ebe380
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66356044"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Planifier et exécuter périodique de tâches automatisées, les processus et les flux de travail avec Azure Logic Apps

Logic Apps vous permet de créer et exécuter des tâches récurrentes automatisées et les processus sur une planification. En créant un workflow d’application logique qui commence par un déclencheur récurrence ou une fenêtre glissante intégré, qui sont des déclencheurs de type de planification, vous pouvez exécuter des tâches immédiatement, à un moment ultérieur, ou selon un intervalle récurrent. Vous pouvez appeler des services à l’intérieur et en dehors d’Azure, tels que des points de terminaison HTTP ou HTTPS, publier des messages aux services Azure tels que stockage Azure et Azure Service Bus ou obtenir les fichiers téléchargés vers un partage de fichiers. Avec le déclencheur de périodicité, vous pouvez également définir des planifications complexes et avancées de récurrences pour exécuter des tâches. Pour plus d’informations sur les déclencheurs de planification intégrées et les actions, consultez [planification et exécution périodique automatisés, des tâches et des flux de travail avec Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Voici quelques exemples qui montrent les types de tâches que vous pouvez exécuter :

* Obtenir des données internes, telle que l’exécution d’une procédure stockée SQL tous les jours.

* Obtenir des données externes, telles qu’extraction météorologiques de NOAA toutes les 15 minutes.

* Envoyer des données de rapport, tels que courrier électronique un résumé de toutes les commandes supérieures à une quantité spécifique de la semaine dernière.

* Traiter des données, telles qu’aujourd'hui compresser les images téléchargées le tous les jours ouvrables pendant les heures creuses.

* Nettoyer les données, telles que supprimer tous les tweets de plus de trois mois.

* Archiver les données, tels que les factures de push à un service de sauvegarde à 1 h 00 chaque jour pendant neuf mois.

Vous pouvez également utiliser les actions intégrées de planification pour suspendre votre flux de travail avant d’exécute l’action suivante, par exemple :

* Attendre un jour de semaine pour envoyer une mise à jour d’état par e-mail.

* Retarder le workflow jusqu’à ce qu’un appel HTTP dispose d’assez de temps avant la reprise et la récupération du résultat.

Cet article décrit les fonctionnalités pour les déclencheurs de planification intégrées et les actions.

## <a name="schedule-triggers"></a>Déclencheurs de planification

Vous pouvez démarrer le workflow de votre application logique en utilisant le déclencheur de périodicité ou d’un déclencheur de fenêtre glissante, ce qui ne sont pas associés à n’importe quel service spécifique ou d’un système, par exemple, Office 365 Outlook ou SQL Server. Ces déclencheurs démarrent et exécuter votre flux de travail selon la périodicité spécifiée dans laquelle vous permet de sélectionner l’intervalle et la fréquence, tel que le nombre de secondes, minutes et les heures pour les déclencheurs à la fois, ou le nombre de jours, semaines ou mois pour le déclencheur de périodicité. Vous pouvez également définir la date de début et heure, ainsi que le fuseau horaire. Chaque fois qu’un déclencheur est activé, Logic Apps crée et exécute une nouvelle instance de flux de travail pour votre application logique.

Voici les différences entre ces déclencheurs :

* **Périodicité**: S’exécute votre flux de travail à intervalles réguliers selon votre planification spécifiée. Si les récurrences ne sont pas incluses, le déclencheur de périodicité ne traite pas les récurrences manquées mais redémarre les récurrences avec le prochain intervalle planifié. Vous pouvez spécifier une date de début et heure, ainsi que le fuseau horaire. Si vous sélectionnez « Jour », vous pouvez spécifier des heures de la journée et les minutes de l’heure, par exemple, tous les jours à 2 h 30. Si vous sélectionnez « Semaine », vous pouvez également sélectionner les jours de la semaine, telles que le mercredi et le samedi. Pour plus d’informations, consultez [créer, planification et exécution des tâches récurrentes et des flux de travail avec le déclencheur de périodicité](../connectors/connectors-native-recurrence.md).

* **Fenêtre glissante**: S’exécute votre flux de travail à intervalles réguliers qui gèrent des données dans des segments continues. Si les récurrences ne sont pas incluses, le déclencheur de fenêtre glissante revient et traite les récurrences manquées. Vous pouvez spécifier une date de début et heure, fuseau horaire et une durée pour différer chaque périodicité dans votre flux de travail. Ce déclencheur n’a pas les options pour spécifier les jours, semaines, mois et heures de la journée, les minutes de l’heure et les jours de la semaine. Pour plus d’informations, consultez [créer, planification et exécution des tâches récurrentes et des flux de travail avec le déclencheur de fenêtre glissante](../connectors/connectors-native-sliding-window.md).

## <a name="schedule-actions"></a>Planification des actions

Après une action dans votre workflow d’application logique, vous pouvez utiliser les actions retarder et délai jusqu'à ce que pour rendre votre workflow à attendre avant l’exécution de l’action suivante.

* **délai**: Attendez pour exécuter l’action suivante pour le nombre spécifié d’unités de temps, telles que les secondes, minutes, heures, jours, semaines ou mois. Pour plus d’informations, consultez [retarder l’action suivante dans le flux de travail](../connectors/connectors-native-delay.md).

* **Différer jusqu'à**: Attendez pour exécuter l’action suivante jusqu'à ce que la date et heure spécifiées. Pour plus d’informations, consultez [retarder l’action suivante dans le flux de travail](../connectors/connectors-native-delay.md).

## <a name="patterns-for-start-date-and-time"></a>Date et heure de début de modèles pour

<a name="start-time"></a>

Voici quelques modèles qui montrent comment vous pouvez contrôler la périodicité avec la date de début et l’heure, et comment les services de Logic Apps exécute ces périodicités :

| Heure de début | Périodicité sans planification | Périodicité avec planification (déclencheur de périodicité uniquement) |
|------------|-----------------------------|----------------------------------------------------|
| {aucune} | Exécute la première charge de travail instantanément. <p>Exécute les charges de travail suivantes en fonction de la dernière heure d’exécution. | Exécute la première charge de travail instantanément. <p>Exécute les charges de travail suivantes en fonction de la planification spécifiée. |
| Heure de début dans le passé | **Périodicité** déclencheur : Calcule le temps d’exécution en fonction de l’heure de début spécifiée et ignore les heures d’exécution passées. Exécute la première charge de travail à la prochaine heure d’exécution. <p>Exécute les charges de travail suivantes selon des calculs basés sur la dernière heure d’exécution. <p><p>**Fenêtre glissante** déclencheur : Calcule les temps d’exécution en fonction de l’heure de début spécifiée et les heures de honore passées exécuter. <p>Exécute les charges de travail basés sur des calculs à partir de l’heure de début spécifiée. <p><p>Pour une explication plus détaillée, consultez l’exemple fourni après ce tableau. | Exécute la première charge de travail *exactement* à l’heure de début, en fonction de la planification calculée à partir de l’heure de début. <p>Exécute les charges de travail suivantes en fonction de la planification spécifiée. <p>**Remarque :** Si vous spécifiez une récurrence avec une planification, mais sans spécifier d’heures ni de minutes, les exécutions suivantes sont calculées avec les heures ou les minutes (respectivement) depuis le moment de la première d’exécution. |
| Heure de début actuelle ou future | Exécute la première charge de travail à l’heure de début spécifiée. <p>Exécute les charges de travail suivantes selon des calculs basés sur la dernière heure d’exécution. | Exécute la première charge de travail *exactement* à l’heure de début, en fonction de la planification calculée à partir de l’heure de début. <p>Exécute les charges de travail suivantes en fonction de la planification spécifiée. <p>**Remarque :** Si vous spécifiez une récurrence avec une planification, mais sans spécifier d’heures ni de minutes, les exécutions suivantes sont calculées avec les heures ou les minutes (respectivement) depuis le moment de la première d’exécution. |
||||

*Exemple de la dernière heure de début et de périodicité, mais sans planification*

Partez du principe que la date et heure actuelles du 8 septembre 2017 à 13 h 00. Vous spécifiez la date de début et l’heure en tant que le 7 septembre 2017 à 14:00 h, qui se trouve dans le passé et une périodicité qui s’exécute tous les 2 jours.

| Heure de début | Heure actuelle | Périodicité | Planification |
|------------|--------------|------------|----------|
| 2017-09-**07**T14:00:00Z <br>(2017-09 -**07** à 14:00) | 2017-09-**08**T13:00:00Z <br>(2017-09 -**08** à 13:00) | Tous les 2 jours | {aucune} |
|||||

Pour le déclencheur de périodicité, le moteur Logic Apps calcule les exécutions en fonction de l’heure de début, ignore au-delà de temps d’exécution, utilise la prochaine heure de début pour la première exécution et calcule l’avenir s’exécute selon la dernière heure d’exécution.

Voici à quoi ressemble cette périodicité :

| Heure de début | Première heure d'exécution | Heures d’exécution suivantes |
|------------|----------------|------------------|
| **07**/09/2017 à 14h00 | **09**/09/2017 à 14h00 | **11**/09/2017 à 14h00 </br>**13**/09/2017 à 14h00 </br>**15**/09/2017 à 14h00 </br>Etc. |
||||

C’est le cas, non quel que soit l’amplitude dans le passé vous spécifiez l’heure de début, par exemple, 2017-09 -**05** à 14:00 ou 2017-09 -**01** à 2 h 00, votre première série toujours utilise la prochaine heure de début.

Pour le déclencheur de fenêtre glissante, le moteur Logic Apps calcule les exécutions en fonction de l’heure de début, respecte dernières exécutions, utilise l’heure de début pour la première exécution et calcule les exécutions futures en fonction de l’heure de début.

Voici à quoi ressemble cette périodicité :

| Heure de début | Première heure d'exécution | Heures d’exécution suivantes |
|------------|----------------|------------------|
| **07**/09/2017 à 14h00 | **07**/09/2017 à 14h00 | **09**/09/2017 à 14h00 </br>**11**/09/2017 à 14h00 </br>**13**/09/2017 à 14h00 </br>**15**/09/2017 à 14h00 </br>Etc. |
||||

C’est le cas, non quel que soit l’amplitude dans le passé vous spécifiez l’heure de début, par exemple, 2017-09 -**05** à 14:00 ou 2017-09 -**01** à 2 h 00, votre première série toujours utilise spécifié heure de début.

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Répétitions de l’exemple

Voici les récurrences exemple différents que vous pouvez configurer pour les déclencheurs qui prennent en charge les options :

| Déclencheur | Périodicité | Interval | Fréquence | Heure de début | Aux jours indiqués | Aux heures indiquées | Aux minutes indiquées | Remarque |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Périodicité, <br>Fenêtre glissante | Exécution toutes les 15 minutes (sans date ni heure de début) | 15 | Minute | {aucune} | {non disponible} | {aucune} | {aucune} | Cette planification démarre immédiatement, puis calcule les périodicités suivantes en fonction de la dernière heure d’exécution. |
| Périodicité, <br>Fenêtre glissante | Exécution toutes les 15 minutes (avec date et heure de début) | 15 | Minute | *startDate*T*startTime*Z | {non disponible} | {aucune} | {aucune} | Cette planification ne démarre *pas avant* la date et l’heure de début spécifiées. Les périodicités suivantes sont ensuite calculées en fonction de la dernière heure d’exécution. |
| Périodicité, <br>Fenêtre glissante | Exécution toutes les heures, à l’heure spécifiée (avec date et heure de début) | 1 | Heure | *startDate*Thh:00:00Z | {non disponible} | {aucune} | {aucune} | Cette planification ne démarre *pas avant* la date et l’heure de début spécifiées. Les périodicités suivantes exécution toutes les heures à la marque de minute « 00 », qui est calculée à partir de l’heure de début. <p>Si la fréquence est « Semaine » ou « Mois », cette planification s’exécute respectivement un seul jour par semaine ou un seul jour par mois. |
| Périodicité, <br>Fenêtre glissante | Exécution toutes les heures, tous les jours (sans date ni heure de début) | 1 | Heure | {aucune} | {non disponible} | {aucune} | {aucune} | Cette planification démarre immédiatement et calcule les périodicités suivantes en fonction de la dernière heure d’exécution. <p>Si la fréquence est « Semaine » ou « Mois », cette planification s’exécute respectivement un seul jour par semaine ou un seul jour par mois. |
| Périodicité, <br>Fenêtre glissante | Exécution toutes les heures, tous les jours (avec date et heure de début) | 1 | Heure | *startDate*T*startTime*Z | {non disponible} | {aucune} | {aucune} | Cette planification ne démarre *pas avant* la date et l’heure de début spécifiées. Les périodicités suivantes sont ensuite calculées en fonction de la dernière heure d’exécution. <p>Si la fréquence est « Semaine » ou « Mois », cette planification s’exécute respectivement un seul jour par semaine ou un seul jour par mois. |
| Périodicité, <br>Fenêtre glissante | Exécution 15 minutes après l’heure, toutes les heures (avec date et heure de début) | 1 | Heure | *startDate*T00:15:00Z | {non disponible} | {aucune} | {aucune} | Cette planification ne démarre *pas avant* la date et l’heure de début spécifiées. Les périodicités suivantes s’exécutent à la marque de minute « 15 », qui est calculée à partir du début de temps, donc à 00 h 15, 1 h 15, 2 h 15 et ainsi de suite. |
| Périodicité | Exécution 15 minutes après l’heure, toutes les heures (sans date ni heure de début) | 1 | jour | {aucune} | {non disponible} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Cette planification s’exécute à 00h15, 1h15, 2h15, etc. Cette planification est en outre l’équivalent d’une fréquence de type « Heure » et d’une heure de début avec « 15 » minutes. |
| Périodicité | Exécuter toutes les 15 minutes aux marques de minutes spécifiés (aucune date de début et heure). | 1 | jour | {aucune} | {non disponible} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Cette planification ne démarre pas avant la marque de 15 minutes spécifiée suivante. |
| Périodicité | Exécution à 8h00 tous les jours (sans date ni heure de début) | 1 | jour | {aucune} | {non disponible} | 8 | {aucune} | Cette planification s’exécute tous les jours à 8h00, selon la planification spécifiée. |
| Périodicité | Exécution à 8h00 tous les jours (avec date et heure de début) | 1 | jour | *startDate*T08:00:00Z | {non disponible} | {aucune} | {aucune} | Cette planification s’exécute à 8h00 tous les jours, selon l’heure de début spécifiée. | 
| Périodicité | Exécution à 8h30 tous les jours (sans date ni heure de début) | 1 | jour | {aucune} | {non disponible} | 8 | 30 | Cette planification s’exécute tous les jours à 8h30, selon la planification spécifiée. |
| Périodicité | Exécution à 8h30 tous les jours (avec date et heure de début) | 1 | jour | *startDate*T08:30:00Z | {non disponible} | {aucune} | {aucune} | Cette planification démarre à la date de début spécifiée, à 8h30. |
| Périodicité | Exécution à 8h30 et 16h30 tous les jours | 1 | jour | {aucune} | {non disponible} | 8, 16 | 30 | |
| Périodicité | Exécution à 8h30, 8h45, 16h30 et 16h45 tous les jours | 1 | jour | {aucune} | {non disponible} | 8, 16 | 30, 45 | |
| Périodicité | Exécution le samedi à 17h00 (sans date ni heure de début) | 1 | Semaine | {aucune} | Samedi | 17 | 00 | Cette planification s’exécute tous les samedis à 17h00. |
| Périodicité | Exécution le samedi à 17h00 (avec date et heure de début) | 1 | Semaine | *startDate*T17:00:00Z | Samedi | {aucune} | {aucune} | Cette planification ne démarre *pas avant* la date et l’heure de début spécifiées, à savoir le 9 septembre 2017 à 17h00 dans cet exemple. Les exécutions périodiques suivantes ont lieu tous les samedis à 17h00. |
| Périodicité | Exécution les mardi et jeudi à 17h00 | 1 | Semaine | {aucune} | Mardi, Jeudi | 17 | {aucune} | Cette planification s’exécute tous les mardis et jeudis à 17h00. |
| Périodicité | Exécution toutes les heures pendant les heures de travail | 1 | Semaine | {aucune} | Sélectionnez tous les jours, sauf le samedi et le dimanche. | Sélectionnez les heures de la journée souhaitées. | Sélectionnez les minutes de l’heure souhaitées. | Par exemple, si vos heures de travail sont de 8h00 à 17h00, sélectionnez « 8, 9, 10, 11, 12, 13, 14, 15, 16, 17 » comme heures de la journée. <p>Si vos heures de travail sont de 8h30 à 17h30, sélectionnez les heures précédentes de la journée, plus « 30 » minutes de l’heure. |
| Périodicité | Exécution une fois par jour le week-end | 1 | Semaine | {aucune} | Samedi, Dimanche | Sélectionnez les heures de la journée souhaitées. | Sélectionnez les minutes de l’heure souhaitées. | Cette planification s’exécute tous les samedis et dimanches aux heures spécifiées. |
| Périodicité | Exécution toutes les 15 minutes toutes les deux semaines le lundi uniquement | 2 | Semaine | {aucune} | Lundi | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Cette planification s’exécute un lundi sur deux à chaque marque de 15 minutes. |
| Périodicité | Exécuter chaque mois | 1 | Mois | *startDate*T*startTime*Z | {non disponible} | {non disponible} | {non disponible} | Cette planification ne démarre pas *pas avant* que la date et heure de début et calcule les périodicités suivantes sur la date de début et l’heure. Si vous ne spécifiez pas de date et heure de début, cette planification utilise la date et heure de création. |
| Périodicité | Exécution toutes les heures un jour par mois | 1 | Mois | {voir remarque} | {non disponible} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {voir remarque} | Si vous ne spécifiez pas de date et heure de début, cette planification utilise la date et heure de création. Pour contrôler les minutes pour la planification de la périodicité, spécifiez les minutes de l’heure, une heure de début ou utilisez l’heure de création. Par exemple, si l’heure de début ou l’heure de création est 8h25, cette planification s’exécute à 8h25, 9h25, 10h25, etc. |
|||||||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Exécuter une seule fois

Si vous souhaitez exécuter votre application logique uniquement à la fois à l’avenir, vous pouvez utiliser le **planificateur : Exécuter des tâches une fois**. Après avoir créé une application logique, mais avant d’ouvrir le Concepteur d’applications logiques, sous la **modèles** section, à partir de la **catégorie** liste, sélectionnez **planification**, puis sélectionnez Ce modèle :

![Sélectionner le modèle « Planificateur : Exécuter des tâches une fois »](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

Ou, si vous pouvez démarrer votre application logique avec le **quand une demande HTTP est reçue - demande** déclencher et passer l’heure de début en tant que paramètre pour le déclencheur. Pour la première action, utilisez la **différer jusqu'à - planifier** action et indiquez l’heure de lors de l’action suivante commence à s’exécuter.

## <a name="next-steps"></a>Étapes suivantes

* [Créer, planifier et exécuter des tâches récurrentes et les flux de travail avec le déclencheur de périodicité](../connectors/connectors-native-recurrence.md)
* [Créer, planifier et exécuter des tâches récurrentes et les flux de travail avec le déclencheur de fenêtre glissante](../connectors/connectors-native-sliding-window.md)
* [Flux de travail de pause avec actions retarder](../connectors/connectors-native-delay.md)