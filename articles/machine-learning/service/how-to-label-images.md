---
title: Comment utiliser l’outil d’étiquetage des données d’Azure Machine Learning
title.suffix: Azure Machine Learning
description: Cet article explique comment utiliser les outils d’étiquetage des données dans un projet d’étiquetage Azure Machine Learning.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 9cbebb905ba39dabaf905be2fa741702bd1a0088
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838943"
---
# <a name="how-to-tag-images-in-a-labeling-project"></a>Comment baliser des images dans un projet d’étiquetage

Une fois que votre administrateur de projet a créé un projet d’étiquetage dans Azure Machine Learning, vous pouvez utiliser l’outil d’étiquetage pour préparer rapidement les données d’un projet de Machine Learning. 

> [!div class="checklist"]
> * Comment accéder à vos projets d’étiquetage
> * Outils d’étiquetage
> * Comment utiliser les outils pour des tâches d’étiquetage spécifiques

## <a name="prerequisites"></a>Prérequis

* URL du portail d’étiquetage pour un projet d’étiquetage de données en cours d’exécution
* Un [compte Microsoft](https://account.microsoft.com/account) ou
* Un compte Azure Active Directory pour l’organisation et le projet

> [!Note]
> L’administrateur du projet peut trouver l’URL du portail d’étiquetage sous l’onglet **Détails** dans la page **Détails du projet**. 

## <a name="logging-in-to-the-projects-labeling-portal"></a>Connexion au portail d’étiquetage du projet

Accédez à l’URL du portail d’étiquetage fournie par l’administrateur du projet. 

Connectez-vous à l’aide du compte de courrier que l’administrateur a utilisé pour vous ajouter à l’équipe. Pour la plupart des utilisateurs, la connexion est effectuée avec votre compte Microsoft. Si le projet d’étiquetage utilise Azure Active Directory, c’est ainsi que vous vous connectez. 

## <a name="understanding-the-labeling-task"></a>Compréhension de la tâche d’étiquetage

Une fois que vous êtes connecté, vous êtes dirigé vers la page de vue d’ensemble du projet. 

La première chose à faire est d’**Afficher les instructions détaillées**. Ces instructions sont spécifiques de votre projet. Elles décrivent le type de données auquel vous êtes confronté, expliquent comment prendre vos décisions et fournissent d’autres informations pertinentes. Lorsque vous avez terminé, revenez à la page du projet, puis choisissez **Démarrer l’étiquetage**.

## <a name="common-features-of-the-labeling-task"></a>Fonctionnalités courantes de la tâche d’étiquetage

Toutes les tâches d’étiquetage d’image impliquent le choix d’une ou de toutes les balises appropriées à partir d’un jeu spécifié par l’administrateur du projet. Vous pouvez choisir parmi les neuf premières balises à l’aide des touches numériques de votre clavier.  

Les tâches de classification d’image vous permettent de choisir de présenter plusieurs images simultanément. Vous pouvez choisir les différentes dispositions à l’aide des icônes situées au-dessus de la zone d’image. Vous pouvez sélectionner toutes les images affichées simultanément en appuyant sur le bouton **Sélectionner tout**. Pour sélectionnez une image individuelle, utilisez le bouton de sélection circulaire en haut à droite de la zone de l’image. Pour appliquer une balise, vous devez sélectionner au moins une image. Si vous avez sélectionné plusieurs images, la sélection d’une balise a pour effet d’appliquer celle-ci à toutes les images sélectionnées.

Ici, nous avons choisi une disposition 2 x 2 et nous sommes sur le point d’appliquer la balise « Mammal » (Mammifère) aux images de l’ours et de l’orque. L’image de requin a déjà été balisée comme « Cartilaginous fish », (Poisson cartilagineux) celle de l’iguane n’est pas encore balisé.

![Dispositions de page multiples et sélection](media/how-to-label-images/layouts.png)

> [!Important] 
> Lorsque vous avez une nouvelle page de données sans étiquette, changez uniquement les dispositions. Tout changement de disposition a pour effet d’effacer le travail de balisage en cours de la page. 

Lorsque toutes les images de la page sont balisées, Azure active le bouton **Envoyer**. Appuyez sur **Envoyer** pour enregistrer votre travail. 

Une fois que vous avez envoyé les balises pour les données en cours, Azure actualise la page en affichant un nouvel ensemble d’images provenant de la file de travail.  

## <a name="tagging-images-for-multi-class-classification"></a>Balisage d’images pour une classification multiclasse

Si votre projet est de type « classification multiclasse d’images », vous attribuez une seule balise à l’image entière. À tout moment, vous pouvez choisir la page **Instructions** et accéder à **Afficher les instructions détaillées** pour voir les instructions spécifiques du projet. 

Comme nous l’avons vu précédemment, vous avez le choix entre plusieurs dispositions pour présenter des images. Si, après avoir sélectionné une image et lui avoir attribué une balise, vous réalisez que vous avez commis une erreur, vous pouvez la corriger. Si, dans l’étiquette affiche sous l’image, vous cliquez sur la cible `X`, la balise est effacée. Ou bien, si vous sélectionnez l’image et choisissez une autre classe, la balise affiche la valeur que vous venez de sélectionner.

## <a name="tagging-images-for-multi-label-classification"></a>Balisage d’images pour une classification à plusieurs étiquettes

Si vous travaillez sur un projet de type « Classification à plusieurs étiquettes », vous appliquez une _ou plusieurs_ balises à une image. À tout moment, vous pouvez choisir la page **Instructions** et accéder à **Afficher les instructions détaillées** pour voir les instructions spécifiques du projet. 

Sélectionnez l’image que vous souhaitez étiqueter, puis cliquez sur la balise. Le choix de la balise a pour effet d’appliquer celle-ci à toutes les images sélectionnées et de les désélectionner. Pour appliquer d’autres balises aux images, vous devez les resélectionner. Cette animation illustre une page de balisage à plusieurs étiquettes :

* L’option **Sélectionner tout** permet d’appliquer la balise « Ocean » (Océan)
* Une seule image est sélectionnée et balisée « Closeup » (Gros plan)
* Trois images sont sélectionnées et balisées « Wide angle » (Grand angle)

![Animation illustrant un flux d’étiquettes multiples](media/how-to-label-images/multilabel.gif)

Pour corriger une erreur, vous pouvez soit cliquer sur le `X` pour effacer une balise individuelle, soit sélectionner les images, puis choisir la balise, ce qui a pour effet d’effacer celle-ci de toutes les images sélectionnées. Ce scénario est illustré ici. Un clic sur « Land » (Terre) a pour effet d’effacer cette balise des deux images sélectionnées.

![Capture d’écran présentant une désélection multiple](media/how-to-label-images/multiple-deselection.png)

Azure n’activera le bouton **Envoyer** qu’après que vous aurez appliqué au moins une balise à chaque image. Appuyez sur **Envoyer** pour enregistrer votre travail.

## <a name="tagging-images-and-bounding-boxes-for-object-detection"></a>Balisage d’images et insertion de rectangles englobants pour la détection d’objets

Si votre projet est de type « Identification d’objets (zones englobantes) », vous devez insérer un ou plusieurs rectangles englobants dans l’image et appliquer une balise à chaque rectangle. Chaque image peut avoir plusieurs rectangles englobants, chacun associé à une seule balise. Pour déterminer si l’ajout de plusieurs rectangles englobants est approprié pour votre projet, utilisez la fonction **Afficher les instructions détaillées**.

1. Sélectionnez une balise pour le rectangle englobant que vous souhaitez créer
1. Sélectionnez l’outil **Zone rectangulaire** ![outil Zone rectangulaire](media/how-to-label-images/rectangular-box-tool.png) ou appuyez sur « R » 
1. Cliquez et faites glisser en diagonale sur votre cible pour créer un rectangle englobant approximatif
    * Ajustez le rectangle englobant en cliquant et faisant glisser les bords ou les angles de la zone

![Capture d’écran de la création d’un rectangle englobant de base](media/how-to-label-images/bounding-box-sequence.png)

Si vous souhaitez supprimer le rectangle englobant, cliquez sur la cible en forme de X qui s’affiche en regard du rectangle englobant après la création de celui-ci.

Vous ne pouvez pas réattribuer la balise d’un rectangle englobant existant. Si vous commettez une erreur d’attribution de balise, vous devez supprimer le rectangle englobant et en créer un autre avec la balise correcte.

Par défaut, les rectangles englobants existants peuvent être modifiés. L’outil **Verrouiller/déverrouiller des zones** ![outil Verrouiller/Déverrouiller des zones](media/how-to-label-images/lock-bounding-boxes-tool.png), ou « L », permet d’inverser le comportement. Si les zones sont verrouillées, vous pouvez uniquement modifier la forme ou l’emplacement d’un nouveau rectangle englobant.

L’outil **Manipulation des zones** ![outil Manipulation des zones](media/how-to-label-images/regions-tool.png), ou « M », pour ajuster un rectangle englobant existant. Vous pouvez cliquer et faire glisser les bords ou les angles du ajuster un rectangle pour en ajuster la forme. Vous pouvez également le glisser le rectangle englobant en cliquant à l’intérieur de celui-ci. Si vous ne pouvez pas modifier une zone, c’est que vous avez probablement inversé le comportement de l’outil **Verrouiller/déverrouiller des zones**. 

L’outil **Zone basée sur un modèle** ![Zone basée sur un modèle](media/how-to-label-images/template-box-tool.png), ou « T », permet de créer plusieurs rectangles englobants de même taille. Si l’image ne comporte pas de rectangle englobant et que vous activez l’outil Zone basée sur un modèle, celui-ci génère des zones de 50 x 50 pixels. Si, après avoir créé un rectangle englobant, vous activez l’outil Zone basée sur un modèle, les nouveaux rectangles englobants ont la taille du dernier rectangle créé. Une fois les zones basées sur un modèle placées, vous pouvez les redimensionner. Le redimensionnement d’une zone basée sur un modèle modifie uniquement cette zone. 

Si vous souhaitez supprimer _tous_ les rectangles englobants de l’image active, vous pouvez choisir l’outil **Supprimer toutes les zones** ![outil Supprimer toutes les zones](media/how-to-label-images/delete-regions-tool.png). 

Une fois que vous avez créé les rectangles englobants pour l’image, appuyez sur **Envoyer** pour enregistrer votre travail. Votre travail en cours n’est enregistré que si vous appuyez sur le bouton **Envoyer**. 

## <a name="finishing-up"></a>Finalisation 

Lorsque vous envoyez une page de données balisées, Azure vous attribue de nouvelles données sans étiquette provenant d’une file de travail. S’il n’y a plus de données sans étiquette, un message s’affiche, avec un lien redirigeant vers la page d’accueil du portail. 

Si vous ne comptez plus étiqueter, choisissez votre nom dans l’angle supérieur droit du portail d’étiquetage, puis choisissez **Déconnexion**. Si vous ne vous déconnectez pas, Azure finit par faire expirer votre session et attribuer vos données à un autre « étiqueteur ». 

## <a name="next-steps"></a>Étapes suivantes

* Apprenez à [effectuer l’apprentissage de modèles de classification d’image dans Azure](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
* Découvrez la [détection d’objet à l’aide d’Azure et la technique Faster R-CNN](https://www.microsoft.com/developerblog/2017/10/24/bird-detection-with-azure-ml-workbench/)