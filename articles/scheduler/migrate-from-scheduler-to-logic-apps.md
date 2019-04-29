---
title: Migrer d’Azure Scheduler vers Azure Logic Apps
description: Découvrez comment remplacer des travaux créés dans Azure Scheduler, qui est en phase de mise hors service, par des travaux dans Azure Logic Apps
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/20/2018
ms.openlocfilehash: 25ed66fd75301475542dbac8e8a01670ee37563c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531772"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Migrer des travaux Azure Scheduler vers Azure Logic Apps

> [!IMPORTANT]
> Azure Logic Apps remplace Azure Scheduler, qui est en phase de mise hors service. Si vous avez des travaux à planifier, créez-les plutôt dans Azure Logic Apps en suivant les instructions de cet article.

Cet article vous montre comment planifier des travaux ponctuels et des travaux récurrents en créant des workflows automatisés avec Azure Logic Apps au lieu d’Azure Scheduler. La création de travaux planifiés avec Logic Apps présente les avantages suivants :

* Vous n’avez pas à vous soucier du concept de *collection de travaux*, car chaque application logique est une ressource Azure distincte.

* Vous pouvez exécuter plusieurs travaux ponctuels à l’aide d’une seule application logique.

* Le service Azure Logic Apps prend en charge le fuseau horaire et l’heure d’été (DST).

