---
title: Générer des workflows automatisés basés sur la planification
description: Tutoriel – Créer un workflow automatisé périodique basé sur la planification à l’aide d’Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/12/2019
ms.openlocfilehash: 41f7b1309a9c7fa9a5f2abb3e2e59f08ef31382d
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87124848"
---
# <a name="tutorial-create-automated-schedule-based-recurring-workflows-by-using-azure-logic-apps"></a>Tutoriel : Créer des workflows automatisés périodiques basés sur la planification à l’aide d’Azure Logic Apps

Ce tutoriel montre comment générer une [application logique](../logic-apps/logic-apps-overview.md) et automatiser un workflow périodique qui s’exécute selon une planification. Plus précisément, cet exemple d’application logique s’exécute tous les matins de la semaine et vérifie le temps de trajet, en tenant compte des conditions de circulation, entre deux points. Si la durée dépasse une limite définie, l’application logique envoie un e-mail contenant la durée du trajet et le temps supplémentaire nécessaire pour atteindre votre destination.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créez une application logique vide.
> * Ajoutez un déclencheur de périodicité qui spécifie la planification de votre application logique.
> * Ajoutez une action Bing Cartes qui permet d’obtenir le temps de trajet pour un itinéraire.
> * Ajoutez une action qui crée une variable, convertit le temps de trajet de secondes en minutes, puis stocke ce résultat dans la variable.
> * Ajoutez une condition qui compare la durée du trajet avec une limite définie.
> * Ajoutez une action qui vous envoie un e-mail si le temps de trajet dépasse la limite.

Lorsque vous avez terminé, votre application logique ressemble au flux de travail suivant à un niveau élevé :

