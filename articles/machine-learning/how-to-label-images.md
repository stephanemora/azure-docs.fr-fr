---
title: Étiqueter des images dans un projet d’étiquetage
title.suffix: Azure Machine Learning
description: Découvrez comment utiliser les outils de marquage des données dans un projet d’étiquetage Azure Machine Learning.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 7ff3651d082f1649326e1b41b13fa177069c40d3
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75532342"
---
# <a name="tag-images-in-a-labeling-project"></a>Étiqueter des images dans un projet d’étiquetage

Une fois que votre administrateur de projet a [créé un projet d’étiquetage](https://docs.microsoft.com/azure/machine-learning/service/how-to-create-labeling-projects#create-a-labeling-project) dans Azure Machine Learning, vous pouvez utiliser l’outil d’étiquetage pour préparer rapidement les données d’un projet Machine Learning. Cet article aborde les points suivants :

> [!div class="checklist"]
> * Comment accéder à vos projets d’étiquetage
> * Outils d’étiquetage
> * Comment utiliser les outils pour des tâches d’étiquetage spécifiques

## <a name="prerequisites"></a>Conditions préalables requises

* URL du portail d’étiquetage pour un projet d’étiquetage de données en cours d’exécution
* Un [compte Microsoft](https://account.microsoft.com/account) ou un compte Azure Active Directory pour l’organisation et le projet

> [!NOTE]
> L’administrateur du projet peut trouver l’URL du portail d’étiquetage sous l’onglet **Détails** de la page **Détails du projet**.

## <a name="sign-in-to-the-projects-labeling-portal"></a>Se connecter au portail d’étiquetage du projet

Accédez à l’URL du portail d’étiquetage fournie par l’administrateur du projet. Connectez-vous à l’aide du compte e-mail que l’administrateur a utilisé pour vous ajouter à l’équipe. Pour la plupart des utilisateurs, il s’agit de votre compte Microsoft. Si le projet d’étiquetage utilise Azure Active Directory, vous vous connectez de cette manière.

## <a name="understand-the-labeling-task"></a>Comprendre la tâche d’étiquetage

Une fois connecté, vous voyez s’afficher la page de vue d’ensemble du projet.

Accédez à **Afficher les instructions détaillées**. Ces instructions sont spécifiques à votre projet. Elles décrivent le type de données auquel vous êtes confronté, indiquent comment prendre vos décisions et fournissent d’autres informations pertinentes. Après avoir lu ces informations, retournez à la page du projet, puis sélectionnez **Démarrer l’étiquetage**.

## <a name="common-features-of-the-labeling-task"></a>Fonctionnalités courantes de la tâche d’étiquetage

Dans toutes les tâches d’étiquetage d’images, vous devez choisir une ou plusieurs étiquettes appropriées parmi un ensemble spécifié par l’administrateur du projet. Vous pouvez sélectionner les neuf premières étiquettes à l’aide des touches numériques du clavier.  

Dans les tâches de classification d’images, vous pouvez choisir de voir plusieurs images simultanément. Utilisez les icônes au-dessus de la zone d’images pour sélectionner la disposition. Pour sélectionner toutes les images affichées simultanément, utilisez **Tout sélectionner**. Pour sélectionner des images individuelles, utilisez le bouton de sélection circulaire situé en haut à droite de l’image. Pour appliquer une balise, vous devez sélectionner au moins une image. Si vous sélectionnez plusieurs images, les étiquettes sélectionnées sont appliquées à l’ensemble de ces images.

Ici, nous avons choisi une disposition deux par deux, et nous sommes sur le point d’appliquer l’étiquette « Mammal » (Mammifère) aux images de l’ours et de l’orque. L’image du requin a déjà été étiquetée en tant que « Cartilaginous fish » (Poisson cartilagineux) alors que celle de l’iguane n’a pas encore été étiquetée.

![Dispositions de page multiples et sélection](./media/how-to-label-images/layouts.png)

> [!Important] 
> Changez de disposition uniquement quand vous avez une nouvelle page de données non étiquetées. Tout changement de disposition a pour effet d’effacer le travail de balisage en cours de la page.

Lorsque toutes les images de la page sont balisées, Azure active le bouton **Envoyer**. Sélectionnez **Envoyer** pour enregistrer votre travail.

Une fois que vous avez envoyé les étiquettes relatives aux données disponibles, Azure actualise la page en affichant un nouvel ensemble d’images provenant de la file d’attente de travail.

## <a name="tag-images-for-multi-class-classification"></a>Étiqueter des images pour une classification multiclasse

Si votre projet est de type « classification multiclasse d’images », vous attribuez une seule balise à l’image entière. Pour passer en revue les instructions à tout moment, accédez à la page **Instructions**, puis sélectionnez **Afficher les instructions détaillées**.

Si vous vous rendez compte que vous avez commis une erreur après avoir affecté une étiquette à une image, vous pouvez la corriger. Sélectionnez le « **X** » sur l’étiquette affichée sous l’image pour effacer l’étiquette. Vous pouvez également sélectionner l’image et choisir une autre classe. La valeur que vous venez de sélectionner remplace l’étiquette appliquée.

## <a name="tag-images-for-multi-label-classification"></a>Étiqueter des images pour une classification multiétiquette

Si vous travaillez sur un projet de type « Classification multiétiquette d’images », vous appliquez une *ou plusieurs* étiquettes à une image. Pour voir les instructions spécifiques au projet, sélectionnez **Instructions**, puis accédez à **Afficher les instructions détaillées**.

Sélectionnez l’image à étiqueter, puis sélectionnez l’étiquette. L’étiquette est appliquée à toutes les images sélectionnées, puis les images sont désélectionnées. Pour appliquer d’autres balises aux images, vous devez les resélectionner. L’animation suivante illustre un étiquetage multiétiquette :

1. L’option **Sélectionner tout** permet d’appliquer l’étiquette « Ocean » (Océan).
1. Une seule image est sélectionnée et étiquetée « Closeup » (Gros plan).
1. Trois images sont sélectionnées et étiquetées « Wide angle » (Grand angle).

![Animation illustrant un flux multiétiquette](./media/how-to-label-images/multilabel.gif)

Pour corriger une erreur, cliquez sur le « **X** » afin d’effacer une étiquette, ou sélectionnez les images nécessaires, puis l’étiquette, ce qui permet d’effacer l’étiquette de toutes les images sélectionnées. Ce scénario est illustré ici. Cliquez sur « Land » (Terre) pour effacer cette étiquette des deux images sélectionnées.

![Capture d’écran qui illustre plusieurs désélections](./media/how-to-label-images/multiple-deselection.png)

Azure n’activera le bouton **Envoyer** qu’après que vous aurez appliqué au moins une balise à chaque image. Sélectionnez **Envoyer** pour enregistrer votre travail.

## <a name="tag-images-and-specify-bounding-boxes-for-object-detection"></a>Étiqueter des images et spécifier des cadres englobants pour la détection d’objet

Si votre projet est de type « Identification d’objets (cadres englobants) », vous devez spécifier un ou plusieurs cadres englobants dans l’image, puis appliquer une étiquette à chaque cadre. Les images peuvent avoir plusieurs cadres englobants, chacun associé à une seule étiquette. Pour déterminer si plusieurs cadres englobants sont utilisés dans votre projet, accédez à **Afficher les instructions détaillées**.

1. Sélectionnez une étiquette pour le cadre englobant à créer.
1. Sélectionnez l’outil **Zone rectangulaire**![Outil Zone rectangulaire](./media/how-to-label-images/rectangular-box-tool.png), ou sélectionnez « R ».
3. Cliquez sur votre cible, puis faites glisser le curseur en diagonale pour créer un cadre englobant approximatif. Pour ajuster le cadre englobant, faites glisser ses bords ou ses angles.

![Capture d’écran qui illustre la création de base d’un cadre englobant.](./media/how-to-label-images/bounding-box-sequence.png)

Pour supprimer un cadre englobant, cliquez sur la cible en forme de X qui apparaît à côté du cadre englobant après sa création.

Vous ne pouvez pas changer l’étiquette d’un cadre englobant. Si vous commettez une erreur d’affectation d’étiquette, vous devez supprimer le cadre englobant et en créer un autre avec l’étiquette appropriée.

Par défaut, vous pouvez modifier les cadres englobants existants. L’outil **Verrouiller/déverrouiller des zones**![outil Verrouiller/Déverrouiller des zones](./media/how-to-label-images/lock-bounding-boxes-tool.png), ou « L », permet d’inverser le comportement. Si les zones sont verrouillées, vous pouvez uniquement changer la forme ou l’emplacement d’un nouveau cadre englobant.

L’outil **Manipulation des zones**![outil Manipulation des zones](./media/how-to-label-images/regions-tool.png), ou « M », pour ajuster un rectangle englobant existant. Pour ajuster la forme, faites glisser ses bords ou ses angles. Cliquez à l’intérieur du cadre englobant pour pouvoir le faire glisser entièrement. Si vous ne pouvez pas modifier une zone, c’est que vous avez probablement inversé le comportement de l’outil **Verrouiller/déverrouiller des zones**.

L’outil **Zone basée sur un modèle**![Zone basée sur un modèle](./media/how-to-label-images/template-box-tool.png), ou « T », permet de créer plusieurs rectangles englobants de même taille. Si l’image ne contient pas de cadre englobant et si vous activez les cadres basés sur un modèle, l’outil génère des cadres de 50 x 50 pixels. Si vous créez un cadre englobant et si vous activez les cadres basés sur un modèle, les nouveaux cadres englobants ont la taille du dernier cadre créé. Une fois les zones basées sur un modèle placées, vous pouvez les redimensionner. Le redimensionnement d’un cadre basé sur un modèle entraîne uniquement le redimensionnement de ce cadre.

Pour supprimer *tous* les cadres englobants de l’image active, sélectionnez l’outil **Supprimer toutes les zones**![Outil Supprimer toutes les zones](./media/how-to-label-images/delete-regions-tool.png).

Après avoir créé les cadres englobants d’une image, sélectionnez **Envoyer** pour enregistrer votre travail. Sinon, ce dernier ne sera pas enregistré.

## <a name="finish-up"></a>Terminer

Quand vous envoyez une page de données marquées, Azure vous affecte de nouvelles données non étiquetées en provenance d’une file d’attente de travail. S’il ne reste plus de données non étiquetées, vous recevez un message correspondant ainsi qu’un lien vers la page d’accueil du portail.

Une fois l’étiquetage terminé, sélectionnez votre nom dans le coin supérieur droit du portail d’étiquetage, puis sélectionnez **Déconnexion**. Si vous ne vous déconnectez pas, Azure finit par faire expirer votre session et attribuer vos données à un autre « étiqueteur ».

## <a name="next-steps"></a>Étapes suivantes

* Apprenez à [effectuer l’apprentissage de modèles de classification d’image dans Azure](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
* Découvrez [la détection d’objet à l’aide d’Azure et la technique « Faster R-CNN »](https://www.microsoft.com/developerblog/2017/10/24/bird-detection-with-azure-ml-workbench/)
