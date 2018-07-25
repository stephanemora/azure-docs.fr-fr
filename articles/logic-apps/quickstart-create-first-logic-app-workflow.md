---
title: Créer et automatiser votre premier flux de travail - Azure Logic Apps| Microsoft Docs
description: Guide de démarrage rapide pour créer votre première application logique pour automatiser des tâches, des processus et des flux de travail avec Azure Logic Apps. Créer des applications logiques pour des scénarios d’intégration des systèmes et des Applications d’Entreprise (IAE) pour vos systèmes et services cloud
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: quickstart
ms.custom: mvc
ms.date: 07/20/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 4774efda6748ac640d87ce83e2d5c4ee68310546
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125737"
---
# <a name="quickstart-create-your-first-automated-workflow-with-azure-logic-apps---azure-portal"></a>Démarrage rapide : créer votre premier flux de travail automatisé avec Azure Logic Apps - portail Azure

Ce guide de démarrage rapide explique comment créer votre premier flux de travail automatisé avec [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Dans cet article, vous créez une application logique qui vérifie régulièrement le flux RSS d’un site web et recherche de nouveaux éléments. Si de nouveaux éléments existent, l’application logique envoie un e-mail pour chacun d’eux. Lorsque vous avez terminé, votre application logique ressemble au flux de travail suivant à un niveau élevé :

![Vue d’ensemble - Exemple d’application logique](./media/quickstart-create-first-logic-app-workflow/overview.png)

Pour suivre ce guide de démarrage rapide, vous avez besoin d’un compte de messagerie d’un fournisseur pris en charge par Logic Apps, par exemple Office 365 Outlook, Outlook.com ou Gmail. Pour les autres fournisseurs, [passez en revue la liste des connecteurs ici](https://docs.microsoft.com/connectors/). Cette application logique utilise un compte Office 365 Outlook. Si vous utilisez un autre compte de messagerie, les étapes générales sont identiques, mais votre interface utilisateur peut-être légèrement différente. 

De même, si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au <a href="https://portal.azure.com" target="_blank">portail Azure</a> avec les informations d’identification de votre compte Azure.

## <a name="create-your-logic-app"></a>Créer votre application logique 

1. Dans le menu principal Azure, choisissez **Créer une ressource** > **Intégration** > **Application logique**.

   ![Créer une application logique](./media/quickstart-create-first-logic-app-workflow/create-logic-app.png)

3. Sous **Créer une application logique**, indiquez les détails de votre application logique comme indiqué ici. Lorsque c’est fait, choisissez **Épingler au tableau de bord** > **Créer**.

   ![Spécifier les détails de l’application logique](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Propriété | Valeur | Description | 
   |----------|-------|-------------| 
   | **Name** | MyFirstLogicApp | Nom de l’application logique. | 
   | **Abonnement** | <*your-Azure-subscription-name*> | Nom de votre abonnement Azure. | 
   | **Groupe de ressources** | My-First-LA-RG | Nom du [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) utilisé pour organiser les ressources connexes. | 
   | **Lieu** | USA Ouest | Région dans laquelle stocker les informations sur votre application logique. | 
   | **Log Analytics** | Off | Maintenez le paramètre de journalisation des diagnostics **Désactivé**. | 
   |||| 

3. Une fois qu’Azure a déployé votre application, le Concepteur d’applications logiques s’ouvre et affiche une page contenant une vidéo de présentation et les déclencheurs couramment utilisés. Sous **Modèles**, choisissez **Application logique vide**.

   ![Choisir un modèle d’application logique vide](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Ensuite, ajoutez un [déclencheur](../logic-apps/logic-apps-overview.md#logic-app-concepts) qui s’active lorsqu’un nouvel élément de flux RSS apparaît. Chaque application logique doit démarrer avec un déclencheur, qui s’active lorsqu’un événement spécifique se produit ou lorsqu’une condition particulière est remplie. Chaque fois que le déclencheur s’active, le moteur Logic Apps crée une instance d’application logique qui démarre et exécute votre flux de travail.

<a name="add-rss-trigger"></a>

## <a name="check-rss-feed-with-a-trigger"></a>Vérifier le flux RSS avec un déclencheur

1. Dans le concepteur, entrez « rss » dans la zone de recherche. Sélectionnez le déclencheur suivant : **RSS - Lors de la publication d’un élément de flux**

   ![Sélectionnez le déclencheur : « RSS - Lors de la publication d’un élément de flux »](./media/quickstart-create-first-logic-app-workflow/add-trigger-rss.png)

2. Fournissez ces informations pour votre déclencheur, comme illustré et décrit : 

   ![Définir un déclencheur avec le flux RSS, la fréquence et l’intervalle](./media/quickstart-create-first-logic-app-workflow/add-trigger-rss-settings.png)

   | Propriété | Valeur | Description | 
   |----------|-------|-------------| 
   | **URL du flux RSS** | ```http://feeds.reuters.com/reuters/topNews``` | Lien du flux RSS que vous souhaitez surveiller. | 
   | **Intervalle** | 1 | Nombre d’intervalles d’attente entre les vérifications. | 
   | **Fréquence** | Minute | Unité de temps de chaque intervalle entre les vérifications.  | 
   |||| 

   Ensemble, l’intervalle et la fréquence définissent la planification du déclencheur de votre application logique. 
   Cette application logique vérifie le flux toutes les minutes.

3. Pour masquer les informations du déclencheur pour le moment, cliquez dans sa barre de titre.

   ![Réduire la forme pour masquer les informations](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

4. Enregistrez votre application logique. Dans la barre d’outils du concepteur, choisissez **Enregistrer**. 

Votre application logique est à présent en ligne mais elle ne fait rien d’autre que vérifier le flux RSS. Par conséquent, ajoutez une action qui répond à l’activation du déclencheur.

## <a name="send-email-with-an-action"></a>Envoyer un e-mail à l’aide d’une action

Ajoutez une [action](../logic-apps/logic-apps-overview.md#logic-app-concepts) qui envoie un e-mail lorsqu’un nouvel élément apparaît dans le flux RSS. 

1. Sous le déclencheur **Lors de la publication d’un élément de flux**, choisissez **+ Nouvelle étape** > **Ajouter une action**.

   ![Ajouter une action](./media/quickstart-create-first-logic-app-workflow/add-new-action.png)

2. Sous **Choisir une action**, entrez « envoyer un e-mail » comme filtre. Dans la liste d’actions, sélectionnez l’action « envoyer un e-mail » pour le fournisseur de messagerie de votre choix. 

   ![Sélectionnez l’action : « Office 365 Outlook - Envoyer un e-mail »](./media/quickstart-create-first-logic-app-workflow/add-action-send-email.png)

   Pour filtrer la liste d’actions pour un service ou une application spécifique, vous pouvez commencer par sélectionner ce service ou cette application :

   * Pour les comptes Azure professionnels ou scolaires, sélectionnez Office 365 Outlook. 
   * Pour les comptes Microsoft personnels, sélectionnez Outlook.com.

3. Si vous êtes invité à entrer vos informations d’identification, connectez-vous à votre compte de messagerie afin que Logic Apps puisse établir une connexion avec votre compte de messagerie.

4. Dans l’action **Envoyer un e-mail**, spécifiez les données que vous souhaitez inclure dans l’e-mail. 

   1. Dans la zone **À**, entrez l’adresse e-mail du destinataire. 
   À des fins de test, vous pouvez utiliser votre propre adresse e-mail.

      Pour l’instant, ignorez la liste **Ajouter du contenu dynamique** qui s’affiche. 
      Lorsque vous cliquez dans certaines zones d’édition, cette liste apparaît et affiche les paramètres disponibles de l’étape précédente que vous pouvez inclure en tant qu’entrées dans votre flux de travail. 

   2. Dans la zone **Objet**, entrez ce texte avec un espace vide de fin : ```New RSS item: ```.

      ![Entrer l’objet du message électronique](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject.png)
 
   3. Dans la liste **Ajouter du contenu dynamique**, sélectionnez **Titre du flux** pour inclure le titre de l’élément RSS.

      ![Liste de contenu dynamique : « Titre du flux »](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject-dynamic-content.png)

      Lorsque vous avez terminé, l’objet de l’e-mail ressemble à l’exemple suivant :

      ![Titre du flux ajouté](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-feed-title.png)

      Si une boucle « For Each » s’affiche dans le concepteur, c’est que vous avez sélectionné un jeton qui contient un tableau, par exemple, le jeton **categories-Item**. 
      Pour ces types de jeton, le concepteur ajoute automatiquement cette boucle autour de l’action qui référence ce jeton. 
      De cette façon, votre application logique effectue la même action sur chaque élément du tableau. 
      Pour supprimer la boucle, choisissez le bouton **points de suspension** (**...**) sur la barre de titre de la boucle, puis **Supprimer**.

   4. Dans la zone **Corps**, entrez ce texte, puis sélectionnez ces jetons pour le corps de l’e-mail. 
   Pour ajouter des lignes vides dans une zone d’édition, appuyez sur Maj + Entrée. 

      ![Ajouter du contenu pour le corps de l’e-mail](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-body.png)

      | Propriété | Description | 
      |----------|-------------| 
      | **Titre du flux** | Titre de l’élément. | 
      | **Flux publié le** | Date et heure de publication de l’élément. | 
      | **Lien du flux principal** | URL de l’élément. | 
      ||| 
   
5. Enregistrez votre application logique.

À présent, testez votre application logique.

## <a name="run-your-logic-app"></a>Exécuter votre application logique

Pour lancer manuellement votre application logique, sélectionnez **Exécuter** dans la barre d’outils du concepteur. Vous pouvez également attendre que votre application logique vérifie le flux RSS selon la planification que vous avez spécifiée (toutes les minutes). Si le flux RSS a de nouveaux éléments, votre application logique envoie un e-mail pour chaque nouvel élément. Dans le cas contraire, votre application logique attend jusqu’à l’intervalle suivant avant de procéder à une nouvelle vérification. 

Par exemple, voici un e-mail classique envoyé par cette application logique. Si vous ne recevez aucun e-mail, vérifiez votre dossier Courrier indésirable.

![E-mail envoyé en raison d’un nouvel élément dans le flux RSS](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Techniquement, lorsque le déclencheur vérifie le flux RSS et trouve de nouveaux éléments, le déclencheur s’active et le moteur Logic Apps crée une instance de workflow d’application logique qui exécute les actions dans le workflow.
Si le déclencheur ne trouve pas de nouveaux éléments, il ne s’active pas et « ignore » l’instanciation du workflow.

Félicitations, vous avez correctement généré et exécuté votre première application logique avec le portail Azure !

## <a name="clean-up-resources"></a>Supprimer les ressources

Quand vous n’en avez plus besoin, supprimez le groupe de ressources qui contient votre application logique et les ressources associées. 

1. Dans le menu Azure principal, accédez à **Groupes de ressources**, puis sélectionnez le groupe de ressources de votre application logique. Dans la page **Vue d’ensemble**, choisissez **Supprimer un groupe de ressources**. 

   ![« Groupes de ressources » > « Vue d’ensemble » > « Supprimer un groupe de ressources »](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

2. Confirmez le nom du groupe de ressources, puis choisissez **Supprimer**.

   ![Confirmer la suppression](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé votre première application logique qui vérifie les mises à jour RSS en fonction de la planification que vous avez spécifiée (toutes les minutes) et exécute une action (envoi d’un e-mail) lorsqu’il existe des mises à jour. Pour en savoir plus, poursuivez ce didacticiel qui crée des flux de travail planifiés plus avancés :

> [!div class="nextstepaction"]
> [Contrôler le trafic à l’aide d’une application logique planifiée](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
