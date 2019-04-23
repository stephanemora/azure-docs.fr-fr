---
title: Définir et utiliser des flux de travail contenu via l’outil de vérification - Content Moderator
titlesuffix: Azure Cognitive Services
description: Vous pouvez utiliser le Concepteur de flux de travail Azure Content Moderator pour définir le flux de travail personnalisés et les seuils en fonction de vos stratégies de contenu.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 04/04/2019
ms.author: sajagtap
ms.openlocfilehash: 006f7d6691b8872aaa7ff8ccacff484585761d00
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795795"
---
# <a name="define-and-use-moderation-workflows"></a>Définir et utiliser des flux de travail de modération

Dans ce guide, vous allez apprendre à configurer et utiliser [workflows](../review-api.md#workflows) sur le [outil de révision](https://contentmoderator.cognitive.microsoft.com) site Web. Flux de travail est des filtres personnalisés basés sur le cloud qui vous permettent de gérer plus efficacement le contenu. Flux de travail peut se connecter à une variété de services pour filtrer le contenu de différentes façons et puis exécutez l’action appropriée. Ce guide vous montre comment utiliser le connecteur de Content Moderator (qui est inclus par défaut) pour filtrer le contenu et configurer des révisions humaines dans un scénario classique de modération.

## <a name="create-a-new-workflow"></a>Créer un nouveau flux de travail

Accédez à la [outil de révision du contenu modérateur](https://contentmoderator.cognitive.microsoft.com/) et s’y connecter. Dans l’onglet **Paramètres**, sélectionnez **Flux de travail**.

![Paramètres des flux de travail](images/2-workflows-0.png)

Dans l’écran suivant, sélectionnez **ajouter un flux de travail**.

![Modifier un flux de travail](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>Assigner un nom et une description

Nommez votre flux de travail, entrez une description et choisir si le flux de travail gère les images ou du texte.

![Nom et description du flux de travail](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>Définir les critères d’évaluation

Sur l’écran suivant, accédez à la **si** section. Dans le menu déroulant supérieur, choisissez **Condition**. Cela vous permettra de configurer la condition sur laquelle le flux de travail effectuera une action. Si vous souhaitez utiliser plusieurs conditions, choisissez **combinaison** à la place. 

Ensuite, sélectionnez un connecteur. Cet exemple utilise **Content Moderator**. Selon le connecteur que vous choisissez, vous obtiendrez des options différentes pour la sortie de données. Consultez le [connecteurs](./configure.md#connectors) section du guide de paramètres d’outil de révision pour apprendre à configurer d’autres connecteurs.

![Sélectionnez le connecteur de flux de travail](images/image-workflow-connect-to.PNG)

Choisissez le résultat souhaité à utiliser et définir les conditions à vérifier par rapport à.

![Définir la condition du flux de travail](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>Définir l’action

Accédez à la **puis** section, où vous sélectionnez une action. L’exemple suivant crée une révision d’image et affecte une balise. Si vous le souhaitez, vous pouvez ajouter un autre chemin d’accès (Else) et définir une action pour ce faire, ainsi.

![Définir l’action du flux de travail](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>Enregistrer le flux de travail

Notez le nom de flux de travail ; vous avez besoin du nom pour démarrer un travail de modération avec l’API de flux de travail (voir ci-dessous). Enfin, enregistrez le flux de travail à l’aide de la **enregistrer** bouton en haut de la page.

## <a name="test-the-workflow"></a>Tester le flux de travail

Maintenant que vous avez défini un flux de travail personnalisé, testez-le avec exemple de contenu. Accédez à **Workflows** et sélectionnez le correspondantes **exécuter le flux de travail** bouton.

![Test du flux de travail](images/image-workflow-execute.PNG)

Enregistrer ce [exemple d’image](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) sur votre disque local. Puis sélectionnez **ou choisissez les fichiers** et charger l’image dans le flux de travail.

![Un exécuteur avec une citation superposée sur l’image](images/sample-text.jpg)

### <a name="track-progress"></a>Suivre la progression

Vous pouvez afficher la progression du flux de travail dans la fenêtre contextuelle suivante.

![Suivre l’exécution du flux de travail](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>Vérifier l’action de flux de travail

Accédez à la **Image** onglet sous **Examinez** et vérifiez qu’il existe une révision de l’image qui vient d’être créée.

![Réviser les images](images/image-workflow-review.PNG)

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide, vous avez appris à configurer et utiliser des flux de travail modération de contenu modérateur [outil de vérification](https://contentmoderator.cognitive.microsoft.com). Ensuite, consultez le [guide de l’API REST](../try-review-api-workflow.md) pour apprendre à créer des flux de travail par programmation.
