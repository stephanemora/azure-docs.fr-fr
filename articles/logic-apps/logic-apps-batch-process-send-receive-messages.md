---
title: Traiter des messages par lots (groupe ou collection de messages) - Azure Logic Apps | Microsoft Docs
description: Envoyer et recevoir des messages sous forme de lots dans Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, LADocs
ms.topic: article
ms.date: 08/19/2018
ms.openlocfilehash: ee1df77dc18350a64082cb62c297a53700cad223
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128743"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>Envoyer, recevoir et traiter par lots des messages dans Azure Logic Apps

Pour envoyer et traiter ensemble des messages sous la forme spécifique de groupes, vous pouvez créer une solution de traitement par lots qui collecte les messages dans un *lot* jusqu’à ce que vos critères précisés soient remplis pour la mise en production et le traitement des messages mis en lots. Le traitement par lots peut réduire la fréquence à laquelle votre application logique traite les messages. Cet article montre comment concevoir une solution de traitement par lots en créant deux applications logiques dans le même abonnement et la même région Azure, en suivant cet ordre précis : 

* L’application logique [« réceptrice de lots »](#batch-receiver), qui accepte et collecte les messages dans un lot jusqu’à ce que vos critères précisés soient remplis pour mettre en production et traiter ces messages.

  Veillez à commencer par la création du récepteur de lots, afin de pouvoir sélectionner ensuite la destination des lots lors de la création de l’expéditeur de lots.

* Une ou plusieurs applications logiques [« expéditrices de lots »](#batch-sender), qui envoient les messages au récepteur de lots précédemment créé. 

   Vous pouvez également spécifier une clé unique (telle qu’un numéro de client), qui *partitionne* ou divise en sous-ensembles logiques le lot cible, en fonction de cette clé. De cette façon, l’application réceptrice peut collecter tous les éléments avec la même clé et les traiter ensemble.

Assurez-vous que votre récepteur et votre expéditeur de lots partagent le même abonnement *et* la même région Azure. Si ce n’est pas le cas, vous ne pouvez pas sélectionner le récepteur de lots lorsque vous créez l’expéditeur de lots, car ils ne sont pas visibles entre eux.

## <a name="prerequisites"></a>Prérequis

Pour suivre cet exemple, vous avez besoin de ce qui suit :

* Un abonnement Azure. Si vous ne disposez d’aucun abonnement, vous pouvez [commencer par créer gratuitement un compte Azure](https://azure.microsoft.com/free/). Vous pouvez aussi [souscrire un abonnement avec paiement à l’utilisation](https://azure.microsoft.com/pricing/purchase-options/).

* Un compte de courrier de n’importe quel [fournisseur de messagerie pris en charge par Azure Logic Apps](../connectors/apis-list.md)

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Pour utiliser Visual Studio plutôt que le portail Azure, veillez à [configurer Visual Studio pour travailler avec Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>Créer le récepteur de lots

Avant de pouvoir envoyer des messages à un lot, ce lot doit d’abord exister en tant que destination vers laquelle envoyer ces messages. Ainsi, vous devez commencer par créer l’application logique « réceptrice de lots », qui démarre avec le déclencheur **Lot**. De cette façon, lorsque vous créez l’application logique « expéditrice de lots », vous pouvez sélectionner l’application logique réceptrice de lots. Le récepteur de lots continue à collecter les messages jusqu’à ce que vos critères spécifiés soient remplis pour mettre en production et traiter ces messages. Alors que les récepteurs de lots n’ont pas besoin de connaître les expéditeurs de lots, les expéditeurs de lots, eux, doivent connaître la destination vers laquelle envoyer les messages. 

1. Dans le [portail Azure](https://portal.azure.com) ou dans Visual Studio, créez une application logique et nommez-la « BatchReceiver ». 

2. Dans le Concepteur d'applications logiques, ajoutez le déclencheur **Lot** qui démarre le flux de travail de votre application logique. Dans la zone de recherche, entrez « lot » comme filtre. Sélectionnez ce déclencheur : **Traiter les messages par lots**

   ![Ajout d’un déclencheur « Traiter les messages par lots »](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Définissez les propriétés du récepteur de lots : 

   | Propriété | Description | 
   |----------|-------------|
   | **Mode Batch** | - **Inline** : Pour définir des critères de mise en production à l’intérieur du déclencheur de lots <br>- **Compte d’intégration** : Pour définir plusieurs configurations de critères de mise en production via un [compte d’intégration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Avec un compte d’intégration, vous pouvez conserver ces configurations toutes au même endroit, plutôt que dans des applications logiques distincts. | 
   | **Nom du lot** | Le nom de votre lot, qui est « TestBatch » dans cet exemple, et qui s’applique uniquement au mode batch **Inline** |  
   | **Critères de mise en production** | S’applique uniquement au mode batch **Inline** et spécifie les critères à respecter avant de traiter chaque lot : <p>- **Sur la base du nombre de messages** : Nombre de messages à collecter dans le lot, par exemple 10 messages <br>- **En fonction de la taille** : Taille de lot maximale en octets, par exemple 100 Mo <br>- **Sur la base de la planification** : Intervalle et fréquence entre les mises en production de lots, par exemple 10 minutes. Vous pouvez également indiquer une date et une heure de début. <br>- **Tout sélectionner** : Utilisation de tous les critères spécifiés. | 
   ||| 
   
   Cet exemple sélectionne tous les critères :

   ![Détails à fournir concernant le déclencheur Lot](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

4. Ajoutez maintenant une ou plusieurs actions qui traitent chaque lot. 

   Pour cet exemple, ajoutez une action qui envoie un e-mail lorsque le déclencheur de lot est activé. 
   Le déclencheur s’exécute et envoie un e-mail lorsque le lot contient 10 messages, atteint 10 Mo, ou au bout de 10 minutes.

   1. Sous le déclencheur Lot, sélectionnez **Nouvelle étape**.

   2. Dans la zone de recherche, entrez «  envoyer e-mail » comme filtre.
   Sélectionnez un connecteur de messagerie en fonction de votre fournisseur de messagerie.
      
      Par exemple, si vous avez un compte personnel, tel que @outlook.com ou @hotmail.com, sélectionnez le connecteur Outlook.com. 
      Si vous avez un compte Gmail, sélectionnez le connecteur Gmail. 
      Office 365 Outlook est utilisé dans cet exemple. 

   3. Sélectionnez cette action : **Envoyer un e-mail - <*fournisseur de messagerie*>**

      Par exemple : 

      ![Sélection de l’action « Envoyer un message électronique » pour votre fournisseur de messagerie](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

5. Si vous y êtes invité, connectez-vous à votre compte e-mail. 

6. Définissez les propriétés de l’action que vous avez ajoutée.

   * Dans la zone **À**, entrez l’adresse e-mail du destinataire. 
   À des fins de test, vous pouvez utiliser votre propre adresse e-mail.

   * Dans la zone **Objet**, lorsque la liste de contenu dynamique s’affiche, sélectionnez le champ **Nom de partition**.

     ![Dans la liste de contenu dynamique, sélection du « Nom de partition »](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Dans une section ultérieure, vous pouvez spécifier une clé de partition unique, afin de diviser le lot cible en plusieurs sous-ensembles logiques vers lesquels envoyer des messages. 
     Chaque ensemble est associé à un numéro unique qui est généré par l’application logique expéditrice de lots. 
     Cette fonctionnalité permet d’utiliser un lot comprenant plusieurs sous-ensembles et de définir chaque sous-ensemble avec le nom que vous fournissez.

     > [!IMPORTANT]
     > Une partition a une limite de 5 000 messages ou 80 Mo. Si l’une ou l’autre des conditions est remplie, Logic Apps peut mettre en production le lot, même lorsque votre condition de mise en production définie n’est pas remplie.

   * Dans la zone **Corps**, lorsque la liste de contenu dynamique s’affiche, sélectionnez le champ **ID de message**. 

     Le concepteur Logic Apps ajoute automatiquement une boucle « For each » autour de l’action d’envoi d’un e-mail, car cette action accepte un tableau en tant qu’entrée. 
     Cette boucle envoie un e-mail pour chaque message du lot. 
     Ainsi, lorsque le déclencheur de lot est défini sur 10 messages, vous obtenez 10 e-mails chaque fois que le déclencheur est activé.

     ![Pour « Corps », sélectionnez « ID de message »](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

7.  Enregistrez votre application logique. Vous venez de créer un récepteur de lots.

    ![Enregistrer votre application logique](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

8. Si vous utilisez Visual Studio, veillez à [déployer votre application logique réceptrice de lots sur Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). Sinon, vous ne pouvez pas sélectionner le récepteur de lots lorsque vous créez l’expéditeur de lots.

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>Créer l’expéditeur de lots

À présent, créez une ou plusieurs applications logiques expéditrices de lots qui envoient des messages vers l’application logique réceptrice de lots. Dans chaque expéditeur de lots, vous spécifiez le récepteur de lots avec le nom du lot, le contenu du message et tout autre paramètre nécessaire. Vous pouvez éventuellement fournir une clé de partition unique pour diviser le lot en sous-ensembles logiques pour la collecte des messages avec cette clé. 

* En vous assurant d’avoir [créé votre récepteur de lots](#batch-receiver) avant de créer votre expéditeur de lots, vous pouvez sélectionner ce récepteur existant comme destinataire de lots. Alors que les récepteurs de lots n’ont pas besoin de connaître les expéditeurs de lots, les expéditeurs de lots, eux, doivent savoir où envoyer les messages. 

* Vérifiez que vos récepteur et expéditeur de lots partagent la même région *et* le même abonnement Azure. Si ce n’est pas le cas, vous ne pouvez pas sélectionner le récepteur de lots lorsque vous créez l’expéditeur de lots, car ils ne sont pas visibles entre eux.

1. Créez une autre application logique et nommez-la « BatchSender ».

   1. Dans la zone de recherche, entrez « récurrence » comme filtre. 
   Sélectionnez ce déclencheur : **Récurrence - Planification**

      ![Ajout du déclencheur « Récurrence - Planification »](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   2. Définissez la fréquence et l’intervalle d’exécution de l’application logique expéditrice sur une minute.

      ![Définition de la fréquence et de l’intervalle pour le déclencheur Récurrence](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

2. Ajoutez une nouvelle étape pour envoyer des messages vers un lot.

   1. Sous le déclencheur Récurrence, choisissez **Nouvelle étape**.

   2. Dans la zone de recherche, entrez « lot » comme filtre. 
   Sélectionnez la liste **Actions**, puis sélectionnez l’action suivante : **Choisir un workflow Logic Apps avec déclencheur de lot - Envoyer les messages au lot**.

      ![Sélection de « Choisir un workflow Logic Apps avec déclencheur de lot »](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   3. Sélectionnez l’application logique réceptrice de lots que vous avez créée précédemment.

      ![Sélection de l’application logique « BatchReceiver »](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!NOTE]
      > La liste affiche également toutes les autres applications logiques qui sont associées à un déclencheur de lot. 
      > 
      > Si vous utilisez Visual Studio et que vous ne voyez aucun récepteur de lot à sélectionner, vérifiez que vous avez déployé votre récepteur de lots sur Azure. Si ce n’est pas le cas, découvrez comment [déployer votre application logique réceptrice de lots sur Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). 

   4. Sélectionnez cette action : **Traiter_les_messages_par_lots - <*votre récepteur de lots*>**

      ![Sélection de l’action : « Traiter_les_messages_par_lots - <votre-application-logique> »](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

3. Définissez les propriétés de l’expéditeur de lots :

   | Propriété | Description | 
   |----------|-------------| 
   | **Nom du lot** | Nom défini par l’application logique réceptrice (« TestBatch » dans cet exemple) <p>**Important** : le nom du lot est validé lors de l’exécution et doit correspondre au nom spécifié par l’application logique réceptrice. Si vous modifiez ce nom, l’expéditeur de lots échoue. | 
   | **Contenu du message** | Contenu du message que vous voulez envoyer | 
   ||| 

   Pour cet exemple, ajoutez l’expression suivante qui insère la date et l’heure actuelles dans le contenu du message que vous envoyez au lot :

   1. Cliquez dans la zone **Contenu du Message**. 

   2. Lorsque la liste de contenu dynamique s’affiche, choisissez **Expression**. 

   3. Entrez l’expression `utcnow()`, puis choisissez **OK**. 

      ![Dans « Contenu du message », choisissez « Expression », entrez « utcnow() » et choisissez « OK ».](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

4. À présent, définissez une partition pour le lot. Dans l’action « BatchReceiver », choisissez **Afficher les options avancées** et définissez ces propriétés :

   | Propriété | Description | 
   |----------|-------------| 
   | **Nom de partition** | Clé de partition unique facultative, à utiliser pour diviser le lot cible en sous-ensembles logiques et collecter les messages en fonction de cette clé | 
   | **ID de message** | Identificateur de message facultatif, qui est un identificateur global unique généré (GUID) lorsqu’il est vide | 
   ||| 

   Pour cet exemple, dans la zone **Nom de partition**, ajoutez une expression qui génère un nombre aléatoire compris entre 1 et 5. Laissez la zone **ID de message** vide.
   
   1. Cliquez dans la zone **Nom de partition** pour afficher la liste de contenu dynamique. 

   2. Dans la liste de contenu dynamique, choisissez **Expression**.
   
   3. Entrez l’expression `rand(1,6)`, puis choisissez **OK**.

      ![Définition d’une partition pour le lot cible](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      Cette fonction **rand** génère un nombre compris entre 1 et 5. 
      Vous divisez donc ce lot en cinq partitions numérotées, définies dynamiquement par cette expression.

5. Enregistrez votre application logique. Votre application logique expéditrice doit désormais ressembler à ceci :

   ![Enregistrement de l’application logique](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Tester les applications logiques

Pour tester votre solution de traitement par lots, laissez vos applications logiques s’exécuter pendant quelques minutes. Vous allez commencer à recevoir des e-mails par groupes de cinq, tous avec la même clé de partition.

Votre application logique expéditrice de lots s’exécute toutes les minutes, elle génère un nombre aléatoire compris entre 1 et 5 et utilise ce numéro généré comme clé de partition pour le lot cible où sont envoyés les messages. Chaque fois que le lot comporte cinq éléments avec la même clé de partition, l’application logique réceptrice de lots se déclenche et envoie un e-mail pour chaque message.

> [!IMPORTANT]
> Lorsque vous avez terminé vos tests, veillez à désactiver l’application logique BatchSender afin d’arrêter l’envoi de messages et éviter la surcharge de la boîte de réception.

## <a name="next-steps"></a>Étapes suivantes

* [Créer des définitions d’application logique à l’aide de JSON](../logic-apps/logic-apps-author-definitions.md)
* [Créer une application sans serveur dans Visual Studio avec Azure Logic Apps et Azure Functions](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Gestion des exceptions et journalisation des erreurs pour les applications logiques](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
