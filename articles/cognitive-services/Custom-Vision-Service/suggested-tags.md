---
title: Étiqueter les images plus rapidement en utilisant des suggestions d’étiquettes
titleSuffix: Azure Cognitive Services
description: Dans ce guide, vous allez découvrir comment utiliser des suggestions d’étiquettes pour étiqueter un grand nombre d’images plus rapidement pendant l’entraînement de modèles Custom Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: pafarley
ms.openlocfilehash: 31b8dfc234ac99d6f04061d6596e3dc8113e8d0f
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213827"
---
# <a name="label-images-faster-with-suggested-tags"></a>Étiqueter les images plus rapidement en utilisant des suggestions d’étiquettes

Dans ce guide, vous allez découvrir comment utiliser des suggestions d’étiquettes pour étiqueter un grand nombre d’images plus rapidement pendant l’entraînement d’un modèle Custom Vision. 

Quand vous étiquetez des images pour un modèle Custom Vision, le service utilise la dernière itération entraînée du modèle pour prédire les étiquettes d’images non balisées. Il présente ensuite ces prédictions sous forme de suggestions d’étiquettes en fonction du seuil de confiance sélectionné et du niveau d’incertitude de prédiction. Vous pouvez alors confirmer ou modifier les suggestions, ce qui a pour effet d’accélérer l’étiquetage manuel des images pour l’entraînement.

## <a name="when-to-use-suggested-tags"></a>Quand utiliser des suggestions d’étiquettes

Gardez à l’esprit les limitations suivantes :

* Réservez les demandes de suggestions d’étiquettes aux images dont le contenu a déjà été entraîné une fois. Ne sollicitez pas de suggestions pour une nouvelle étiquette dont vous commencez à peine l’entraînement.
* Les suggestions d’étiquettes ne peuvent être appliquées qu’à des images non étiquetées ; vous ne pouvez pas obtenir de suggestions d’étiquettes supplémentaires pour une image déjà balisée.

> [!IMPORTANT]
> La fonctionnalité de suggestion d’étiquettes s’appuie sur le même [modèle de prix](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) que les prédictions normales. La première fois que vous déclenchez les suggestions d’étiquettes pour un ensemble d’images, vous êtes facturé comme si vous appeliez des prédictions. Après quoi, le service stocke les résultats pour les images sélectionnées dans une base de données pendant 30 jours, et vous pouvez y accéder gratuitement à tout moment au cours de cette période. Passé ce délai de 30 jours, vous êtes facturé si vous demandez à nouveau ces suggestions d’étiquettes.

## <a name="suggested-tags-workflow"></a>Workflow des suggestions d’étiquettes

Les étapes suivantes montrent comment utiliser la fonctionnalité de suggestion d’étiquettes :

1. Chargez toutes vos images d’entraînement dans votre projet Custom Vision.
1. Étiquetez une partie de votre jeu de données en choisissant un nombre égale d’images pour chaque étiquette.
    > [!TIP]
    > Veillez à utiliser toutes les étiquettes pour lesquelles vous souhaitez obtenir des suggestions par la suite.
1. Commencez le processus d’entraînement.
1. Une fois l’entraînement terminé, accédez à la vue **Untagged** (Non étiqueté), puis sélectionnez le bouton **Get suggested tags** (Obtenir des suggestions d’étiquettes) dans le volet gauche.
    > [!div class="mx-imgBorder"]
    > ![Le bouton de suggestion d’étiquettes figure sous l’onglet des images non étiquetées.](./media/suggested-tags/suggested-tags-button.png)
1. Sélectionnez l’ensemble d’images pour lesquelles vous voulez des suggestions. Dans un premier temps, vous obtenez des suggestions d’étiquettes seulement pour une partie des images non étiquetées. Des suggestions plus abouties vous sont ensuite proposées à mesure que vous répétez ce processus.
1. Confirmez les étiquettes suggérées tout en prenant soin de corriger celles qui sont incorrectes.
    > [!TIP]
    > Les images assorties d’étiquettes suggérées sont triées selon le niveau d’incertitude de leur prédiction (des valeurs faibles indiquant un haut niveau de confiance). Vous pouvez changer l’ordre de tri à l’aide de l’option **Sort by uncertainty** (Trier par niveau d’incertitude). Si vous définissez cet ordre sur **high to low** (élevé à faible), vous pouvez corriger d’abord les prédictions à fort niveau d’incertitude, puis confirmer rapidement celles dont le niveau d’incertitude est faible.
    * Dans les projets de classification d’images, vous pouvez sélectionner et confirmer les étiquettes par lots (batch). Filtrez la vue en fonction d’une balise suggérée déterminée, désélectionnez les images dont l’étiquetage est incorrect, puis confirmez les autres en une seule opération.
        > [!div class="mx-imgBorder"]
        > ![Les suggestions d’étiquettes s’affichent en mode batch pour la classification d’images avec des filtres.](./media/suggested-tags/ic-batch-mode.png)

        Vous pouvez aussi utiliser les suggestions d’étiquettes en mode image individuelle en sélectionnant une image dans la galerie.

        ![Les suggestions d’étiquettes s’affichent en mode image individuelle pour la classification d’images.](./media/suggested-tags/ic-individual-image-mode.png)
    * Dans les projets de détection d’objets, les confirmations par lots ne sont pas prises en charge. Il vous reste cependant la possibilité de filtrer et trier l’affichage en fonction d’étiquettes suggérées pour une expérience d’étiquetage mieux organisée. Les miniatures de vos images étiquetées présentent un cadre englobant superposé qui indique l’emplacement de l’étiquette suggérée. Si vous ne sélectionnez pas de filtre d’étiquette suggérée, toutes vos images non étiquetées s’affichent sans cadre englobant superposé.
        > [!div class="mx-imgBorder"]
        > ![Les suggestions d’étiquettes s’affichent en mode batch pour la détection d’objets avec des filtres.](./media/suggested-tags/od-batch-mode.png)

        Pour confirmer les étiquettes de détection d’objets, vous devez les appliquer individuellement à chaque image de la galerie.

        ![Les étiquettes suggérées s’affichent en mode image individuelle dans le cas de la détection d’objets.](./media/suggested-tags/od-individual-image-mode.png)
1. Recommencez le processus d’entraînement.
1. Répétez les étapes précédentes jusqu’à ce que la qualité de suggestion vous convienne.

## <a name="next-steps"></a>Étapes suivantes

Suivez un guide de démarrage rapide pour commencer à créer et entraîner un projet Custom Vision.

* [Créer un classifieur](getting-started-build-a-classifier.md)
* [Créer un détecteur d’objets](get-started-build-detector.md)