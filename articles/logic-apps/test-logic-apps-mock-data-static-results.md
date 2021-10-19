---
title: Flux de travail de test fictifs
description: Configurez des données fictives pour tester des flux de travail dans Azure Logic Apps sans affecter les environnements de production.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 10/08/2021
ms.openlocfilehash: 4167dcffe0a1b4db50f6d1580ad6284f2cf9321d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129712423"
---
# <a name="test-workflows-with-mock-data-in-azure-logic-apps-preview"></a>Flux de travail de test avec données fictives dans Azure Logic Apps (préversion)

> [!NOTE]
> Cette fonctionnalité est en préversion et est soumise aux [conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) des préversions de Microsoft Azure.

Pour tester vos flux de travail sans appeler des applications, des données, des services ou des systèmes dynamiques, ni y accéder, vous pouvez configurer et retourner des valeurs fictives à partir d’actions. Par exemple, vous souhaitez peut-être tester différents chemins d’action en fonction de différentes conditions, forcer des erreurs, fournir des corps de réponse de message spécifiques ou même essayer d’ignorer certaines étapes. La configuration de données fictives sur une action n’entraîne pas l’exécution de l’action, mais renvoie les données fictives.

Par exemple, si vous configurez des données fictives pour l’action d’envoi d’un e-mail d’Outlook 365, Azure Logic Apps retourne simplement les données fictives que vous avez fournies, au lieu d’appeler Outlook et d’envoyer un e-mail.

