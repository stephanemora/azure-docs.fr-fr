---
title: Scénario de budget de facturation et de gestion des coûts Azure | Microsoft Docs
description: Découvrez comment utiliser Azure automation pour arrêter les machines virtuelles en fonction de seuils de budget spécifiques.
author: bandersmsft
ms.reviewer: adwise
tags: billing
ms.service: cost-management-billing
ms.topic: reference
ms.tgt_pltfrm: na
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: ae17ecc72bb1e6af1b79d4a2952c2f78dce4b5bd
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200980"
---
# <a name="manage-costs-with-azure-budgets"></a>Gérer les coûts avec Azure Budgets

La maîtrise des coûts représente un composant essentiel dans l’optimisation de la valeur de votre investissement dans le cloud. Il existe plusieurs scénarios où la visibilité des coûts, le reporting et l’orchestration basée sur les coûts sont cruciaux pour la continuité de l’activité commerciale. Les [API de gestion des coûts Azure](https://docs.microsoft.com/rest/api/consumption/) proposent un ensemble d’API permettant de prendre en charge chacun de ces scénarios. Ces API fournissent des détails précis sur l’utilisation, ce qui vous permet de voir les coûts affinés au niveau des instances.

Les budgets font souvent partie intégrante du contrôle des coûts. Les budgets peuvent être délimités dans Azure. Par exemple, vous pouvez réduire votre prévision budgétaire en fonction de l’abonnement, des groupes de ressources ou d’une collection de ressources. En plus de l’utilisation des API de budgets qui vous informent par e-mail qu’un seuil de budget est atteint, vous pouvez utiliser des [groupes d’actions Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) pour déclencher un ensemble d’actions orchestré suite à un événement de budget.

Un client exécutant une charge de travail non critique peut recourir à un scénario de budget courant quand il souhaite gérer son utilisation en fonction d’un budget et obtenir un coût prévisible sur sa facture mensuelle. Ce scénario nécessite une orchestration qui s’appuie sur les coûts des ressources faisant partie de l’environnement Azure. Dans ce scénario, un budget mensuel de 1 000 dollars US est défini pour l’abonnement. Des seuils de notification sont également définis pour le déclenchement de certaines orchestrations. Ce scénario débute avec un seuil à 80 % du coût, qui interrompra toutes les machines virtuelles dans le groupe de ressources **Facultatif**. Puis, au seuil de 100 % du coût, toutes les instances de machines virtuelles seront arrêtées.
Pour configurer ce scénario, effectuez les actions décrites dans les étapes suivantes qui sont fournies à chaque section de ce tutoriel.

Les actions prévues dans ce tutoriel vous permettent de :

- Créer un runbook Azure Automation pour arrêter les machines virtuelles à l’aide de webhooks.
- Créer une application logique Azure devant se déclencher en fonction des seuils de budget, et appeler le runbook avec les paramètres appropriés.
- Créer un groupe d’actions Azure Monitor qui sera configuré pour déclencher l’application logique Azure lorsque le seuil budgétaire sera atteint.
- Créer le budget Azure, avec les seuils désirés, et l’associer au groupe d’actions.

## <a name="create-an-azure-automation-runbook"></a>Créer un runbook Azure Automation

[Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) est un service qui vous permet de mettre en script la plupart de vos tâches de gestion des ressources, et d’exécuter ces tâches en mode planifié ou à la demande. Dans le cadre de ce scénario, vous créez un [runbook Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-types) qui sert à arrêter les machines virtuelles. Vous utilisez le runbook graphique [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) à partir de la [galerie](https://docs.microsoft.com/azure/automation/automation-runbook-gallery) pour générer ce scénario. En important ce runbook dans votre compte Azure, et en le publiant, vous êtes en mesure d’arrêter les machines virtuelles lorsqu’un seuil de budget est atteint.

### <a name="create-an-azure-automation-account"></a>Créer un compte Azure Automation

1. Connectez-vous au [portail Azure](https://portal.azure.com/) avec les informations d’identification de votre compte Azure.
2. Cliquez sur le bouton **Créer une ressource** situé dans le coin supérieur gauche d’Azure.
3. Sélectionnez **Outils de gestion** > **Automation**.
   > [!NOTE]
   > Si vous ne possédez pas de compte Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/).
4. Entrez les informations de votre compte. Pour **Créer un compte d’identification Azure**, choisissez **Oui**, vous activez alors automatiquement les paramètres nécessaires pour simplifier l’authentification à Azure.
5. Lorsque vous avez terminé, cliquez sur **Créer** pour démarrer le déploiement du compte Automation.

### <a name="import-the-stop-azure-v2-vms-runbook"></a>Importer le runbook Stop Azure V2 VMs

À l’aide d’un [runbook Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-types), importez le runbook graphique [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) à partir de la galerie.

1.  Connectez-vous au [portail Azure](https://portal.azure.com/) avec les informations d’identification de votre compte Azure.
2.  Ouvrez votre compte Automation en sélectionnant **Tous les services** > **Comptes Automation**. Ensuite, sélectionnez votre Compte Automation.
3.  Cliquez sur **Galerie de Runbooks** à partir de la section **Automatisation des processus**.
4.  Définissez la **Source de galerie** sur **Centre de scripts** et sélectionnez **OK**.
5.  Recherchez et sélectionnez l’élément de la galerie [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) dans le portail Azure.
6.  Cliquez sur le bouton **Importer** pour afficher le panneau **Importer** et sélectionnez **OK**. Le panneau de la vue d’ensemble du runbook s’affiche.
7.  À l’issue de l’opération d’importation du runbook, sélectionnez **Modifier** pour afficher l’éditeur du runbook graphique et l’option de publication.

    ![Azure - Modifier un runbook graphique](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-01.png)
8.  Cliquez sur le bouton **Publier** pour publier le runbook, puis sélectionnez **Oui** quand vous y êtes invité. Lorsque vous publiez un runbook, vous remplacez la version publiée existante par la version brouillon. Dans le cas présent, vous n’avez pas de version publiée puisque vous venez de créer le runbook.

    Pour plus d’informations sur la publication d’un runbook, consultez [Créer un runbook graphique](https://docs.microsoft.com/azure/automation/automation-first-runbook-graphical).

## <a name="create-webhooks-for-the-runbook"></a>Créer des webhooks pour le runbook

À l’aide du runbook graphique [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b), vous créez deux webhooks pour démarrer le runbook dans Azure Automation au moyen d’une requête HTTP unique. Avec comme paramètre le nom du groupe de ressources, le premier webhook appellera le runbook à un seuil de budget de 80 %, autorisant ainsi l’arrêt des machines virtuelles facultatives. Ensuite, le deuxième webhook appellera le runbook sans paramètre (à 100 %), ce qui interrompra toutes les autres instances de machine virtuelle.

1. Dans la page **Runbooks** du [portail Azure](https://portal.azure.com/), cliquez sur le runbook **StopAzureV2Vm** qui affiche le panneau de la vue d’ensemble du runbook.
2. Cliquez sur **Webhook** en haut de la page pour ouvrir le panneau **Ajouter un Webhook**.
3. Cliquez sur **Créer un Webhook** pour ouvrir le panneau **Créer un webhook**.
4. Définissez le **Nom** du Webhook sur **Facultatif**. La propriété **Activé** doit être **Oui**. La valeur **Expire** ne doit pas être modifiée. Pour plus d’informations sur les propriétés de Webhook, consultez [Détails d’un webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#details-of-a-webhook).
5. En regard de la valeur URL, cliquez sur l’icône de copie pour copier l’URL du webhook.
   > [!IMPORTANT]
   > Enregistrez dans un endroit sûr l’URL du webhook nommé **Facultatif**. Vous allez utiliser cette URL plus loin dans ce tutoriel. Pour des raisons de sécurité, après la création du webhook, il ne vous est plus possible de revoir l’URL ou de la récupérer.
6. Cliquez sur **OK** pour créer le webhook.
7. Cliquez sur **Configurer les paramètres et les valeurs pour l’exécution** pour voir les valeurs des paramètres du runbook.
   > [!NOTE]
   > Si le Runbook possède des paramètres obligatoires, vous ne pouvez pas créer le webhook, sauf si des valeurs sont fournies.
8. Cliquez sur **OK** pour accepter les valeurs des paramètres du webhook.
9. Cliquez sur **Créer** pour créer le webhook.
10. À présent, suivez les étapes ci-dessus pour créer un deuxième webhook nommé **Complet**.
    > [!IMPORTANT]
    > Veillez à enregistrer les deux URL de webhook pour les utiliser ultérieurement dans ce tutoriel. Pour des raisons de sécurité, après la création du webhook, il ne vous est plus possible de revoir l’URL ou de la récupérer.

Vous devez disposer à présent de deux webhooks configurés, chacun étant accessible par le biais des l’URL que vous avez enregistrées.

![Webhooks - Facultatif et Complet](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-02.png)

Vous en avez désormais terminé avec la configuration d’Azure Automation. Vous pouvez tester les webhooks avec un simple test Postman pour confirmer qu’ils fonctionnent correctement. Ensuite, vous devez créer l’application logique pour l’orchestration.

## <a name="create-an-azure-logic-app-for-orchestration"></a>Créer une application logique Azure pour l’orchestration

Logic Apps vous permet de générer, planifier et automatiser des processus en tant que flux de travail, afin que vous puissiez intégrer applications, données, systèmes et services à des entreprises ou à des organisations. Dans ce scénario, l’[application logique](https://docs.microsoft.com/azure/logic-apps/) que vous créez va faire un peu plus qu’appeler seulement le webhook Automation que vous avez créé.

Les budgets peuvent être configurés pour déclencher une notification lorsqu’un seuil spécifié est atteint. Vous pouvez indiquer plusieurs seuils auxquels être tenu informé, et l’application logique vous montre ensuite les possibilités que vous avez pour effectuer différentes actions en fonction du seuil atteint. Dans cet exemple, vous configurez un scénario dans lequel vous recevez deux notifications, la première survient lorsque 80 % du budget ont été atteints, et la deuxième, lorsque la limite de 100 % est atteinte. L’application logique est utilisée pour arrêter toutes les machines virtuelles dans le groupe de ressources. Tout d’abord, le seuil **Facultatif** est atteint à 80 %, puis c’est le tour du second seuil auquel toutes les machines virtuelles de l’abonnement sont arrêtées.

Logic Apps vous permet de fournir un exemple de schéma pour le déclencheur HTTP, mais vous devez définir l’en-tête **Content-Type**. Le groupe d’actions n’ayant pas d’en-tête personnalisé pour le webhook, vous devez analyser la charge utile dans une étape distincte. Vous utilisez l’action **Analyser** et lui fournissez un exemple de charge utile.

### <a name="create-the-logic-app"></a>Création de l’application logique

L’application logique effectue plusieurs actions. La liste suivante fournit un ensemble global d’actions que l’application logique effectue :
- Reconnaître quand une requête HTTP est reçue.
- Analyser les données JSON transmises pour déterminer la valeur de seuil qui a été atteinte.
- Utiliser une instruction conditionnelle pour vérifier si la quantité seuil a atteint 80 % ou plus de la fourchette du budget, sans toutefois dépasser ou égaler 100 %.
    - Si cette quantité seuil est atteinte, envoyer une requête HTTP POST avec le webhook nommé **Facultatif**. Cette action arrête les machines virtuelles dans le groupe « Facultatif ».
- Utiliser une instruction conditionnelle pour vérifier si la quantité seuil a atteint ou dépassé 100 % de la valeur du budget.
    - Si la quantité seuil est atteinte, envoyer une requête HTTP POST avec le webhook nommé **Complet**. Cette action arrête toutes les autres machines virtuelles.

Les étapes suivantes sont nécessaires pour créer l’application logique qui effectue les étapes ci-dessus :

1.  Dans le [portail Azure](https://portal.azure.com/), sélectionnez **Créer une ressource** > **Intégration** > **Application logique**.

    ![Azure - Sélectionner la ressource Application logique](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-03.png)
2.  Dans le panneau **Créer une application logique**, renseignez les détails nécessaires à la création de votre application logique, sélectionnez **Épingler au tableau de bord** et cliquez sur **Créer**.

    ![Azure - Créer une application logique](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-03a.png)

Après le déploiement de votre application logique par Azure, le **Concepteur d’applications logiques** s’ouvre et affiche un panneau contenant une vidéo de présentation, et les déclencheurs généralement utilisés.

### <a name="add-a-trigger"></a>Ajouter un déclencheur

Chaque application logique doit démarrer avec un déclencheur, qui s’active lorsqu’un événement spécifique se produit ou lorsqu’une condition particulière est remplie. Chaque fois que le déclencheur s’active, le moteur Logic Apps crée une instance d’application logique qui démarre et exécute votre flux de travail. Les actions représentent toutes les étapes qui se produisent après le déclencheur.

1.  Sous **Modèles** dans le panneau **Concepteur d’applications logiques**, choisissez **Application logique vide**.
2.  Ajoutez un [déclencheur](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts) en indiquant « requête http » dans la zone de recherche du **Concepteur d’applications logiques** pour trouver et sélectionner le déclencheur nommé **Requête – Lors de la réception d’une requête HTTP**.

    ![Azure - Application logique - Déclencheur Http](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-04.png)
3.  Sélectionnez **Nouvelle étape** > **Ajouter une action**.

    ![Azure - Nouvelle étape - Ajouter une action](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-05.png)
4.  Recherchez « Analyser JSON » dans la zone de recherche **Concepteur Logic Apps** pour trouver et sélectionner **l’action** [Opérations sur les données – Analyser JSON](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts).

    ![Azure - Application logique - Ajouter une action d’analyse JSON](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-06.png)
5.  Entrez « Charge utile » comme nom de **Contenu** pour la charge utile Analyser JSON, ou utilisez la balise « Body » à partir du contenu dynamique.
6.  Sélectionnez l’option **Utiliser l’exemple de charge utile pour générer le schéma** dans la boîte de dialogue **Analyser JSON**.

    ![Azure - Application logique - Utiliser l’exemple de fichier de données JSON pour générer le schéma](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-07.png)
7.  Collez l’exemple de charge utile JSON suivant dans la zone de texte : `{"schemaId":"AIP Budget Notification","data":{"SubscriptionName":"CCM - Microsoft Azure Enterprise - 1","SubscriptionId":"<GUID>","SpendingAmount":"100","BudgetStartDate":"6/1/2018","Budget":"50","Unit":"USD","BudgetCreator":"email@contoso.com","BudgetName":"BudgetName","BudgetType":"Cost","ResourceGroup":"","NotificationThresholdAmount":"0.8"}}`

    La zone de texte s’affiche comme suit :

    ![Azure - Application logique - L’exemple de charge utile JSON](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-08.png)
8.  Cliquez sur **Done**.

### <a name="add-the-first-conditional-action"></a>Ajouter la première action conditionnelle

Utiliser une instruction conditionnelle pour vérifier si la quantité seuil a atteint 80 % ou plus de la fourchette du budget, sans toutefois dépasser ou égaler 100 %. Si cette quantité seuil est atteinte, envoyer une requête HTTP POST avec le webhook nommé **Facultatif**. Cette action arrête les machines virtuelles dans le groupe **Facultatif**.

1.  Sélectionnez **Nouvelle étape** > **Ajouter une condition**.

    ![Azure - Application logique - Ajouter une condition](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-09.png)
2.  Dans la boîte de dialogue **Condition**, cliquez sur la zone de texte contenant **Choisir une valeur** pour afficher une liste de valeurs disponibles.

    ![Azure - Application logique - Boîte de dialogue Condition](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-10.png)

3.  Cliquez sur **Expression** en haut de la liste et entrez l’expression suivante dans l’éditeur d’expressions : `float()`

    ![Azure - Application logique - Expression de type Float](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-11.png)

4.  Sélectionnez **Contenu dynamique**, placez le curseur entre les parenthèses () et sélectionnez **NotificationThresholdAmount** dans la liste pour remplir l’expression complète.

    L’expression est la suivante :<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  Sélectionnez **OK** pour définir l’expression.
6.  Sélectionnez **est supérieur ou égal à** dans la zone déroulante de la **Condition**.
7.  Dans la boîte de dialogue **Choisir une valeur** de la condition, entrez `.8`.

    ![Azure - Application logique - Expression de type Float avec une valeur](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-12.png)

8.  Cliquez sur **Ajouter** > **Ajouter une ligne** dans la boîte de dialogue Condition pour ajouter une autre partie de la condition.
9.  Dans la boîte de dialogue **Condition**, cliquez sur la zone de texte contenant **Choisir une valeur**.
10. Cliquez sur **Expression** en haut de la liste et entrez l’expression suivante dans l’éditeur d’expressions : `float()`
11. Sélectionnez **Contenu dynamique**, placez le curseur entre les parenthèses () et sélectionnez **NotificationThresholdAmount** dans la liste pour remplir l’expression complète.
12. Sélectionnez **OK** pour définir l’expression.
13. Sélectionnez **est inférieur à** dans la zone déroulante de la **Condition**.
14. Dans la boîte de dialogue **Choisir une valeur** de la condition, entrez `1`.

    ![Azure - Application logique - Expression de type Float avec une valeur](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-13.png)

15. Dans la boîte de dialogue **Si true**, sélectionnez **Ajouter une action**. Vous ajoutez une action HTTP POST permettant d’arrêter les machines virtuelles facultatives.

    ![Azure - Application logique - Ajouter une action](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-14.png)

16. Entrez **HTTP** pour rechercher l’action HTTP et sélectionnez l’action **HTTP – HTTP**.

    ![Azure - Application logique - Ajouter l’action HTTP](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-15.png)

17. Sélectionnez **Post** comme valeur de **Méthode**.
18. Comme valeur de l’**Uri**, entrez l’URL du webhook nommé **Facultatif** que vous avez créée précédemment dans ce tutoriel.

    ![Azure - Application logique - URI de l’action HTTP](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-16.png)

19. Dans la boîte de dialogue **Si true**, sélectionnez **Ajouter une action**. Vous ajoutez une action de messagerie pour l’envoi d’un e-mail informant le destinataire que les machines virtuelles facultatives ont été arrêtées.
20. Recherchez « Envoyer un e-mail » et sélectionnez une action *Envoyer un e-mail* selon le service de messagerie que vous utilisez.

    ![Azure - Application logique - Action Envoyer un e-mail](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-17.png)

    Pour les comptes Microsoft personnels, sélectionnez **Outlook.com**. Pour les comptes Azure professionnels ou scolaires, sélectionnez **Office 365 Outlook**. Si vous ne disposez pas déjà d’une connexion, vous êtes invité à vous connecter à votre compte de messagerie. Logic Apps crée une connexion à votre compte de messagerie.

    Vous devez autoriser l’accès de vos informations de messagerie à l’application logique.

    ![Azure - Application logique - Avis d’accès](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-18.png)

21. Ajoutez les zones **À**, **Objet** et **Corps** de texte pour l’e-mail qui notifie l’arrêt des machines virtuelles facultatives au destinataire. Utilisez le contenu dynamique de **BudgetName** et de **NotificationThresholdAmount** pour remplir les champs Objet et Corps.

    ![Azure - Application logique - Détails de l’e-mail](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-19.png)

### <a name="add-the-second-conditional-action"></a>Ajouter la seconde action conditionnelle

Utiliser une instruction conditionnelle pour vérifier si la quantité seuil a atteint ou dépassé 100 % de la valeur du budget. Si la quantité seuil est atteinte, envoyer une requête HTTP POST avec le webhook nommé **Complet**. Cette action arrête toutes les autres machines virtuelles.

1.  Sélectionnez **Nouvelle étape** > **Ajouter une condition**.

    ![Azure - Application logique - Ajouter une condition](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-20.png)

2.  Dans la boîte de dialogue **Condition**, cliquez sur la zone de texte contenant **Choisir une valeur** pour afficher une liste de valeurs disponibles.
3.  Cliquez sur **Expression** en haut de la liste et entrez l’expression suivante dans l’éditeur d’expressions : `float()`
4.  Sélectionnez **Contenu dynamique**, placez le curseur entre les parenthèses () et sélectionnez **NotificationThresholdAmount** dans la liste pour remplir l’expression complète.

    L’expression est la suivante :<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  Sélectionnez **OK** pour définir l’expression.
6.  Sélectionnez **est supérieur ou égal à** dans la zone déroulante de la **Condition**.
7.  Dans la boîte de dialogue **Choisir une valeur** de la condition, entrez `1`.

    ![Azure - Application logique - Définir une valeur de condition](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-21.png)

8.  Dans la boîte de dialogue **Si true**, sélectionnez **Ajouter une action**. Vous ajoutez une action HTTP POST permettant d’arrêter toutes les autres machines virtuelles.

    ![Azure - Application logique - Ajouter une action](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-22.png)

9.  Entrez **HTTP** pour rechercher l’action HTTP et sélectionnez l’action **HTTP – HTTP**.
10. Sélectionnez **Post** comme valeur de **Méthode**.
11. Comme valeur de l’**Uri**, entrez l’URL du webhook nommé **Complet** que vous avez créée précédemment dans ce tutoriel.

    ![Azure - Application logique - Ajouter une action](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-23.png)

12. Dans la boîte de dialogue **Si true**, sélectionnez **Ajouter une action**. Vous ajoutez une action de messagerie pour l’envoi d’un e-mail informant le destinataire que les machines virtuelles restantes ont été arrêtées.
13. Recherchez « Envoyer un e-mail » et sélectionnez une action *Envoyer un e-mail* selon le service de messagerie que vous utilisez.
14. Ajoutez les zones **À**, **Objet** et **Corps** de texte pour l’e-mail qui notifie l’arrêt des machines virtuelles facultatives au destinataire. Utilisez le contenu dynamique de **BudgetName** et de **NotificationThresholdAmount** pour remplir les champs Objet et Corps.

    ![Azure - Application logique - Détails Envoyer un e-mail](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-24.png)

15. Cliquez sur **Enregistrer** en haut du panneau **Concepteur d’application logique**.

### <a name="logic-app-summary"></a>Récapitulatif de l’application logique

Voici à quoi ressemble votre application logique lorsque vous avez terminé. Dans les scénarios les plus simples, où l’orchestration de seuils n’est pas nécessaire, vous pouvez appeler directement le script d’automatisation à partir de **Moniteur** et ignorer l’étape **Application logique**.

   ![Azure - Application logique - Vue complète](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-25.png)

Lorsque vous avez enregistré votre application logique, une URL a été générée, qu’il vous est possible d’appeler. Vous allez vous servir de cette URL dans la section suivante de ce tutoriel.

## <a name="create-an-azure-monitor-action-group"></a>Créer un groupe d’actions Azure Monitor

Un groupe d’actions est une collection de préférences de notification que vous définissez. Lorsqu’une alerte est déclenchée, un groupe d’actions spécifique peut recevoir l’alerte par le biais de la notification. Une alerte Azure déclenche par anticipation une notification basée sur des conditions spécifiques pour donner la possibilité d’agir. Une alerte peut utiliser les données provenant de plusieurs sources, notamment les métriques et les journaux d’activité.

Les groupes d’actions sont le seul point de terminaison que vous intégrez à votre budget. Vous pouvez configurer des notifications sur de nombreux canaux, mais dans le cadre de ce scénario, vous privilégiez l’application logique que vous avez créée précédemment dans ce tutoriel.

### <a name="create-an-action-group-in-azure-monitor"></a>Créer un groupe d’actions dans Azure Monitor

Lorsque vous créez le groupe d’actions, vous pointez vers l’application logique que vous avez créée auparavant dans ce tutoriel.

1.  Si vous n’êtes pas déjà connecté au [portail Azure](https://portal.azure.com/), faites-le et sélectionnez **Tous les services** > **Moniteur**.
2.  Sélectionnez **Alertes**, puis **Gérer les actions**.
3.  Sélectionnez **Ajouter un groupe d’actions** à partir du panneau **Groupes d’actions**.
4.  Ajoutez et vérifiez les éléments suivants :
    - Nom du groupe d’actions
    - Nom court
    - Abonnement
    - Resource group

    ![Azure - Application logique - Ajouter un groupe d’actions](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-26.png)

5.  Dans le volet **Ajouter un groupe d’actions**, ajoutez une action d’application logique. Nommez l’action **Budget-BudgetLA**. Dans le volet **Application logique**, sélectionnez l’**Abonnement** et le **Groupe de ressources**. Ensuite, sélectionnez l’**Application logique** que vous avez créée précédemment dans ce tutoriel.
6.  Cliquez sur **OK** pour configurer l’application logique. Ensuite, sélectionnez **OK** dans le volet **Ajouter un groupe d’actions** pour créer le groupe d’actions.

Vous en avez terminé avec la création et la sélection de tous les composants connexes nécessaires à la bonne orchestration de votre budget. Il ne vous reste plus maintenant qu’à créer le budget et à le configurer pour utiliser le groupe d’actions que vous avez créé.

## <a name="create-the-azure-budget"></a>Créer le budget Azure

Vous pouvez créer un budget dans le portail Azure en utilisant la [fonctionnalité Budget](../costs/tutorial-acm-create-budgets.md) dans Cost Management. Ou bien, vous pouvez créer un budget à l’aide d’API REST, d’applets de commande PowerShell ou de l’interface CLI. La procédure suivante utilise l’API REST. Avant d’appeler l’API REST, vous devez vous procurer un jeton d’autorisation. Pour créer un jeton d’autorisation, vous pouvez utiliser le projet [ARMClient](https://github.com/projectkudu/ARMClient). Avec **ARMClient**, vous vous authentifiez à Azure Resource Manager et obtenez un jeton pour appeler les API.

### <a name="create-an-authentication-token"></a>Créer un jeton d’authentification

1.  Accédez au projet [ARMClient](https://github.com/projectkudu/ARMClient) sur GitHub.
2.  Clonez le dépôt pour obtenir une copie locale.
3.  Ouvrez le projet dans Visual Studio et générez-le.
4.  Une fois le build correctement créé, le fichier exécutable est enregistré dans le dossier *\bin\debug*.
5.  Exécutez le projet ARMClient. Ouvrez une invite de commandes et accédez au dossier *\bin\debug* à partir de la racine du projet.
6.  Pour vous connecter et vous authentifier, entrez la commande suivante à l’invite de commandes :<br>
    `ARMClient login prod`
7.  Copiez le **Guid d’abonnement** à partir de la sortie.
8.  Pour copier un jeton d’autorisation dans votre Presse-papiers, entrez la commande suivante à l’invite de commandes, en veillant à bien utiliser l’ID d’abonnement copié dans l’étape ci-dessus : <br>
    `ARMClient token <subscription GUID from previous step>`

    À la fin de cette étape, vous voyez le texte suivant :<br>
    **Token copied to clipboard successfully.** (Jeton correctement copié dans le Presse-papiers.)
9.  Enregistrez le jeton à utiliser pour les étapes décrites dans la section suivante de ce tutoriel.

### <a name="create-the-budget"></a>Créer le budget

À présent, vous allez configurer **Postman** pour créer un budget en appelant les API REST d’Azure Consumption. Postman est un environnement de développement d’API. Vous allez importer les fichiers d’environnement et de collection dans Postman. La collection contient des définitions groupées de requêtes HTTP qui appellent les API REST d’Azure Consumption. Le fichier d’environnement contient des variables qui sont utilisées par la collection.

1.  Téléchargez et ouvrez le [client REST Postman](https://www.getpostman.com/) pour exécuter les API REST.
2.  Dans Postman, créez une requête.

    ![Postman - Créer une requête](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-27.png)

3.  Enregistrez la requête en tant que collection, pour que cette nouvelle requête n’ait rien.

    ![Postman - Enregistrer la nouvelle requête](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-28.png)

4.  Changez la requête d’une action `Get` en `Put`.
5.  Modifiez l’URL suivante en remplaçant `{subscriptionId}` par l’**ID d’abonnement** que vous avez utilisé à la section précédente de ce tutoriel. Modifiez également l’URL pour inclure « SampleBudget » en tant que valeur pour `{budgetName}` : `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2018-03-31`
6.  Dans Postman, sélectionnez l’onglet **Headers** (En-têtes).
7.  Ajouter une nouvelle **Key** (Clé) nommée « Authorization ».
8.  Définissez **Value** (Valeur) sur le jeton qui a été créé à l’aide du projet ArmClient à la fin de la dernière section.
9.  Sélectionnez l’onglet **Body** (Corps) dans Postman.
10. Sélectionnez la case d’option **raw** (brut).
11. Dans la zone de texte, collez l’exemple de définition de budget ci-dessous ; par contre, vous devez remplacer les paramètres de **subscriptionid**, **budgetname** et **actiongroupname** par votre ID d’abonnement, un nom unique pour votre budget et le nom du groupe d’actions que vous avez créé à la fois dans l’URL et dans le corps de la demande :

    ```
        {
            "properties": {
                "category": "Cost",
                "amount": 100.00,
                "timeGrain": "Monthly",
                "timePeriod": {
                "startDate": "2018-06-01T00:00:00Z",
                "endDate": "2018-10-31T00:00:00Z"
                },
                "filters": {
                },
            "notifications": {
                "Actual_GreaterThan_80_Percent": {
                    "enabled": true,
                    "operator": "GreaterThan",
                    "threshold": 80,
                    "contactEmails": [
                    ],
                    "contactRoles": [
                    ],
                    "contactGroups": [
                    "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}
                    ]
                },
            "Actual_EqualTo_100_Percent": {
                    "operator": "EqualTo",
                    "threshold": 100,
                    "contactGroups": [
                "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}"
                    ]
                }
            }
        }
    ```
12. Appuyez sur **Send** (Envoyer) pour envoyer la requête.

Vous avez désormais à votre disposition tous les éléments nécessaires pour appeler l’[API Budgets](https://docs.microsoft.com/rest/api/consumption/budgets). La référence de l’API Budgets offre des informations détaillées supplémentaires sur les requêtes spécifiques, entre autres :
    - **budgetName** - Plusieurs budgets sont pris en charge.  Les noms de budget doivent être uniques.
    - **category** - Doit être soit **Cost**, soit **Usage**. L’API prend en charge les budgets des coûts et de l’utilisation.
    - **timeGrain** - Un budget mensuel, trimestriel ou annuel. Le montant se réinitialise à la fin de la période.
    - **filters** - Les filtres vous permettent de restreindre le budget à un ensemble particulier de ressources dans la limite sélectionnée. Par exemple, un filtre peut être une collection de groupes de ressources pour un budget au niveau de l’abonnement.
    - **notifications** – Détermine les seuils et les détails de la notification. Vous pouvez configurer plusieurs seuils et fournir une adresse e-mail ou un groupe d’actions devant recevoir une notification.

## <a name="summary"></a>Résumé

En suivant ce tutoriel, vous avez appris à :
- Créer un runbook Azure Automation pour arrêter les machines virtuelles.
- Créer une application logique Azure qui est déclenchée en fonction des valeurs de seuil de budget, et appeler le runbook associé avec les paramètres appropriés.
- Créer un groupe d’actions Azure Monitor à configurer ensuite pour déclencher l’application logique Azure lorsque le seuil de budget est atteint.
- Créer le budget Azure avec les seuils souhaités, et l’associer au groupe d’actions.

Vous disposez maintenant d’un budget tout à fait fonctionnel pour votre abonnement, qui arrête vos machines virtuelles lorsque vous atteignez les seuils de budget configurés.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les scénarios de facturation Azure, consultez [Scénarios d’automatisation de la facturation et de la gestion des coûts](cost-management-automation-scenarios.md).
