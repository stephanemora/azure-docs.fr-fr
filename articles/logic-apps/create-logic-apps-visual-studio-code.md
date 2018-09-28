---
title: Créer et gérer des workflows automatisés avec Visual Studio Code - Azure Logic Apps | Microsoft Docs
description: Créer et gérer des applications logiques dans Visual Studio Code (VS Code)
services: logic-apps
ms.service: logic-apps
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.topic: article
ms.reviewer: klam, deli, LADocs
ms.suite: integration
ms.date: 09/24/2018
ms.openlocfilehash: fd92b093b70dba2df3ec645b3ecb77233d3ebd5c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958354"
---
# <a name="create-and-manage-automated-logic-app-workflows---visual-studio-code"></a>Créer et gérer des workflows automatisés d’application logique - Visual Studio Code

Avec [Azure Logic Apps](../logic-apps/logic-apps-overview.md) et Visual Studio Code, vous pouvez créer et gérer des applications logiques qui vous permettent d’automatiser tâches, workflows et processus liés à l’intégration d’applications, de données, de systèmes et de services dans les entreprises et les organisations. Cet article explique comment créer et modifier des définitions de workflow d’application logique en utilisant une expérience basée sur du code. Vous pouvez également travailler sur les applications logiques déjà déployées dans <a href="https://docs.microsoft.com/azure/guides/developer/azure-developer-guide" target="_blank">Azure</a> sur le cloud. 

Même si vous pouvez effectuer ces tâches dans le <a href="https://portal.azure.com" target="_blank">portail Azure</a> et dans Visual Studio, vous démarrez plus rapidement dans Visual Studio Code lorsque vous choisissez de travailler directement dans du code. Par exemple, vous pouvez également désactiver, activer, supprimer et actualiser des applications logiques déjà créées.

Pour cet article, vous pouvez créer la même application logique que celle du [Guide de démarrage rapide pour la création d’une application logique dans le portail Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), qui privilégie les concepts de base. Dans Visual Studio Code, l’application logique ressemble à cet exemple :

![Application logique terminée](./media/create-logic-apps-visual-studio-code/overview.png)

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous disposez des éléments suivants :

* Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>.

* Connaissances de base sur les [définitions de workflow](../logic-apps/logic-apps-workflow-definition-language.md) d’application logique et leur structure, qui utilisent JavaScript Object Notation (JSON)

* Accès au web pour la connexion à Azure et à votre abonnement Azure

