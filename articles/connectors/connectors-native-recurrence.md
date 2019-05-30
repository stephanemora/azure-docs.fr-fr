---
title: Planifier des tâches récurrentes avec déclencheur de périodicité - Azure Logic Apps
description: Planifier et exécuter des tâches automatisées récurrentes et des flux de travail avec le déclencheur de périodicité dans Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: f5fc778ee4d8f91232bc732cc276f642f748b29d
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297531"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Créer, planifier et exécuter des tâches récurrentes et les flux de travail avec le déclencheur de périodicité dans Azure Logic Apps

Pour exécuter régulièrement des tâches, des processus ou des travaux selon une planification spécifique, vous pouvez démarrer le workflow de votre application logique avec intégrés **périodicité - planification** déclencheur. Vous pouvez définir une date et heure ainsi un fuseau horaire pour démarrer le flux de travail et une périodicité pour la répétition de ce flux de travail. Si les récurrences manquent pour une raison quelconque, ce déclencheur continue périodique lors du prochain intervalle planifié. Pour plus d’informations sur les déclencheurs de planification intégrées et les actions, consultez [planification et exécution périodique automatisés, des tâches et des flux de travail avec Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Voici quelques modèles qui ce déclencheur prend en charge, ainsi que les récurrences plus avancées et des planifications complexes :

* Exécuter immédiatement et répéter chaque *n* nombre de secondes, minutes, heures, jours, semaines ou mois.

* Démarrer à une date et heure spécifiques, exécuter immédiatement et répéter chaque *n* nombre de secondes, minutes, heures, jours, semaines ou mois.

* Exécuter et répéter une ou plusieurs fois par jour, par exemple à 8h00 et 17h00.

* Exécuter et répéter chaque semaine, mais uniquement certains jours, par exemple le samedi et le dimanche.

* Exécuter et répéter chaque semaine, mais uniquement certains jours et à des heures précises, par exemple du lundi au vendredi à 8h00 et 17h00.

Pour connaître les différences entre ce déclencheur et le déclencheur de fenêtre glissante, ou pour plus d’informations sur la planification périodique du flux de travail, consultez [planification et exécution périodique automatisée des tâches, des processus et des flux de travail avec Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Si vous souhaitez déclencher votre application logique et l’exécuter qu’une seule fois dans le futur, consultez [exécuter des travaux une seule fois](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Conditions préalables

* Un abonnement Azure. Si vous n’êtes pas abonné, vous pouvez [s’inscrire à un compte Azure gratuit](https://azure.microsoft.com/free/).

* Connaissances de base sur [applications logiques](../logic-apps/logic-apps-overview.md). Si vous débutez avec logic apps, Découvrez [comment créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-recurrence-trigger"></a>Ajouter un déclencheur de périodicité

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Créez une application logique vide.

1. Une fois que le Concepteur d’application logique s’affiche, dans la zone de recherche, entrez « périodicité » comme filtre. Dans la liste des déclencheurs, sélectionnez ce déclencheur en tant que la première étape dans votre workflow d’application logique : **Périodicité**

   ![Sélectionnez le déclencheur « Récurrence »](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Définissez l’intervalle et la fréquence de la périodicité. Dans cet exemple, définissez ces propriétés de sorte que votre flux de travail soit exécuté chaque semaine.

   ![Définir l’intervalle et la fréquence](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Propriété | Obligatoire | Nom JSON | type | Description |
   |----------|----------|-----------|------|-------------|
   | **Intervalle** | Oui | interval | Entier  | Nombre entier positif qui décrit la fréquence à laquelle le flux de travail s’exécute en fonction de la fréquence. Les intervalles minimaux et maximaux sont les suivants : <p>- Mois : 1-16 mois </br>Jour : 1-500 jours </br>- Heure : 1-12 000 heures </br>- Minute : 1-72 000 minutes </br>- Seconde : 1-9 999 999 secondes<p>Par exemple, si l’intervalle est de 6 et que la fréquence soit définie sur « Mois », la périodicité est alors tous les 6 mois. |
   | **Fréquence** | Oui | frequency | String | Unité de temps à utiliser pour la récurrence : **Seconde**, **Minute**, **Heure**, **Jour**, **Semaine** ou **Mois** |
   ||||||

   Pour plus d’options planifications, ouvrez le **ajouter un nouveau paramètre** liste. 
   Toutes les options que vous sélectionnez s’affichent sur le déclencheur après la sélection.

   ![Options avancées de planification](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Propriété | Obligatoire | Nom JSON | type | Description |
   |----------|----------|-----------|------|-------------|
   | **Fuseau horaire** | Non | timeZone | String | S’applique uniquement quand vous spécifiez une heure de début, car ce déclencheur n’accepte pas le [décalage UTC](https://en.wikipedia.org/wiki/UTC_offset). Sélectionnez le fuseau horaire à appliquer. |
   | **Heure de début** | Non  | startTime | String | Fournir une date de début et une heure au format suivant : <p>AAAA-MM-JJThh:mm:ss si vous sélectionnez un fuseau horaire <p>-ou- <p>AAAA-MM-JJThh:mm:ssZ si vous ne sélectionnez pas de fuseau horaire <p>Par exemple, si vous choisissez le 18 septembre 2017 à 14:00, puis spécifiez « 2017-09-18T14:00:00 » et sélectionnez un fuseau horaire comme heure du Pacifique. Vous pouvez également spécifier « 2017-09-18T14:00:00Z » sans fuseau horaire. <p>**Remarque :** Cette heure de début doit être conforme à la [spécification de date/heure ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) au [format de date/heure UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), mais sans [décalage UTC](https://en.wikipedia.org/wiki/UTC_offset). Si vous ne sélectionnez pas de fuseau horaire, vous devez ajouter la lettre « Z » à la fin sans espace. Ce « Z » fait référence à l’équivalent en [temps nautique](https://en.wikipedia.org/wiki/Nautical_time). <p>Pour les planifications simples, l’heure de début est la première occurrence, tandis que pour les planifications complexes, le déclencheur ne s’active pas avant l’heure de début. [*Comment puis-je utiliser la date et l’heure de début ?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **Aux jours indiqués** | Non  | weekDays | Chaîne ou tableau de chaînes | Si vous sélectionnez « Semaine », vous pouvez sélectionner un ou plusieurs jours où vous voulez exécuter le workflow : **Lundi**, **Mardi**, **Mercredi**, **Jeudi**, **Vendredi**, **Samedi** et **Dimanche** |
   | **Aux heures indiquées** | Non  | hours | Entier ou tableau d’entiers | Si vous sélectionnez « Jour » ou « Semaine », vous pouvez sélectionner un ou plusieurs entiers compris entre 0 et 23 les heures de la journée pour lorsque vous souhaitez exécuter le workflow. <p><p>Par exemple, si vous spécifiez « 10 », « 12 » et « 14 », vous obtenez 10 h, 12 h et 14 h 00 pendant les heures de la journée, mais les minutes de la journée sont calculées en fonction de démarrage de la périodicité. Pour définir les minutes de la journée, spécifiez la valeur de la **aux minutes indiquées** propriété. |
   | **Aux minutes indiquées** | Non  | minutes | Entier ou tableau d’entiers | Si vous sélectionnez « Jour » ou « Semaine », vous pouvez sélectionner un ou plusieurs entiers compris entre 0 et 59 pour les minutes de l’heure durant lesquelles exécuter le flux de travail. <p>Par exemple, vous pouvez spécifier « 30 » pour les minutes et à l’aide de l’exemple précédent des heures de la journée, vous obtenez 10h30, 12h30 et 14h30. |
   |||||

   Par exemple, supposons que nous sommes le lundi 4 septembre 2017 aujourd’hui. Le déclencheur de périodicité suivant ne déclenche pas *pas avant* à la date de début et l’heure, qui est le lundi 18 septembre 2017 à 8:00 PST. Notez toutefois que la périodicité planifiée est définie sur 10h30, 12h30 et 14h30 uniquement le lundi. Par conséquent, le déclencheur s’activera et créera une instance de flux de travail d’application logique pour la première fois à 10h30. Pour plus d’informations sur le fonctionnement des heures de début, consultez ces [exemples d’heure de début](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time).

   Les exécutions suivantes auront lieu le même jour à 12h30 et 14h30. Chaque périodicité crée sa propre instance de flux de travail. L’ensemble de la planification se répète ensuite régulièrement chaque lundi. [*Existe-t-il d’autres exemples de périodicité ?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Exemple de planification avancée](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > Le déclencheur affiche un aperçu de la périodicité que vous avez spécifiée uniquement quand vous sélectionnez « Jour » ou « Semaine » comme fréquence.

1. Créez maintenant votre flux de travail restant avec d’autres actions. Pour plus d’actions que vous pouvez ajouter, consultez [connecteurs pour Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---recurrence"></a>Définition de flux de travail - périodicité

Dans la définition de flux de travail sous-jacente de votre application logique, qui utilise JSON, vous pouvez afficher le [définition de déclencheur de périodicité](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) avec les options que vous avez choisi. Pour afficher cette définition, dans la barre d’outils Concepteur, choisissez **mode Code**. Pour revenir au concepteur, choisissez la barre d’outils Concepteur, **concepteur**.

Cet exemple montre comment se présenteraient une définition de déclencheur de périodicité dans une définition de flux de travail sous-jacent :

``` json
"triggers": {
   "Recurrence": {
      "type": "Recurrence",
      "recurrence": {
         "frequency": "Week",
         "interval": 1,
         "schedule": {
            "hours": [
               10,
               12,
               14
            ],
            "minutes": [
               30
            ],
            "weekDays": [
               "Monday"
            ]
         },
         "startTime": "2017-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

## <a name="next-steps"></a>Étapes suivantes

* [Flux de travail de pause avec actions retarder](../connectors/connectors-native-delay.md)
* [Connecteurs pour Logic Apps](../connectors/apis-list.md)
