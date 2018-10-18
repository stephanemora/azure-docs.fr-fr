---
title: 'Améliorer votre classifieur : Service Vision personnalisée'
titlesuffix: Azure Cognitive Services
description: Découvrez comment améliorer la qualité de votre classifieur.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: pafarley
ms.openlocfilehash: 2bee7f0af98bf03a13e376dea9dbf083b3f61815
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49340288"
---
# <a name="how-to-improve-your-classifier"></a>Comment améliorer votre classifieur

Découvrez comment améliorer la qualité de votre classifieur Service Vision personnalisée. La qualité d’un classifieur dépend de la quantité, de la qualité et de la variété des données étiquetées fournies, ainsi que de l’équilibre du jeu de données. En général, un bon classifieur comporte un jeu de données d’apprentissage équilibré et représentatif de ce qui lui sera soumis. Le processus de création d’un tel classifieur est souvent itératif. Il est courant de passer par quelques cycles d’apprentissage pour atteindre les résultats attendus.

Voici les étapes généralement suivies pour améliorer un classifieur. Il ne s’agit pas de règles absolues, mais d’heuristiques qui vous aideront à créer un meilleur classifieur.

1. Effectuer le premier cycle d’apprentissage.
1. Ajouter des images et équilibrer les données.
1. Recyclage
1. Ajouter des images avec différents arrière-plans, éclairages, tailles d’objet, angles de vue et styles.
1. Relancer l’apprentissage et soumettre une image à la prédiction.
1. Examiner les résultats de la prédiction.
1. Modifier les données d’apprentissage existantes.

## <a name="data-quantity-and-data-balance"></a>Quantité et équilibre des données

Le plus important est de charger suffisamment d’images pour pouvoir effectuer l’apprentissage du classifieur. Le volume recommandé au départ est d’au moins 50 images par étiquette dans le jeu d’apprentissage. Avec moins d’images, le risque de surajustement est élevé. Même si les performances indiquent une bonne qualité, il pourrait être difficile de se confronter à des données du monde réel. En général, un classifieur entraîné avec davantage d’images donnera des résultats de prédiction plus précis.

Autre considération importante, veillez à ce que vos données soient équilibrées. Par exemple, si une étiquette comporte 500 images et une autre 50, le jeu de données d’apprentissage s’en trouvera déséquilibré et le modèle sera plus précis pour prédire une étiquette que l’autre. Les résultats seront très certainement meilleurs avec un ratio d’au moins 1:2 entre l’étiquette la moins bien dotée et l’étiquette la plus foisonnante. Si, par exemple, cette dernière comporte 500 images, les autres doivent inclure au moins 250 images pour l’apprentissage.

## <a name="train-more-diverse-images"></a>Diversification des images

Fournissez des images représentatives de ce qui va être soumis au classifieur en utilisation normale. Supposons, par exemple, que vous effectuez l’apprentissage d’un classifieur « pommes » : il ne sera peut-être pas très précis s’il a été exclusivement entraîné avec des photos de pommes dans des assiettes et qu’il doit établir des prédictions sur des photos de pommes dans des arbres. La variété des images permet d’éviter les biais dans le classifieur et lui permet de généraliser correctement. Voici quelques moyens de rendre un jeu d’apprentissage plus varié :

__Arrière-plan :__ fournissez des images de votre objet devant différents arrière-plans (autrement dit, à la fois des fruits sur une assiette et des fruits dans un sac d’épicerie). Les photos en contexte sont plus performantes que celles dont l’arrière-plan est neutre, car elles apportent davantage d’informations au classifieur.

![Exemples d’images (arrière-plan)](./media/getting-started-improving-your-classifier/background.png)

__Éclairage :__ fournissez des images présentant des éclairages variés (autrement dit, prises avec flash, avec exposition élevée, etc.), en particulier si les images utilisées pour la prédiction sont diversifiées de ce point de vue. Il est également utile d’inclure des images montrant différents niveaux de saturation, de teinte et de luminosité.

![Exemples d’images (éclairage)](./media/getting-started-improving-your-classifier/lighting.png)

__Taille de l’objet :__ fournissez des images dans lesquelles les objets sont de tailles variées et qui capturent différentes parties de l’objet. Par exemple, prenez une photo de grappes de bananes et un gros plan sur une seule banane. Les différences de taille permettent au classifieur de mieux généraliser.

![Exemples d’images (taille)](./media/getting-started-improving-your-classifier/size.png)

__Angle de vue :__ fournissez des images prises avec différents angles de vue. Si toutes vos photos sont prises avec un ensemble d’appareils photo fixes (par exemple, des caméras de surveillance), attribuez une étiquette différente à chaque appareil photo, même s’ils photographient les mêmes objets, pour éviter le surajustement, c’est-à-dire la modélisation d’objets étrangers (par exemple, des réverbères) comme trait central.