* Téléchargez et installez ces outils, si vous ne les avez pas déjà : 

  * <a href="https://code.visualstudio.com/" target="_blank">Version de Visual Studio Code 1.25.1 ou version ultérieure</a>, qui est gratuite

  * Extension Visual Studio Code pour Azure Logic Apps

    Vous pouvez installer cette extension à partir de Visual Studio Code lui-même. 
    Assurez-vous de recharger Visual Studio Code après l’installation. 

    ![Recherche de l’« Extension Visual Studio Code pour Azure Logic Apps »](./media/create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Pour savoir si l’installation de l’extension s’est correctement déroulée, contrôlez la présence de l’icône Azure dans la barre d’outils de Visual Studio Code. 

    ![Extension installée](./media/create-logic-apps-visual-studio-code/installed-extension.png)

    Pour plus d’informations, consultez la <a href="https://code.visualstudio.com/docs/editor/extension-gallery" target="_blank">Place de marché des extensions</a>. 

<a name="sign-in-azure"></a>

## <a name="sign-in-to-azure"></a>Connexion à Azure

1. Ouvrez Visual Studio Code. Dans la barre d’outils de Visual Studio Code, sélectionnez l’icône Azure. 

   ![Sélection de l’icône Azure](./media/create-logic-apps-visual-studio-code/open-extension.png)

1. Dans la fenêtre Azure, sous **Logic Apps**, sélectionnez **Connexion à Azure**. 

   ![Sélection de « Connexion à Azure »](./media/create-logic-apps-visual-studio-code/sign-in-azure.png)

   Vous êtes à présent invité à vous connecter en utilisant le code d’authentification fourni. 

1. Copiez le code d’authentification, puis choisissez **Copier et ouvrir**, ce qui ouvre une nouvelle fenêtre de navigateur.

   ![Invite de connexion](./media/create-logic-apps-visual-studio-code/sign-in-prompt.png)

1. Indiquez votre code d’authentification. Lorsque vous y êtes invité, choisissez **Continuer**.

   ![Indication du code](./media/create-logic-apps-visual-studio-code/authentication-code.png)

1. Sélectionnez votre compte Azure. Lorsque vous êtes connecté, vous pouvez fermer le navigateur et revenir à Visual Studio Code.

   Dans la fenêtre Azure, les volets Logic Apps et Comptes d’intégration affichent désormais les abonnements Azure dans votre compte. 

   ![Sélectionnez un abonnement](./media/create-logic-apps-visual-studio-code/select-azure-subscription.png)

   Si vous ne voyez pas ces abonnements, en regard de **Logic Apps**, choisissez **Sélectionner des abonnements** (icône de filtre). Recherchez et sélectionnez les abonnements que vous souhaitez.

1. Pour voir toutes les applications logiques ou comptes d’intégration existants dans votre abonnement Azure, développez votre abonnement.

   ![Affichage des applications logiques et des comptes d’intégration](./media/create-logic-apps-visual-studio-code/existing-logic-apps.png)

<a name="create-logic-app"></a>

## <a name="create-logic-app"></a>Créer une application logique

1. Si vous n’êtes pas connecté à votre abonnement Azure depuis Visual Studio Code, suivez les étapes décrites dans cet article pour [vous connecter maintenant](#sign-in-azure).

1. Dans le menu contextuel de votre abonnement, sélectionnez **Créer**.

   ![Sélection de « Créer »](./media/create-logic-apps-visual-studio-code/create-logic-app.png)

1. Dans la liste qui affiche les groupes de ressources Azure dans votre abonnement, sélectionnez un groupe de ressources existant ou **Créer un groupe de ressources**. 

   Cet exemple crée un groupe de ressources :

   ![Créer un groupe de ressources](./media/create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Précisez un nom pour votre groupe de ressources Azure, puis appuyez sur Entrée.

   ![Attribution d’un nom au groupe de ressources](./media/create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Sélectionnez l’emplacement du centre de données où enregistrer les métadonnées de votre application logique.

   ![Sélection d’un emplacement](./media/create-logic-apps-visual-studio-code/select-location.png)

1. Indiquez un nom pour votre application logique et appuyez sur Entrée.

   ![Attribution d’un nom à l’application logique](./media/create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   Votre nouvelle application logique apparaît désormais dans la fenêtre Azure, sous votre abonnement Azure. Vous pouvez à présent créer la définition du workflow de votre application logique.

1. Dans le menu contextuel de l’application logique, sélectionnez **Ouvrir dans l’éditeur**. 

   ![Ouverture d’une application logique dans l’éditeur](./media/create-logic-apps-visual-studio-code/open-new-logic-app.png)

   Visual Studio Code ouvre un modèle de définition de workflow pour application logique (fichier .logicapp.json) afin que vous puissiez commencer à créer le workflow de votre application logique.

   ![Définition du workflow de la nouvelle application logique](./media/create-logic-apps-visual-studio-code/blank-logic-app-workflow-definition.png)

1. Dans le fichier modèle de définition de workflow pour application logique, démarrez la création de la définition du workflow de votre application logique. Pour toute référence technique, reportez-vous à l’article [Schéma du langage de définition de workflow pour Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md).

   Voici un exemple de définition logique. En règle générale, les éléments JSON apparaissent par ordre alphabétique dans chaque section, mais cet exemple montre ici ces éléments pratiquement dans l’ordre selon lequel les étapes de l’application logique s’affichent dans le concepteur.

   ```json
   {
      "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
         "$connections": {
            "defaultValue": {},
            "type": "Object"
         }
      },
      "triggers": {
         "When_a_feed_item_is_published": {
            "recurrence": {
               "frequency": "Minute",
               "interval": 1
            },
            "splitOn": "@triggerBody()?['value']",
            "type": "ApiConnection",
            "inputs": {
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['rss']['connectionId']"
                  }
               },
               "method": "get",
               "path": "/OnNewFeed",
               "queries": {
                  "feedUrl": "http://feeds.reuters.com/reuters/topNews"
               }
            }
         }
      },
      "actions": {
         "Send_an_email": {
            "runAfter": {},
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "Title: @{triggerBody()?['title']}\n\nDate published: @{triggerBody()?['publishDate']}\n\nLink: @{triggerBody()?['primaryLink']}",
                  "Subject": "New RSS item: @{triggerBody()?['title']}",
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['outlook']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            }
         }
      },
      "outputs": {}
   }   
   ```

1. Lorsque vous avez terminé, enregistrez votre fichier de définition de l’application logique. Lorsque Visual Studio Code vous invite à confirmer le chargement de votre définition d’application logique dans votre abonnement Azure, choisissez **Charger**.

   ![Chargement de votre nouvelle application logique](./media/create-logic-apps-visual-studio-code/upload-new-logic-app.png)

   Après sa publication par Visual Studio Code dans Azure, vous retrouvez désormais votre application logique en ligne et opérationnelle dans le portail Azure. 

   ![Application logique publiée dans le portail Azure](./media/create-logic-apps-visual-studio-code/published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-logic-app"></a>Modifier l’application logique

Pour travailler sur une application logique existante qui est déjà déployée dans Azure, vous pouvez ouvrir le fichier de définition du workflow de cette application dans Visual Studio Code.

1. Si vous n’êtes pas connecté à votre abonnement Azure depuis Visual Studio Code, suivez les étapes décrites dans cet article pour [vous connecter maintenant](#sign-in-azure).

1. Dans la fenêtre Azure, sous **Logic Apps**, développez votre abonnement Azure et sélectionnez l’application logique que vous souhaitez. 

1. Dans le menu de l’application logique, sélectionnez **Ouvrir dans l’éditeur**. Ou, en regard du nom de l’application logique, choisissez l’icône de modification.

   ![Ouverture de l’éditeur pour l’application logique existante](./media/create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   Visual Studio Code ouvre le fichier .logicapp.json de la définition du workflow de votre application logique.

   ![Fichier de définition du workflow de l’application logique ouvert](./media/create-logic-apps-visual-studio-code/edit-logic-app-workflow-definition-file.png)

1. Apportez des modifications à la définition de votre application logique.

1. Lorsque vous avez terminé, enregistrez les modifications.

1. Lorsque Visual Studio Code vous invite à mettre à jour la définition de l’application logique dans votre abonnement Azure, choisissez **Charger**. 

   ![Chargement de vos modifications](./media/create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">forum Azure Logic Apps</a>.
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le <a href="http://aka.ms/logicapps-wish" target="_blank">site de commentaires des utilisateurs Logic Apps</a>.

