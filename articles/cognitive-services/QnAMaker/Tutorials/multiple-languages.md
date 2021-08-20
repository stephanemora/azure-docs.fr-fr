---
title: Créer des bases de connaissances dans plusieurs langues
description: Dans ce tutoriel, vous allez apprendre à créer des bases de connaissances dans plusieurs langues.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/29/2021
ms.openlocfilehash: 41b15b70434c2515349074088d3b049de7482b1a
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113235174"
---
# <a name="create-knowledge-bases-in-multiple-languages"></a>Créer des bases de connaissances dans plusieurs langues

Ce tutoriel vous guide tout au long du processus de création de bases de connaissances dans plusieurs langues. Nous utilisons l’URL de la [FAQ relative au stylet Surface](https://support.microsoft.com/surface/how-to-use-your-surface-pen-8a403519-cd1f-15b2-c9df-faa5aa924e98) pour créer des bases de connaissances en allemand et en anglais. Nous publions ensuite les bases de connaissances et utilisons l’API [GenerateAnswerAPI](/rest/api/cognitiveservices-qnamaker/QnAMaker4.0/Runtime/GenerateAnswer) pour les interroger et obtenir des réponses aux FAQ dans la langue souhaitée.

## <a name="create-knowledge-base-in-german"></a>Créer une base de connaissances en allemand

Pour pouvoir créer une base de connaissances dans plusieurs langues, le paramètre de langue doit être défini lors de la création de la première base de connaissances du service QnA.

> [!NOTE]
> L’option permettant d’ajouter des bases de connaissances dans plusieurs langues à un service n’est disponible que dans le cadre de la réponse personnalisée aux questions, qui est une fonctionnalité d’Analyse de texte.
>
> Si vous utilisez la version en disponibilité générale de QnA Maker, vous devez créer une ressource QnA Maker distincte pour chaque langue.

> [!div class="mx-imgBorder"]
> [ ![Capture d’écran de l’interface utilisateur pour Connecter votre service QnA à votre base de connaissances avec l’option Ajouter des bases de connaissances dans plusieurs langues sélectionnée]( ../media/multiple-languages/add-knowledge-bases.png) ]( ../media/multiple-languages/add-knowledge-bases.png#lightbox)

À l’**étape 2** : Activez « Ajouter des bases de connaissances dans plusieurs langues à ce service » et choisissez l’**allemand** comme langue de la base de connaissances dans la liste déroulante « Langue ».
Remplissez les détails pertinents aux étapes 3 et 4, puis sélectionnez **Créer votre base de connaissances**.
 
À cette étape, QnA Maker lit le document et extrait les paires question/réponse à partir de l’URL source pour créer la base de connaissances en allemand. La page de la base de connaissances s’ouvre et vous permet de modifier le contenu de la base de connaissances.

> [!div class="mx-imgBorder"]
> [ ![Capture d’écran de l’interface utilisateur avec des questions et réponses en allemand]( ../media/multiple-languages/german.png) ]( ../media/multiple-languages/german.png#lightbox)

## <a name="create-knowledge-base-in-english"></a>Créer une base de connaissances en anglais

Nous répétons maintenant les étapes ci-dessus avec des changements spécifiques à la langue à l’étape 2 et à l’étape 4 pour créer la base de connaissances en anglais :
1.  **Étape 2** : Choisissez l’**anglais** comme langue.
2.  **Étape 4** : Sélectionnez le fichier source dans la langue sélectionnée pour créer une base de connaissances en anglais.
Une fois la base de connaissances créée, nous pouvons voir les paires question/réponse pertinentes générées par QnA Maker en anglais, comme indiqué ci-dessous :

> [!div class="mx-imgBorder"]
> [ ![Capture d’écran de la réponse de l’API REST]( ../media/multiple-languages/english.png) ]( ../media/multiple-languages/english.png#lightbox)

## <a name="publish-and-query-knowledge-base"></a>Publier et interroger la base de connaissances

Nous sommes maintenant prêts à publier les deux bases de connaissances et à les interroger dans la langue souhaitée en utilisant l’API [GenerateAnswerAPI](/rest/api/cognitiveservices-qnamaker/QnAMaker4.0/Runtime/GenerateAnswer). Une fois qu’une base de connaissances est publiée, la page suivante s’affiche et fournit les détails pour interroger la base de connaissances.

> [!div class="mx-imgBorder"]
> [ ![Capture d’écran de l’interface utilisateur avec des questions et réponses en anglais]( ../media/multiple-languages/postman.png) ]( ../media/multiple-languages/postman.png#lightbox)

La langue de la requête entrante de l’utilisateur peut être détectée grâce à l’[API Détection de langue](../../text-analytics/how-tos/text-analytics-how-to-language-detection.md) et l’utilisateur peut appeler le point de terminaison et la base de connaissances appropriés en fonction de la langue détectée.