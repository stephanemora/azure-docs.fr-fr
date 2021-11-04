---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: b71810497c555a33fcf8a7359c32c8397cb6cec8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096552"
---
Une fois votre ressource et votre conteneur de stockage configurés, créez un projet de classification de texte. Un projet est une zone de travail qui vous permet de créer des modèles IA personnalisés en fonction de vos données. Votre projet est uniquement accessible à vous-même et aux autres personnes disposant d’un accès de contributeur à la ressource Azure utilisée.

1. Connectez-vous à [Language Studio](https://aka.ms/languageStudio). Une fenêtre apparaît pour vous permettre de sélectionner votre abonnement et votre ressource Language. Sélectionnez la ressource que vous avez créée à l’étape ci-dessus.

2. Dans la section **Classifier du texte** de Language Studio, sélectionnez **classification de texte personnalisée** dans la liste des services disponibles.

3. Sélectionnez **Créer un projet** dans le menu supérieur de la page des projets. La création d’un projet vous permet d’étiqueter les données, d’entraîner, d’évaluer, d’améliorer et de déployer vos modèles. 

    :::image type="content" source="../media/create-project.png" alt-text="Capture d’écran de la page de création de projet." lightbox="../media/create-project.png":::

4. Si vous avez créé votre ressource en suivant les étapes ci-dessus, ajoutez des informations relatives au projet, par exemple son nom, puis sélectionnez votre conteneur de stockage.

    1. Sélectionnez votre type de projet. Pour ce guide de démarrage rapide, nous allons créer un projet de classification multi-étiquettes. Cliquez ensuite sur **Suivant**.

    2. Entrez les informations relatives au projet, notamment son nom, sa description et la langue des fichiers qu’il contient. Vous ne pourrez pas changer le nom de votre projet par la suite.

        >[!TIP]
        > Votre jeu de données n’a pas besoin d’être entièrement dans la même langue. Vous pouvez avoir plusieurs fichiers, chacun comportant différentes langues prises en charge. Si votre jeu de données contient des fichiers de différentes langues ou si vous prévoyez d’autres langues au moment de l’exécution, sélectionnez **enable multi-lingual dataset** (activer le jeu de données multilingue) quand vous entrez les informations de base de votre projet.

    3. Sélectionnez le conteneur dans lequel vous avez chargé vos données. Pour ce guide de démarrage rapide, nous allons utiliser le fichier d’étiquettes existant disponible dans le conteneur. Cliquez ensuite sur **Suivant**.
 
    4. Passez en revue les données entrées, puis sélectionnez **Créer un projet**.
