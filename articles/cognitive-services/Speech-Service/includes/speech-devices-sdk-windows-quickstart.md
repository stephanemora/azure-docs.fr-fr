---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: dapine
ms.openlocfilehash: 1269ff3e21a794fddc2e0f0dc5d62816fa98215b
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658464"
---
Dans ce guide de démarrage rapide, vous allez découvrir comment utiliser le SDK Speech Devices pour Windows pour créer un produit avec reconnaissance vocale ou pour l’utiliser comme appareil de [transcription de conversation](../conversation-transcription-service.md). Pour la transcription de conversation, seul [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) est pris en charge. Pour les autres utilisations des fonctions vocales, les réseaux de microphones linéaires fournissant une géométrie de réseau de microphones sont pris en charge.

L’application est créée avec le package du SDK Speech et l’IDE Eclipse Java (v4) sur Windows 64 bits. Elle s’exécute sur un environnement d’exécution Java 8 (JRE) 64 bits.

Ce guide nécessite un compte [Azure Cognitive Services](../get-started.md) avec une ressource de service Speech. Si vous n’avez pas de compte, vous pouvez utiliser la [version d’évaluation gratuite](https://azure.microsoft.com/try/cognitive-services/) pour obtenir une clé d’abonnement.

Le code source de l’[exemple d’application](https://aka.ms/sdsdk-download-JRE) est inclus avec le SDK Speech Devices. Il est également [disponible sur GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

* Système d’exploitation : Windows 64 bits
* Un réseau de microphones comme [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [IDE Eclipse Java](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) uniquement.
* [Redistribuable Microsoft Visual C++](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)
* Clé d’abonnement Azure pour le service Speech. [Vous pouvez en obtenir une gratuitement](../get-started.md).
* Téléchargez la dernière version du [SDK Speech Devices](https://aka.ms/sdsdk-download-JRE) pour Java et décompressez le fichier .zip dans votre répertoire de travail.
   > [!NOTE]
   > Ce guide de démarrage rapide part du principe que l’application est extraite dans C:\SDSDK\JRE-Sample-Release.

Transcription de conversation est actuellement disponible seulement pour « en-US » et « zh-CN » dans les régions « USA Centre » et « Asie Est ». Vous devez disposer d’une clé Speech dans une de ces régions pour utiliser la transcription de conversation.

Si vous prévoyez d’utiliser les intentions, vous aurez besoin d’un abonnement [Language Understanding Intelligent Service (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription). Pour plus d’informations sur LUIS et la reconnaissance des intentions, consultez [Reconnaître les intentions vocales avec LUIS, C#](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp). Un [exemple de modèle LUIS](https://aka.ms/sdsdk-luis) est disponible pour cette application.

## <a name="create-and-configure-the-project"></a>Créer et configurer le projet

1. Démarrez Eclipse.

1. Dans le champ **Workspace** (Espace de travail) de **Eclipse IDE Launcher**, entrez le nom d’un nouveau répertoire d’espace de travail. Sélectionnez ensuite **Launch** (Lancer).

   ![Capture d’écran d’Eclipse Launcher](../media/speech-devices-sdk/eclipse-launcher.png)

1. La fenêtre principale de l’IDE Eclipse apparaît au bout d’un instant. Fermez l’écran d’accueil s’il en existe un.

1. Dans la barre de menus Eclipse, créez un projet en choisissant **File (Fichier)**  > **New (Nouveau)**  > **Java Project (Projet Java)** . Si ce n’est pas disponible, choisissez **Project** (Projet), puis **Java Project** (Projet Java).

1. L’Assistant **New Java Project** (Nouveau projet Java) démarre. **Accédez**  à l’emplacement de l’exemple de projet. Sélectionnez **Terminer**.

   ![Capture d’écran de l’Assistant New Java Project (Nouveau projet Java)](../media/speech-devices-sdk/eclipse-new-java-project.png)

1. Dans **Package explorer** (l’Explorateur de package), cliquez avec le bouton droit sur votre projet. Choisissez **Configure (Configurer)**  > **Convert to Maven Project (Convertir en projet Maven)** dans le menu contextuel. Sélectionnez **Terminer**.

   ![Capture d’écran de l’explorateur de package](../media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Ouvrez le fichier pom.xml et modifiez-le.

    À la fin du fichier, avant la balise de fermeture `</project>`, créez des éléments `repositories` et `dependencies`, comme indiqué ici, et vérifiez que le `version` correspond bien à votre version actuelle :
    ```xml
    <repositories>
         <repository>
             <id>maven-cognitiveservices-speech</id>
             <name>Microsoft Cognitive Services Speech Maven Repository</name>
             <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
         </repository>
    </repositories>
 
    <dependencies>
        <dependency>
             <groupId>com.microsoft.cognitiveservices.speech</groupId>
             <artifactId>client-sdk</artifactId>
             <version>1.11.0</version>
        </dependency>
    </dependencies>
   ```

1. Copiez le contenu de **Windows x64** à l’emplacement du projet Java, par exemple **C:\SDSDK\JRE-Sample-Release**

1. Copiez `kws.table`, `participants.properties` et `Microsoft.CognitiveServices.Speech.extension.pma.dll` dans le dossier **target/classes** du projet

## <a name="configure-the-sample-application"></a>Configurer l’exemple d’application

1. Ajoutez votre clé d’abonnement Speech au code source. Si vous voulez essayer la reconnaissance de l’intention, ajoutez également votre clé d’abonnement au [service Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), ainsi que l’ID de l’application.

   Pour la reconnaissance vocale et LUIS, vos informations vont dans `FunctionsList.java` :

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   Si vous utilisez la transcription de conversation, vos informations de clé et de région de reconnaissance vocale sont également nécessaires dans `Cts.java` :

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. Le mot clé par défaut (keyword) est « Computer ». Vous pouvez également essayer l’un des autres mots clés fournis, tels que « Machine » ou « Assistant ». Les fichiers de ressources pour ces mots clés alternatifs figurent dans le dossier keyword du Kit de développement logiciel (SDK) Speech Devices. Par exemple, `C:\SDSDK\JRE-Sample-Release\keyword\Computer` contient les fichiers utilisés pour le mot clé « Computer ».

    > [!TIP]
    > Vous pouvez également [créer un mot clé personnalisé](../speech-devices-sdk-create-kws.md).

    Pour utiliser un nouveau mot clé, mettez à jour la ligne suivante dans `FunctionsList.java`, puis copiez le mot clé dans votre application. Par exemple, pour utiliser le mot clé « Machine » du package de mots clés `machine.zip` :

   * Copiez le fichier `kws.table` du package zip dans le dossier projet **target/classes**.
   * Mettez à jour le `FunctionsList.java` avec le nom du mot clé :

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Exécuter l’exemple d’application à partir d’Eclipse

1. Dans la barre de menu Eclipse, sélectionnez **Run (Exécuter)**  > **Run As (Exécuter en tant que)**  > **Java Application (Application Java)** . Sélectionnez ensuite **FunctionsList**, puis **OK**.

   ![Capture d’écran de la sélection de Application Java](../media/speech-devices-sdk/eclipse-run-sample.png)

1. L’exemple d’application du Kit de développement logiciel (SDK) Speech Devices démarre et affiche les options suivantes :

   ![Exemple d’application et options du Kit de développement logiciel (SDK) Speech Devices](../media/speech-devices-sdk/java-sample-app-windows.png)

1. Essayez la nouvelle démonstration de **Transcription de conversation**. Démarrez la transcription avec **Session** > **Démarrer**. Par défaut, tout le monde est invité. Cependant, si vous avez des signatures vocales des participants, vous pouvez les placer dans un fichier `participants.properties` dans le dossier **target/classes** du projet. Pour générer la signature vocale, consultez [Transcrire des conversations (SDK)](../how-to-use-conversation-transcription-service.md).

   ![Application de démonstration de la transcription de conversation](../media/speech-devices-sdk/cts-sample-app-windows.png)

## <a name="create-and-run-a-standalone-application"></a>Créer et exécuter une application autonome

1. Dans **Package explorer** (l’Explorateur de package), cliquez avec le bouton droit sur votre projet. Choisissez **Export** (Exporter).

1. La fenêtre **Export** (Exporter) apparaît. Développez **Java**, sélectionnez **Runnable JAR file** (Fichier JAR exécutable), puis sélectionnez **Suivant**.

   ![Capture d’écran de la fenêtre Export (Exporter)](../media/speech-devices-sdk/eclipse-export-windows.png)

1. La fenêtre **Runnable JAR File Export** (Fichier JAR exécutable) apparaît. Choisissez une **Export destination** (Destination de l’exportation) pour l’application, puis sélectionnez **Finish** (Terminer).

   ![Capture d’écran de l’exportation du fichier JAR exécutable](../media/speech-devices-sdk/eclipse-export-jar-windows.png)

1. Placez `kws.table`, `participants.properties`, `unimic_runtime.dll`, `pma.dll` et `Microsoft.CognitiveServices.Speech.extension.pma.dll` dans le dossier de destination sélectionné ci-dessus, car ces fichiers sont nécessaires à l’application.

1. Pour exécuter l’application autonome

   ```powershell
   java -jar SpeechDemo.jar
   ```
