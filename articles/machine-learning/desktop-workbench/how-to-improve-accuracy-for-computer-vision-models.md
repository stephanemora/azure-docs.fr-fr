---
title: Meilleure précision des modèles de classification et de vision par ordinateur dans Azure Machine Learning
description: Découvrez comment améliorer la précision de vos modèles de classification d’images, détection d’objets et similarité d’images de la vision par ordinateur à l’aide du package Azure Machine Learning de la vision par ordinateur.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 04/23/2018
ms.openlocfilehash: 6d6c540cd8711ae89784cdc797ca56d312522a83
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989309"
---
# <a name="improve-the-accuracy-of-computer-vision-models"></a>Améliorer la précision des modèles de la vision par ordinateur

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

Avec le **package Azure Machine Learning de la vision par ordinateur**, vous pouvez générer et déployer des modèles de classification d’images, de détection d’objets et de similarité d’images. En savoir plus sur ce package et comment l’installer.

Dans cet article, vous allez apprendre à optimiser ces modèles pour accroître leur précision. 

## <a name="accuracy-of-image-classification-models"></a>Précision des modèles de classification d’images

Il apparaît que le package Vision par ordinateur donne de bons résultats pour un large éventail de jeux de données. Cependant, à l’instar de la plupart des projets d’apprentissage automatique (« machine learning »), l’obtention des meilleurs résultats possibles pour un nouveau jeu de données nécessite un réglage minutieux des paramètres, ainsi qu’une évaluation pertinente des différents choix de conception. Les sections suivantes fournissent des instructions visant à améliorer la précision d’un jeu de données, autrement dit, à savoir quels paramètres sont plus prometteurs à optimiser en priorité, quelles valeurs de ces paramètres doivent être essayées et quels pièges sont à éviter.

En règle générale, la formation des modèles Deep Learning s’envisage en acceptant un compromis entre le temps de formation et la précision du modèle. Les paramètres par défaut prédéfinis (la première ligne du tableau ci-dessous) du package Vision par ordinateur privilégient une vitesse de formation élevée tout en produisant généralement des modèles de haute précision. Cette précision peut souvent être encore améliorée avec des modèles plus profonds, par exemple, ou une résolution d’image supérieure, mais c’est au détriment de la durée de formation qui peut être multipliée par 10 ou plus.

Il est préférable de commencer par travailler avec les paramètres par défaut, former un modèle, examiner les résultats, corriger les annotations réelles si nécessaire, et seulement après essayer les paramètres susceptibles d’allonger la durée de formation (voir dans le tableau ci-dessous les valeurs de paramètre suggérées). Une compréhension de ces paramètres, bien qu’elle ne soit pas techniquement nécessaire, est toutefois recommandée.


### <a name="best-practices-and-tips"></a>Bonnes pratiques et conseils

* Qualité des données : les jeux de formation et de test doivent être de qualité supérieure. Autrement dit, les images sont annotées correctement, les images ambiguës sont supprimées (par exemple, quand dans une image l’œil humain ne différencie pas une balle de tennis d’un citron) et les attributs sont mutuellement exclusifs (c’est-à-dire que chaque image appartient précisément à un seul attribut).

* Avant d’affiner le réseau neuronal profond, un classifieur SVM doit être formé au moyen d’un réseau neuronal profond, fixe et préformé, en tant que caractériseur. Cette possibilité est prise en charge dans le package Vision par ordinateur ; sa formation est rapide, car le réseau neuronal profond lui-même n’est pas modifié. Même si elle est simple, cette approche permet souvent d’obtenir de bonnes précisions, elle représente donc un bon point de départ. L’étape suivante consiste alors à affiner le réseau neuronal profond qui doit offrir une meilleure précision.

* Si l’objet d’intérêt est petit sur l’image, les approches de classification d’images sont en règle générale moins efficaces. Dans ce cas, songez à utiliser une approche de détection d’objets, telle que Faster R-CNN qui s’appuie sur Tensorflow, dans le package Vision par ordinateur.

