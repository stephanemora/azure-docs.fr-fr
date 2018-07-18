---
title: Améliorer votre classifieur avec le service Vision personnalisée - Azure Cognitive Services | Microsoft Docs
description: Découvrez comment améliorer la qualité de votre classifieur Service Vision personnalisée.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 65b1424f259066c7d5bd6b2b508d2a4052ff0527
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368836"
---
# <a name="how-to-improve-your-classifier"></a>Comment améliorer votre classifieur

Découvrez comment améliorer la qualité de votre classifieur Service Vision personnalisée. La qualité de votre classifieur dépend de la qualité des données étiquetées que vous lui fournissez. 

## <a name="train-more-varied-images"></a>Entraîner plus d’images variées

L’apport d’images étiquetées, prises en photo selon des angles, des arrière-plans, des tailles d’objet, des groupes différents et d’autres variantes, améliore le classifieur. Les photos en contexte sont plus efficaces que les photos réalisées devant des arrière-plans neutres. Joignez des images qui sont représentatives de ce qui va être soumis au classifieur lors d’une utilisation normale.

Pour plus d’informations sur l’ajout d’images, consultez le document [Créer un classifieur](getting-started-build-a-classifier.md).

> [!IMPORTANT]
> N’oubliez pas d’entraîner le classifieur après avoir ajouté des images.

## <a name="use-images-submitted-for-prediction"></a>Utiliser des images soumises pour la prédiction

Le Service Vision personnalisée stocke les images envoyées au point de terminaison de prédiction. Pour utiliser ces images et améliorer le classifieur, effectuez les opérations suivantes :

1. Pour voir les images soumises au classifieur, ouvrez la [page web Custom Vision](https://customvision.ai) et sélectionnez l’onglet __Predictions__.

    ![Image de l’onglet des prédictions](./media/getting-started-improving-your-classifier/predictions-tab.png)

    > [!TIP]
    > La vue par défaut montre des images à partir de l’itération active. Vous pouvez utiliser la zone déroulante __Iteration__ pour voir les images envoyées au cours des précédentes itérations.

2. Pointez sur une image pour lire les mots clés qui ont été prédits par le classifieur.

    > [!TIP]
    > Les images étant classées par ordre de priorité, celles pouvant apporter des bénéfices plus importants pour le classifieur sont placées en haut. Pour sélectionner un tri différent, utilisez la section __Sort__ (Tri).

    Pour ajouter une image à vos données d’entraînement, sélectionnez l’image, sélectionnez le mot clé, puis __Save and close__ (Enregistrer et fermer). L’image est supprimée des __prédictions__ et ajoutée aux images d’entraînement. Vous pouvez la voir en sélectionnant l’onglet __Training Images__ (Images d’entraînement).

    ![Image de la page d’étiquetage](./media/getting-started-improving-your-classifier/tag-image.png)

3. Utilisez le bouton __Train__ (Entraîner) pour ré-entraîner le classifieur.

## <a name="visually-inspect-predictions"></a>Inspecter visuellement les prédictions

Pour examiner les prédictions d’image, sélectionnez l’onglet __Training Images__ (Images d’entraînement), puis __Iteration History__ (Historique des itérations). Les images bordées d’un trait rouge ont été prédites de façon incorrecte.

![Image de l’historique des itérations](./media/getting-started-improving-your-classifier/iteration-history.png)

Parfois, l’inspection visuelle permet d’identifier des modèles que vous pouvez ensuite rectifier avec l’ajout de données d’entraînement supplémentaires. Par exemple, un classifieur de roses et de marguerites risque de mal étiqueter toutes les roses blanches en les prenant pour des marguerites. Vous pouvez peut-être résoudre ce problème en ajoutant et fournissant des données d’entraînement qui contiennent des images étiquetées de roses blanches.

## <a name="unexpected-classification"></a>Classification inattendue

Parfois, le classifieur apprend les caractéristiques que vos images ont en commun. Par exemple, vous souhaitez créer un classifieur pour les roses et les tulipes. Vous fournissez des images de champs de tulipes, et des images de roses dans un vase rouge devant un mur bleu. Avec ces données, le classifieur peut s’entraîner en classant séparément le champ d’un côté et le mur + vase de l’autre au lieu d’opposer les roses aux tulipes.

Pour corriger ce problème, suivez les instructions sur l’entraînement avec plus d’images variées et fournissez des images prises selon des angles, des arrière-plans, des tailles d’objet, des groupes différents et d’autres variantes.

## <a name="negative-image-handling"></a>Traitement des images négatives

Le service Vision personnalisée prend en charge un traitement automatique des images négatives. Si vous générez un classifieur classant des chats et des chiens, et que vous soumettez l’image d’une chaussure pour la prédiction, ce classifieur doit évaluer cette image comme proche de 0 % autant pour les chats que pour les chiens. 

> [!WARNING]
> L’approche automatique fonctionne pour les images clairement négatives. Elle peut ne pas fonctionner correctement dans le cas où les images négatives sont simplement une variante des images utilisées dans l’entraînement. 
>
> Par exemple, si vous avez un classifieur distinguant les huskys des corgis, et que vous proposez l’image d’un loulou de Poméranie, il peut classer le loulou comme husky. Si vos images négatives sont de cette nature, créez un nouveau mot clé (par exemple, « Autre ») et appliquez-le aux images d’entraînement négatives.

## <a name="next-steps"></a>Étapes suivantes

[Utiliser l’API de prédiction](use-prediction-api.md)