Pour en savoir plus, consultez [Présentation d’Azure Logic Apps](../logic-apps/logic-apps-overview.md). ou essayez de créer votre première application logique dans ce démarrage rapide : [Créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Conditions préalables

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>.

* Pour déclencher votre application logique en envoyant des requêtes HTTP, utilisez un outil tel que [l’application de bureau Postman](https://www.getpostman.com/apps).

## <a name="schedule-one-time-jobs"></a>Planifier des travaux ponctuels

Vous pouvez exécuter plusieurs travaux ponctuels en créant simplement une application logique. 

### <a name="create-your-logic-app"></a>Créer votre application logique

1. Dans le [portail Azure](https://portal.azure.com), créez une application logique vide dans le concepteur d’application logique. 

   Pour les étapes de base, suivez [Guide de démarrage rapide : Créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Dans la zone de recherche, saisissez le filtre « when a http request ». Dans la liste des déclencheurs, sélectionnez ce déclencheur : **Lors de la réception d’une requête HTTP** 

   ![Ajouter le déclencheur Requête](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. Pour le déclencheur Requête, vous pouvez éventuellement fournir un schéma JSON, qui permet au concepteur d’application logique de mieux comprendre la structure des entrées de la demande reçue et, pour vous, facilite la sélection ultérieure des sorties dans votre workflow.

   Pour spécifier un schéma, entrez-le dans la zone **Schéma JSON de corps de la demande**, par exemple : 

   ![Schéma de requête](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   Si vous n’avez pas de schéma, mais un exemple de charge utile au format JSON, vous pouvez générer un schéma à partir de cette charge utile.

   1. Dans le déclencheur Requête, choisissez **Utiliser l’exemple de charge utile pour générer le schéma**.

   1. Sous **Entrer ou coller un exemple de charge utile JSON**, fournissez votre exemple de charge utile, puis choisissez **Terminé**. Par exemple :

      ![Exemple de charge utile](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

1. Sous le déclencheur, choisissez **Étape suivante**. 

1. Dans la zone de recherche, entrez « différer jusqu’à » comme filtre. Dans la liste des actions, sélectionnez cette action : **Différer jusqu'à**

   Cette action interrompt le workflow de votre application logique jusqu’à la date et l’heure spécifiées.

   ![Ajouter l’action « Différer jusqu’à »](./media/migrate-from-scheduler-to-logic-apps/delay-until.png)

1. Entrez l’horodatage pour définir le moment où démarrer le workflow de l’application logique. 

   Quand vous cliquez dans la zone **Horodatage**, une liste de contenu dynamique s’affiche, dans laquelle vous pouvez éventuellement sélectionner une sortie du déclencheur.

   ![Entrer les détails « Différer jusqu’à »](./media/migrate-from-scheduler-to-logic-apps/delay-until-details.png)

1. Ajoutez les autres actions que vous souhaitez exécuter parmi un choix de [plus de 200 connecteurs](../connectors/apis-list.md). 

   Par exemple, vous pouvez ajouter une action HTTP qui envoie une requête à une URL, ou des actions qui interagissent avec les files d’attente de stockage, les files d’attente Service Bus ou les rubriques Service Bus : 

   ![Action HTTP](./media/migrate-from-scheduler-to-logic-apps/request-http-action.png)

1. Lorsque vous avez terminé, enregistrez votre application logique.

   ![Enregistrer votre application logique](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

   Quand vous enregistrez votre application logique pour la première fois, l’URL de point de terminaison associée au déclencheur Requête de votre application logique s’affiche dans la zone **URL HTTP POST**. 
   Si vous souhaitez appeler votre application logique et lui envoyer les entrées à traiter, utilisez cette URL comme destination de l’appel.

   ![Enregistrer l’URL de point de terminaison du déclencheur Requête](./media/migrate-from-scheduler-to-logic-apps/request-endpoint-url.png)

1. Copiez et enregistrez cette URL de point de terminaison, car vous en aurez besoin plus loin pour envoyer une requête manuelle qui déclenche votre application logique. 

## <a name="start-a-one-time-job"></a>Démarrer un travail ponctuel

Pour exécuter ou déclencher un travail ponctuel manuellement, envoyez un appel à l’URL de point de terminaison associée au déclencheur Requête de votre application logique. Dans cet appel, spécifiez l’entrée ou la charge utile à envoyer, que vous avez peut-être déjà décrite précédemment quand vous avez spécifié un schéma. 

Par exemple, dans l’application Postman, vous pouvez créer une requête POST avec les paramètres similaires à cet exemple, puis choisir **Envoyer** pour effectuer la requête.

| Méthode de requête | URL | body | headers |
|----------------|-----|------|---------| 
| **POST** | <*endpoint-URL*> | **raw** <p>**JSON(application/json)** <p>Dans la zone **raw**, entrez la charge utile à envoyer dans la requête. <p>**Remarque**: Ce paramètre automatiquement configure le **en-têtes** valeurs. | **Clé** : Content-Type <br>**Valeur** : application/json
 |||| 

![Envoyer la requête pour déclencher manuellement votre application logique](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

Après l’envoi de l’appel, la réponse de votre application logique s’affiche dans la zone **raw** sous l’onglet **Body**. 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> Si vous souhaitez ultérieurement annuler le travail, choisissez l’onglet **Headers**. Recherchez et copiez la valeur de l’en-tête **x-ms-workflow-run-id** dans la réponse. 
>
> ![response](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>Annuler un travail ponctuel

Dans Logic Apps, chaque travail ponctuel s’exécute comme une seule instance d’exécution dans l’application logique. Pour annuler un travail ponctuel, vous pouvez utiliser l’opération [Annuler les exécutions de workflow](https://docs.microsoft.com/rest/api/logic/workflowruns/cancel) dans l’API REST Logic Apps. Quand vous envoyez un appel au déclencheur, entrez [l’ID d’exécution de workflow](#workflow-run-id).

## <a name="schedule-recurring-jobs"></a>Planifier des travaux récurrents

### <a name="create-your-logic-app"></a>Créer votre application logique

1. Dans le [portail Azure](https://portal.azure.com), créez une application logique vide dans le concepteur d’application logique. 

   Pour les étapes de base, suivez [Guide de démarrage rapide : Créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Dans la zone de recherche, entrez « récurrence » comme filtre. Dans la liste des déclencheurs, sélectionnez ce déclencheur : **Périodicité** 

   ![Déclencheur Récurrence](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. Configurez une planification plus avancée, si vous le souhaitez.

   ![Planification avancée](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   Pour plus d’informations sur les options de planification avancée, consultez [Créer et exécuter des travaux et des workflows récurrents avec Azure Logic Apps](../connectors/connectors-native-recurrence.md)

1. Ajoutez les autres actions souhaitées parmi un choix de [plus de 200 connecteurs](../connectors/apis-list.md). Sous le déclencheur, choisissez **Étape suivante**. Recherchez et sélectionnez les actions souhaitées.

   Par exemple, vous pouvez ajouter une action HTTP qui envoie une requête à une URL, ou des actions qui interagissent avec les files d’attente de stockage, les files d’attente Service Bus ou les rubriques Service Bus : 

   ![Action HTTP](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. Lorsque vous avez terminé, enregistrez votre application logique.

   ![Enregistrer votre application logique](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>Configuration avancée

Voici d’autres façons de personnaliser vos travaux.

### <a name="retry-policy"></a>Stratégie de nouvelle tentative

Pour déterminer la façon dont une action réessaie de s’exécuter dans votre application logique après des défaillances intermittentes, vous pouvez définir une [stratégie de nouvelles tentatives](../logic-apps/logic-apps-exception-handling.md#retry-policies) dans les paramètres de chaque action. Par exemple :

1. Ouvrez le menu (**...**) de l’action, puis sélectionnez **Paramètres**.

   ![Ouvrir les paramètres de l’action](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. Sélectionnez la stratégie de nouvelles tentatives souhaitée. Pour plus d’informations sur chaque stratégie, consultez [Stratégies de nouvelles tentatives](../logic-apps/logic-apps-exception-handling.md#retry-policies).

   ![Sélectionner une stratégie de nouvelles tentatives](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>Gérer les erreurs et les exceptions

Dans Azure Scheduler, si l’action par défaut échoue à s’exécuter, vous pouvez exécuter une autre action qui gère la condition d’erreur. C’est également possible dans Azure Logic Apps.

1. Dans le concepteur d’application logique, au-dessus de l’action à gérer, déplacez votre pointeur sur la flèche entre les étapes, puis sélectionnez **Ajouter une branche parallèle**. 

   ![Ajouter une branche parallèle](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. Recherchez et sélectionnez l’action à exécuter comme action de remplacement.

   ![Ajouter une action parallèle](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. Sur l’autre action, ouvrez le menu (**...**), puis sélectionnez **Configure run after** (Configurer l’exécution après).

   ![Configurer l’exécution après](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. Décochez la case de la propriété **is successful** (a réussi). Cochez les propriétés **has failed** (a échoué), **is skipped** (est ignorée) et **has timed out** (a expiré)

   ![Configurer les propriétés « exécuter après »](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. Quand vous avez fini, choisissez **Terminé**.

Pour en savoir plus sur la gestion des exceptions, consultez [Gérer les erreurs et exceptions - Propriété RunAfter](../logic-apps/logic-apps-exception-handling.md#catch-and-handle-failures-with-the-runafter-property).

## <a name="faq"></a>Forum Aux Questions

<a name="retire-date"></a> 

**Q** : Lorsque est Azure Scheduler mise hors service ? <br>
**R** : Azure Scheduler est planifiée pour mettre hors service sur le 30 septembre 2019.

**Q** : Que se passe-t-il pour Mes collections de travaux du planificateur et les tâches une fois que le service retire ? <br>
**R** : Toutes les tâches et les collections de travaux du planificateur sont supprimés à partir du système.

**Q** : Je dois sauvegarder ou effectuer d’autres tâches avant la migration de Mes travaux du planificateur vers Logic Apps ? <br>
**R** : Comme meilleure pratique, sauvegardez toujours votre travail. Vérifiez que vos nouvelles applications logiques s’exécutent comme prévu avant de supprimer ou désactiver vos travaux Scheduler. 

**Q** : Existe-t-il un outil qui peut aider à migrer mes travaux à partir du planificateur vers Logic Apps ? <br>
**R** : Chaque tâche du planificateur étant unique, un outil universel n’existe pas. Toutefois, nous fournirons différents scripts que vous pourrez modifier selon vos besoins. Revenez vérifier ultérieurement si des scripts sont disponibles.

**Q** : Où puis-je obtenir la prise en charge pour la migration de Mes travaux du planificateur ? <br>
**R** : Voici quelques méthodes permettant d’obtenir un support technique : 

**Portail Azure**

Si votre abonnement Azure s’accompagne d’un plan de support payant, vous pouvez créer une demande de support technique dans le portail Azure. Sinon, choisissez une autre option de support.

1. Dans le menu principal du [portail Azure](https://portal.azure.com), sélectionnez **Aide + support**.

1. Sous **Support**, sélectionnez **Nouvelle demande de support**. Entrez ces informations pour votre demande :

   | Paramètre | Valeur |
   |---------|-------|
   | **Type de problème** | **Technique** | 
   | **Abonnement** | <*votre-abonnement-Azure*> | 
   | **Service** | Sous **Supervision et gestion**, sélectionnez **Scheduler**. | 
   ||| 

1. Sélectionnez l’option de support souhaitée. Si vous avez un plan de support payant, choisissez **Suivant**.

**Communauté**

* [Forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>Étapes suivantes

* [Créer et exécuter des travaux et des workflows récurrents avec Azure Logic Apps](../connectors/connectors-native-recurrence.md)
* [Tutoriel : Vérifier le trafic avec une application logique basée sur la planification](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)