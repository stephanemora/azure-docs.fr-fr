---
title: Créer une application de fonction Azure avec Java et IntelliJ | Microsoft Docs
description: Guide pratique pour créer et publier sur Azure Functions une application serverless simple déclenchée par HTTP avec Java et IntelliJ.
services: functions
documentationcenter: na
author: jeffhollan
manager: jpconnock
keywords: azure functions, fonctions, traitement des événements, calcul, architecture serverless, java
ms.service: functions
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 317d8bf40ef152e5b7dae6406be29330feaaa8d3
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2018
ms.locfileid: "43842468"
---
# <a name="create-your-first-function-with-java-and-intellij-preview"></a>Créer votre première fonction avec Java et IntelliJ (préversion)

> [!NOTE] 
> Java pour Azure Functions est actuellement disponible en préversion.

Cet article vous montre comment créer un projet de fonction [serverless](https://azure.microsoft.com/overview/serverless-computing/) avec IntelliJ IDEA et Apache Maven, comment le tester et le déboguer sur votre propre ordinateur à partir de l’IDE, et comment le déployer sur Azure Functions. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Configuration de l'environnement de développement

Pour développer une application de fonction avec Java et IntelliJ, les éléments suivants doivent être installés :

-  [Java Developer Kit (JDK)](https://www.azul.com/downloads/zulu/) version 8.
-  [Apache Maven](https://maven.apache.org) version 3.0 ou ultérieure.
-  [IntelliJ IDEA](https://www.jetbrains.com/idea/download), Community ou Ultimate avec les outils Maven.
-  [interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> Pour pouvoir effectuer ce démarrage rapide, vous devez définir la variable d’environnement JAVA_HOME sur l’emplacement d’installation du JDK.

Il est fortement recommandé d’installer aussi [Azure Functions Core Tools, version 2](functions-run-local.md#v2), qui offre un environnement de développement local pour écrire, exécuter et déboguer les fonctions Azure. 


## <a name="create-a-functions-project"></a>Créer un projet Functions

1. Dans IntelliJ IDEA, cliquez sur **Create New Project** (Créer un projet).  
1. Choisissez la création à partir de **Maven**
1. Cochez la case pour **Create from archetype** (Créer à partir d’un archétype) et **Add Archetype** (Ajouter un archétype) pour [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
    - GroupId : com.microsoft.azure
    - ArtifactId : azure-functions-archetype
    - Version : Utilisez la dernière version du [référentiel central](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![IntelliJ Maven create](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. Cliquez sur **Next** (Suivant), entrez les détails pour le projet actuel, puis cliquez sur **Finish** (Terminer).

Maven crée les fichiers projet dans un nouveau dossier avec le nom de _artifactId_. Le code généré dans le projet est une fonction simple [déclenchée par HTTP](/azure/azure-functions/functions-bindings-http-webhook) qui retourne le corps de la demande HTTP à l’origine du déclenchement.

## <a name="run-functions-locally-in-the-ide"></a>Exécuter des fonctions localement dans l’IDE

> [!NOTE]
> [Azure Functions Core Tools, version 2](functions-run-local.md#v2) doit être installé pour pouvoir exécuter et déboguer des fonctions localement.

1. Choisissez d’importer les modifications ou vérifiez que [l’importation automatique](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html) est activée.
1. Ouvrez la barre d’outils **Projets Maven**.
1. Sous Cycle de vie, double-cliquez sur **package** pour empaqueter et générer la solution, et créez un répertoire cible.
1. Sous Plug-ins -> azure-functions, double-cliquez sur **azure-functions:run** pour démarrer le runtime local d’Azure Functions.  
  ![Barre d’outils Maven pour Azure Functions](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

Fermez la boîte de dialogue d’exécution quand vous avez terminé le test de votre fonction. Un seul hôte de fonction à la fois peut être actif et en cours d’exécution localement.

### <a name="debug-the-function-in-intellij"></a>Déboguer la fonction dans IntelliJ
Pour démarrer l’hôte de fonction en mode débogage, ajoutez **-DenableDebug** comme argument quand vous exécutez votre fonction. Vous pouvez exécuter la ligne de commande ci-dessous dans un terminal ou la configurer dans des [objectifs maven](https://www.jetbrains.com/help/idea/maven-support.html#run_goal). L’hôte de la fonction ouvre alors le port de débogage 5005. 

```
mvn azure-functions:run -DenableDebug
```

Pour déboguer dans IntelliJ, dans le menu **Run** (Exécuter), sélectionnez **Edit Configurations** (Modifier les configurations). Cliquez sur **+** pour ajouter une configuration à distance (**Remote**). Renseignez **Name** (Nom) et **Settings** (Paramètres), puis cliquez sur **OK** pour enregistrer la configuration. Une fois terminé, cliquez sur **Debug** (Déboguer) « nom de la configuration à distance » ou appuyez sur **Maj+F9** pour démarrer le débogage.

![Déboguer des fonctions dans IntelliJ](media/functions-create-first-java-intellij/debug-configuration-intellij.PNG)

Quand vous avez terminé, arrêtez le débogueur et le processus en cours d’exécution. Un seul hôte de fonction à la fois peut être actif et en cours d’exécution localement.

## <a name="deploy-the-function-to-azure"></a>Déployer la fonction sur Azure

Le processus de déploiement sur Azure Functions utilise les informations d’identification du compte provenant d’Azure CLI. [Connectez-vous avec Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) avant de continuer en utilisant l’invite de commandes de votre ordinateur.

```azurecli
az login
```

Déployez votre code dans une nouvelle application de fonction en utilisant la cible Maven `azure-functions:deploy`, ou sélectionnez l’option azure-functions:deploy dans la fenêtre Projets Maven.

```
mvn azure-functions:deploy
```

Quand le déploiement est terminé, vous voyez l’URL que vous pouvez utiliser pour accéder à votre application de fonction Azure :

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur le développement de fonctions Java, consultez le [Guide du développeur de fonctions Java](functions-reference-java.md).
- Ajoutez des fonctions supplémentaires avec différents déclencheurs à votre projet en utilisant la cible Maven `azure-functions:add`.
