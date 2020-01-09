---
title: Générer des workflows automatisés basés sur l’approbation
description: 'Tutoriel : Créer un workflow automatisé basé sur l’approbation qui traite les abonnements aux listes de diffusion à l’aide d’Azure Logic Apps'
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/20/2019
ms.openlocfilehash: 7d7f573e5b18e6e0e63d3275aecefe408a9143fb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456608"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Tutoriel : Créer des workflows automatisés basés sur l’approbation à l’aide d’Azure Logic Apps

Ce tutoriel montre comment créer une [application logique](../logic-apps/logic-apps-overview.md) qui automatise un workflow basé sur l’approbation. Plus précisément, cette application logique traite les demandes d’abonnement à une liste de diffusion gérée par le service [MailChimp](https://mailchimp.com/). Cette application logique surveille un compte de messagerie pour détecter ces requêtes, les envoie pour approbation et ajoute les membres approuvés à la liste de diffusion.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créez une application logique vide.
> * Ajouter un déclencheur qui surveille les e-mails et détecte les requêtes d’abonnement.
> * Ajouter une action qui envoie des e-mails pour approuver ou rejeter ces requêtes.
> * Ajouter une condition qui vérifie la réponse d’approbation.
> * Ajouter une action qui ajoute les membres approuvés à la liste de diffusion.
> * Ajouter une condition qui vérifie si ces membres ont rejoint la liste.
> * Ajouter une action qui envoie des e-mails confirmant que ces membres ont rejoint la liste.

Lorsque vous avez terminé, votre application logique ressemble au flux de travail suivant à un niveau élevé :

![Vue d’ensemble globale de l’application logique terminée](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-overview.png)

## <a name="prerequisites"></a>Conditions préalables requises

* Un abonnement Azure. Si vous n’avez pas d’abonnement, [créez un compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.

* Un compte MailChimp qui contient une liste nommée « test-members-ML » dans laquelle votre application logique peut ajouter les adresses e-mail des membres approuvés. Si vous n’avez pas de compte, [créez un compte gratuit](https://login.mailchimp.com/signup/) et découvrez [comment créer une liste MailChimp](https://us17.admin.mailchimp.com/lists/#).

* Un compte de courrier Office 365 Outlook ou Outlook.com, qui prend en charge les workflows d’approbation. Office 365 Outlook est utilisé dans cet article. Si vous utilisez un autre compte de messagerie, les étapes générales sont identiques, mais l’affichage de l’interface utilisateur peut être légèrement différent.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com) avec les informations d’identification de votre compte Azure.

## <a name="create-your-logic-app"></a>Créer votre application logique

1. Dans le menu principal Azure, choisissez **Créer une ressource** > **Intégration** > **Application logique**.

   ![Créer votre ressource d’application logique](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. Sous **Créer une application logique**, indiquez les informations suivantes sur votre application logique comme illustré et décrit. Sélectionnez **Créer** lorsque vous avez terminé.

   ![Fournir des informations sur votre application logique](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Propriété | Valeur | Description |
   |----------|-------|-------------|
   | **Nom** | LA-MailingList | Nom de votre application logique, qui peut contenir uniquement des lettres, des chiffres, des traits d’union (`-`), des traits de soulignement (`_`), des parenthèses (`(`, `)`) et des points (`.`). Cet exemple utilise « LA-MailingList ». |
   | **Abonnement** | <*your-Azure-subscription-name*> | Le nom de votre abonnement Azure |
   | **Groupe de ressources** | LA-MailingList-RG | Nom du [groupe de ressources Azure](../azure-resource-manager/management/overview.md), qui est utilisé pour organiser les ressources connexes. Cet exemple utilise « LA-MailingList-RG ». |
   | **Lieu** | USA Ouest | Région dans laquelle stocker les informations sur votre application logique. Cet exemple utilise la région « USA Ouest ». |
   | **Log Analytics** | Off | Maintenez le paramètre de journalisation des diagnostics **Désactivé**. |
   ||||

1. Une fois qu’Azure a déployé votre application, dans la barre d’outils Azure, sélectionnez **Notifications** > **Accéder à la ressource** pour votre application logique déployée.

   ![Accéder à votre nouvelle ressource d’application logique](./media/tutorial-process-mailing-list-subscriptions-workflow/go-to-logic-app-resource.png)

   Ou bien, vous pouvez rechercher et sélectionner votre application logique en tapant son nom dans la zone de recherche.

   Le Concepteur Logic Apps s’ouvre et affiche une page contenant une vidéo de présentation et des déclencheurs et autres modèles d’application logique couramment utilisés. Sous **Modèles**, sélectionnez **Application logique vide**.

   ![Sélectionner un modèle d’application logique vide](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Ajoutez ensuite un [déclencheur](../logic-apps/logic-apps-overview.md#logic-app-concepts) qui écoute les e-mails entrants avec des requêtes d’abonnement. Chaque application logique doit commencer par un déclencheur, qui est activé lorsqu’un événement spécifique se produit ou lorsque de nouvelles données respectent une condition particulière. Pour plus d’informations, consultez [Quickstart: Build your first logic app workflow - Azure portal](../logic-apps/quickstart-create-first-logic-app-workflow.md) (Démarrage rapide : Générer votre premier flux de travail d’application logique - portail Azure).

## <a name="add-trigger-to-monitor-emails"></a>Ajouter le déclencheur pour surveiller les e-mails

1. Dans le Concepteur d’application logique, dans la zone de recherche, entrez `when email arrives` en guise de filtre. Dans la liste **Déclencheurs**, sélectionnez le déclencheur **À la réception d’un e-mail** pour votre fournisseur de messagerie.

   Cet exemple utilise le déclencheur Office 365 Outlook :

   ![Sélection du déclencheur « À la réception d’un e-mail » pour le fournisseur de messagerie](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * Pour les comptes Azure professionnels ou scolaires, sélectionnez Office 365 Outlook.
   * Pour les comptes Microsoft personnels, sélectionnez Outlook.com.

1. Si vous y êtes invité, connectez-vous à votre compte de courrier à l’aide de vos informations d’identification afin que Logic Apps puisse établir une connexion avec votre compte de courrier.

1. Dans le déclencheur, fournissez les critères permettant de vérifier l’arrivée de tous les nouveaux e-mails.

   1. Spécifiez le dossier, l’intervalle et la fréquence de vérification des e-mails.

      ![Spécifier le dossier, l’intervalle et la fréquence de vérification des e-mails](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | Propriété | Valeur | Description |
      |----------|-------|-------------|
      | **Folder** | `Inbox` | Dossier de messagerie à surveiller |
      | **Intervalle** | `1` | Nombre d’intervalles d’attente entre les vérifications. |
      | **Fréquence** | `Hour` | Unité de temps à utiliser pour la périodicité. |
      ||||

   1. Ajoutez une autre propriété au déclencheur afin de filtrer selon l’objet de l’e-mail. Ouvrez la liste **Ajouter un nouveau paramètre**, puis sélectionnez la propriété **Filtre d’objet**.

      ![Ajouter la propriété « Subject Filter » (Filtre d’objet) au déclencheur](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Pour plus d’informations sur les propriétés du déclencheur, consultez les [informations de référence sur le connecteur Office 365 Outlook](https://docs.microsoft.com/connectors/office365/) ou les [informations de référence sur le connecteur Outlook.com](https://docs.microsoft.com/connectors/outlook/).

   1. Une fois que la propriété s’affiche dans le déclencheur, entrez le texte suivant : `subscribe-test-members-ML`

      ![Entrer le texte de la propriété « Subject Filter » (Filtre d’objet)](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Pour masquer les informations du déclencheur pour le moment, cliquez sur sa barre de titre.

   ![Réduire la forme pour masquer les informations](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Enregistrez votre application logique. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

Votre application logique est à présent en ligne, mais elle ne fait rien d’autre que de vérifier vos e-mails entrants. Par conséquent, ajoutez une action qui répond à l’activation du déclencheur.

## <a name="send-approval-email"></a>Envoyer un message électronique d’approbation

Maintenant que vous disposez d’un déclencheur, ajoutez une [action](../logic-apps/logic-apps-overview.md#logic-app-concepts) qui envoie un e-mail pour approuver ou rejeter la requête.

1. Sous le déclencheur, sélectionnez **Nouvelle étape**. 

1. Sous **Choisir une action**, dans la zone de recherche, entrez `approval` en guise de filtre. Dans la liste d’actions, sélectionnez l’action **Envoyer un e-mail d’approbation** pour le fournisseur de messagerie. 

   Cet exemple utilise l’action Office 365 Outlook :

   ![Sélection de l’action « Envoyer un e-mail d’approbation »](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Fournissez les informations concernant cette action de la façon suivante : 

   ![Envoyer un e-mail d’approbation - Propriétés](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Propriété | Valeur | Description |
   |----------|-------|-------------|
   | **To** | <*your-email-address*> | Adresse e-mail de l’approbateur. À des fins de test, vous pouvez utiliser votre propre adresse. Cet exemple utilise l’adresse e-mail fictive « sophia.owen@fabrikam.com ». |
   | **Subject** | `Approve member request for test-members-ML` | Un objet d’e-mail descriptif |
   | **Options utilisateur** | `Approve, Reject` | Options de réponse que l’approbateur peut sélectionner. Par défaut, l’approbateur peut sélectionner « Approuver » ou « Rejeter » comme réponse. |
   ||||

   Pour l’instant, ignorez la liste de contenu dynamique qui s’affiche lorsque vous cliquez à l’intérieur de certaines zones de modification. Cette liste vous permet de sélectionner la sortie disponible des actions précédentes que vous pouvez utiliser en tant qu’entrées dans votre workflow.

   Pour plus d’informations sur les propriétés de cette action, consultez les [informations de référence sur le connecteur Office 365 Outlook](https://docs.microsoft.com/connectors/office365/) ou les [informations de référence sur le connecteur Outlook.com](https://docs.microsoft.com/connectors/outlook/).
 
1. Enregistrez votre application logique.

Ensuite, ajoutez une condition pour vérifier la réponse de l’approbateur.

## <a name="check-approval-response"></a>Vérifier la réponse d’approbation

1. Sous l’action **Envoyer un e-mail d’approbation**, sélectionnez **Nouvelle étape**.

1. Sous **Choisir une action**, sélectionnez **Intégré**. Dans la zone de recherche, entrez `condition` en guise de filtre. Dans la liste des actions, sélectionnez l’action **Condition**.

   ![Rechercher et sélectionner l’action « Condition »](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. Renommez la condition en utilisant une meilleure description.

   1. Dans la barre de titre de la condition, sélectionnez le bouton représentant des **points de suspension** ( **...** ) > **Renommer**.

      ![Renommer la description de la condition](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

   1. Renommez votre condition à l’aide de cette description : `If request approved`

1. Créez une condition qui vérifie si l’approbateur a sélectionné **Approuver**.

   1. Dans la condition, cliquez sur la zone **Choisir une valeur** située à gauche de la condition.

   1. Dans la liste de contenu dynamique qui s’affiche, sous **Envoyer un e-mail d’approbation**, sélectionnez la propriété **SelectedOption**.

      ![Dans la liste de contenu dynamique, sélectionner « SelectedOption »](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. Dans la zone de comparaison du milieu, sélectionnez l’opérateur **est égal à**.

   1. Dans la zone **Choisir une valeur** située à droite de la condition, entrez le texte suivant : `Approve`

      Quand vous avez terminé, votre condition ressemble à cet exemple :

      ![Condition terminée pour l’exemple Approved](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Enregistrez votre application logique.

Ensuite, spécifiez l’action que votre application logique effectue lorsque le réviseur approuve la requête. 

## <a name="add-member-to-mailchimp-list"></a>Ajouter un membre à la liste MailChimp

Ajoutez maintenant une action qui ajoute le membre approuvé à votre liste de diffusion.

1. Dans la branche **Si true** de la condition, sélectionnez **Ajouter une action**.

1. Sous **Choisir une action**, entrez `mailchimp` en guise de filtre, puis sélectionnez l’action **Ajouter un membre à la liste**.

   ![Sélection de l’action « Ajouter un membre à la liste »](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Si vous êtes invité à vous connecter à votre compte MailChimp, connectez-vous avec vos informations d’identification MailChimp.

1. Fournissez des informations à propos de cette action comme illustré et décrit ici :

   ![Fournir des informations pour « Ajouter un membre à la liste »](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Propriété | Obligatoire | Valeur | Description |
   |----------|----------|-------|-------------|
   | **ID de liste** | Oui | `test-members-ML` | Nom de votre liste de diffusion MailChimp. Cet exemple utilise « test-members-ML ». |
   | **État** | Oui | `subscribed` | Sélectionnez l’état de l’abonnement du nouveau membre. Cet exemple utilise « subscribed ». <p>Pour plus d’informations, consultez [Manage Subscribers with the MailChimp API](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/) (Gérer les abonnés avec l’API MailChimp). |
   | **Adresse e-mail** | Oui | <*new-member-email-address*> | Dans la liste de contenu dynamique, sélectionnez le champ **De** sous **À la réception d’un e-mail** pour transmettre l’adresse e-mail du nouveau membre. |
   ||||

   Pour plus d’informations sur les propriétés de cette action, consultez les [informations de référence sur le connecteur MailChimp](https://docs.microsoft.com/connectors/mailchimp/).

1. Enregistrez votre application logique.

Ensuite, ajoutez une condition afin que vous puissiez vérifier si le nouveau membre a rejoint votre liste de diffusion. De cette façon, votre application logique vous avertit si cette opération réussit ou échoue.

## <a name="check-for-success-or-failure"></a>Vérifier la réussite ou l’échec

1. Dans la branche **Si true**, sous l’action **Ajouter un membre à la liste**, sélectionnez **Ajouter une condition**.

1. Sous **Choisir une action**, sélectionnez **Intégré**. Dans la zone de recherche, entrez `condition` en guise de filtre. Dans la liste des actions, sélectionnez **Condition**.

1. Renommez la condition à l’aide de cette description : `If add member succeeded`.

1. Générez une condition qui vérifie si le membre approuvé parvient ou non à rejoindre votre liste de diffusion :

   1. Dans la condition, cliquez sur la zone **Choisir une valeur** située à gauche de la condition. Dans la liste de contenu dynamique, sous **Ajouter un membre à la liste**, sélectionnez la propriété **État**.

      Votre condition ressemble maintenant à cet exemple :

      ![Sous « Ajouter un membre à la liste », sélectionner « État »](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. Dans la zone de comparaison du milieu, sélectionnez l’opérateur **est égal à**.

   1. Dans la zone **Choisir une valeur** située à droite de la condition, entrez le texte suivant : `subscribed`

      Quand vous avez terminé, votre condition ressemble à cet exemple :

      ![Condition terminée pour l’exemple Subscribed](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Ensuite, configurez les e-mails à envoyer lorsque le membre approuvé parvient ou non à rejoindre votre liste de diffusion.

## <a name="send-email-if-member-added"></a>Envoyer un e-mail si le membre est ajouté

1. Sous la condition **Si l’ajout du membre a réussi**, dans la branche **Si true**, sélectionnez **Ajouter une action**.

   ![Dans la branche « Si true », sélectionner « Ajouter une action »](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. Sous **Choisir une action**, dans la zone de recherche, entrez `outlook send email` en guise de filtre, puis sélectionnez l’action **Envoyer un message électronique**.

   ![Ajout de l’action « Envoyer un message électronique »](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Renommez l’action à l’aide de cette description : `Send email on success`.

1. Fournissez des informations pour cette action comme illustré et décrit :

   ![Fournir des informations pour l’e-mail de réussite](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Propriété | Obligatoire | Valeur | Description |
   |----------|----------|-------|-------------|
   | **To** | Oui | <*your-email-address*> | Adresse e-mail à laquelle envoyer l’e-mail de réussite. À des fins de test, vous pouvez utiliser votre propre adresse e-mail. |
   | **Subject** | Oui | <*subject-for-success-email*> | Objet de l’e-mail de réussite. Pour ce didacticiel, entrez le texte suivant : <p>`Success! Member added to "test-members-ML": ` <p>Dans la liste de contenu dynamique, sous **Ajouter un membre à la liste**, sélectionnez la propriété **Adresse e-mail**. |
   | **Corps** | Oui | <*body-for-success-email*> | Contenu du corps de l’e-mail de réussite. Pour ce didacticiel, entrez le texte suivant : <p>`New member has joined "test-members-ML":` <p>Dans la liste de contenu dynamique, sélectionnez la propriété **Adresse e-mail**. <p>Sur la ligne qui suit, entrez le texte suivant : `Member opt-in status: ` <p> Dans la liste de contenu dynamique, sous **Ajouter un membre à la liste**, sélectionnez la propriété **État**. |
   |||||

1. Enregistrez votre application logique.

## <a name="send-email-if-member-not-added"></a>Envoyer un e-mail si le membre n’est pas ajouté

1. Sous la condition **Si l’ajout du membre a réussi**, dans la branche **Si false**, sélectionnez **Ajouter une action**.

   ![Dans la branche « Si false », sélectionner « Ajouter une action »](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. Sous **Choisir une action**, dans la zone de recherche, entrez `outlook send email` en guise de filtre, puis sélectionnez l’action **Envoyer un message électronique**.

   ![Ajouter une action pour « Envoyer un message électronique »](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Renommez l’action à l’aide de cette description : `Send email on failure`.

1. Fournissez des informations à propos de cette action comme illustré et décrit ici :

   ![Fournir des informations pour l’e-mail d’échec](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Propriété | Obligatoire | Valeur | Description |
   |----------|----------|-------|-------------|
   | **To** | Oui | <*your-email-address*> | Adresse e-mail à laquelle envoyer l’e-mail d’échec. À des fins de test, vous pouvez utiliser votre propre adresse e-mail. |
   | **Subject** | Oui | <*subject-for-failure-email*> | Objet de l’e-mail d’échec. Pour ce didacticiel, entrez le texte suivant : <p>`Failed, member not added to "test-members-ML": ` <p>Dans la liste de contenu dynamique, sous **Ajouter un membre à la liste**, sélectionnez la propriété **Adresse e-mail**. |
   | **Corps** | Oui | <*body-for-failure-email*> | Contenu du corps de l’e-mail d’échec. Pour ce didacticiel, entrez le texte suivant : <p>`Member might already exist. Check your MailChimp account.` |
   |||||

1. Enregistrez votre application logique. 

Ensuite, testez votre application logique, qui ressemble désormais à l’exemple suivant :

![Exemple de workflow d’application logique terminée](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>Exécuter votre application logique

1. Envoyez-vous un e-mail de requête pour rejoindre votre liste de diffusion. Attendez que la requête s’affiche dans votre boîte de réception.

1. Pour lancer manuellement votre application logique, sélectionnez **Exécuter** dans la barre d’outils du concepteur. 

   Si l’objet de votre e-mail correspond au filtre d’objet du déclencheur, votre application logique vous envoie un e-mail pour approuver la requête d’abonnement.

1. Dans l’e-mail d’approbation, sélectionnez **Approuver**.

1. Si l’adresse e-mail de l’abonné n’existe pas dans votre liste de diffusion, votre application logique ajoute l’adresse e-mail de cette personne et vous envoie un e-mail similaire à l’exemple suivant :

   ![Exemple d’e-mail - abonnement réussi](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Si votre application logique ne peut pas ajouter l’abonné, vous obtenez un e-mail semblable à l’exemple suivant :

   ![Exemple d’e-mail - abonnement ayant échoué](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

   Si vous ne recevez pas d’e-mail, vérifiez le dossier Courrier indésirable de votre messagerie. Il se peut que le filtre de courrier indésirable redirige ces types d’e-mails. Sinon, si vous ne savez pas si votre application logique s’est correctement exécutée, consultez [Dépanner votre application logique](../logic-apps/logic-apps-diagnosing-failures.md).

Félicitations, vous venez de créer et d’exécuter une application logique qui intègre des informations des services Azure et Microsoft et d’autres applications SaaS.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’avez plus besoin de l’exemple d’application logique, supprimez le groupe de ressources qui contient votre application logique et les ressources associées. 

1. Dans le menu Azure principal, accédez à **Groupes de ressources**, puis sélectionnez le groupe de ressources de votre application logique.

1. Dans le menu de groupe de ressources, sélectionnez **Vue d’ensemble** > **Supprimer un groupe de ressources**. 

   ![« Vue d’ensemble » > « Supprimer un groupe de ressources »](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. Confirmez le nom du groupe de ressources, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé une application logique qui gère des approbations pour les requêtes de liste de diffusion. Maintenant, découvrez comment créer une application logique qui traite et stocke les pièces jointes des e-mails en intégrant des services Azure, tels que Stockage Azure et Azure Functions.

> [!div class="nextstepaction"]
> [Process emails and attachments with a logic app](../logic-apps/tutorial-process-email-attachments-workflow.md) (Traiter les e-mails et les pièces jointes avec une application logique)