* Plus il y a de données de formation, mieux c’est. En règle générale, il faut compter au moins 100 exemples par classe, par exemple 100 images de « chien », 100 images de « chat », etc. La formation d’un modèle avec moins d’images est possible, mais les résultats obtenus risquent d’être moins bons.

* Les images de formation doivent résider localement sur la machine avec le GPU, et se trouver sur un lecteur SSD (pas sur un disque dur). Dans le cas contraire, la latence de lecture de l’image peut réduire considérablement la vitesse de formation (pouvant même la diviser par 100).


### <a name="parameters-to-optimize"></a>Paramètres à optimiser

Trouver des valeurs optimales pour ces paramètres est important et peut souvent améliorer considérablement la précision :
* Taux d’apprentissage (`lr_per_mb`) : le paramètre sans doute le plus intéressant pour obtenir un bon résultat. Si la précision du jeu de formation, suite à l’affinement du réseau neuronal profond, est supérieure à ~5 %, le taux d’apprentissage est très probablement trop élevé, ou le nombre d’époques de formation trop faible. Le réseau neuronal profond a tendance, surtout avec de petits jeux de données, à adopter un comportement de surapprentissage par rapport aux données de formation, toutefois, dans la pratique, il s’avère que de bons modèles soient produits sur le jeu de test. Nous utilisons généralement 15 époques, où le taux d’apprentissage initial est réduit à deux reprises ; une formation utilisant plus d’époques peut, dans certains cas, accroître les performances.

* Résolution d’entrée (`image_dims`) : la résolution d’image par défaut est de 224 x 224 pixels. L’utilisation d’une résolution d’image plus élevée, par exemple de 500 x 500 pixels ou de 1000 x 1000 pixels, peut améliorer la précision de façon importante mais ralentir aussi l’affinement du réseau neuronal profond. Le package Vision par ordinateur s’attend à ce que la résolution d’entrée soit un tuple de (canaux de couleur, largeur de l’image, hauteur de l’image), par exemple (3, 224, 224), où le nombre de canaux de couleur doit être défini sur 3 (les bandes Rouge-Vert-Bleu).

* Architecture de modèle (`base_model_name`) : essayez d’utiliser des réseaux neuronaux profonds encore plus profonds, tels que ResNet-34 ou ResNet-50 à la place du modèle ResNet-18 par défaut. Non seulement le modèle Resnet-50 est plus profond, mais sa sortie de l’avant-dernière couche a une taille de 2 048 floats (contre 512 floats pour les modèles ResNet-18 et ResNet-34). Cette dimensionnalité accrue peut être particulièrement utile lorsque le réseau neuronal profond est maintenu fixe, et que la formation d’un classifieur SVM est préférée à la place.

* Taille de mini-lot (`mb_size`) : les tailles supérieures de mini-lot se traduisent par des durées de formation plus courtes, mais c’est au détriment d’une consommation accrue de la mémoire du réseau neuronal profond. Par conséquent, lorsque vous sélectionnez un modèle plus profond (par exemple, ResNet-50 au lieu de ResNet-18) et/ou une résolution d’image plus élevée (500\*500 pixels au lieu de 224\*224 pixels), il faut généralement réduire la taille de mini-lot pour éviter les erreurs de mémoire insuffisante. Lorsque vous modifiez la taille de mini-lot, souvent il faut aussi ajuster le taux d’apprentissage, comme illustré dans le tableau ci-dessous.
* Taux d’abandon (`dropout_rate`) et régulateur L2 (`l2_reg_weight`) : le surapprentissage du réseau neuronal profond peut être diminué à l’aide d’un taux d’abandon d’au moins 0,5 (la valeur par défaut est de 0,5 dans le package Vision par ordinateur) tout en augmentant la pondération du régulateur (la valeur par défaut est de 0,0005 dans le package Vision par ordinateur). Notez cependant qu’avec de petits jeux de données surtout, le surapprentissage du réseau neuronal profond est difficile voire souvent impossible à éviter.


### <a name="parameter-definitions"></a>Définitions des paramètres