Cet article explique comment configurer des données fictives sur une action dans un flux de travail pour le type de ressource [**application logique (Consommation)** et **application logique (Standard)** ](logic-apps-overview.md#resource-environment-differences). Vous pouvez rechercher les précédentes exécutions du flux de travail qui utilisent ces données fictives et réutiliser des sorties d’action existantes en tant que données fictives.

## <a name="prerequisites"></a>Prérequis

* Un compte et un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez <a href="https://azure.microsoft.com/free/?WT.mc_id=A261C142F" target="_blank">vous inscrire pour obtenir un compte Azure gratuitement</a>.

* La ressource d’application logique et le flux de travail dans lesquels vous souhaitez utiliser des données fictives. Cet article utilise un déclencheur de **périodicité** et une action **HTTP** comme exemple de flux de travail.

  Si vous n’êtes pas familiarisé avec les applications logiques, consultez les sections [Présentation d’Azure Logic Apps](logic-apps-overview.md) et [Démarrage rapide : créer votre premier flux de travail d’application logique](quickstart-create-first-logic-app-workflow.md).

<a name="enable-mock-data"></a>

## <a name="enable-mock-data-output"></a>Activer la sortie des données fictives

### <a name="consumption"></a>[Consommation](#tab/consumption)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez le flux de travail de votre application logique dans le concepteur.

1. Procédez comme suit pour l’action pour laquelle vous souhaitez retourner des données fictives :

   1. En haut à droite de l’action, sélectionnez le bouton représentant des points de suspension( *...* ), puis **Test**, par exemple :

      ![Capture d’écran montrant le portail Azure, le concepteur de flux de travail, le menu contextuel action et « Test » sélectionné.](./media/test-logic-apps-mock-data-static-results/select-testing.png)

   1. Dans le volet **Test**, sélectionnez **Activer le résultat statique (préversion)** . Quand les propriétés requises (*) de l’action s’affichent, spécifiez les valeurs de sortie fictive que vous souhaitez retourner comme réponse de l’action.

      Les propriétés varient en fonction du type d’action sélectionné. Par exemple, l’action HTTP a les propriétés obligatoires suivantes :

      | Propriété | Description |
      |----------|-------------|
      | **État** | État de l’action à renvoyer |
      | **Code d’état** | Code d’état spécifique à renvoyer |
      | **En-têtes** | Contenu d’en-tête à renvoyer |
      |||

      ![Capture d’écran montrant le volet « Test » après avoir sélectionné « Activer le résultat statique ».](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      > [!TIP]
      > Pour entrer les données au format JavaScript Objet Notation (JSON), choisissez **Basculer en mode JSON** (![icône « Basculer en mode JSON »](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)).

   1. Pour les propriétés facultatives, ouvrez la liste **Sélectionner des champs facultatifs**, puis sélectionnez les propriétés que vous souhaitez simuler.

      ![Capture d’écran montrant le volet « Test » avec la liste « Sélectionner les champs facultatifs » ouverte.](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. Quand vous êtes prêt, sélectionnez **Terminé**.

   En haut à droite de l’action, la barre de titre affiche maintenant une icône de bécher de laboratoire (![icône du résultat statique](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)) qui indique que vous avez activé les résultats statiques.

   ![Capture d’écran montrant une action avec l’icône de résultat statique.](./media/test-logic-apps-mock-data-static-results/static-result-enabled.png)

   Pour rechercher les exécutions du flux de travail qui utilisent des données fictives, consultez [Rechercher les exécutions qui utilisent des résultats statiques](#find-runs-mock-data) plus loin dans cette rubrique.

### <a name="standard"></a>[Standard](#tab/standard)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez le flux de travail de votre application logique dans le concepteur.

1. Dans le concepteur, sélectionnez l’action pour laquelle vous souhaitez retourner les données fictives afin que le volet Détails de l’action s’affiche.

1. Une fois que le volet Détails de l’action s’ouvre sur le côté droit, sélectionnez **Test**.

   ![Capture d’écran montrant le portail Azure, le concepteur de flux de travail, le volet Détails de l’action et « Test » sélectionné.](./media/test-logic-apps-mock-data-static-results/select-testing-standard.png)

1. Dans l’onglet **Test**, sélectionnez **Activer le résultat statique (préversion)** . Quand les propriétés requises (*) de l’action s’affichent, spécifiez les valeurs de sortie fictive que vous souhaitez retourner comme réponse de l’action.

   Les propriétés varient en fonction du type d’action sélectionné. Par exemple, l’action HTTP a les propriétés obligatoires suivantes :

   | Propriété | Description |
   |----------|-------------|
   | **État** | État de l’action à renvoyer |
   | **Code d’état** | Code d’état spécifique à renvoyer |
   | **En-têtes** | Contenu d’en-tête à renvoyer |
   |||

   ![Capture d’écran montrant l’onglet « Test » après avoir sélectionné « Activer le résultat statique ».](./media/test-logic-apps-mock-data-static-results/enable-static-result-standard.png)

   > [!TIP]
   > Pour entrer les données au format JavaScript Objet Notation (JSON), choisissez **Basculer en mode JSON** (![icône « Basculer en mode JSON »](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)).

1. Pour les propriétés facultatives, ouvrez la liste **Sélectionner des champs facultatifs**, puis sélectionnez les propriétés que vous souhaitez simuler.

   ![Capture d’écran montrant le volet « Test » avec la liste « Sélectionner les champs facultatifs » ouverte.](./media/test-logic-apps-mock-data-static-results/optional-properties-standard.png)

1. Quand vous êtes prêt, sélectionnez **Terminé**.

   En bas à droite de l’action s’affiche désormais une icône de bécher de laboratoire (![icône du résultat statique](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)) qui indique que vous avez activé les résultats statiques.

   ![Capture d’écran montrant une action avec l’icône de résultat statique sur le concepteur.](./media/test-logic-apps-mock-data-static-results/static-result-enabled-standard.png)

   Pour rechercher les exécutions du flux de travail qui utilisent des données fictives, consultez [Rechercher les exécutions qui utilisent des résultats statiques](#find-runs-mock-data) plus loin dans cette rubrique.

---

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-mock-data"></a>Rechercher les exécutions qui utilisent des données fictives

### <a name="consumption"></a>[Consommation](#tab/consumption)

Pour rechercher les exécutions du flux de travail antérieures dans lesquelles des actions utilisent des données fictives, passez en revue l’historique des exécutions du flux de travail.

1. Dans le [portail Azure](https://portal.azure.com), ouvrez le flux de travail de votre application logique dans le concepteur.

1. Dans le menu de ressources de votre application logique, sélectionnez **Vue d’ensemble**.

1. Dans la section **Essentials**, sélectionnez **Historique des exécutions** s’il n’est pas déjà sélectionné.

1. Dans le tableau **Historique des exécutions**, recherchez la colonne **Résultats statiques**.

   Toute exécution qui inclut des actions avec des données fictives à la colonne **Résultats statiques** définie sur **Activée**, par exemple :

   ![Capture d’écran montrant l’historique des exécutions du flux de travail avec la colonne « Résultats statiques ».](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. Pour afficher les actions qui utilisent des données fictives, sélectionnez l’exécution que vous souhaitez avec la colonne **Résultats statiques** définie sur **Activée**.

   Les actions qui utilisent les résultats statiques affichent le bécher de laboratoire (![icône du résultat statique](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)), par exemple :

   ![Capture d’écran montrant l’historique des exécutions du flux de travail avec des actions utilisant un résultat statique.](./media/test-logic-apps-mock-data-static-results/static-result-enabled-run-details.png)

### <a name="standard"></a>[Standard](#tab/standard)

Pour rechercher d’autres exécutions du flux de travail où les actions utilisent des données fictives, vous devez vérifier chaque exécution.

1. Dans le [portail Azure](https://portal.azure.com), ouvrez le flux de travail de votre application logique dans le concepteur.

1. Dans le menu du flux de travail, sélectionnez **Vue d’ensemble**.

1. Dans la section **Essentials**, sélectionnez **Historique des exécutions** s’il n’est pas déjà sélectionné.

1. Dans le tableau **Historique des exécutions**, sélectionnez l’exécution que vous souhaitez passer en revue.

   ![Capture d’écran montrant l’historique des exécutions du flux de travail.](./media/test-logic-apps-mock-data-static-results/select-run-standard.png)

1. Dans le volet Détails de l’exécution, vérifiez si des actions affichent le bécher de laboratoire (![icône du résultat statique](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)), par exemple :

   ![Capture d’écran montrant l’historique des exécutions du flux de travail avec des actions utilisant un résultat statique.](./media/test-logic-apps-mock-data-static-results/run-history-static-result-standard.png)

---

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs-as-mock-data"></a>Réutiliser les sorties précédentes en tant que données fictives

Si vous avez une précédente exécution du flux de travail avec des sorties, vous pouvez réutiliser ces sorties en tant que données fictives en effectuant un copier-coller de ces sorties à partir de cette exécution.

### <a name="consumption"></a>[Consommation](#tab/consumption)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez le flux de travail de votre application logique dans le concepteur.

1. Dans le menu de ressources de votre application logique, sélectionnez **Vue d’ensemble**.

1. Dans la section **Essentials**, sélectionnez **Historique des exécutions** s’il n’est pas déjà sélectionné. Dans la liste qui apparaît, sélectionnez l’exécution du flux de travail souhaité.

   ![Capture d’écran montrant l’historique des exécutions du flux de travail.](./media/test-logic-apps-mock-data-static-results/select-run.png)

1. Une fois le volet Détails de l’exécution ouvert, développez l’action qui contient les sorties souhaitées.

1. Dans la section **Sorties**, sélectionnez **Afficher les sorties brutes**.

1. Dans le volet **Sorties**, copiez soit l’objet JSON (JavaScript Objet Notation) complet, soit la sous-section spécifique que vous souhaitez utiliser, par exemple, la section des sorties ou tout simplement la section des en-têtes.

1. Consultez la section précédente expliquant comment [configurer des données fictives](#enable-mock-data) pour une action et suivez les instructions pour ouvrir le volet **Test** de l’action.

1. Une fois le volet **Test** ouvert, choisissez l’une des options suivantes :

   * Pour coller un objet JSON complet, à côté de l’étiquette **Test**, sélectionnez **Basculer en mode JSON** (![icône « Basculer en mode JSON » ](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)) :

     ![Capture d’écran montrant l’icône « Basculer en mode JSON » sélectionnée pour coller l’objet JSON complet.](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Pour coller uniquement une section JSON, à côté de l’étiquette de cette section, par exemple **Sorties** ou **En-têtes**, sélectionnez **Basculer en mode JSON**, par exemple :

     ![Capture d’écran montrant l’icône « Basculer en mode JSON » sélectionnée pour coller une section à partir d’un objet JSON.](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-output.png)

1. Dans l’éditeur JSON, collez votre élément JSON copié précédemment.

   ![Capture d’écran montrant la section JSON collée dans l’éditeur.](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. Quand vous avez terminé, cliquez sur **Terminé**. Ou bien sélectionnez **Basculer en mode Éditeur** (![Icône pour « Basculer en mode Éditeur »](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)) pour revenir au concepteur.

### <a name="standard"></a>[Standard](#tab/standard)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez le flux de travail de votre application logique dans le concepteur.

1. Dans le menu du flux de travail, sélectionnez **Vue d’ensemble**.

1. Dans la section **Essentials**, sélectionnez **Historique des exécutions** s’il n’est pas déjà sélectionné.

1. Dans le tableau **Historique des exécutions**, sélectionnez l’exécution que vous souhaitez passer en revue.

   ![Capture d’écran montrant l’historique des exécutions du flux de travail.](./media/test-logic-apps-mock-data-static-results/select-run-standard.png)

1. Une fois le volet Détails de l’exécution ouvert, sélectionnez l’action qui contient les sorties souhaitées.

1. Dans la section **Sorties**, sélectionnez **Afficher les sorties brutes**.

1. Dans le volet **Sorties**, copiez soit l’objet JSON (JavaScript Objet Notation) complet, soit la sous-section spécifique que vous souhaitez utiliser, par exemple, la section des sorties ou tout simplement la section des en-têtes.

1. Consultez la section précédente expliquant comment [configurer des données fictives](#enable-mock-data) pour une action et suivez les instructions pour ouvrir l’onglet **Test** de l’action.

1. Une fois l’onglet **Test** ouvert, choisissez l’une des options suivantes :

   * Pour coller un objet JSON complet, à côté de l’étiquette **Test**, sélectionnez **Basculer en mode JSON** (![icône « Basculer en mode JSON » ](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)) :

     ![Capture d’écran montrant l’icône « Basculer en mode JSON » sélectionnée pour coller l’objet JSON complet.](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete-standard.png)

   * Pour coller uniquement une section JSON, à côté de l’étiquette de cette section, par exemple **Sorties** ou **En-têtes**, sélectionnez **Basculer en mode JSON**, par exemple :

     ![Capture d’écran montrant l’icône « Basculer en mode JSON » sélectionnée pour coller une section à partir d’un objet JSON.](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-output-standard.png)

1. Dans l’éditeur JSON, collez votre élément JSON copié précédemment.

   ![Capture d’écran montrant la section JSON collée dans l’éditeur.](./media/test-logic-apps-mock-data-static-results/json-editing-mode-standard.png)

1. Quand vous avez terminé, cliquez sur **Terminé**. Ou bien sélectionnez **Basculer en mode Éditeur** (![Icône pour « Basculer en mode Éditeur »](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)) pour revenir au concepteur.

---

## <a name="disable-mock-data"></a>Désactiver les données fictives

La désactivation des résultats statiques sur une action ne supprime pas les valeurs de votre dernière installation. Par conséquent, si vous réactivez le résultat statique sur la même action, vous pouvez continuer à utiliser les valeurs précédentes.

### <a name="consumption"></a>[Consommation](#tab/consumption)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez le flux de travail de votre application logique dans le concepteur. Recherchez l’action pour laquelle vous souhaitez désactiver les données fictives.

1. En haut à droite de l’action, sélectionnez l’icône de bécher de laboratoire (![icône du résultat statique](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)).

   ![Capture d’écran montrant l’action et l’icône du bécher de laboratoire sélectionnée.](./media/test-logic-apps-mock-data-static-results/disable-static-result.png)

1. Sélectionnez **Désactiver le résultat statique** > **Terminé**.

   ![Capture d’écran montrant l’option « Désactiver les résultats statiques » sélectionnée.](./media/test-logic-apps-mock-data-static-results/disable-static-result-button.png)

### <a name="standard"></a>[Standard](#tab/standard)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez le flux de travail de votre application logique dans le concepteur. Sélectionnez l’action pour laquelle vous souhaitez désactiver les données fictives.

1. Dans le volet Détails de l’action, sélectionnez l’onglet **Test**.

1. Sélectionnez **Désactiver le résultat statique** > **Terminé**.

   ![Capture d’écran montrant l’option « Désactiver les résultats statiques » sélectionnée pour Standard.](./media/test-logic-apps-mock-data-static-results/disable-static-result-button-standard.png)

---

## <a name="reference"></a>Informations de référence

Pour plus d’informations sur ce paramètre dans vos définitions de flux de travail sous-jacent, consultez [Schema reference for Workflow Definition Language in Azure Logic Apps](logic-apps-workflow-definition-language.md#static-results) (Référence de schéma du langage de définition de flux de travail dans Azure Logic Apps) et [runtimeConfiguration.staticResult - Runtime configuration settings](logic-apps-workflow-actions-triggers.md#runtime-configuration-settings) (runtimeConfiguration.staticResult - paramètres de configuration du runtime)

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [Azure Logic Apps](logic-apps-overview.md)