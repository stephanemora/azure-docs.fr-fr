---
title: Définir et utiliser des flux de travail de modération du contenu - Content Moderator
titlesuffix: Azure Cognitive Services
description: Vous pouvez utiliser les API et le concepteur de flux de travail Azure Content Moderator pour définir des flux de travail personnalisés et des seuils en fonction de vos stratégies de contenu.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 265d5a2bd6a256d287e6ae164e883ade783f960f
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54262402"
---
# <a name="define-test-and-use-workflows"></a>Définir, tester et utiliser des flux de travail

Vous pouvez utiliser les API et le concepteur de flux de travail Azure Content Moderator pour définir des flux de travail personnalisés et des seuils en fonction de vos stratégies de contenu.

Les flux de travail « se connectent » à l’API Content Moderator à l’aide de connecteurs. Vous pouvez utiliser d’autres API si un connecteur pour cette API est disponible. L’exemple présenté ici utilise le connecteur Content Moderator qui est inclus par défaut.

## <a name="browse-to-the-workflows-section"></a>Accéder à la section Flux de travail

Dans l’onglet **Paramètres**, sélectionnez **Flux de travail**.

  ![Paramètres des flux de travail](images/2-workflows-0.png)

## <a name="start-a-new-workflow"></a>Démarrage d’un nouveau flux de travail

Sélectionnez **Ajouter un flux de travail**.

  ![Modifier un flux de travail](images/2-workflows-1.png)

## <a name="assign-a-name-and-description"></a>Assigner un nom et une description

Nommez votre flux de travail, entrez une description et choisissez si le flux de travail va gérer les images ou du texte.

  ![Nom et description du flux de travail](images/ocr-workflow-step-1.PNG)

## <a name="define-the-evaluation-criteria-condition"></a>Définir les critères d’évaluation (« condition »)

Dans la capture d’écran suivante, vous pouvez voir les champs et les sélections if-then-else que vous devez définir dans vos flux de travail. Choisir un connecteur. Cet exemple utilise **Content Moderator**. Selon le connecteur que vous choisissez, les options disponibles pour la sortie changent.

  ![Définir la condition du flux de travail](images/ocr-workflow-step-2-condition.PNG)

Une fois que vous avez sélectionné le connecteur et la sortie de votre choix, sélectionnez un opérateur et la valeur de la condition.

## <a name="define-the-action-to-take"></a>Définir l’action à entreprendre

Sélectionnez l’action à entreprendre et la condition à respecter. L’exemple suivant crée une révision de l’image, attribue une balise `a`,et la met en évidence pour la condition indiquée. Vous pouvez également combiner plusieurs conditions pour obtenir les résultats souhaités. Vous pouvez éventuellement ajouter un autre chemin d’accès alternatif (Else).

  ![Définir l’action du flux de travail](images/ocr-workflow-step-3-action.PNG)

## <a name="save-your-workflow"></a>Enregistrer votre flux de travail

Enfin, enregistrez le flux de travail et notez le nom du flux de travail. Vous avez besoin du nom pour démarrer un travail de modération à l’aide de l’API Review.

## <a name="test-the-workflow"></a>Tester le flux de travail

Maintenant que vous avez défini un flux de travail personnalisé, testez-le avec un exemple de contenu.

Sélectionnez le bouton **Exécuter le flux de travail** correspondant.

  ![Test du flux de travail](images/ocr-workflow-step-6-list.PNG)

### <a name="upload-a-file"></a>Charger un fichier

Enregistrez l’[exemple d’image](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) sur votre disque local. Pour tester le flux de travail, sélectionnez **Choisir le(s) fichier(s)** et chargez l’image.

  ![Charger le fichier image](images/ocr-workflow-step-7-upload.PNG)

### <a name="track-the-workflow"></a>Suivre le flux de travail

Suivez le flux de travail lors de son exécution.

  ![Suivre l’exécution du flux de travail](images/ocr-workflow-step-4-test.PNG)

### <a name="review-any-images-flagged-for-human-moderation"></a>Réviser toutes les images marquées d’un indicateur pour la modération humaine

Pour afficher la révision d’images, accédez à l’onglet **Image** sous **Révision**.

  ![Réviser les images](images/ocr-sample-image-workflow1.PNG)

## <a name="next-steps"></a>Étapes suivantes 

Pour appeler le flux de travail à partir du code, utilisez les flux de travail personnalisés avec le [`Job`démarrage rapide de la console d’API](../try-review-api-job.md) et le [démarrage rapide du Kit de développement logiciel (SDK) .NET](../moderation-jobs-quickstart-dotnet.md).
