---
title: Tester des applications logiques avec des données fictives - Azure Logic Apps
description: Configurer les résultats statiques pour le test des applications logiques avec des données fictives sans affecter les environnements de production
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 03/18/2019
ms.openlocfilehash: 0fbe56ceeeba71bcbb5ef358cd66de15e36508fc
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58165094"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>Tester des applications logiques avec des données fictives en configurant des résultats statiques

Lorsque vous testez vos applications logiques, vous n’êtes peut-être pas prêt à appeler ou à accéder aux applications, services et systèmes pour diverses raisons. Généralement dans ces scénarios, vous devrez peut-être exécuter les chemins d’accès de la condition différente, forcer les erreurs, fournissent les corps de réponse de message spécifique ou même tenter d’ignorer certaines étapes. En configurant des résultats statiques pour une action dans votre application logique, vous pouvez simuler des données de sortie de cette action. L’activation des résultats statiques sur une action ne s’exécute pas l’action, mais retourne à la place des données fictives.

Par exemple, si vous avez configuré des résultats statiques pour le Outlook 365 envoyer l’action de messagerie, le moteur Logic Apps retourne uniquement les données fictifs que vous avez spécifié en tant que résultats statiques, plutôt qu’appeler Outlook et envoyer un courrier électronique.

## <a name="prerequisites"></a>Conditions préalables

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>.

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L’application logique dans lequel vous souhaitez configurer des résultats statiques

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>Configurer les résultats statiques

1. Si vous n’avez pas déjà fait, dans le [Azure portal](https://portal.azure.com), ouvrez votre application logique dans le Concepteur d’applications logiques.

1. Sur l’action où vous souhaitez configurer les résultats statiques, procédez comme suit : 

   1. Dans angle supérieur droit l’action de le, choisissez le bouton de sélection (*...* ) bouton, puis sélectionnez **résultat statique**, par exemple :

      ![Sélectionnez « Result statique » > « Activer statique Result »](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. Choisissez **activer résultat statique**. Pour les propriétés requises (*), spécifiez les valeurs de sortie factice que vous souhaitez renvoyer la réponse de l’action de le.

      Par exemple, voici les propriétés requises pour l’action HTTP :

      | Propriété | Description |
      |----------|-------------|
      | **État** | État de l’action à retourner |
      | **Code d’état** | Le code d’état spécifique pour retourner |
      | **En-têtes** | Le contenu d’en-tête à retourner |
      |||

      ![Sélectionnez « Activer le résultat statique »](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      Pour entrer des données fictives dans format JavaScript Objet Notation (JSON), choisissez **basculer en Mode JSON** (![choisir « Passer en Mode JSON »](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)).

   1. Pour les propriétés facultatives, ouvrez le **sélectionner les champs facultatifs** liste, puis sélectionnez les propriétés que vous souhaitez simuler.

      ![Sélectionnez les propriétés facultatives](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. Lorsque vous êtes prêt à enregistrer, choisissez **fait**.

   Dans angle supérieur droit l’action de le, la barre de titre affiche maintenant une icône de bécher test (![icône pour résultats statiques](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)), ce qui indique que vous avez activé les résultats statiques.

   ![Affichage de l’icône est activée génère une statique](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   Pour rechercher les exécutions précédentes qui utilisent des données fictives, consultez [rechercher les exécutions qui utilisent des résultats statiques](#find-runs-mock-data) plus loin dans cette rubrique.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>Réutiliser des sorties précédentes

Si votre application logique a une précédente exécuter avec des sorties, que vous pouvez réutiliser en tant que sorties factices, vous pouvez copier et coller les sorties qui s’exécutent.

1. Si vous n’avez pas déjà fait, dans le [Azure portal](https://portal.azure.com), ouvrez votre application logique dans le Concepteur d’applications logiques.

1. Dans le menu principal de votre application logique, sélectionnez **vue d’ensemble**.

1. Dans le **historique des exécutions** section, sélectionnez l’application logique vous souhaitez.

1. Dans le flux de travail de votre application logique, recherchez et développez l’action qui a les sorties.

1. Choisissez le **afficher les sorties brutes** lien.

1. Copier l’objet JavaScript Objet Notation (JSON) complet ou la sous-section spécifique que vous souhaitez utiliser, par exemple, la section des sorties ou même simplement la section en-têtes.

1. Suivez les étapes pour l’ouverture du **résultat statique** pour votre action dans la boîte de [configurer résultats statiques](#set-up-static-results).

1. Après le **résultat statique** s’ouvre, sélectionnez l’étape :

   * Pour coller un objet JSON complet, choisissez **basculer en Mode JSON** (![choisir « Passer en Mode JSON »](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)) :

     ![Choisissez « Passer en Mode JSON » pour l’objet complet](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Pour coller uniquement une section JSON, en regard de l’étiquette de cette section, sélectionnez **basculer en Mode JSON** pour cette section, par exemple :

     ![Choisissez « Passer en Mode JSON » pour les sorties](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. Dans l’éditeur JSON, collez votre JSON copié précédemment.

   ![Mode JSON](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. Quand vous avez fini, choisissez **Terminé**. Ou, pour revenir au concepteur, choisissez **Mode de l’éditeur de commutateur** (![choisir « Changer de Mode éditeur »](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)).

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>Rechercher les exécutions qui utilisent des résultats statiques

Historique des exécutions de votre application logique identifiant les exécutions où les actions utilisent les résultats statiques. Pour rechercher ces exécutions, procédez comme suit :

1. Dans le menu principal de votre application logique, sélectionnez **vue d’ensemble**. 

1. Dans le volet droit, sous **historique des exécutions**, recherchez le **résultats statique** colonne. 

   Toute exécution qui inclut les actions avec des résultats a la **résultats statique** colonne définie sur **activé**, par exemple :

   ![Exécuter l’historique - colonne de résultats statique](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. Pour afficher les actions qui utilisent les résultats statiques, sélectionnez la série de tests où le **résultats statique** colonne est définie sur **activé**.

   Affichent les actions qui utilisent les résultats statiques bécher test (![icône pour résultats statiques](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)) icône, par exemple :

   ![Exécuter des actions qui utilisent les résultats statiques historique-](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>Désactiver les résultats statiques

La désactivation de résultats statiques ne jeter les valeurs à partir de votre dernière installation. Par conséquent, lorsque vous activez des résultats statiques la prochaine fois, vous pouvez continuer à l’aide de vos valeurs précédentes.

1. Recherchez l’action où vous souhaitez désactiver les sorties statiques. Dans angle supérieur droit l’action de le, cliquez sur l’icône de bécher test (![icône pour résultats statiques](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)).

   ![Désactiver les résultats statiques](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. Choisissez **désactiver résultat statique** > **fait**.

   ![Désactiver les résultats statiques](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [Azure Logic Apps](../logic-apps/logic-apps-overview.md)