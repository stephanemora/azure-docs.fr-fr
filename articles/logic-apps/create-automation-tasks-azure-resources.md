---
title: Créer des tâches d'automatisation pour gérer et surveiller les ressources Azure
description: Configurez des tâches automatisées pour gérer les ressources Azure et surveiller les coûts en créant des workflows exécutés sur Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 436a9422adcc9f0e564d34c68d3ed0d5b9185151
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131079659"
---
# <a name="manage-azure-resources-and-monitor-costs-by-creating-automation-tasks-preview"></a>Gérer les ressources Azure et surveiller les coûts en créant des tâches d'automatisation (préversion)

> [!IMPORTANT]
> Cette fonctionnalité est en préversion et est soumise aux [conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) des préversions de Microsoft Azure.

Afin de faciliter la gestion des [ressources Azure](../azure-resource-manager/management/overview.md#terminology), vous pouvez créer des tâches de gestion automatisées pour une ressource ou un groupe de ressources spécifique à l'aide de modèles de tâches d'automatisation, dont la disponibilité varie en fonction du type de ressource. Par exemple, vous pouvez configurer une tâche d'automatisation qui vous envoie le coût mensuel d'un [compte de stockage Azure](../storage/common/storage-account-overview.md). Vous pouvez également créer une tâche d'automatisation qui active ou désactive une [machine virtuelle Azure](https://azure.microsoft.com/services/virtual-machines/) selon un calendrier prédéfini.

Les modèles de tâches disponibles dans cette préversion sont les suivants :

| Type de ressource | Modèles de tâches d'automatisation |
|---------------|---------------------------|
| Groupes de ressources Azure | **Quand la ressource est supprimée** |
| Toutes les ressources Azure | **Envoyer le coût mensuel de la ressource** |
| Machines virtuelles Azure | En outre : <p>- **Mettre hors tension la machine virtuelle** <br>- **Démarrer la machine virtuelle** |
| Comptes de stockage Azure | En outre : <p>- **Supprimer les anciens objets blob** |
| Azure Cosmos DB | En outre : <p>- **Envoyer le résultat de la requête par e-mail** |
|||

Cet article explique comment accomplir les tâches suivantes :

* [Créer une tâche d'automatisation](#create-automation-task) pour une ressource Azure spécifique

* [Examiner l'historique d'une tâche](#review-task-history), qui comprend l'état de l'exécution, les entrées, les sorties et d'autres informations historiques

* [Modifier la tâche](#edit-task) pour pouvoir la mettre à jour ou personnaliser le workflow sous-jacent de la tâche dans le concepteur de workflow.

<a name="differences"></a>

## <a name="how-do-automation-tasks-differ-from-azure-automation"></a>En quoi les tâches d'automatisation diffèrent-elles d'Azure Automation ?

Les tâches d’automatisation sont plus basiques et légères qu’[Azure Automation](../automation/automation-intro.md). Actuellement, vous ne pouvez créer une tâche d’automatisation qu’au niveau de la ressource Azure. En arrière-plan, une tâche d’automatisation est en fait une ressource d’application logique qui exécute un workflow et est alimentée par le [service Azure Logic Apps *multilocataire*](logic-apps-overview.md). Une fois la tâche d’automatisation créée, vous pouvez consulter et modifier le workflow sous-jacent en ouvrant la tâche dans le concepteur de workflow. Après au moins une exécution de la tâche, vous pouvez consulter l’état de la tâche, l’historique des exécutions du workflow, les entrées et les sorties de chaque exécution.

À titre de comparaison, Azure Automation est un service d’automatisation et de configuration cloud qui prend en charge une gestion cohérente de vos environnements Azure et non-Azure. Le service comprend l'[automatisation des processus pour assurer leur orchestration](../automation/automation-intro.md#process-automation) à l'aide de [runbooks](../automation/automation-runbook-execution.md), la gestion de la configuration avec [le suivi et l'inventaire des modifications](../automation/change-tracking/overview.md), la gestion des mises à jour, des fonctionnalités partagées et des fonctionnalités hétérogènes. Azure Automation vous offre un contrôle complet lors du déploiement, des opérations et de la désaffectation des charges de travail et des ressources.

<a name="pricing"></a>

## <a name="pricing"></a>Tarifs

Quand vous créez une tâche d’automatisation, les frais ne démarrent pas automatiquement. Une tâche d’automatisation est alimentée par un workflow dans une ressource d’application logique hébergée dans une instance Azure Logic Apps multilocataire. Par conséquent, le [modèle tarifaire de consommation](logic-apps-pricing.md) s’applique aux tâches d’automatisation. Le comptage et la facturation sont basés sur les exécutions de déclencheur et d’action dans le workflow d’application logique sous-jacent.

Les exécutions sont mesurées et facturées, que le workflow s’exécute normalement ou qu’il soit même instancié. Par exemple, supposons que votre tâche d’automatisation utilise un déclencheur d’interrogation qui effectue régulièrement un appel sortant à un point de terminaison. Cette requête sortante est mesurée et facturée en tant qu’exécution, que le déclencheur soit activé ou non, ce qui détermine si une instance de workflow est créée ou non.

Les déclencheurs et les actions suivent les [tarifs du plan de consommation](https://azure.microsoft.com/pricing/details/logic-apps/), qui diffèrent selon que ces opérations sont [« intégrées »](../connectors/built-in.md) ou [« managées » (Standard ou Enterprise)](../connectors/managed.md). Les déclencheurs et les actions effectuent également des transactions de stockage qui utilisent le [débit de données du plan de consommation](https://azure.microsoft.com/pricing/details/logic-apps/).

> [!TIP]
> En prime, le plan de consommation comprend tous les mois *plusieurs milliers* d’exécutions intégrées gratuites. Pour des informations spécifiques, consultez les [Tarifs du plan de consommation](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="prerequisites"></a>Prérequis

* Un compte et un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* La ressource Azure que vous souhaitez gérer. Cet article utilise comme exemple un compte de stockage Azure.

* Un compte Office 365, si vous souhaitez suivre l'exemple, qui vous envoie des e-mails via Office 365 Outlook.

<a name="create-automation-task"></a>

## <a name="create-an-automation-task"></a>Créer une tâche d'automatisation

1. Sur le [portail Azure](https://portal.azure.com), recherchez la ressource que vous souhaitez gérer.

1. Dans le menu de navigation de la ressource, dans la section **Automatisation**, sélectionnez **Tâches (préversion)** .

   ![Capture d’écran montrant le portail Azure et le menu de ressource de compte de stockage avec « Tâches (préversion) » sélectionné.](./media/create-automation-tasks-azure-resources/storage-account-menu-automation-section.png)

1. Dans le volet **Tâches**, sélectionnez **Ajouter une tâche** pour pouvoir sélectionner un modèle de tâche.

   ![Capture d’écran montrant le volet « Tâches (préversion) » avec « Ajouter une tâche » sélectionné.](./media/create-automation-tasks-azure-resources/add-automation-task.png)

1. Dans le volet **Ajouter une tâche**, sous **Sélectionner un modèle**, dans le modèle de la tâche de réplication que vous voulez créer, sélectionnez **Sélectionner**. Si la page suivante ne s’affiche pas, sélectionnez **Suivant : Authentifier**.

   Cet exemple se poursuit avec la sélection du modèle de tâche **Envoyer le coût mensuel de la ressource**.

   ![Capture d’écran montrant le volet « Ajouter une tâche » avec le modèle « Envoyer le coût mensuel de la ressource » sélectionné.](./media/create-automation-tasks-azure-resources/select-task-template.png)

1. Sous **Authentifier**, dans la section **Connexions**, sélectionnez **Créer** pour chacune des connexions qui apparaissent dans la tâche afin de pouvoir fournir toutes les informations d’authentification. Les types de connexions varient en fonction de la tâche.

   Cet exemple ne montre qu’une seule des connexions requises par cette tâche.

   ![Capture d’écran montrant l’option « Créer » sélectionnée pour la connexion Azure Resource Manager.](./media/create-automation-tasks-azure-resources/create-authenticate-connections.png)

1. Lorsque vous y êtes invité, connectez-vous avec les informations d'identification de votre compte Azure.

   ![Capture d’écran montrant la sélection, « Se connecter ».](./media/create-automation-tasks-azure-resources/create-connection-sign-in.png)

   Chaque connexion authentifiée se présente comme dans l'exemple suivant :

   ![Capture d’écran montrant une connexion créée.](./media/create-automation-tasks-azure-resources/create-connection-success.png)

1. Après avoir authentifié toutes les connexions, sélectionnez **Suivant : Configurer** si la page suivante n’apparaît pas.

1. Sous **Configurer**, donnez un nom à la tâche et renseignez les autres informations nécessaires. Quand vous avez terminé, sélectionnez **Vérifier + créer**.

   > [!NOTE]
   > Une fois créé, le nom de la tâche ne peut pas être modifié. Choisissez donc un nom qui restera pertinent si jamais vous [modifiez le workflow sous-jacent](#edit-task-workflow). Les modifications que vous apportez au workflow sous-jacent s'appliquent uniquement à la tâche que vous avez créée, et non au modèle de tâche.
   >
   > Par exemple, si vous nommez votre tâche `SendMonthlyCost` et que vous modifiez ensuite le workflow sous-jacent pour qu'il s'exécute sur une base hebdomadaire, vous ne pourrez pas remplacer le nom de votre tâche par `SendWeeklyCost`.

   Les tâches qui envoient des notifications par e-mail requièrent une adresse e-mail.

   ![Capture d’écran montrant les informations nécessaires pour la tâche sélectionnée.](./media/create-automation-tasks-azure-resources/provide-task-information.png)

   La tâche que vous avez créée, qui est automatiquement active et en cours d’exécution, apparaît désormais dans la liste **Tâches**.

   ![Capture d'écran de la liste des tâches d'automatisation](./media/create-automation-tasks-azure-resources/automation-tasks-list.png)

   > [!TIP]
   > Si la tâche n'apparaît pas immédiatement, essayez d'actualiser la liste ou patientez un peu. Sur la barre d'outils, sélectionnez **Actualiser**.

   Dès que la tâche sélectionnée est exécutée, vous recevez un e-mail semblable au suivant :

   ![Capture d’écran montrant l’e-mail de notification envoyé par la tâche.](./media/create-automation-tasks-azure-resources/email-notification-received.png)

<a name="review-task-history"></a>

## <a name="review-task-history"></a>Examiner l'historique des tâches

Pour consulter l'historique des exécutions d'une tâche, avec l'état, les entrées, les sorties et autres informations relatives à celles-ci, procédez comme suit :

1. Sur le [portail Azure](https://portal.azure.com), recherchez la ressource contenant l'historique de la tâche que vous souhaitez examiner.

1. Dans le menu de la ressource, sous **Paramètres**, dans la section **Automatisation**, sélectionnez **Tâches (préversion)** .

1. Dans la liste des tâches, recherchez la tâche que vous souhaitez examiner. Dans la colonne **Exécutions** de cette tâche, sélectionnez **Afficher**.

   ![Capture d’écran montrant une tâche et l’option « Voir » sélectionnée.](./media/create-automation-tasks-azure-resources/view-runs-for-task.png)

   Le volet **Historique des exécutions** présente toutes les exécutions de la tâche ainsi que leur état, heure de début, identificateur et durée.

   ![Capture d’écran montrant les exécutions d’une tâche, leur état et d’autres informations.](./media/create-automation-tasks-azure-resources/view-runs-history.png)

   Les états possibles d'une exécution sont les suivants :

   | Statut | Description |
   |--------|-------------|
   | **Annulé** | La tâche a été annulée pendant son exécution. |
   | **Échec** | Au moins une action de la tâche a échoué, et aucune action ultérieure n'a permis de gérer l'échec. |
   | **Exécution** | La tâche est en cours d'exécution. |
   | **Réussi** | Toutes les actions ont réussi. Une tâche peut encore se terminer avec succès après l'échec d'une action, à condition qu'une action ultérieure permette de gérer cet échec. |
   | **En attente** | L'exécution n'a pas encore été lancée car une instance antérieure de la tâche est toujours en cours d'exécution. |
   |||

   Pour plus d’informations, consultez [Examiner l’historique des exécutions dans la vue de supervision](monitor-logic-apps.md#review-runs-history).

1. Pour afficher l'état et d'autres informations sur chacune des étapes d'une exécution, sélectionnez cette exécution.

   Le volet **Exécution de l'application logique** s'ouvre et affiche le workflow sous-jacent exécuté.

   * Un workflow démarre toujours avec un [*déclencheur*](../connectors/apis-list.md#triggers). Pour cette tâche, le workflow démarre avec le déclencheur [**Périodicité**](../connectors/connectors-native-recurrence.md).

   * Chaque étape indique son état et sa durée d'exécution. Une durée de 0 seconde indique que l'exécution de l'étape a nécessité moins d'une seconde.

   ![Capture d’écran montrant chaque étape de l’exécution, avec l’état et la durée d’exécution.](./media/create-automation-tasks-azure-resources/runs-history-details.png)

1. Pour examiner les entrées et les sorties d'une étape, sélectionnez-la afin de la développer.

   Cet exemple présente les entrées du déclencheur Périodicité, qui n'a pas de sortie car il indique uniquement à quel moment le workflow s'exécute et ne fournit aucune sortie pour les actions suivantes à traiter.

   ![Capture d’écran montrant le déclencheur développé et les entrées.](./media/create-automation-tasks-azure-resources/view-trigger-inputs.png)

   L'action **Envoyer un e-mail**, quant à elle, contient des entrées issues d'actions antérieures du workflow ainsi que des sorties.

   ![Capture d’écran montrant une action développée, les entrées et les sorties.](./media/create-automation-tasks-azure-resources/view-action-inputs-outputs.png)

Pour apprendre à créer vos propres workflows automatisés afin d'intégrer des applications, des données, des services et des systèmes en dehors du contexte des tâches d'automatisation pour les ressources Azure, consultez [Démarrage rapide : Créer votre premier workflow d'intégration avec Azure Logic Apps - Portail Azure](quickstart-create-first-logic-app-workflow.md).

<a name="edit-task"></a>

## <a name="edit-the-task"></a>Modifier la tâche

Pour modifier une tâche, vous disposez des options suivantes :

* [Modifier la tâche « inline »](#edit-task-inline) qui vous permet de modifier les propriétés de la tâche, comme les informations de connexion ou de configuration (votre adresse e-mail, par exemple)

* [Modifier le workflow sous-jacent de la tâche](#edit-task-workflow) dans le concepteur de workflow.

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>Modifier la tâche inline

1. Sur le [portail Azure](https://portal.azure.com), recherchez la ressource contenant la tâche que vous souhaitez mettre à jour.

1. Dans le menu de navigation de la ressource, dans la section **Automatisation**, sélectionnez **Tâches (préversion)** .

1. Dans la liste des tâches, recherchez la tâche que vous souhaitez mettre à jour. Ouvrez le menu **...** de la tâche, puis sélectionnez **Modifier inline**.

   ![Capture d’écran montrant le menu de points de suspension ouvert et l’option « Modifier inline » sélectionnée.](./media/create-automation-tasks-azure-resources/edit-task-in-line.png)

   Par défaut, l’onglet **Authentifier** s’affiche et montre les connexions existantes.

1. Pour ajouter de nouvelles informations d'authentification ou sélectionner des informations d'authentification existantes, ouvrez le menu **...** de la connexion, puis choisissez **Ajouter une nouvelle connexion** ou sélectionnez les informations d'authentification existantes, le cas échéant.

   ![Capture d’écran montrant l’onglet Authentification, les connexions existantes et le menu de points de suspension sélectionné.](./media/create-automation-tasks-azure-resources/edit-connections.png)

1. Pour mettre à jour d’autres propriétés de la tâche, sélectionnez **Suivant : Configurer**.

   Pour la tâche décrite dans cet exemple, la seule propriété modifiable est l'adresse e-mail.

   ![Capture d’écran montrant l’onglet « Configurer ».](./media/create-automation-tasks-azure-resources/edit-task-configuration.png)

1. Quand vous avez terminé, sélectionnez **Enregistrer**.

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>Modifier le workflow sous-jacent de la tâche

Lorsque vous modifiez le workflow sous-jacent d'une tâche d'automatisation, vos modifications se répercutent uniquement sur la tâche que vous avez créée, et non sur le modèle qui la crée. Une fois vos modifications enregistrées, il se peut que le nom que vous avez donné à la tâche d'origine ne convienne plus et que vous deviez recréer la tâche sous un autre nom.

> [!TIP]
> Nous vous recommandons de cloner le workflow sous-jacent et de modifier la version copiée plutôt que la version d'origine. Vous pourrez ainsi apporter vos modifications et les tester sur la copie pendant que la tâche d'automatisation d'origine continuera de fonctionner et de s'exécuter sans risquer de perturber ou d'interrompre les fonctionnalités existantes. Après avoir apporté vos modifications et vérifié que la nouvelle version s'exécute correctement, vous pouvez désactiver ou supprimer la tâche d'automatisation d'origine et utiliser la version clonée. Les étapes suivantes comprennent des informations sur le clonage de votre workflow.

1. Sur le [portail Azure](https://portal.azure.com), recherchez la ressource contenant la tâche que vous souhaitez mettre à jour.

1. Dans le menu de navigation de la ressource, dans la section **Automatisation**, sélectionnez **Tâches**.

1. Dans la liste des tâches, recherchez la tâche que vous souhaitez mettre à jour. Ouvrez le menu **...** de la tâche et sélectionnez **Ouvrir dans Logic Apps**.

   ![Capture d’écran montrant le menu des points de suspension ouvert et l’option « Ouvrir dans Logic Apps » sélectionnée.](./media/create-automation-tasks-azure-resources/edit-task-logic-app-designer.png)

   Le workflow sous-jacent de la tâche s'ouvre dans le service Azure Logic Apps et affiche le volet **Vue d'ensemble** dans lequel vous pouvez consulter le même historique des exécutions que celui qui est disponible pour la tâche.

   ![Capture d’écran montrant la tâche dans la vue Azure Logic Apps, avec le volet Vue d’ensemble sélectionné.](./media/create-automation-tasks-azure-resources/task-logic-apps-view.png)

1. Pour ouvrir le workflow sous-jacent dans le concepteur, dans le menu de navigation de l’application logique, sélectionnez **Concepteur d’application logique**.

   ![Capture d’écran montrant l’option de menu « Concepteur d’application logique » sélectionnée et l’aire du concepteur avec le workflow sous-jacent.](./media/create-automation-tasks-azure-resources/view-task-workflow-logic-app-designer.png)

   Vous pouvez désormais modifier les propriétés du déclencheur et des actions du workflow, ainsi que le déclencheur et les actions qui définissent le workflow proprement dit. Nous vous recommandons toutefois de suivre la procédure décrite pour cloner votre workflow afin de pouvoir apporter vos modifications sur une copie pendant que le workflow d'origine continue de fonctionner et de s'exécuter.

1. Pour cloner votre workflow et modifier la version copiée, procédez comme suit :

   1. Dans le menu du workflow d'application logique, sélectionnez **Vue d'ensemble**.

   1. Sur la barre d'outils du volet Vue d'ensemble, sélectionnez **Cloner**.

   1. Dans le volet de création de l'application logique, sous **Nom**, entrez un nouveau nom pour le workflow d'application logique copié.

      À l'exception de l'**État de l'application logique**, les autres propriétés ne peuvent pas être modifiées.

   1. Sous **État de l'application logique**, sélectionnez **Désactivé** afin que le workflow cloné ne s'exécute pas pendant que vous apportez vos modifications. Vous pourrez activer le workflow lorsque vous serez prêt à tester vos modifications.

   1. Une fois qu’Azure a terminé le provisionnement de votre workflow cloné, recherchez-le et ouvrez-le dans le concepteur.

1. Pour afficher les propriétés du déclencheur ou d'une action, développez ce déclencheur ou cette action.

   Par exemple, vous pouvez modifier le déclencheur Périodicité pour qu'il s'exécute sur une base hebdomadaire plutôt que mensuelle.

   ![Capture d’écran montrant le déclencheur Périodicité développé avec la liste Fréquence ouverte sur les options de fréquence disponibles.](./media/create-automation-tasks-azure-resources/edit-recurrence-trigger.png)

   Pour plus d’informations sur le déclencheur Périodicité, consultez [Créer, planifier et exécuter des tâches et des workflows récurrents avec le déclencheur Périodicité.](../connectors/connectors-native-recurrence.md) Pour plus d'informations sur les autres déclencheurs et actions que vous pouvez utiliser, consultez [Connecteurs pour Azure Logic Apps](../connectors/apis-list.md).

1. Pour enregistrer vos modifications, sélectionnez **Enregistrer** sur la barre d'outils du concepteur.

   ![Capture d’écran montrant la barre d’outils du concepteur et la commande « Enregistrer » sélectionnée.](./media/create-automation-tasks-azure-resources/save-updated-workflow.png)

1. Pour tester et exécuter le workflow mis à jour, sélectionnez **Exécuter** sur la barre d'outils du concepteur.

   Au terme de l'exécution du workflow, le concepteur affiche les détails de celle-ci.

   ![Capture d’écran montrant les détails d’exécution du workflow dans le concepteur.](./media/create-automation-tasks-azure-resources/view-run-details-designer.png)

1. Pour désactiver le workflow afin d'interrompre l'exécution de la tâche, consultez [Gérer les applications logiques sur le portail Azure](../logic-apps/manage-logic-apps-with-azure-portal.md).

## <a name="provide-feedback"></a>Fournir un feedback

Nous attendons vos remarques ! Pour nous signaler des bogues, nous faire part de vos commentaires ou nous poser des questions sur cette préversion, [contactez l'équipe Azure Logic Apps](mailto:logicappspm@microsoft.com).

## <a name="next-steps"></a>Étapes suivantes

* [Gérer les applications logiques dans le portail Azure](manage-logic-apps-with-azure-portal.md)
