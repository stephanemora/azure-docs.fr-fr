---
title: 'Démarrage rapide : Bibliothèque de client Reconnaissance optique de caractères pour Java'
description: Dans ce guide de démarrage rapide, vous allez vous familiariser avec la bibliothèque de client Reconnaissance optique de caractères pour Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 2e3f7a747d69bcc28fc34ba439d8e8523c750230
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112083908"
---
<a name="HOLTop"></a>

Utilisez la bibliothèque de client Reconnaissance optique de caractères pour lire des textes imprimés et manuscrits dans des images.

[Documentation de référence](/java/api/overview/azure/cognitiveservices/client/computervision) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-computervision) |[Artefact (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision) | [Exemples](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)
* La version actuelle du [JDK (Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* L’[outil de génération Gradle](https://gradle.org/install/) ou un autre gestionnaire de dépendances.
* Une fois que vous avez votre abonnement Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="créez une ressource Vision par ordinateur"  target="_blank">créer une ressource Vision par ordinateur </a> dans le portail Azure pour obtenir votre clé et votre point de terminaison. Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application au service Vision par ordinateur. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-gradle-project"></a>Créer un projet Gradle

Dans une fenêtre de console (telle que cmd, PowerShell ou bash), créez un répertoire pour votre application et accédez-y. 

```console
mkdir myapp && cd myapp
```

Exécutez la commande `gradle init` à partir de votre répertoire de travail. Cette commande crée des fichiers de build essentiels pour Gradle, notamment *build.gradle.kts*, qui est utilisé au moment de l’exécution pour créer et configurer votre application.

```console
gradle init --type basic
```

Quand vous êtes invité à choisir un **DSL**, sélectionnez **Kotlin**.

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Ce guide de démarrage rapide utilise le gestionnaire de dépendances Gradle. Vous trouverez la bibliothèque de client et des informations concernant d’autres gestionnaires de dépendances sur le [référentiel central Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision).

Recherchez *build.gradle.kts* et ouvrez-le avec votre IDE ou votre éditeur de texte habituel. Copiez-y ensuite la configuration de build suivante. Cette configuration définit le projet en tant qu’application Java dont le point d’entrée est la classe **ComputerVisionQuickstart**. Elle importe la bibliothèque Vision par ordinateur.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "ComputerVisionQuickstart"
}
repositories {
    mavenCentral()
}
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.6-beta")
}
```

### <a name="create-a-java-file"></a>Créer un fichier Java

Dans votre répertoire de travail, exécutez la commande suivante pour créer un dossier de projet source :

```console
mkdir -p src/main/java
```

> [!TIP]
> Vous voulez voir l’intégralité du fichier de code de démarrage rapide à la fois ? Vous le trouverez sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java), qui contient les exemples de code utilisés dans ce guide de démarrage rapide.

Accédez au nouveau dossier et créez le fichier *ComputerVisionQuickstart.java*. Ouvrez-le dans l’éditeur ou l’IDE de votre choix.

### <a name="find-the-subscription-key-and-endpoint"></a>Rechercher la clé d’abonnement et le point de terminaison

Accédez au portail Azure. Si la ressource Vision par ordinateur que vous avez créée dans la section **Prérequis** a été déployée, cliquez sur le bouton **Accéder à la ressource** sous **Étapes suivantes**. Votre clé d’abonnement et votre point de terminaison se trouvent dans la page **Clé et point de terminaison** de la ressource, sous **Gestion des ressources**. 

Définissez la classe **ComputerVisionQuickstart**. Créez des variables pour votre clé d’abonnement et votre point de terminaison Vision par ordinateur. Collez votre clé d’abonnement et votre point de terminaison dans le code suivant à l’endroit indiqué. Votre point de terminaison Vision par ordinateur a le format `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/`.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> N’oubliez pas de supprimer la clé d’abonnement de votre code une fois que vous avez terminé, et ne la publiez jamais publiquement. Pour la production, envisagez d’utiliser une méthode de stockage et d’accès sécurisée pour vos informations d’identification. Par exemple, [Azure Key Vault](../../../../key-vault/general/overview.md).

Dans la méthode **main** de l’application, ajoutez des appels pour les méthodes utilisées dans ce guide de démarrage rapide. Vous les définirez plus tard.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_main)]


> [!div class="nextstepaction"]
> [J’ai configuré le client](?success=set-up-client#object-model) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=set-up-client&product=computer-vision&page=java-sdk)

## <a name="object-model"></a>Modèle objet

Les classes et interfaces suivantes gèrent certaines des principales fonctionnalités du SDK OCR Java.

|Nom|Description|
|---|---|
| [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | Cette classe est nécessaire pour toutes les fonctionnalités de Vision par ordinateur. Vous pouvez l’instancier avec vos informations d’abonnement et l’utiliser pour produire des instances d’autres classes.|

## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les tâches suivantes avec la bibliothèque de client OCR pour Java :

* [Authentifier le client](#authenticate-the-client)
* [Lire du texte imprimé et manuscrit](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Authentifier le client

Dans une nouvelle méthode, instanciez un objet [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) avec votre point de terminaison et votre clé.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_auth)]

> [!div class="nextstepaction"]
> [J’ai authentifié le client](?success=authenticate-client#read-printed-and-handwritten-text) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=authenticate-client&product=computer-vision&page=java-sdk)



## <a name="read-printed-and-handwritten-text"></a>Lire du texte imprimé et manuscrit

Le service OCR peut lire du texte visible dans une image et le convertir en flux de caractères. Cette section définit une méthode, `ReadFromFile`, qui prend un chemin de fichier local et imprime le texte de l’image dans la console.

> [!TIP]
> Vous pouvez également lire du texte dans une image distante référencée par URL. Consultez les méthodes [ComputerVision](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision), comme **read**. Ou consultez l’exemple de code sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) pour obtenir des scénarios impliquant des images distantes.

### <a name="set-up-test-image"></a>Configurer une image de test

Créez un dossier **resources/** dans le dossier **src/main/** de votre projet, puis ajoutez une image à partir de laquelle vous souhaitez lire du texte. Vous pouvez télécharger un [exemple d’image](https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/cognitive-services/Computer-vision/Images/readsample.jpg) à utiliser ici.

Ajoutez ensuite la définition de méthode suivante à la classe **ComputerVisionQuickstart**. Changez la valeur de `localFilePath` pour qu’elle corresponde à votre fichier image. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_setup)]

### <a name="call-the-read-api"></a>Appeler l’API Lire

Ajoutez ensuite le code suivant pour appeler la méthode **readInStreamWithServiceResponseAsync** pour l’image donnée.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_call)]

Le bloc de code suivant extrait l’ID d’opération de la réponse de l’appel à Read. Il utilise cet ID avec une méthode d’aide pour imprimer les résultats de lecture du texte dans la console. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_response)]

Fermez le bloc try/catch et la définition de la méthode.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_catch)]

### <a name="get-read-results"></a>Obtenir les résultats de la lecture

Ajoutez ensuite une définition pour la méthode d’aide. Cette méthode utilise l’ID d’opération obtenu à l’étape précédente pour interroger l’opération de lecture et obtenir les résultats OCR quand ils sont disponibles.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_call)]

Le reste de la méthode analyse les résultats OCR et les imprime dans la console.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_print)]

Enfin, ajoutez l’autre méthode d’aide utilisée ci-dessus, qui extrait l’ID d’opération de la réponse initiale.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_opid_extract)]

> [!div class="nextstepaction"]
> [J’ai lu du texte](?success=read-printed-handwritten-text#run-the-application) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=read-printed-handwritten-text&product=computer-vision&page=java-sdk)

## <a name="run-the-application"></a>Exécution de l'application

Vous pouvez générer l’application avec :

```console
gradle build
```

Exécutez l’application avec la commande `gradle run` :

```console
gradle run
```

> [!div class="nextstepaction"]
> [J’ai exécuté l’application](?success=run-the-application#clean-up-resources) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=run-the-application&product=computer-vision&page=java-sdk)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [J’ai nettoyé des ressources](?success=clean-up-resources#next-steps) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=clean-up-resources&product=computer-vision&page=java-sdk)

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à installer la bibliothèque de client OCR et à utiliser l’API Lire. À présent, découvrez-en plus sur les fonctionnalités de l’API Lire.

> [!div class="nextstepaction"]
>[Appeler l’API Lire](../../Vision-API-How-to-Topics/call-read-api.md)

* [Vue d’ensemble de la reconnaissance OCR](../../overview-ocr.md)
* Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java).
