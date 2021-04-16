---
title: Créer une fonction Java dans Azure Functions à l’aide d’IntelliJ
description: Découvrez comment utiliser IntelliJ pour créer une fonction Java simple déclenchée par HTTP, que vous publiez ensuite pour l’exécuter dans un environnement serverless dans Azure.
author: yucwan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: yucwan
ms.custom: mvc, devcenter, devx-track-java
ms.openlocfilehash: 45fb62b446e6b589dc0cb9287a8aebe7f4e699b1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105543638"
---
# <a name="create-your-first-java-function-in-azure-using-intellij"></a>Créer votre première fonction Java dans Azure à l’aide d’IntelliJ

Cet article vous explique :
- Comment créer une fonction Java déclenchée par HTTP dans un projet IntelliJ IDEA
- Les étapes pour tester et déboguer le projet dans l’environnement de développement intégré (IDE) sur votre ordinateur
- Les instructions pour déployer le projet de fonction sur Azure Functions

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Configurer l''environnement de développement

Pour créer et publier des fonctions Java sur Azure avec IntelliJ, installez les logiciels suivants :

+ Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
+ Un [kit de développement Java (JDK) pris en charge par Azure](/azure/developer/java/fundamentals/java-jdk-long-term-support) pour Java 8
+ [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition ou Community Edition
+ [Maven 3.5.0 (ou version supérieure)](https://maven.apache.org/download.cgi)
+ La dernière version de [Functions Core Tools](https://github.com/Azure/azure-functions-core-tools)


## <a name="installation-and-sign-in"></a>Installation et connexion

1. Dans la boîte de dialogue Settings/Preferences d’IntelliJ IDEA (Ctrl+Alt+S), sélectionnez **Plugins**. Ensuite, recherchez **Azure Toolkit for IntelliJ** sur la **Place de marché** et cliquez sur **Installer**. Une fois l’installation terminée, cliquez sur **Redémarrer** pour activer le plug-in. 

    ![Plug-in Azure Toolkit for IntelliJ sur la Place de marché][marketplace]

2. Pour vous connecter à votre compte Azure, ouvrez la barre latérale **Azure Explorer**, puis cliquez sur l’icône **Connexion à Azure** dans la barre en haut (ou dans le menu IDEA **Tools/Azure/Azure Sign in**).
    ![Commande IntelliJ de connexion à Azure][intellij-azure-login]

3. Dans la fenêtre **Connexion à Azure** qui s’affiche, sélectionnez **Connexion à l’appareil**, puis cliquez sur **Connexion** ([autres options de connexion](/azure/developer/java/toolkit-for-intellij/sign-in-instructions)).

   ![Fenêtre Connexion à Azure avec l’option Connexion à l’appareil activée][intellij-azure-popup]

4. Cliquez sur **Copier et ouvrir** dans la boîte de dialogue **Connexion à l’appareil Azure**.

   ![Boîte de dialogue Connexion à Azure][intellij-azure-copycode]

5. Dans le navigateur, collez le code de votre appareil (qui a été copié quand vous avez cliqué sur **Copier et ouvrir** à la dernière étape), puis cliquez sur **Suivant**.

   ![Navigateur de connexion à l’appareil][intellij-azure-link-ms-account]

6. Lorsque la boîte de dialogue **Sélectionner des abonnements**, sélectionnez les abonnements que vous souhaitez utiliser, puis cliquez sur **Sélectionner**.

   ![Boîte de dialogue Sélectionner des abonnements][intellij-azure-login-select-subs]
   
## <a name="create-your-local-project"></a>Créer votre projet local

Dans cette section, vous utilisez Azure Toolkit for IntelliJ pour créer un projet Azure Functions local. Plus loin dans cet article, vous allez publier votre code de fonction sur Azure. 

1. Ouvrez la boîte de dialogue d’accueil IntelliJ, sélectionnez *Create New Project* pour ouvrir l’Assistant de création de projet, puis sélectionnez *Azure Functions*.

    ![Créer un projet de fonction](media/functions-create-first-java-intellij/create-functions-project.png)

1. Sélectionnez *Http Trigger*, puis cliquez sur *Next* et suivez les étapes de l’Assistant pour effectuer toutes les configurations dans les pages suivantes. Confirmez l’emplacement du projet, puis cliquez sur *Finish*. Intellj IDEA ouvre alors votre nouveau projet.

    ![Créer un projet de fonction, Terminer](media/functions-create-first-java-intellij/create-functions-project-finish.png)

## <a name="run-the-project-locally"></a>Exécutez le projet localement.

1. Accédez à `src/main/java/org/example/functions/HttpTriggerFunction.java` pour voir le code généré. À la ligne *17*, vous remarquez un bouton *Run* vert. Cliquez dessus, puis sélectionnez *Run 'azure-function-exam...'* . Vous voyez que votre application de fonction s’exécute localement avec quelques entrées de journal.

    ![Exécution locale du projet](media/functions-create-first-java-intellij/local-run-functions-project.png)

    ![Exécution locale du projet, sortie](media/functions-create-first-java-intellij/local-run-functions-output.png)

1. Vous pouvez essayer la fonction en accédant au point de terminaison affiché à partir du navigateur comme `http://localhost:7071/api/HttpTrigger-Java?name=Azure`.

    ![Exécution locale de la fonction, résultat de test](media/functions-create-first-java-intellij/local-run-functions-test.png)

1. Le journal est également affiché dans votre environnement IDEA. À présent, arrêtez l’application de fonction en cliquant sur le bouton *stop*.

    ![Exécution locale de la fonction, journal de test](media/functions-create-first-java-intellij/local-run-functions-log.png)

## <a name="debug-the-project-locally"></a>Déboguer le projet localement

1. Pour déboguer localement le code de la fonction dans votre projet, sélectionnez le bouton *Déboguer* dans la barre d’outils. Si vous ne voyez pas la barre d’outils, activez-la en choisissant **View** > **Appearance** > **Toolbar** (Afficher > Apparence > Barre d’outils).

    ![Bouton Déboguer localement l’application de fonction](media/functions-create-first-java-intellij/local-debug-functions-button.png)

1. Cliquez sur la ligne *20* du fichier `src/main/java/org/example/functions/HttpTriggerFunction.java` pour ajouter un point d’arrêt, puis accédez de nouveau au point de terminaison `http://localhost:7071/api/HttpTrigger-Java?name=Azure`. Vous voyez que le point d’arrêt est atteint. Vous pouvez tester d’autres fonctionnalités de débogage comme *step*, *watch* et *evaluation*. Arrêtez la session de débogage en cliquant sur le bouton d’arrêt.

    ![Arrêt du débogage local de l’application de fonction](media/functions-create-first-java-intellij/local-debug-functions-break.png)

## <a name="deploy-your-project-to-azure"></a>Déployer votre projet sur Azure

1. Cliquez avec le bouton droit sur votre projet dans l’explorateur de projets IntelliJ, puis sélectionnez *Azure -> Deploy to Azure Functions*.

    ![Déployer le projet sur Azure](media/functions-create-first-java-intellij/deploy-functions-to-azure.png)

1. Si vous n’avez pas encore d’application de fonction, cliquez sur *+* dans la ligne *Fonction*. Saisissez le nom de l’application de fonction et choisissez la plateforme appropriée. Ici, nous pouvons simplement accepter la valeur par défaut. Cliquez sur *OK*. La nouvelle application de fonction que vous venez de créer sera automatiquement sélectionnée. Cliquez sur *Run* pour déployer vos fonctions.

    ![Create function app in Azure](media/functions-create-first-java-intellij/deploy-functions-create-app.png)

    ![Déployer une application de fonction sur Azure, journal](media/functions-create-first-java-intellij/deploy-functions-log.png)

## <a name="manage-function-apps-from-idea"></a>Gérer les applications de fonction à partir d’IDEA

1. Vous pouvez gérer vos applications de fonction à l’aide d’*Azure Explorer* dans votre environnement IDEA. Cliquez sur *Function App* (Application de fonction) pour y voir toutes vos applications de fonction.

    ![Voir les applications de fonction dans l’explorateur](media/functions-create-first-java-intellij/explorer-view-functions.png)

1. Cliquez sur l’une de vos applications de fonction pour la sélectionner, puis cliquez avec le bouton droit et sélectionnez *Show Properties* (Afficher les propriétés) pour ouvrir la page de détails. 

    ![Afficher les propriétés de l’application de fonction](media/functions-create-first-java-intellij/explorer-functions-show-properties.png)

1. Cliquez avec le bouton droit sur votre application de fonction *HttpTrigger-Java* et sélectionnez *Trigger Function* (Fonction de déclencheur). Le navigateur s’ouvre sur l’URL du déclencheur.

    ![Capture d’écran montrant un navigateur avec l’URL.](media/functions-create-first-java-intellij/explorer-trigger-functions.png)

## <a name="add-more-functions-to-the-project"></a>Ajouter des fonctions au projet

1. Cliquez avec le bouton droit sur le package *org.example.functions* et sélectionnez *New -> Azure Function Class*. 

    ![Ajouter des fonctions au projet - Entrée](media/functions-create-first-java-intellij/add-functions-entry.png)

1. Renseignez le nom de la classe *HttpTest* et sélectionnez *HttpTrigger* dans l’Assistant de création de classe de fonction. Cliquez sur *OK* pour confirmer la création. Vous pouvez ainsi créer des fonctions comme vous le souhaitez.

    ![Capture d’écran montrant la boîte de dialogue Créer une classe de fonction.](media/functions-create-first-java-intellij/add-functions-trigger.png)
    
    ![Ajouter des fonctions au projet - Sortie](media/functions-create-first-java-intellij/add-functions-output.png)

## <a name="cleaning-up-functions"></a>Nettoyage des fonctions

1. Suppression de fonctions dans Azure Explorer
      
      ![Capture d’écran montrant le bouton Supprimer sélectionné dans un menu contextuel.](media/functions-create-first-java-intellij/delete-function.png)
      

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé un projet Java avec une fonction déclenchée par HTTP, l’avez exécuté sur votre ordinateur local et l’avez déployée sur Azure. À présent, étendez votre fonction en effectuant un...

> [!div class="nextstepaction"]
> [Ajout de liaison de sortie de file d’attente Stockage Azure](./functions-add-output-binding-storage-queue-java.md)


[marketplace]:./media/functions-create-first-java-intellij/marketplace.png
[intellij-azure-login]: media/functions-create-first-java-intellij/intellij-azure-login.png
[intellij-azure-popup]: media/functions-create-first-java-intellij/intellij-azure-login-popup.png
[intellij-azure-copycode]: media/functions-create-first-java-intellij/intellij-azure-login-copyopen.png
[intellij-azure-link-ms-account]: media/functions-create-first-java-intellij/intellij-azure-login-linkms-account.png
[intellij-azure-login-select-subs]: media/functions-create-first-java-intellij/intellij-azure-login-selectsubs.png
