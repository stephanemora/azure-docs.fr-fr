---
title: Créer une fonction Azure avec Java et IntelliJ
description: Découvrez comment créer et publier sur Azure une application serverless simple, déclenchée par HTTP, avec Java et IntelliJ.
author: jeffhollan
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 46044d4c6dd65944291aff157229be0abc60d3d7
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77913240"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>Créer votre première fonction Azure avec Java et IntelliJ

Cet article vous explique :
- Comment créer un projet de fonction [serverless](https://azure.microsoft.com/overview/serverless-computing/) avec IntelliJ IDEA et Apache Maven
- Les étapes pour tester et déboguer la fonction dans l’environnement de développement intégré (IDE) sur votre ordinateur
- Les instructions pour déployer le projet de fonction sur Azure Functions

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Configurer l''environnement de développement

Pour développer une fonction avec Java et IntelliJ, installez les logiciels suivants :

- [Kit de développeur Java](https://www.azul.com/downloads/zulu/) (JDK), version 8
- [Apache Maven](https://maven.apache.org), version 3.0 ou ultérieure
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download), versions Community ou Ultimate avec Maven
- [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT]
> Pour pouvoir suivre les différentes étapes de cet article, vous devez définir la variable d’environnement JAVA_HOME sur l’emplacement d’installation du JDK.

 Nous vous recommandons d’installer [Azure Functions Core Tools, version 2](functions-run-local.md#v2). Il fournit un environnement de développement local pour l’écriture, l’exécution et le débogage des fonctions Azure Functions.

## <a name="create-a-functions-project"></a>Créer un projet Functions

1. Dans IntelliJ IDEA, sélectionnez **Create New Project** (Créer un projet).  
1. Dans la fenêtre **New Project** (Nouveau projet), sélectionnez **Maven** dans le volet gauche.
1. Cochez la case **Create from archetype** (Créer à partir d’un archétype) et sélectionnez **Add Archetype** (Ajouter un archétype) pour [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
1. Dans la fenêtre **Add Archetype** (Ajouter un archétype), renseignez les champs comme suit :
    - _GroupId_ : com.microsoft.azure
    - _ArtifactId_ : azure-functions-archetype
    - _Version_ : utilisez la version la plus récente **1.22** du [dépôt central](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![Créer un projet Maven à partir d’un archétype dans IntelliJ IDEA](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. Sélectionnez **OK**, puis **Suivant**.
1. Entrez les détails de votre projet en cours et sélectionnez **Finish** (Terminer).

Maven crée les fichiers projet dans un nouveau dossier avec le même nom que la valeur _ArtifactId_. Le code généré du projet est une fonction simple, [déclenchée par HTTP](/azure/azure-functions/functions-bindings-http-webhook), qui retourne le corps de la requête HTTP à l’origine du déclenchement.

## <a name="run-functions-locally-in-the-ide"></a>Exécuter des fonctions localement dans l’IDE

> [!NOTE]
> Pour exécuter et déboguer des fonctions localement, assurez-vous qu’[Azure Functions Core Tools, version 2](functions-run-local.md#v2) est installé.

1. Importez les modifications manuellement ou activez l’[importation automatique](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Ouvrez la barre d’outils **Projets Maven**.
1. Développez **Cycle de vie**, puis ouvrez **package**. La solution est générée et empaquetée dans un répertoire cible nouvellement créé.
1. Développez **Plug-ins** >  **azure-functions** et ouvrez **azure-functions:run** pour démarrer le runtime local d’Azure Functions.  
  ![Barre d’outils Maven pour Azure Functions](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

1. Fermez la boîte de dialogue d’exécution quand vous avez fini de tester votre fonction. Un seul hôte de fonction à la fois peut être actif et en cours d’exécution localement.

## <a name="debug-the-function-in-intellij"></a>Déboguer la fonction dans IntelliJ

1. Pour démarrer l’hôte de fonction en mode débogage, ajoutez **-DenableDebug** comme argument quand vous exécutez votre fonction. Vous pouvez modifier la configuration dans les [objectifs maven](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) ou exécuter la commande suivante dans une fenêtre de terminal :  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   Cette commande force l’hôte de la fonction à ouvrir un port de débogage sur 5005.

1. Dans le menu **Run** (Exécuter), sélectionnez **Edit Configurations** (Modifier les configurations).
1. Sélectionnez **+** pour ajouter une configuration **Remote** (Distante).
1. Renseignez les champs _Name_ (Nom) et _Settings_ (Paramètres), puis sélectionnez **OK** pour enregistrer la configuration.
1. Après l’installation, sélectionnez **Debug (Déboguer) < Nom de la configuration distante >** ou appuyez sur les touches MAJ+F9 de votre clavier pour démarrer le débogage.

1. Quand vous avez terminé, arrêtez le débogueur et le processus en cours d’exécution. Un seul hôte de fonction à la fois peut être actif et en cours d’exécution localement.

## <a name="deploy-the-function-to-azure"></a>Déployer la fonction sur Azure

1. Avant de pouvoir déployer votre fonction sur Azure, vous devez vous [connecter par l’intermédiaire de l’interface Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   ``` azurecli
   az login
   ```

1. Déployez votre code dans une nouvelle fonction en utilisant la cible Maven `azure-functions:deploy`. Vous pouvez également sélectionner l’option **azure-fonctions:deploy** dans la fenêtre des projets Maven.

   ```
   mvn azure-functions:deploy
   ```

1. Trouvez l’URL de votre fonction dans la sortie de l’interface Azure CLI une fois la fonction déployée avec succès.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur le développement de fonctions Java, consultez le [Guide du développeur de fonctions Java](functions-reference-java.md).
- Ajoutez à votre projet des fonctions supplémentaires avec différents déclencheurs en utilisant la cible Maven `azure-functions:add`.
