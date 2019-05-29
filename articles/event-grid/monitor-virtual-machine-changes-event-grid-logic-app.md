---
title: Surveiller les modifications d’une machine virtuelle - Azure Event Grid et Azure Logic Apps | Microsoft Docs
description: Vérifier les modifications de configuration dans des machines virtuelles (VM) à l’aide d’Azure Event Grid et Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 33634773b436114f4a5f2942028710ae50e0e703
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65801160"
---
# <a name="tutorial-monitor-virtual-machine-changes-with-azure-event-grid-and-logic-apps"></a>Didacticiel : Surveiller les modifications d’une machine virtuelle avec Azure Event Grid et Azure Logic Apps

Vous pouvez démarrer un [flux de travail d’application logique](../logic-apps/logic-apps-overview.md) automatisé lorsque des événements spécifiques se produisent dans des ressources Azure ou tierces. Ces ressources peuvent publier ces événements dans une [grille d’événements Azure](../event-grid/overview.md). À son tour, la grille d’événements envoie ces événements aux abonnés qui possèdent des files d’attente, webhooks ou [hubs d’événements](../event-hubs/event-hubs-what-is-event-hubs.md) comme points de terminaison. En tant qu’abonné, votre application logique peut attendre ces événements dans la grille d’événements avant d’exécuter des flux de travail automatisés pour effectuer les tâches, sans qu’il soit nécessaire d’écrire du code.

Par exemple, voici certains événements que les éditeurs peuvent envoyer aux abonnés via le service Azure Event Grid :

* Créer, lire, mettre à jour ou supprimer une ressource. Par exemple, vous pouvez surveiller les modifications susceptibles d’être facturées sur votre abonnement Azure. 
* Ajouter ou retirer une personne d’un abonnement Azure.
* Votre application effectue une action particulière.
* Un nouveau message apparaît dans une file d’attente.

Ce tutoriel crée une application logique qui surveille les modifications apportées à une machine virtuelle et envoie des e-mails à ce sujet. Si vous créez une application logique avec abonnement aux événements d’une ressource Azure, ces événements sont transférés de cette ressource vers l’application logique par le biais d’une grille d’événements. Le didacticiel vous guide tout au long de la création de cette application logique :

![Vue d’ensemble - surveiller une machine virtuelle avec une grille d’événements et une application logique](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * créer une application logique qui surveille les événements d’une grille d’événements ;
> * ajouter une condition qui recherche spécifiquement les modifications apportées à la machine virtuelle ;
> * envoyer un e-mail en cas de modification de la machine virtuelle.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).

* Un compte e-mail d’un fournisseur de messagerie pris en charge par Logic Apps, par exemple Outlook Office 365, Outlook.com ou Gmail, pour envoyer les notifications. Pour les autres fournisseurs, [passez en revue la liste des connecteurs ici](/connectors/). 

  Ce tutoriel utilise un compte Outlook Office 365. Si vous utilisez un autre compte de messagerie, les étapes générales sont identiques, mais l’affichage de l’interface utilisateur peut être légèrement différent.