- **Taux d’apprentissage**: taille d’étape utilisée pendant l’apprentissage de descente de gradient. S’il est trop bas, le modèle nécessite plusieurs époques pour se former ; s’il est trop élevé, le modèle ne peut pas converger vers une bonne solution. Une planification est généralement utilisée, où le taux d’apprentissage est réduit après un certain nombre d’époques. Par exemple, la planification du taux d’apprentissage `[0.05]*7 + [0.005]*7 + [0.0005]` correspond à l’utilisation d’un taux d’apprentissage initial de 0,05 pour les sept premières époques, suivi par un taux d’apprentissage divisé par 10 de 0,005 pour les sept époques suivantes, et enfin à l’ajustement optimal du modèle pour une époque unique au taux d’apprentissage divisé par 100 de 0,0005.

- **Taille de mini-lot** : les GPU peuvent traiter plusieurs images en parallèle pour accélérer le calcul. Ces images traitées en parallèle sont également appelées « mini-lot ». Plus la taille de mini-lot est importante, plus la formation est rapide, mais c’est au prix d’une consommation accrue de la mémoire du réseau neuronal profond.

### <a name="recommended-parameter-values"></a>Valeurs de paramètres recommandées

Le tableau ci-dessous présente différents jeux de paramètres qui se sont caractérisés par la production de modèles de haute précision, sur un large éventail de tâches de classification d’images. Les paramètres optimaux dépendent du jeu de données spécifique et du GPU exact utilisé, ainsi ce tableau ne doit être consulté qu’à titre indicatif. Après avoir essayé ces paramètres, songez à utiliser également des résolutions d’image supérieures à 500 x 500 pixels, ou des modèles plus profonds, tels que Resnet-101 ou Resnet-152.

La première ligne du tableau correspond aux paramètres par défaut qui sont définis dans le package Vision par ordinateur. Les autres lignes demandent un temps de formation plus long (tel que précisé dans la première colonne), mais c’est au profit d’une précision accrue (se reporter à la deuxième colonne pour l’exactitude moyenne sur trois jeux de données internes). Par exemple, les paramètres de la dernière ligne demandent 5 à 15 fois plus de temps pour former, mais la précision qui en résulte est meilleure et passe de 82,6 % à 92,8 % (en moyenne).

Des modèles plus profonds et une résolution d’entrée supérieure sollicitent plus de mémoire au niveau du réseau neuronal profond, ainsi la taille de mini-lot doit être réduite avec la complexité accrue du modèle, afin d’éviter des erreurs de mémoire insuffisante. Comme indiqué dans le tableau ci-dessous, il est préférable de diviser par deux le taux d’apprentissage chaque fois que la taille de mini-lot est diminuée du même coefficient. Vous pouvez être amené à réduire plus encore la taille de mini-lot sur les GPU disposant de petites quantités de mémoire.

| Temps de formation (estimation approximative) | Exemple de précision | Taille de mini-lot (*taille_mo*) | Taux d’apprentissage (*tda_par_mo*) | Résolution d’image (*dims_image*) | Architecture du réseau neuronal profond (*nom_modèle_base*) |
|------------- |:-------------:|:-------------:|:-----:|:-----:|:---:|
| 1 fois (durée de référence) | 82,6 % | 32 | [0,05]\*7  + [0,005]\*7  + [0,0005]  | (3, 224, 224) | ResNet18_ImageNet_CNTK |
| 2 à 5 fois    | 90,2 % | 16 | [0,025]\*7  + [0,0025]\*7  + [0,00025] | (3, 500, 500) | ResNet18_ImageNet_CNTK |
| 2 à 5 fois    | 87,5 % | 16 | [0,025]\*7  + [0,0025]\*7  + [0,00025] | (3, 224, 224) | ResNet50_ImageNet_CNTK |
| 5 à 15 fois        | 92,8 % |  8 | [0,01]\*7  + [0,001]\*7  + [0,0001]  | (3, 500, 500) | ResNet50_ImageNet_CNTK |


## <a name="next-steps"></a>Étapes suivantes

Pour obtenir plus d’informations sur le package Azure Machine Learning de la vision par ordinateur :
+ Consulter la documentation de référence

+ En savoir plus sur les [autres packages Python pour Azure Machine Learning](reference-python-package-overview.md)