![Exemples d’images (angle)](./media/getting-started-improving-your-classifier/angle.png)

__Style :__ fournissez des images de différents styles de la même classe (autrement dit, différents types d’agrumes). Toutefois, si vous avez des images d’objets de styles radicalement différents (par exemple, Mickey Mouse et un vrai rat), il est recommandé de les étiqueter comme classes distinctes pour mieux représenter leurs traits propres.

![Exemples d’images (style)](./media/getting-started-improving-your-classifier/style.png)

## <a name="use-images-submitted-for-prediction"></a>Utiliser des images soumises pour la prédiction

Le Service Vision personnalisée stocke les images envoyées au point de terminaison de prédiction. Pour utiliser ces images et améliorer le classifieur, effectuez les opérations suivantes :

1. Pour voir les images soumises au classifieur, ouvrez la [page web Custom Vision](https://customvision.ai), accédez à votre projet et sélectionnez l’onglet __Prédictions__. La vue par défaut montre des images à partir de l’itération active. Vous pouvez utiliser la zone déroulante __Iteration__ pour voir les images envoyées au cours des précédentes itérations.

    ![Image de l’onglet des prédictions](./media/getting-started-improving-your-classifier/predictions.png)

2. Pointez sur une image pour lire les mots clés qui ont été prédits par le classifieur. Les images sont classées des plus intéressantes pour le classifieur (en haut) aux moins utiles. Pour sélectionner un tri différent, utilisez la section __Trier__. Pour ajouter une image à vos données d’apprentissage, sélectionnez-la, sélectionnez la balise correspondante et cliquez sur __Enregistrer et fermer__. L’image est supprimée de __Prédictions__ et ajoutée aux images d’apprentissage. Vous pouvez la voir en sélectionnant l’onglet __Training Images__ (Images d’entraînement).

    ![Image de la page d’étiquetage](./media/getting-started-improving-your-classifier/tag.png)

3. Utilisez le bouton __Train__ (Entraîner) pour ré-entraîner le classifieur.

## <a name="visually-inspect-predictions"></a>Inspecter visuellement les prédictions

Pour examiner les prédictions d’image, sélectionnez l’onglet __Training Images__ (Images d’entraînement), puis __Iteration History__ (Historique des itérations). Les images bordées d’un trait rouge ont été prédites de façon incorrecte.

![Image de l’historique des itérations](./media/getting-started-improving-your-classifier/iteration.png)

Parfois, l’inspection visuelle permet d’identifier des modèles que l’on peut ensuite rectifier en ajoutant des données d’apprentissage supplémentaires ou en modifiant les données existantes. Par exemple, un classifieur pommes/citrons verts risquerait d’étiqueter à tort toutes les pommes vertes comme des citrons verts. Il serait peut-être possible de résoudre ce problème en ajoutant et en fournissant des données d’apprentissage comportant des images étiquetées de pommes vertes.

## <a name="unexpected-classification"></a>Classification inattendue

Il peut arriver que le classifieur apprenne mal les caractéristiques communes des images. Si, par exemple, vous créez un classifieur pommes/citrons verts et que vous fournissez des images de pommes tenues à la main et de citrons verts dans des assiettes blanches, le classifieur risque d’effectuer son apprentissage sur la différence entre les mains et les assiettes blanches plutôt qu’entre les pommes et les citrons verts.

![Image de classification inattendue](./media/getting-started-improving-your-classifier/unexpected.png)

Pour corriger ce problème, suivez les instructions ci-dessus afin d’effectuer l’apprentissage avec des images plus variées : fournissez des images prises selon différents angles, arrière-plans, tailles d’objet, groupes et autres variantes.

## <a name="negative-image-handling"></a>Traitement des images négatives

Le service Vision personnalisée prend en charge un traitement automatique des images négatives. Si vous générez un classifieur raisins/bananes et que vous soumettez une image de chaussure à la prédiction, il devra lui donner un score proche de 0 % aussi bien pour les raisins que pour les bananes.

À l’inverse, dans les cas où les images négatives représentent simplement une variante des images utilisées pour l’apprentissage, il est probable que le modèle les catégorise comme une classe étiquetée en raison des grandes similitudes qu’elles présentent. Si, par exemple, si vous avez un classifieur oranges/pamplemousses et que vous fournissez une image de clémentine, il est possible qu’il l’évalue comme étant une orange. En effet, de nombreux traits de la clémentine (couleur, forme, texture, milieu naturel, etc.) sont semblables à ceux des oranges.  Si vos images négatives sont de cette nature, il est recommandé de créer une ou plusieurs balises distinctes (« Autre ») et d’étiqueter ainsi les images négatives pendant l’apprentissage pour permettre au modèle de mieux faire la distinction entre ces classes.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment tester les images par programmation en les soumettant à l’API Prédiction.

> [!div class="nextstepaction"]
[Utiliser l’API de prédiction](use-prediction-api.md)