![Vue d’ensemble globale du workflow d’application logique](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement, [créez un compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.

* Un compte de messagerie d’un fournisseur de messagerie pris en charge par Logic Apps, par exemple Office 365 Outlook, Outlook.com ou Gmail. Pour les autres fournisseurs, [passez en revue la liste des connecteurs ici](/connectors/). Ce guide de démarrage rapide utilise un compte Office 365 Outlook. Si vous utilisez un autre compte de messagerie, les étapes générales sont identiques, mais l’interface utilisateur peut être légèrement différente.

  > [!IMPORTANT]
  > Si vous souhaitez utiliser le connecteur Gmail, seuls les comptes professionnels G-Suite peuvent utiliser ce connecteur sans restriction dans Logic Apps. Si vous disposez d’un compte de consommateur Gmail, vous pouvez utiliser ce connecteur uniquement avec certains services approuvés par Google, ou vous pouvez [créer une application cliente Google pour servir lors de l’authentification avec votre connecteur Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Pour plus d’informations, consultez [Stratégies de confidentialité et de sécurité des données pour les connecteurs Google dans Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Pour obtenir la durée de trajet d’un itinéraire, vous avez besoin d’une clé d’accès pour l’API Bing Cartes. Pour obtenir cette clé, suivez les étapes décrites dans l’article indiquant [comment obtenir une clé Bing Cartes](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key).

## <a name="create-your-logic-app"></a>Créer votre application logique

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec les informations d’identification de votre compte Azure.

1. Dans le menu principal Azure, choisissez **Créer une ressource** > **Intégration** > **Application logique**.

   ![Créer votre ressource d’application logique](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. Sous **Créer une application logique**, indiquez les informations suivantes sur votre application logique comme illustré et décrit. Sélectionnez **Créer** lorsque vous avez terminé.

   ![Fournir des informations sur votre application logique](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Propriété | Valeur | Description |
   |----------|-------|-------------|
   | **Nom** | LA-TravelTime | Nom de votre application logique, qui peut contenir uniquement des lettres, des chiffres, des traits d’union (`-`), des traits de soulignement (`_`), des parenthèses (`(`, `)`) et des points (`.`). Cet exemple utilise « LA-TravelTime ». |
   | **Abonnement** | <*your-Azure-subscription-name*> | Le nom de votre abonnement Azure |
   | **Groupe de ressources** | LA-TravelTime-RG | Nom du [groupe de ressources Azure](../azure-resource-manager/management/overview.md), qui est utilisé pour organiser les ressources connexes. Cet exemple utilise « LA-TravelTime-RG ». |
   | **Lieu** | USA Ouest | Région dans laquelle stocker les informations sur votre application logique. Cet exemple utilise la région « USA Ouest ». |
   | **Log Analytics** | Off | Maintenez le paramètre de journalisation des diagnostics **Désactivé**. |
   ||||

1. Une fois qu’Azure a déployé votre application, dans la barre d’outils Azure, sélectionnez **Notifications** > **Accéder à la ressource** pour votre application logique déployée.

   ![Accéder à votre nouvelle ressource d’application logique](./media/tutorial-build-scheduled-recurring-logic-app-workflow/go-to-new-logic-app-resource.png)

   Ou bien, vous pouvez rechercher et sélectionner votre application logique en tapant son nom dans la zone de recherche.

   Le Concepteur Logic Apps s’ouvre et affiche une page contenant une vidéo de présentation et des déclencheurs et autres modèles d’application logique couramment utilisés. Sous **Modèles**, sélectionnez **Application logique vide**.

   ![Sélectionner un modèle d’application logique vide](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Ajoutez ensuite le [déclencheur](../logic-apps/logic-apps-overview.md#logic-app-concepts) de périodicité qui s’active selon une planification spécifiée. Chaque application logique doit commencer par un déclencheur, qui est activé lorsqu’un événement spécifique se produit ou lorsque de nouvelles données respectent une condition particulière. Pour plus d’informations, consultez [Quickstart: Build your first logic app workflow - Azure portal](../logic-apps/quickstart-create-first-logic-app-workflow.md) (Démarrage rapide : Générer votre premier flux de travail d’application logique - portail Azure).

## <a name="add-the-recurrence-trigger"></a>Ajouter le déclencheur de périodicité

1. Dans le Concepteur Logic Apps, dans la zone de recherche, entrez « périodicité » en guise de filtre. Dans la liste **Déclencheurs**, sélectionnez le déclencheur **Périodicité**.

   ![Déclencheur Récurrence](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. Dans la forme **Périodicité**, sélectionnez le bouton représentant des **points de suspension** ( **...** ), puis **Renommer**. Renommez le déclencheur à l’aide de cette description : `Check travel time every weekday morning`.

   ![Renommer la description du déclencheur de périodicité](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. Dans le déclencheur, modifiez ces propriétés.

   ![Modifier l’intervalle et la fréquence du déclencheur de périodicité](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Propriété | Obligatoire | Valeur | Description |
   |----------|----------|-------|-------------|
   | **Intervalle** | Oui | 1 | Nombre d’intervalles d’attente entre les vérifications. |
   | **Fréquence** | Oui | Week | Unité de temps à utiliser pour la périodicité. |
   |||||

1. Sous **Intervalle** et **Fréquence**, ouvrez la liste **Ajouter un nouveau paramètre**, puis sélectionnez ces propriétés pour ajouter le déclencheur.

   * **Aux jours indiqués**
   * **Aux heures indiquées**
   * **Aux minutes indiquées**

   ![Ajouter des propriétés pour le déclencheur de périodicité](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. À présent, définissez les valeurs des propriétés supplémentaires comme indiqué et décrit ici.

   ![Fournir les détails de planification et de périodicité](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | Propriété | Valeur | Description |
   |----------|-------|-------------|
   | **Aux jours indiqués** | Lundi,mardi,mercredi,jeudi,vendredi | Disponible uniquement lorsque le paramètre **Fréquence** est défini sur « Semaine ». |
   | **Aux heures indiquées** | 7,8,9 | Disponible uniquement lorsque le paramètre **Fréquence** est défini sur « Semaine » ou « Jour ». Sélectionnez les heures de la journée pour exécuter cette périodicité. Cet exemple s’exécute aux marques de 7, 8 et 9 heures. |
   | **Aux minutes indiquées** | 0,15,30,45 | Disponible uniquement lorsque le paramètre **Fréquence** est défini sur « Semaine » ou « Jour ». Sélectionnez les minutes de la journée pour exécuter cette périodicité. Cet exemple s’exécute toutes les 15 minutes à partir de la marque zéro heure. |
   ||||

   Ce déclencheur s’active tous les jours ouvrables, toutes les 15 minutes, de à 7 h 00 à 9 h 45. La zone **Aperçu** indique la planification de la périodicité. Pour plus d’informations, voir [Planifier des tâches et flux de travail à exécuter régulièrement avec des applications logiques](../connectors/connectors-native-recurrence.md) et [Actions et déclencheurs de flux de travail](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger).

1. Pour masquer les informations du déclencheur pour le moment, cliquez dans la barre de titre de la forme.

   ![Réduire la forme pour masquer les informations](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. Enregistrez votre application logique. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

Votre application logique est à présent active, mais elle s’exécute uniquement en fonction d’une périodicité. Par conséquent, ajoutez une action qui répond à l’activation du déclencheur.

## <a name="get-the-travel-time-for-a-route"></a>Obtenir la durée du trajet d’un itinéraire

Maintenant que vous disposez d’un déclencheur, ajoutez une [action](../logic-apps/logic-apps-overview.md#logic-app-concepts) qui permet de récupérer la durée du trajet entre deux lieux. Logic Apps fournit un connecteur pour l’API Bing Cartes afin que vous puissiez accéder facilement à ces informations. Avant de commencer cette tâche, vérifiez que vous disposez d’une clé de l’API Bing Cartes comme décrit dans les conditions préalables requises de ce didacticiel.

1. Dans le Concepteur Logic App, sous le déclencheur, sélectionnez **Nouvelle étape**.

1. Sous **Choisir une action**, sélectionnez **Standard**. Dans la zone de recherche, entrez « bing cartes » en guise de filtre, puis sélectionnez l’action **Get route** (Obtenir l’itinéraire).

   ![Sélectionner l’action « Get route » (Obtenir l’itinéraire)](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Si vous ne disposez pas d’une connexion à Bing Cartes, vous êtes invité à en créer une. Fournissez ces informations de connexion, puis sélectionnez **Créer**.

   ![Créer une connexion à l’API Bing Maps](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Propriété | Obligatoire | Valeur | Description |
   |----------|----------|-------|-------------|
   | **Nom de connexion** | Oui | BingMapsConnection | Donnez un nom à votre connexion. Cet exemple utilise « BingMapsConnection ». |
   | **Clé API** | Oui | <*your-Bing-Maps-key*> | Entrez la clé Bing Cartes que vous avez reçue précédemment. Si vous ne possédez pas une clé Bing Cartes, découvrez [comment en obtenir une](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key). |
   |||||

1. Renommez l’action à l’aide de cette description : `Get route and travel time with traffic`.

1. Dans l’action, ouvrez la liste **Ajouter un nouveau paramètre**, puis sélectionnez ces propriétés pour les ajouter à l’action.

   * **Optimize**
   * **Unité de distance**
   * **Mode de déplacement**

   ![Ajouter des propriétés à l’action « Get route » (Obtenir l’itinéraire)](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. À présent, définissez les valeurs des propriétés de l’action comme indiqué et décrit ici.

   ![Fournissez les détails de l’action « Get route » (Obtenir l’itinéraire)](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Propriété | Obligatoire | Valeur | Description |
   |----------|----------|-------|-------------|
   | **Étape 1** | Oui | <*start-location*> | Début de l’itinéraire. |
   | **Étape 2** | Oui | <*end-location*> | Destination de l’itinéraire. |
   | **Optimize** | Non | timeWithTraffic | Paramètre permettant d’optimiser votre itinéraire, par exemple la distance, la durée du trajet avec le trafic en cours, etc. Sélectionnez le paramètre « timeWithTraffic ». |
   | **Unité de distance** | Non | <*your-preference*> | Unité de distance utilisé pour l’itinéraire. Cet exemple utilise « Mile » comme unité. |
   | **Mode de déplacement** | Non | Conduite | Mode de déplacement pour parcourir l’itinéraire. Sélectionnez le mode « Driving » (Conduite). |
   ||||

   Pour plus d’informations sur ces paramètres, voir [Calculate a route (Calculer un itinéraire)](/bingmaps/rest-services/routes/calculate-a-route).

1. Enregistrez votre application logique.

Créez une variable afin de pouvoir convertir et stocker la durée du trajet actuel en minutes plutôt qu’en secondes. De cette façon, vous pouvez éviter de répéter la conversion et utiliser la valeur plus facilement dans les étapes ultérieures. 

## <a name="create-a-variable-to-store-travel-time"></a>Créer une variable pour stocker le temps de trajet

Vous souhaiterez parfois exécuter des opérations sur les données de votre workflow et utiliser les résultats dans des actions ultérieures. Pour enregistrer ces résultats afin de pouvoir facilement les réutiliser ou y faire référence, vous pouvez créer des variables pour stocker ces résultats après les avoir traités. Vous ne pouvez créer des variables qu’au niveau supérieur de votre application logique.

Par défaut, l’action **Get route** (Obtenir l’itinéraire) précédente retourne le temps de trajet actuel avec le trafic exprimé en secondes à partir de la propriété **Travel Duration Traffic** (Trafic correspondant au temps de trajet). Si vous préférez convertir et stocker cette valeur en minutes, vous facilitez la réutilisation ultérieure de la valeur sans la reconvertir.

1. Sous l’action **Get route** (Obtenir l’itinéraire), sélectionnez **Nouvelle étape**.

1. Sous **Choisir une action**, sélectionnez **Intégré**. Dans la zone de recherche, entrez « variables », puis sélectionnez l’action **Initialiser la variable**.

   ![Sélectionner l’action « Initialiser la variable »](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Renommez l’action à l’aide de cette description : `Create variable to store travel time`.

1. Fournissez les détails de la variable, comme décrit ici :

   | Propriété | Obligatoire | Valeur | Description |
   |----------|----------|-------|-------------|
   | **Nom** | Oui | travelTime | Nom de la variable. Cet exemple utilise « travelTime ». |
   | **Type** | Oui | Integer | Type de données de la variable. |
   | **Valeur** | Non| Expression qui convertit la durée du trajet actuel de secondes en minutes (voir les étapes décrites sous ce tableau). | Valeur initiale de votre variable. |
   ||||

   1. Pour créer l’expression pour la propriété **Valeur**, cliquez dans la zone pour faire apparaître la liste à contenu dynamique. Si nécessaire, élargissez la fenêtre du navigateur jusqu’à ce que la liste apparaisse. Dans la liste à contenu dynamique, sélectionnez **Expression**.

      ![Fournir les informations pour l’action « Initialiser la variable »](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      Quand vous cliquez dans certaines zones d’édition, la liste à contenu dynamique s’affiche. Cette liste affiche les propriétés d’actions précédentes que vous pouvez utiliser comme entrées dans votre workflow. La liste à contenu dynamique compte un éditeur d’expressions dans lequel vous pouvez sélectionner des fonctions pour exécuter des opérations. Cet éditeur d’expressions s’affiche uniquement dans la liste de contenu dynamique.

   1. Dans l’éditeur d’expressions, entrez cette expression : `div(,60)`.

      ![Entrer cette expression : « div(,60) »](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. Placez votre curseur à l’intérieur de l’expression entre la parenthèse gauche ( **(** ) et la virgule ( **,** ). 
   Sélectionnez **Contenu dynamique**.

      ![Positionner le curseur, sélectionner « Contenu dynamique »](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. Dans la liste de contenu dynamique, sélectionnez **Travel Duration Traffic (Trafic correspondant à la durée du trajet)** .

      ![Sélectionner la propriété « Travel Duration Traffic » (Trafic correspondant au temps de trajet)](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. Une fois la valeur de la propriété résolue dans l’expression, sélectionnez **OK**.

      ![Pour terminer la création de l’expression, sélectionner « OK »](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      La propriété **Valeur** apparaît à présent comme indiqué ici :

      ![La propriété « Valeur » s’affiche avec une expression résolue](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Enregistrez votre application logique.

Ajoutez une condition qui vérifie si la durée du trajet actuel est supérieure à une limite définie.

## <a name="compare-the-travel-time-with-limit"></a>Comparer le temps de trajet à une limite

1. Sous l’action précédente, sélectionnez **Nouvelle étape**.

1. Sous **Choisir une action**, sélectionnez **Intégré**. Dans la zone de recherche, entrez « condition » comme filtre. Dans la liste des actions, sélectionnez l’action **Condition**.

   ![Sélectionner l’action « Condition »](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Renommez la condition à l’aide de cette description : `If travel time exceeds limit`.

1. Générez une condition qui vérifie si la valeur de la propriété **travelTime** dépasse la limite spécifiée comme décrit et indiqué ici :

   1. Dans la condition, cliquez sur la zone **Choisir une valeur** située à gauche de la condition.

   1. Dans la liste de contenu dynamique qui s’affiche, sous **Variables**, sélectionnez la propriété **travelTime**.

      ![Créer le côté gauche de la condition](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. Dans la zone de comparaison du milieu, sélectionnez l’opérateur **est supérieur à**.

   1. Dans la zone **Choisir une valeur** dans la partie gauche de la condition, entrez cette limite : `15`

      Quand vous avez terminé, votre condition ressemble à cet exemple :

      ![Condition de vérification du temps de trajet terminée](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Enregistrez votre application logique.

Ajoutez ensuite l’action à exécuter quand le temps de trajet dépasse la limite.

## <a name="send-email-when-limit-exceeded"></a>Envoyer un e-mail en cas de dépassement de la limite

Ajoutez une action qui vous avertit par e-mail lorsque la durée du trajet dépasse la limite que vous avez définie. Cet e-mail inclut la durée du trajet actuel et le temps supplémentaire nécessaire pour parcourir l’itinéraire spécifié.

1. Dans la branche **Si true** de la condition, sélectionnez **Ajouter une action**.

1. Sous **Choisir une action**, sélectionnez **Standard**. Dans la zone de recherche, entrez « envoyer un e-mail ». Comme la liste retourne un grand nombre de résultats, sélectionnez d’abord le connecteur de messagerie qui vous intéresse, par exemple :

   ![Sélectionner le connecteur de messagerie électronique souhaité](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * Pour les comptes Azure professionnels ou scolaires, sélectionnez **Office 365 Outlook**.
   * Pour les comptes Microsoft personnels, sélectionnez **Outlook.com**.

1. Quand les actions du connecteur apparaissent, sélectionnez l’« action envoyer un e-mail » que vous souhaitez utiliser, par exemple :

   ![Sélectionner l’action « Envoyer un courrier électronique »](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Si vous ne disposez pas déjà d’une connexion, vous êtes invité à vous connecter à votre compte de messagerie.

   Logic Apps crée une connexion à votre compte de messagerie.

1. Renommez l’action à l’aide de cette description : `Send email with travel time`.

1. Dans la zone **À**, entrez l’adresse e-mail du destinataire. À des fins de test, utilisez votre adresse e-mail.

1. Dans la zone **Objet**, spécifiez l’objet de l’e-mail, puis insérez la variable **travelTime**.

   1. Entrez le texte `Current travel time (minutes):` avec un espace de fin. 

   1. Dans la liste à contenu dynamique, sous **Variables**, sélectionnez **Afficher plus**.

      ![Rechercher la variable « travelTime »](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

   1. Dès que **travelTime** apparaît sous **Variables**, sélectionnez **travelTime**.

      ![Entrer le texte de l’objet et l’expression qui retourne la durée du trajet](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. Dans la zone **Corps**, spécifiez le contenu du corps de l’e-mail.

   1. Entrez le texte `Add extra travel time (minutes):` avec un espace de fin.

   1. Dans la liste à contenu dynamique, sélectionnez **Expression**.

      ![Générer une expression pour le corps de l’e-mail](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. Dans l’éditeur d’expressions, entrez cette expression afin que vous puissiez calculer le nombre de minutes au-delà de la limite que vous avez définie : ```sub(,15)```.

      ![Entrer une expression pour calculer les minutes au-delà de la durée du trajet](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. Placez votre curseur à l’intérieur de l’expression entre la parenthèse gauche ( **(** ) et la virgule ( **,** ). Sélectionnez **Contenu dynamique**.

      ![Poursuivre la création de l’expression pour calculer les minutes au-delà de la durée du trajet](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. Sous **Variables**, sélectionnez **travelTime**.

      ![Sélectionner la propriété « travelTime » à utiliser dans l’expression](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. Une fois la propriété résolue dans l’expression, sélectionnez **OK**.

      ![Après la résolution de la propriété « Corps », sélectionner « OK ».](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      La propriété **Corps** apparaît à présent comme indiqué ici :

      ![Propriété « Corps » résolue dans l’expression](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Enregistrez votre application logique.

Ensuite, testez votre application logique, qui ressemble désormais à l’exemple suivant :

![Exemple de workflow d’application logique terminée](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Exécuter votre application logique

Pour lancer manuellement votre application logique, sélectionnez **Exécuter** dans la barre d’outils du concepteur.

* Si le temps de trajet actuel reste en dessous de la limite que vous avez définie, votre application logique ne fait rien d’autre et attend l’intervalle suivant avant de procéder à une nouvelle vérification. 

* Mais si le temps de trajet actuel dépasse la limite que vous avez définie, vous obtenez un e-mail indiquant le temps de trajet actuel et le nombre de minutes au-dessus de la limite définie. Voici un exemple d’e-mail envoyé par votre application logique :

![Exemple d’e-mail envoyé qui indique le temps de trajet](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

Si vous ne recevez pas d’e-mail, vérifiez le dossier Courrier indésirable de votre messagerie. Il se peut que le filtre de courrier indésirable redirige ces types d’e-mails. Sinon, si vous ne savez pas si votre application logique s’est correctement exécutée, consultez [Dépanner votre application logique](../logic-apps/logic-apps-diagnosing-failures.md).

Félicitations ! Vous venez de créer et d’exécuter une application logique périodique basée sur une planification. 

Pour créer d’autres applications logiques qui utilisent le déclencheur **Périodicité**, découvrez ces modèles, qui sont disponibles après que vous avez créé une application logique :

* Recevez des rappels quotidiens.
* Supprimez d’anciens objets blob Azure.
* Ajoutez un message à une file d’attente Stockage Azure.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’avez plus besoin de l’exemple d’application logique, supprimez le groupe de ressources qui contient votre application logique et les ressources associées. 

1. Dans le menu Azure principal, accédez à **Groupes de ressources**, puis sélectionnez le groupe de ressources de votre application logique.

1. Dans le menu de groupe de ressources, sélectionnez **Vue d’ensemble** > **Supprimer un groupe de ressources**. 

   ![« Vue d’ensemble » > « Supprimer un groupe de ressources »](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. Confirmez le nom du groupe de ressources, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé une application logique qui vérifie le trafic selon une planification spécifiée (les matins des jours ouvrables), et effectue une action (envoi d’un e-mail) lorsque la durée du trajet excède une limite définie. À présent, découvrez comment créer une application logique qui envoie des demandes de liste de diffusion pour approbation en intégrant des services Azure et Microsoft et d’autres applications SaaS.

> [!div class="nextstepaction"]
> [Gérer les demandes de liste de diffusion](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
