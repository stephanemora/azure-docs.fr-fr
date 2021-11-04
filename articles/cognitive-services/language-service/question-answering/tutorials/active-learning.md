---
title: Enrichir votre projet avec l’apprentissage actif
description: Dans ce tutoriel, découvrez comment enrichir vos projets de réponse aux questions avec l’apprentissage actif.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: cec2c7bd2f44d918de4392be90b2be0210931afb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096968"
---
# <a name="enrich-your-project-with-active-learning"></a>Enrichir votre projet avec l’apprentissage actif

Dans ce tutoriel, vous allez apprendre à :

<!-- green checkmark -->
> [!div class="checklist"]
> * Télécharger un fichier de test d’apprentissage actif.
> * Importer le fichier de test dans votre projet existant.
> * Accepter/rejeter les suggestions d’apprentissage actif.
> * Ajouter des questions alternatives

Ce tutoriel montre comment améliorer votre projet de réponse aux questions avec l’apprentissage actif. Vous remarquerez peut-être que les clients posent des questions qui ne font pas partie de votre projet. Il existe souvent des variantes de questions qui sont tournées différemment.

Ces variantes, lorsqu’elles sont ajoutées en tant que questions alternatives à la paire question/réponse appropriée, aident à optimiser le projet pour répondre aux requêtes utilisateur réelles. Vous pouvez ajouter manuellement des questions alternatives aux paires question/réponse à l’aide de l’éditeur. Dans le même temps, vous pouvez également utiliser la fonctionnalité d’apprentissage actif pour générer des suggestions d’apprentissage actives sur la base des requêtes utilisateur. Toutefois, la fonctionnalité d’apprentissage actif requiert que la base de connaissances reçoive un trafic utilisateur normal pour générer des suggestions.

## <a name="enable-active-learning"></a>Activer l’apprentissage actif

L’apprentissage actif est activé par défaut pour les ressources compatibles avec les ressources de réponses aux questions.

Pour tester les suggestions d’apprentissage actif, vous pouvez importer le fichier suivant en tant que nouveau projet : [SampleActiveLearning.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/SampleActiveLearning.tsv).

## <a name="download-file"></a>Télécharger un fichier

Exécutez la commande suivante à partir de l’invite de commandes pour télécharger une copie locale du fichier `SampleActiveLearning.tsv`.

```cmd
curl "https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/SampleActiveLearning.tsv" --output SampleActiveLearning.tsv
```

## <a name="import-file"></a>Importer un fichier

Dans le volet de modification de la base de connaissances de votre projet, sélectionnez l’icône `...` (points de suspension) dans le menu > **Import questions and answers** > **Import as TSV**. Sélectionnez **Choose file** pour accéder à la copie de `SampleActiveLearning.tsv` que vous avez téléchargée sur votre ordinateur à l’étape précédente, puis sélectionnez Done.

> [!div class="mx-imgBorder"]
> [ ![Capture d’écran de la barre de menus de modification de la base de connaissances avec l’option Import as TSV affichée.]( ../media/active-learning/import-questions.png) ]( ../media/active-learning/import-questions.png#lightbox)

## <a name="view-and-addreject-active-learning-suggestions"></a>Afficher et ajouter/refuser des suggestions d’apprentissage actif

Une fois l’importation du fichier de test terminée, les suggestions d’apprentissage actif peuvent être consultées dans le volet de révision de suggestions :

> [!div class="mx-imgBorder"]
> [ ![Capture d’écran avec la page de révision de suggestions affichée.]( ../media/active-learning/review-suggestions.png) ]( ../media/active-learning/review-suggestions.png#lightbox)

Nous pouvons maintenant accepter ces suggestions ou les rejeter à l’aide des options de la barre de menus pour accepter toutes les suggestions (**Accept all suggestions**) ou refuser toutes les suggestions (**Reject all suggestions**).

Pour accepter ou refuser des suggestions individuelles, vous pouvez également sélectionner le symbole de coche (accepter) ou de corbeille (rejeter) qui apparaît en regard de chaque question dans la page **Review suggestions**.

> [!div class="mx-imgBorder"]
> [ ![Capture d’écran avec option d’acceptation ou de rejet mise en surbrillance en rouge.]( ../media/active-learning/accept-reject.png) ]( ../media/active-learning/accept-reject.png#lightbox)

## <a name="add-alternate-questions"></a>Ajouter des questions alternatives

Tandis que l’apprentissage actif suggère automatiquement d’autres questions basées sur les requêtes de l’utilisateur qui sollicitent le projet, nous pouvons également ajouter des variantes d’une question dans la page de modification de la base de connaissances en sélectionnant **Add alternate phrase** aux paires de réponses/questions.

En ajoutant d’autres questions avec l’apprentissage actif, nous enrichissons davantage le projet avec des variantes d’une question qui aident à fournir des réponses cohérentes aux requêtes utilisateur.

> [!NOTE]
> Lorsque les autres questions présentent de nombreux mots vides, elles peuvent avoir un impact négatif sur la précision des réponses. Par conséquent, si la seule différence par rapport aux autres questions réside dans les mots vides, ces questions alternatives ne sont pas requises.
> Pour examiner la liste des mots vides, consultez l’[article sur les mots vides](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/STOPWORDS.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Améliorer la qualité des réponses avec des synonymes](adding-synonyms.md)