* Une [machine virtuelle](https://azure.microsoft.com/services/virtual-machines). Si ce n’est pas déjà fait, créez une machine virtuelle en suivant le [tutoriel Créer une machine virtuelle](../virtual-machines/windows/quick-create-portal.md). Pour que la machine virtuelle publie des événements, [aucun autre élément n’est nécessaire](../event-grid/overview.md).

## <a name="create-blank-logic-app"></a>Créer une application logique vide

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec les informations d’identification de votre compte Azure. 

1. Dans le menu principal Azure, choisissez **Créer une ressource** > **Intégration** > **Application logique**.

   ![Créer une application logique](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. Sous **Application logique**, fournissez des informations sur votre application logique. Lorsque vous êtes prêt, choisissez **Créer**.

   ![Spécifier les détails de l’application logique](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Propriété | Valeur suggérée | Description |
   | -------- | --------------- | ----------- |
   | **Nom** | <*logic-app-name*> | Fournissez un nom unique pour votre application logique. |
   | **Abonnement** | <*Azure-subscription-name*> | Sélectionnez le même abonnement Azure pour tous les services de ce didacticiel. |
   | **Groupe de ressources** | <*Azure-resource-group*> | Sélectionnez le même groupe de ressources Azure pour tous les services de ce didacticiel. |
   | **Lieu** | <*région-centre de données-Azure*> | Sélectionnez la même région pour tous les services de ce didacticiel. |
   |||

   Vous venez de créer une ressource Azure pour votre application logique. 

1. Une fois qu’Azure a déployé votre application logique, le Concepteur d’applications logiques affiche une page contenant une vidéo de présentation et les déclencheurs couramment utilisés. Faites défiler la vidéo et les déclencheurs. 

1. Sous **Modèles**, choisissez **Application logique vide**.

   ![Choisir le modèle d’application logique](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   Le Concepteur d’applications logiques affiche ensuite les [*déclencheurs*](../logic-apps/logic-apps-overview.md#logic-app-concepts) que vous pouvez utiliser pour démarrer votre application logique. Chaque application logique doit démarrer avec un déclencheur, qui s’active lorsqu’un événement spécifique se produit ou lorsqu’une condition particulière est remplie. 
   Chaque fois que le déclencheur est activé, Azure Logic Apps crée une instance de workflow qui exécute votre application logique.

## <a name="add-event-grid-trigger"></a>Ajouter un déclencheur Event Grid 

Maintenant, ajoutez le déclencheur Event Grid qui surveille le groupe de ressources de votre machine virtuelle. 

1. Dans le Concepteur, dans la zone de recherche, entrez « event grid » comme filtre. Dans la liste des déclencheurs, sélectionnez ce déclencheur : **Quand un événement de ressource se produit - Azure Event Grid**

   ![Sélectionner le déclencheur : « Lors d’un événement de ressource »](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. Lorsque vous y êtes invité, connectez-vous à Azure Event Grid avec les informations d’identification de votre compte Azure. Dans la liste **Locataire**, qui affiche le locataire Azure Active Directory qui est associé à votre abonnement Azure, vérifiez que le locataire approprié s’affiche.

   ![Se connecter avec des informations d’identification Azure](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Si vous êtes connecté avec un compte Microsoft personnel, par exemple @outlook.com ou @hotmail.com, le déclencheur Event Grid risque de ne pas apparaître correctement. Il existe une solution de contournement qui consiste à choisir [Se connecter avec le principal du service](../active-directory/develop/howto-create-service-principal-portal.md), ou à s’authentifier en tant que membre de l’instance Azure Active Directory associée à l’abonnement Azure, par exemple, *nom-utilisateur*@emailoutlook.onmicrosoft.com.

1. Maintenant, abonnez votre application logique aux événements de l’éditeur. Renseignez les détails de votre abonnement aux événements, comme l’indique le tableau suivant :

   ![Spécifier les détails de l’abonnement aux événements](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details-generic.png)

   | Propriété | Obligatoire | Value | Description |
   | -------- | -------- | ----- | ----------- |
   | **Abonnement** | OUI | <*event-publisher-Azure-subscription-name*> | Sélectionnez le nom de l’abonnement Azure associé à l’éditeur d’événements. Dans le cadre de ce tutoriel, sélectionnez le nom d’abonnement Azure de votre machine virtuelle. |
   | **Type de ressource** | OUI | <*event-publisher-Azure-resource-type*> | Sélectionnez le type de ressource Azure pour l’éditeur d’événements. Pour ce tutoriel, sélectionnez la valeur suivante pour surveiller les groupes de ressources Azure : <p><p>**Microsoft.Resources.ResourceGroups** |
   | **Nom de la ressource** |  OUI | <*event-publisher-Azure-resource-name*> | Sélectionnez le nom de la ressource Azure pour l’éditeur d’événements. Cette liste varie en fonction du type de ressource que vous avez sélectionné. Pour ce tutoriel, sélectionnez le nom du groupe de ressources Azure pour votre machine virtuelle. |
   | **Élément de type d’événement** |  Non  | <*event-types*> | Sélectionnez un ou plusieurs types d’événements spécifiques à filtrer et envoyer à votre Event Grid. Par exemple, vous pouvez ajouter ces types d’événements pour détecter le moment où des ressources sont modifiées ou supprimées : <p><p>- **Microsoft.Resources.ResourceActionSuccess** <br>- **Microsoft.Resources.ResourceDeleteSuccess** <br>- **Microsoft.Resources.ResourceWriteSuccess** <p>Pour plus d’informations, consultez les rubriques suivantes : <p><p>- [Comprendre le filtrage des événements](../event-grid/event-filtering.md) <br>- [Filtrer des événements pour Event Grid](../event-grid/how-to-filter-events.md) <br>- [Schéma d’événements Azure Event Grid pour les groupes de ressources](../event-grid/event-schema-resource-groups.md) |
   | **Nom d’abonnement** | Non  | <*event-subscription-name*> | Indiquez un nom unique pour votre abonnement aux événements. |
   | Pour les paramètres facultatifs, choisissez **Ajouter un nouveau paramètre**. | Non  | {voir les descriptions} | * **Filtre de préfixe** : Pour ce tutoriel, laissez cette propriété vide. Le comportement par défaut s’applique à toutes les valeurs. Vous pouvez cependant spécifier une chaîne de préfixe en tant que filtre, par exemple, un chemin d’accès et un paramètre pour une ressource spécifique. <p>* **Filtre de suffixe** : Pour ce tutoriel, laissez cette propriété vide. Le comportement par défaut s’applique à toutes les valeurs. Vous pouvez cependant spécifier une chaîne de suffixe en tant que filtre, par exemple, une extension de nom de fichier, si vous ne souhaitez utiliser que des types de fichiers spécifiques. |
   |||

   Une fois que vous avez terminé, votre déclencheur Event Grid peut se présenter ainsi :

   ![Détails d’un exemple de déclencheur Event Grid](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

1. Enregistrez votre application logique. Dans la barre d’outils du concepteur, choisissez **Enregistrer**. Pour réduire et masquer les détails d’une action dans votre logique d’application, choisissez la barre de titre de l’action.

   ![Enregistrer votre application logique](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Lorsque vous enregistrez votre application logique avec un déclencheur Event Grid, Azure crée automatiquement un abonnement aux événements de votre application logique pour la ressource que vous avez sélectionnée. Par conséquent, lorsque la ressource publie un événement dans la grille d’événements, cette grille d’événements transmet automatiquement l’événement à votre application logique. Cet événement déclenche votre application logique, puis crée et lance une instance du flux de travail que vous définissez dans les étapes suivantes.

Votre application logique est désormais en ligne ; elle écoute les événements de la grille d’événements, mais ne fait rien tant que vous n’avez pas ajouté d’actions au flux de travail. 

## <a name="add-condition"></a>Ajouter une condition

Pour exécuter le flux de travail de votre application logique uniquement quand un événement spécifique se produit, ajoutez une condition qui recherche les opérations d’écriture sur la machine virtuelle. Lorsque cette condition est true, votre application logique vous envoie par courrier électronique des informations sur la machine virtuelle mise à jour.

1. Dans le Concepteur d’applications logiques, sous le déclencheur Event Grid, sélectionnez **Nouvelle étape**.

   ![Choisir « Nouvelle étape »](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. Dans la zone de recherche, entrez « condition » comme filtre. Dans la liste des actions, sélectionnez cette action : **Condition**

   ![Ajouter une condition](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   Le Concepteur d’application logique ajoute une condition vide à votre flux de travail, y compris les chemins d’accès des actions à suivre selon que la condition est true ou false.

   ![Condition vide](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. Renommez le titre de la condition comme suit : `If a virtual machine in your resource group has changed`. Dans la barre de titre de la condition, choisissez le bouton représentant des points de suspension ( **...** ), puis sélectionnez **Renommer**.

   ![Renommer la condition](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. Créez une condition qui vérifie dans l’événement `body` un objet `data` dont la propriété `operationName` correspond à l’opération `Microsoft.Compute/virtualMachines/write`. En savoir plus sur le [schéma d’un événement Event Grid](../event-grid/event-schema.md).

   1. Sur la première ligne, sous **et**, cliquez à l’intérieur de la zone de gauche. Dans la liste de contenu dynamique qui s’affiche, choisissez **Expression**.

      ![Choisir « Expression »](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. Dans l’éditeur d’expressions, entrez cette expression, puis choisissez **OK** : 

      `triggerBody()?['data']['operationName']`

      Par exemple : 

      ![Choisir « Expression »](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. Dans la zone du milieu, conservez l’opérateur **est égal à**.

   1. Dans la zone à droite, entrez la valeur suivante :

      `Microsoft.Compute/virtualMachines/write`

   Votre condition terminée ressemble maintenant à l’exemple suivant :

   ![Condition terminée](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   Si vous passez du mode Création au mode Code puis à nouveau au mode Création, l’expression que vous avez spécifiée dans la condition est résolue en jeton **data.operationName** :

   ![Condition résolue](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. Enregistrez votre application logique.

## <a name="send-email-notifications"></a>Envoyer des notifications par e-mail

Ajoutez maintenant une [ *action* ](../logic-apps/logic-apps-overview.md#logic-app-concepts) pour recevoir un message électronique lorsque la condition spécifiée est true.

1. Dans la zone **If true** de la condition, choisissez **Ajouter une action**.

   ![Ajouter une action lorsque la condition est true](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. Dans la zone de recherche, entrez «  envoyer e-mail » comme filtre. Selon votre fournisseur de messagerie, recherchez et sélectionnez le connecteur correspondant. Puis sélectionnez l’action « Envoyer un courrier électronique » pour votre connecteur. Par exemple :  

   * Pour un compte professionnel ou scolaire Azure, sélectionnez le connecteur Outlook Office 365. 

   * Pour des comptes personnels Microsoft, sélectionnez le connecteur Outlook.com. 

   * Pour des comptes Gmail, sélectionnez le connecteur Gmail. 

   Ce tutoriel continue d’utiliser le connecteur Outlook Office 365. 
   Si vous utilisez un autre fournisseur, les étapes restent les mêmes, mais votre interface utilisateur peut s’afficher un peu différemment. 

   ![Sélectionner l’action « Envoyer un courrier électronique »](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. Si vous ne disposez pas déjà d’une connexion pour votre fournisseur de messagerie, connectez-vous à votre compte de messagerie lorsque vous êtes invité vous identifier.

1. Renommez le titre de l’action Envoyer un e-mail comme suit : `Send email when virtual machine updated`.

1. Renseignez les détails de l’e-mail comme l’indique le tableau suivant :

   ![Action E-mail vide](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Pour sélectionner des résultats provenant d’étapes précédentes de votre workflow, cliquez sur une zone d’édition afin d’ouvrir la liste de contenu dynamique, ou choisissez **Ajouter du contenu dynamique**. Pour voir davantage de résultats, choisissez **Plus** pour chaque section de la liste. Pour fermer la liste Contenu dynamique, choisissez de nouveau **Ajouter du contenu dynamique**.

   | Propriété | Obligatoire | Value | Description |
   | -------- | -------- | ----- | ----------- |
   | **To** | OUI | <*destinataire\@domaine*> | Entrez l’adresse e-mail du destinataire. À des fins de test, vous pouvez utiliser votre propre adresse e-mail. |
   | **Objet** | OUI | Mise à jour de la ressource : **Objet** | Entrez le contenu de l’objet de l’e-mail. Dans le cadre de ce tutoriel, entrez le texte spécifié et sélectionnez le champ **Subject** de l’événement. Ici, l’objet de votre e-mail comprend le nom de la ressource mise à jour (machine virtuelle). |
   | **Corps** | OUI | Ressource : **Rubrique** <p>Type d’événement : **Type d’événement**<p>ID d’événement : **Identifiant**<p>Heure : **Heure de l’événement** | Entrez le contenu du corps de l’e-mail. Dans le cadre de ce tutoriel, entrez le texte spécifié et sélectionnez les champs **Topic**, **Event Type**, **ID** et **Event Time** de l’événement de façon à inclure dans votre e-mail la ressource qui a déclenché l’événement, le type d’événement, son horodatage et son ID pour la mise à jour. Pour ce tutoriel, la ressource est le groupe de ressources Azure sélectionné dans le déclencheur. <p>Pour ajouter des lignes vides à votre contenu, appuyez sur Maj + Entrée. |
   ||||

   > [!NOTE]
   > Si vous sélectionnez un champ qui représente un tableau, le concepteur ajoute automatiquement une boucle **For each** autour de l’action qui référence ce tableau. De cette façon, votre application logique effectue cette action sur chaque élément du tableau.

   Votre action d’e-mail peut maintenant se présenter ainsi :

   ![Sélectionner les sorties à inclure dans le courrier électronique](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Et votre application logique terminée peut se présenter ainsi :

   ![Application logique terminée](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. Enregistrez votre application logique. Pour réduire et masquer les détails de chaque action dans votre logique d’application, choisissez la barre de titre de l’action.

   Votre application logique est désormais en ligne, mais elle attend que votre machine virtuelle soit modifiée avant de faire quoi que ce soit. 
   Pour tester maintenant votre application logique, passez à la section suivante.

## <a name="test-your-logic-app-workflow"></a>Tester le flux de travail d’une application logique

1. Pour vérifier que votre application logique reçoit des événements spécifiés, mettez à jour votre machine virtuelle.

   Par exemple, vous pouvez redimensionner votre machine virtuelle dans le portail Azure ou [redimensionner votre machine virtuelle avec Azure PowerShell](../virtual-machines/windows/resize-vm.md).

   Après quelques instants, vous devriez recevoir un courrier électronique. Par exemple : 

   ![Courrier électronique à propos de la mise à jour de la machine virtuelle](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. Pour consulter l’historique des exécutions et des déclenchements de votre application logique, sélectionnez **Vue d’ensemble** dans le menu de votre application logique. Pour afficher plus de détails sur une exécution, sélectionnez la ligne correspondante.

   ![Historique d’exécutions de l’application logique](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

1. Pour afficher les entrées et les sorties de chaque étape, développez celle que vous souhaitez vérifier. Ces informations peuvent vous aider à diagnostiquer et déboguer les problèmes de votre application logique.

   ![Détails de l’historique des exécutions d’une application logique](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

Félicitations, vous avez créé et exécuté une application logique qui surveille les événements liés aux ressources grâce à une grille d’événements et vous envoie un e-mail lorsque ces événements se produisent. Vous avez également appris comment créer facilement des flux de travaux qui automatisent les processus et intégrer des systèmes et des services cloud.

Vous pouvez surveiller les autres modifications de configuration avec des grilles d’événements et des applications logiques. Par exemple :

* Une machine virtuelle se voit attribuer des droits de contrôle d’accès en fonction du rôle (RBAC).
* Des modifications sont apportées à un groupe de sécurité réseau (NSG) sur une carte d’interface réseau (NIC).
* Des disques d’une machine virtuelle ont été ajoutés ou supprimés.
* Une adresse IP publique est affectée à la carte d’interface réseau (NIC) d’une machine virtuelle.

## <a name="clean-up-resources"></a>Supprimer des ressources

Ce didacticiel utilise des ressources et effectue des actions qui peuvent entraîner des frais sur votre abonnement Azure. Par conséquent, lorsque vous aurez terminé de suivre ce didacticiel et d’effectuer les tests, veillez à désactiver ou à supprimer les ressources pour lesquelles vous ne souhaitez pas être facturé.

* Pour arrêter l’exécution de votre application logique sans supprimer votre travail, désactivez votre application. Dans le menu de votre application logique, sélectionnez **Vue d’ensemble**. Dans la barre d’outils, choisissez **Désactiver**.

  ![Désactiver votre application logique](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Si vous ne voyez pas le menu de l’application logique, essayez de revenir au tableau de bord Azure et de rouvrir votre application logique.

* Pour supprimer définitivement votre application logique, sélectionnez **Vue d’ensemble** dans le menu de l’application logique. Dans la barre d’outils, choisissez **Supprimer**. Confirmez que vous souhaitez supprimer votre application logique, puis choisissez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

* [Créer et acheminer des événements personnalisés avec Event Grid](../event-grid/custom-event-quickstart.md)
