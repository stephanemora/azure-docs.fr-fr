---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: aeb4c7ef44791b174940790fa3e03f0f40ed1ba4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097968"
---
Une fois votre ressource et votre conteneur de stockage configurés, créez un projet de NER personnalisée. Un projet est une zone de travail dans laquelle vous créez vos modèles IA personnalisés à partir de vos données. Votre projet est uniquement accessible à vous-même et aux autres personnes disposant d’un accès de contributeur à la ressource Azure utilisée.

1. Connectez-vous au [portail Language Studio](https://aka.ms/languageStudio). Une fenêtre apparaît pour vous permettre de sélectionner votre abonnement et votre ressource Language. Sélectionnez la ressource que vous avez créée à l’étape ci-dessus. 

2. Recherchez la section **Extraction d’entité**, puis sélectionnez **Reconnaissance d’entité nommée personnalisée** parmi les services disponibles.

3. Sélectionnez **Créer un projet** dans le menu supérieur de la page des projets. La création d’un projet vous permet d’étiqueter les données, d’entraîner, d’évaluer, d’améliorer et de déployer vos modèles. 

    
    :::image type="content" source="../media/create-project.png" alt-text="Capture d’écran de la page de création de projet." lightbox="../media/create-project.png":::


4.  Une fois que vous avez cliqué sur **Créer un projet**, un écran apparaît pour vous permettre de connecter votre compte de stockage. Si vous ne trouvez pas votre compte de stockage, vérifiez que vous avez créé une ressource en suivant les étapes ci-dessus. 

    >[!NOTE]
    > * Vous ne devez effectuer cette étape qu’une seule fois pour chaque nouvelle ressource utilisée. 
    > * Ce processus est irréversible. Si vous connectez un compte de stockage à votre ressource, vous ne pourrez pas le déconnecter par la suite.
    > * Vous pouvez connecter votre ressource à un seul compte de stockage.
    > * Si vous avez déjà connecté un compte de stockage, l’écran **Sélectionner le type de projet** s’affiche. Passez à l’étape suivante.
    
    :::image type="content" source="../media/connect-storage.png" alt-text="Capture d’écran montrant l’écran de connexion du stockage." lightbox="../media/connect-storage.png":::

<!--If you're using a preexisting resource, see [creating Azure resources](../concepts/use-azure-resources.md). When you are done, select **Next**.--> 

5. Entrez les informations relatives au projet, notamment son nom, sa description et la langue des fichiers qu’il contient. Vous ne pouvez pas changer le nom de votre projet par la suite. 

6. Passez en revue les données que vous avez entrées et sélectionnez **Créer un projet**.
