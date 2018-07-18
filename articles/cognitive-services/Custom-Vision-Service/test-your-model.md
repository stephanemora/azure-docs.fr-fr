---
title: Tester et recycler un modèle - Service Vision personnalisée - Azure Cognitive Services | Microsoft Docs
description: Découvrez comment tester une image puis comment l’utiliser pour recycler le modèle.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 1933b1a45844ac99308baebe59b49687a957abfa
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369108"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Tester et recycler un modèle avec Service Vision personnalisée

Une fois votre modèle formé, vous pouvez le tester rapidement à l’aide d’une image en ligne ou stockée en local. Le test utilise l’itération la plus récemment formée.

## <a name="test-your-model"></a>Tester votre modèle

1. Sur la [page web Service Vision personnalisée](https://customvision.ai), sélectionnez votre projet. Sélectionnez **Quick Test** (Test rapide) à droite de la barre de menus supérieure. Cette action ouvre une fenêtre intitulée **Quick Test** (Test rapide).

    ![Le bouton Quick Test (Test rapide) s’affiche dans le coin supérieur droit de la fenêtre.](./media/test-your-model/quick-test-button.png)

2. Dans la fenêtre **Quick Test** (Test rapide), cliquez sur le champ **Submit Image** (Envoyer une image) et entrez l’URL de l’image que vous souhaitez utiliser pour votre test. Si vous souhaitez utiliser une image stockée localement à la place, cliquez sur le bouton **Parcourir les fichiers locaux** et sélectionnez un fichier d’image local.

    ![Image de la page d’envoi d’image](./media/test-your-model/submit-image.png)

L’image que vous sélectionnez apparaît au milieu de la page. Les résultats s’affichent ensuite sous l’image, sous la forme d’un tableau avec deux colonnes, intitulées **Balises** et **Confiance**. Après avoir consulté les résultats, vous pouvez fermer la fenêtre **Quick Test** (Test rapide).

Vous pouvez maintenant ajouter cette image de test à votre modèle, puis le recycler.

## <a name="use-the-predicted-image-for-training"></a>Utilisez l’image prévue pour former le modèle.

Pour utiliser l’image précédemment envoyée pour former le modèle, procédez comme suit :

1. Pour afficher des images envoyées au classifieur, ouvrez la [page web Service Vision personnalisée](https://customvision.ai) et sélectionnez l’onglet __Prédictions__.

    ![Image de l’onglet Prédictions](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > L’affichage par défaut montre des images issues de l’itération actuelle. Vous pouvez utiliser le champ de liste déroulante __Itération__ pour afficher les images envoyées au cours des itérations précédentes.

2. Pointez la souris sur une image pour afficher les balises qui ont été prédites par le classifieur.

    > [!TIP]
    > Les images étant classées par ordre de priorité, celles pouvant apporter des avantages plus importants pour le classifieur sont placées en haut. Pour sélectionner un tri différent, utilisez la section __Trier__.

    Pour ajouter une image à vos données pour former le modèle, sélectionnez l’image, sélectionnez la balise, puis __Save and close__ (Enregistrer et fermer). L’image est supprimée de l’onglet __Prédictions__ et ajoutée aux images de formation. Vous pouvez l’afficher en sélectionnant l’onglet __Training Images__ (Images de formation).

    ![Image de la page de balisage](./media/test-your-model/tag-image.png)

3. Utilisez le bouton __Former__ pour recycler le classifieur.

## <a name="next-steps"></a>Étapes suivantes

[Améliorer votre classifieur](getting-started-improving-your-classifier.md)