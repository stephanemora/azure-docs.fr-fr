---
title: Retarder l’action suivante dans les workflows - Azure Logic Apps
description: Attendez pour exécuter l’action suivante dans des workflows d’application logique en utilisant les délai ou retard jusqu'à ce que des actions dans Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
tags: connectors
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 27475fb3f086dbc5166a473e9d657d2dab723938
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297597"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>Délai d’exécution de l’action suivante dans Azure Logic Apps

Pour que votre application logique à attendre pendant un laps de temps avant d’exécuter l’action suivante, vous pouvez ajouter intégrés **retarder - planifier** action avant une action dans le flux de travail de votre application logique. Ou, vous pouvez ajouter intégrés **différer jusqu'à - planifier** action d’attendre jusqu'à une date et heure spécifiques avant d’exécuter l’action suivante. Pour plus d’informations sur les actions intégrées de planification et les déclencheurs, consultez [planification et exécution périodique automatisés, des tâches et des flux de travail avec Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

* **délai**: Attendez que le nombre spécifié d’unités de temps, telles que les secondes, minutes, heures, jours, semaines ou mois, avant l’exécution de l’action suivante.

* **Différer jusqu'à**: Patientez jusqu'à la date et heure spécifiées avant l’exécution de l’action suivante.

Voici quelques façons de l’exemple montre comment utiliser ces actions :

* Attendre un jour de semaine pour envoyer une mise à jour d’état par e-mail.

* Retarder votre flux de travail jusqu'à ce qu’un appel HTTP se termine avant la reprise et la récupération des données.

## <a name="prerequisites"></a>Conditions préalables

* Un abonnement Azure. Si vous n’êtes pas abonné, vous pouvez [s’inscrire à un compte Azure gratuit](https://azure.microsoft.com/free/).

* Connaissances de base sur [applications logiques](../logic-apps/logic-apps-overview.md). Avant de pouvoir utiliser une action, votre application logique doit tout d’abord commencer par un déclencheur. Vous pouvez utiliser n’importe quel déclencheur de votre choix et ajoutez d’autres actions avant d’ajouter une action de délai. Cette rubrique utilise un déclencheur d’Office 365 Outlook. Si vous débutez avec logic apps, Découvrez [comment créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>Ajoutez l’action de délai

1. Dans le Concepteur d’application logique, sous l’étape où vous souhaitez ajouter l’action de délai, choisissez **nouvelle étape**.

   Pour ajouter l’action de délai entre les étapes, placez le pointeur sur la flèche qui connecte les étapes. Choisissez le signe plus (+) qui s’affiche, puis **ajouter une action**.

1. Dans la zone de recherche, entrez « delay » comme filtre. Dans la liste des actions, sélectionnez cette action : **Délai**

   ![Ajouter une action de « Delay »](./media/connectors-native-delay/add-delay-action.png)

1. Spécifiez la quantité de temps d’attente avant l’exécution de l’action suivante.

   ![Définissez la durée du délai](./media/connectors-native-delay/delay-time-intervals.png)

   | Propriété | Nom JSON | Obligatoire | Type | Description |
   |----------|-----------|----------|------|-------------|
   | Nombre | count | Oui | Entier  | Le nombre d’unités à retarder |
   | Unité | unité | Oui | String | L’unité de temps, par exemple : `Second`, `Minute`, `Hour`, `Day`, `Week`, ou `Month` |
   ||||||

1. Ajouter d’autres actions que vous souhaitez exécuter dans votre flux de travail.

1. Lorsque vous avez terminé, enregistrez votre application logique.

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>Ajouter le délai-action until

1. Dans le Concepteur d’application logique, sous l’étape où vous souhaitez ajouter l’action de délai, choisissez **nouvelle étape**.

   Pour ajouter l’action de délai entre les étapes, placez le pointeur sur la flèche qui connecte les étapes. Choisissez le signe plus (+) qui s’affiche, puis **ajouter une action**.

1. Dans la zone de recherche, entrez « delay » comme filtre. Dans la liste des actions, sélectionnez cette action : **Différer jusqu'à**

   ![Ajouter l’action « Différer jusqu’à »](./media/connectors-native-delay/add-delay-until-action.png)

1. Indiquez la date de fin et l’heure lorsque vous voulez reprendre le workflow.

   ![Spécifier l’horodatage pour le moment auquel le délai de fin](./media/connectors-native-delay/delay-until-timestamp.png)

   | Propriété | Nom JSON | Obligatoire | Type | Description |
   |----------|-----------|----------|------|-------------|
   | Timestamp | timestamp | Oui | Chaîne | La date de fin et l’heure pour reprendre le workflow à l’aide de ce format : <p>AAAA-MM-ddTHH <p>Par exemple, si vous choisissez le 18 septembre 2017 à 14:00, spécifiez « 2017-09-18T14:00:00Z ». <p>**Remarque :** Ce format d’heure doit respecter le [spécification date/heure ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) dans [format date/heure UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), mais sans un [décalage UTC](https://en.wikipedia.org/wiki/UTC_offset). Sans un fuseau horaire, vous devez ajouter la lettre « Z » à la fin sans espace. Ce « Z » fait référence au [temps nautique](https://en.wikipedia.org/wiki/Nautical_time) équivalent. |
   ||||||

1. Ajouter d’autres actions que vous souhaitez exécuter dans votre flux de travail.

1. Lorsque vous avez terminé, enregistrez votre application logique.

## <a name="next-steps"></a>Étapes suivantes

* [Créer, planifier et exécuter des tâches récurrentes et les flux de travail avec le déclencheur de périodicité](../connectors/connectors-native-recurrence.md)
* [Connecteurs pour Logic Apps](../connectors/apis-list.md)