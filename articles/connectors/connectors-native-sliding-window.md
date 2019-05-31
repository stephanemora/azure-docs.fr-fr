---
title: Planifier des tâches récurrentes avec déclencheur de fenêtre glissante - Azure Logic Apps
description: Planifier et exécuter des tâches automatisées récurrentes et des flux de travail avec le déclencheur de fenêtre glissante dans Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 44944955019fcf81fb0d296592577e2b00a15928
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299501"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-sliding-window-trigger-in-azure-logic-apps"></a>Créer, planifier et exécuter des tâches récurrentes et les flux de travail avec le déclencheur de fenêtre glissante dans Azure Logic Apps

Pour exécuter régulièrement des tâches, des processus ou des tâches qui doivent gérer les données en segments continues, vous pouvez démarrer des flux de travail de votre application logique avec le **une fenêtre glissante - planification** déclencheur. Vous pouvez définir une date et heure ainsi un fuseau horaire pour démarrer le flux de travail et une périodicité pour la répétition de ce flux de travail. Si les récurrences manquent pour une raison quelconque, ce déclencheur traite ces périodicités manquées. Par exemple, lors de la synchronisation des données entre votre base de données et le stockage de sauvegarde, vous devez utiliser le déclencheur de fenêtre glissante afin que les données obtient synchronisées sans entraîner des lacunes. Pour plus d’informations sur les déclencheurs de planification intégrées et les actions, consultez [planification et exécution périodique automatisés, des tâches et des flux de travail avec Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Voici quelques modèles qui prend en charge de ce déclencheur :

* Exécuter immédiatement et répéter chaque *n* nombre de secondes, minutes ou heures.

* Démarrer à une date et heure spécifiques, exécuter immédiatement et répéter chaque *n* nombre de secondes, minutes ou heures. Avec ce déclencheur, vous pouvez spécifier une heure de début dans le passé, ce qui s’exécute tous les passés les récurrences.

* Retarder chaque périodicité pour une durée spécifique avant de s’exécuter.

Pour connaître les différences entre ce déclencheur et le déclencheur de périodicité ou pour plus d’informations sur la planification périodique du flux de travail, consultez [planification et exécution périodique automatisée des tâches, des processus et des flux de travail avec Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Si vous souhaitez déclencher votre application logique et l’exécuter qu’une seule fois dans le futur, consultez [exécuter des travaux une seule fois](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Conditions préalables

* Un abonnement Azure. Si vous n’êtes pas abonné, vous pouvez [s’inscrire à un compte Azure gratuit](https://azure.microsoft.com/free/).

* Connaissances de base sur [applications logiques](../logic-apps/logic-apps-overview.md). Si vous débutez avec logic apps, Découvrez [comment créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-sliding-window-trigger"></a>Ajouter un déclencheur de fenêtre glissante

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Créez une application logique vide.

1. Une fois que le Concepteur d’application logique s’affiche, dans la zone de recherche, entrez « fenêtre glissante » comme filtre. Dans la liste des déclencheurs, sélectionnez ce déclencheur en tant que la première étape dans votre workflow d’application logique : **Fenêtre glissante**

   ![Sélectionnez le déclencheur « Fenêtre glissante »](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. Définissez l’intervalle et la fréquence de la périodicité. Dans cet exemple, définissez ces propriétés de sorte que votre flux de travail soit exécuté chaque semaine.

   ![Définir l’intervalle et la fréquence](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | Propriété | Obligatoire | Nom JSON | type | Description |
   |----------|----------|-----------|------|-------------|
   | **Intervalle** | Oui | interval | Entier  | Nombre entier positif qui décrit la fréquence à laquelle le flux de travail s’exécute en fonction de la fréquence. Les intervalles minimaux et maximaux sont les suivants : <p>- Heure : 1-12 000 heures </br>- Minute : 1-72 000 minutes </br>- Seconde : 1-9 999 999 secondes<p>Par exemple, si l’intervalle est 6, et la fréquence est « Hour », la périodicité est alors toutes les 6 heures. |
   | **Fréquence** | Oui | frequency | Chaîne | Unité de temps à utiliser pour la récurrence : **Deuxième**, **Minute**, ou **heure** |
   ||||||

   ![Options de périodicité avancée](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   Pour plus d’options périodicité, ouvrez le **ajouter un nouveau paramètre** liste. 
   Toutes les options que vous sélectionnez s’affichent sur le déclencheur après la sélection.

   | Propriété | Obligatoire | Nom JSON | type | Description |
   |----------|----------|-----------|------|-------------|
   | **Délai** | Non | delay | String | La durée de retarder chaque périodicité à l’aide de la [spécification date/heure ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
   | **Fuseau horaire** | Non  | timeZone | String | S’applique uniquement quand vous spécifiez une heure de début, car ce déclencheur n’accepte pas le [décalage UTC](https://en.wikipedia.org/wiki/UTC_offset). Sélectionnez le fuseau horaire à appliquer. |
   | **Heure de début** | Non  | startTime | Chaîne | Fournir une date de début et une heure au format suivant : <p>AAAA-MM-JJThh:mm:ss si vous sélectionnez un fuseau horaire <p>-ou- <p>AAAA-MM-JJThh:mm:ssZ si vous ne sélectionnez pas de fuseau horaire <p>Par exemple, si vous choisissez le 18 septembre 2017 à 14:00, puis spécifiez « 2017-09-18T14:00:00 » et sélectionnez un fuseau horaire comme heure du Pacifique. Vous pouvez également spécifier « 2017-09-18T14:00:00Z » sans fuseau horaire. <p>**Remarque :** Cette heure de début doit être conforme à la [spécification de date/heure ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) au [format de date/heure UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), mais sans [décalage UTC](https://en.wikipedia.org/wiki/UTC_offset). Si vous ne sélectionnez pas de fuseau horaire, vous devez ajouter la lettre « Z » à la fin sans espace. Ce « Z » fait référence au [temps nautique](https://en.wikipedia.org/wiki/Nautical_time) équivalent. <p>Pour les planifications simples, l’heure de début est la première occurrence, tandis que pour les récurrences avancées, le déclencheur ne s’active pas avant que l’heure de début. [*Comment puis-je utiliser la date et l’heure de début ?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. Créez maintenant votre flux de travail restant avec d’autres actions. Pour plus d’actions que vous pouvez ajouter, consultez [connecteurs pour Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---sliding-window"></a>Définition de flux de travail - fenêtre glissante

Dans la définition de flux de travail sous-jacente de votre application logique, qui utilise JSON, vous pouvez afficher la définition du déclencheur de fenêtre glissante avec les options que vous avez choisi. Pour afficher cette définition, dans la barre d’outils Concepteur, choisissez **mode Code**. Pour revenir au concepteur, choisissez la barre d’outils Concepteur, **concepteur**.

Cet exemple montre comment une définition de déclencheur de fenêtre glissante dans une définition de flux de travail sous-jacent où le délai pour chaque périodicité est cinq secondes pour une périodicité de toutes les heures :

``` json
"triggers": {
   "Recurrence": {
      "type": "SlidingWindow",
      "Sliding_Window": {
         "inputs": {
            "delay": "PT5S"
         },
         "recurrence": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2019-05-13T14:00:00Z",
            "timeZone": "Pacific Standard Time"
         }
      }
   }
}
```

## <a name="next-steps"></a>Étapes suivantes

* [Retarder l’action suivante dans le flux de travail](../connectors/connectors-native-delay.md)
* [Connecteurs pour Logic Apps](../connectors/apis-list.md)