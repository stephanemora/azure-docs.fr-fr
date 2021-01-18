---
title: Créer une fonction Kotlin dans Azure Functions avec IntelliJ
description: Découvrez comment utiliser IntelliJ pour créer une fonction Kotlin simple déclenchée par HTTP et la publier pour l’exécuter dans un environnement serverless dans Azure.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: 0207e4af9f845343866714ec207ca306cb327b36
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98035170"
---
# <a name="create-your-first-kotlin-function-in-azure-using-intellij"></a>Créer votre première fonction Kotlin dans Azure avec IntelliJ

Cet article vous montre comment créer une fonction Java déclenchée par HTTP dans un projet IntelliJ IDEA, comment exécuter et déboguer le projet dans l’environnement de développement intégré (IDE), et enfin comment déployer le projet de fonction sur une application de fonction dans Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Configurer l’environnement de développement

Pour créer et publier des fonctions Kotlin sur Azure avec IntelliJ, installez les logiciels suivants :

- [Kit de développeur Java](/azure/developer/java/fundamentals/java-jdk-long-term-support) (JDK), version 8
- [Apache Maven](https://maven.apache.org), version 3.0 ou ultérieure
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download), versions Community ou Ultimate avec Maven
- [Azure CLI](/cli/azure)
- [Version 2.x](functions-run-local.md#v2) d’Azure Functions Core Tools. Il fournit un environnement de développement local pour l’écriture, l’exécution et le débogage des fonctions Azure Functions.

> [!IMPORTANT]
> Pour pouvoir suivre les différentes étapes de cet article, vous devez définir la variable d’environnement JAVA_HOME sur l’emplacement d’installation du JDK.

## <a name="create-a-function-project"></a>Créer un projet Function

1. Dans IntelliJ IDEA, sélectionnez **Create New Project** (Créer un projet).  
1. Dans la fenêtre **New Project** (Nouveau projet), sélectionnez **Maven** dans le volet gauche.
1. Cochez la case **Create from archetype** (Créer à partir d’un archétype) et sélectionnez **Add Archetype** (Ajouter un archétype) pour [azure-functions-kotlin-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype).
1. Dans la fenêtre **Add Archetype** (Ajouter un archétype), renseignez les champs comme suit :
    - _GroupId_ : com.microsoft.azure
    - _ArtifactId_ : azure-functions-kotlin-archetype
    - _Version_ : utilisez la version la plus récente du [dépôt central](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype)
    ![Créer un projet Maven à partir d’un archétype dans IntelliJ IDEA](media/functions-create-first-kotlin-intellij/functions-create-intellij.png)  
1. Sélectionnez **OK**, puis **Suivant**.
1. Entrez les détails de votre projet en cours et sélectionnez **Finish** (Terminer).

Maven crée les fichiers projet dans un nouveau dossier avec le même nom que la valeur _ArtifactId_. Le code généré du projet est une fonction simple, [déclenchée par HTTP](./functions-bindings-http-webhook.md), qui retourne le corps de la requête HTTP à l’origine du déclenchement.

## <a name="run-project-locally-in-the-ide"></a>Exécuter le projet localement dans l’IDE

> [!NOTE]
> Pour exécuter et déboguer le projet localement, vérifiez qu’[Azure Functions Core Tools, version 2](functions-run-local.md#v2) est installé.

1. Importez les modifications manuellement ou activez l’[importation automatique](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Ouvrez la barre d’outils **Projets Maven**.
1. Développez **Cycle de vie**, puis ouvrez **package**. La solution est générée et empaquetée dans un répertoire cible nouvellement créé.
1. Développez **Plug-ins** >  **azure-functions** et ouvrez **azure-functions:run** pour démarrer le runtime local d’Azure Functions.  
  ![Barre d’outils Maven pour Azure Functions](media/functions-create-first-kotlin-intellij/functions-intellij-kotlin-maven-toolbar.png)  

1. Fermez la boîte de dialogue d’exécution quand vous avez fini de tester votre fonction. Un seul hôte de fonction à la fois peut être actif et en cours d’exécution localement.

## <a name="debug-the-project-in-intellij"></a>Déboguer le projet dans IntelliJ

1. Pour démarrer l’hôte de fonction en mode débogage, ajoutez **-DenableDebug** comme argument quand vous exécutez votre fonction. Vous pouvez modifier la configuration dans les [objectifs maven](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) ou exécuter la commande suivante dans une fenêtre de terminal :  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   Cette commande force l’hôte de la fonction à ouvrir un port de débogage sur 5005.

1. Dans le menu **Run** (Exécuter), sélectionnez **Edit Configurations** (Modifier les configurations).
1. Sélectionnez **+** pour ajouter une configuration **Remote** (Distante).
1. Renseignez les champs _Name_ (Nom) et _Settings_ (Paramètres), puis sélectionnez **OK** pour enregistrer la configuration.
1. Après l’installation, sélectionnez **Debug (Déboguer) < Nom de la configuration distante >** ou appuyez sur les touches MAJ+F9 de votre clavier pour démarrer le débogage.

   ![Déboguer le projet dans IntelliJ](media/functions-create-first-kotlin-intellij/debug-configuration-intellij.PNG)

1. Quand vous avez terminé, arrêtez le débogueur et le processus en cours d’exécution. Un seul hôte de fonction à la fois peut être actif et en cours d’exécution localement.

## <a name="deploy-the-project-to-azure"></a>Déployer le projet dans Azure

1. Avant de pouvoir déployer votre projet sur une application de fonction dans Azure, vous devez [vous connecter avec Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   ``` azurecli
   az login
   ```

1. Déployez votre code sur une nouvelle application de fonction en utilisant la cible Maven `azure-functions:deploy`. Vous pouvez également sélectionner l’option **azure-fonctions:deploy** dans la fenêtre des projets Maven.

   ```
   mvn azure-functions:deploy
   ```

1. Trouvez l’URL de votre fonction de déclencheur HTTP dans la sortie Azure CLI une fois l’application de fonction déployée.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez déployé votre première application de fonction Kotlin sur Azure, passez en revue le [guide des développeurs Java sur Azure Functions](functions-reference-java.md) pour plus d’informations sur le développement de fonctions Java et Kotlin.
- Ajoutez à votre projet des applications de fonction supplémentaires avec différents déclencheurs en utilisant la cible Maven `azure-functions:add`.
