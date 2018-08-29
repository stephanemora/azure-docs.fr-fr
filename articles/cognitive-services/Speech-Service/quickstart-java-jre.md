---
title: 'Démarrage rapide : Reconnaissance vocale dans Java (Windows ou Linux)'
titleSuffix: Microsoft Cognitive Services
description: Découvrir comment fonctionne la reconnaissance vocale dans Java (Windows ou Linux)
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 08/16/2018
ms.author: fmegen
ms.openlocfilehash: 11aba1ae6f49d6c00fabd928ae3aefedcbea8ed8
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234263"
---
# <a name="quickstart-recognize-speech-in-java-windows-or-linux"></a>Démarrage rapide : Reconnaissance vocale dans Java (Windows ou Linux)

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ce document décrit comment créer une application console basée sur Java pour le JRE (Java Runtime Environment) qui utilise le kit Speech SDK.
L’application est basée sur le package Maven du kit SDK Microsoft Cognitive Services.
Nous utilisons Eclipse comme environnement de développement intégré (IDE).

## <a name="prerequisites"></a>Prérequis

* Clé d’abonnement pour le service Speech. Consultez l’article [Essayer le service Speech gratuitement](get-started.md).
* PC (Windows x64, Ubuntu 16.04 x64) capable d’exécuter Eclipse, avec un microphone en état de marche.
* JRE 64 bits/JDK pour Java 8.
* Version 4.8 d’[Eclipse](https://www.eclipse.org) 64 bits.
* Sur Ubuntu 16.04, exécutez les commandes suivantes pour installer les packages requis :

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

## <a name="create-and-configure-your-project"></a>Créer et configurer votre projet

1. Démarrez Eclipse.

1. Dans le Lanceur d’Eclipse, entrez le nom d’un nouveau répertoire dans le champ **Espace de travail**.
   Ensuite, cliquez sur **Lancer**.

   ![Créer un espace de travail Eclipse](media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. Après un moment, la fenêtre principale de l’IDE Eclipse apparaît.
   Si vous voyez un écran de bienvenue, fermez-le.

1. Sélectionnez **Fichier** \> **Nouveau** \> **Projet**.

1. Dans l’Assistant **Nouveau projet** qui s’affiche, sélectionnez **Projet Java** et cliquez sur **Suivant**.

   ![Sélectionner un Assistant](media/sdk/qs-java-jre-02-select-wizard.png)

1. Dans la fenêtre suivante, entrez **quickstart** comme nom de projet et choisissez **JavaSE-1.8** (ou ultérieur) comme environnement d’exécution.
   Cliquez sur **Terminer**.

   ![Sélectionner un Assistant](media/sdk/qs-java-jre-03-create-java-project.png)

1. Si une fenêtre intitulée **Ouvrir la perspective associée ?** s’affiche, sélectionnez **Ouvrir la perspective**.

1. Dans l’**Explorateur de packages**, cliquez avec le bouton droit sur le projet **quickstart** et sélectionnez **Configurer** \> **Convertir en projet Maven**.

   ![Convertir en projet Maven](media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. Dans la fenêtre qui s’affiche, entrez **com.microsoft.cognitiveservices.speech.samples** comme **ID de groupe** et **quickstart** comme **ID d’artefact**. Sélectionnez **Terminer**.

   ![Configurer le modèle POM Maven](media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Modifiez le fichier **pom.xml**.

  * À la fin du fichier, avant la balise de fermeture `</project>`, créez une section de référentiels avec une référence au référentiel Maven pour le kit Speech SDK :

    [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

  * Ajoutez ensuite une section de dépendances avec le kit Speech SDK version 0.6.0 en tant que dépendance :

    [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

  * Enregistrez les modifications.

## <a name="add-the-sample-code"></a>Ajouter l’exemple de code

1. Sélectionnez **Fichier** \> **Nouvelle** \> **Classe** pour ajouter une nouvelle classe vide dans votre projet Java.

1. Dans la fenêtre **Nouvelle classe Java** entrez **speechsdk.quickstart** dans le champ **Package** et **Main** dans le champ **Nom**.

   ![Création d’une classe Main](media/sdk/qs-java-jre-06-create-main-java.png)

1. Remplacez tout le code `Main.java` par l’extrait de code suivant :

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. Remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

1. Enregistrez les modifications apportées au projet.

## <a name="build-and-run-the-sample"></a>Créer et exécuter l’exemple.

Appuyez sur F11 ou sélectionnez **Exécuter** \> **Déboguer**.
Les 15 secondes suivantes de saisie vocale provenant de votre microphone seront reconnues et enregistrées dans la fenêtre console.

![Sortie de la console après une reconnaissance réussie](media/sdk/qs-java-jre-07-console-output.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Recherchez cet exemple dans le dossier `quickstart/java-jre`.

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez nos exemples](speech-sdk.md#get-the-samples)
