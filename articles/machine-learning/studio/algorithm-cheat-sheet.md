---
title: Aide-mémoire d’algorithme de machine learning - Azure Machine Learning Studio | Microsoft Docs
description: Un aide-mémoire imprimable d'algorithme d'apprentissage automatique vous permet de choisir l'algorithme adapté à votre modèle prédictif dans Azure Machine Learning Studio.
keywords: aide mémoire d’algorithme, aide mémoire, algorithme d’apprentissage automatique
services: machine-learning
author: ericlicoding
ms.custom: previous-ms.author=pakalra, previous-author=pakalra
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: e1dc31ec-1acb-463f-ba77-de565d4ddf4d
ms.service: machine-learning
ms.component: studio
ms.topic: article
ms.date: 12/18/2017
ms.openlocfilehash: 7ddc3a9829dc05ac1c1df450091789e4cad2b2f5
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53274501"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-studio"></a>Aide-mémoire d’algorithme d’apprentissage automatique pour Azure Machine Learning Studio

**L’Aide-mémoire d’algorithme Azure Machine Learning Studio** vous permet de choisir l’algorithme adapté à un modèle d’analyse prédictif.

[Azure Machine Learning Studio](https://studio.azureml.net/) possède une grande bibliothèque d’algorithmes allant de la ***régression***, de la ***classification*** et du ***clustering*** à la ***détection des anomalies***. Chacun est conçu pour traiter un type de problème de Machine Learning différent.

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Télécharger : Aide-mémoire de l’algorithme Machine Learning

**Téléchargez l’aide-mémoire ici : [Aide-mémoire de l’algorithme Machine Learning (11 x 17 pouces)](https://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v7.pdf)**

![Aide-mémoire de l’algorithme Machine Learning : Découvrez comment choisir un algorithme Machine Learning.][cheat-sheet]

[cheat-sheet]: ./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-small_v_0_6-01.png

Téléchargez et imprimez l’Aide-mémoire d’algorithme Machine Learning Studio au format tabloïd pour l’avoir à portée de main et obtenir de l’aide lors du choix d’un algorithme.

> [!NOTE]
> Pour obtenir de l’aide concernant l’utilisation de cet aide-mémoire pour le choix de l’algorithme approprié et plus d’informations sur les différents types d’algorithmes d’apprentissage et leur utilisation, consultez [Comment choisir des algorithmes Microsoft Azure Machine Learning](algorithm-choice.md).

## <a name="notes-and-terminology-definitions-for-the-machine-learning-studio-algorithm-cheat-sheet"></a>Remarques et définitions terminologiques pour l’aide-mémoire d’algorithme Machine Learning Studio

* Les suggestions proposées dans cet aide-mémoire d'algorithme sont des règles de base approximatives. Certaines peuvent être contournées et d’autres ignorées. Elles visent à proposer un point de départ. N’hésitez pas à comparer plusieurs algorithmes avec vos données. Il est tout simplement indispensable de comprendre les principes de chaque algorithme et le système qui a généré les données.

* Chaque algorithme d’apprentissage automatique a son propre style ou *décalage inductif*. Plusieurs algorithmes peuvent être appropriés pour un problème spécifique et un algorithme peut être un meilleur choix que d’autres. Mais il n’est pas toujours possible de savoir au préalable lequel convient le mieux. Dans ce cas, plusieurs algorithmes sont affichés dans l’aide-mémoire. Une stratégie appropriée consiste à essayer un algorithme et, si les résultats ne sont pas satisfaisants, à essayer les autres. Voici un exemple tiré d’[Azure AI Gallery](http://gallery.azure.ai/). Cette expérience teste plusieurs algorithmes sur les mêmes données et compare les résultats : [Compare Multi-class Classifiers: Letter recognition](http://gallery.azure.ai/Details/a635502fc98b402a890efe21cec65b92) (Comparer des classifieurs multiclasses : reconnaissance de lettres).

* Il existe trois catégories principales d’apprentissage automatique : l’**apprentissage supervisé**, l’**apprentissage non supervisé** et l’**apprentissage par renforcement**.

  * Dans l’**apprentissage supervisé**, chaque point de données est étiqueté ou associé à une catégorie ou une valeur qui vous intéresse.  La définition d’une image comme un « chat » ou un « chien » est un exemple d’étiquette de catégorie.  Le prix de vente associé à un véhicule d’occasion est un exemple d’étiquette de valeur. L’objectif de l’apprentissage supervisé est d’étudier de nombreux exemples étiquetés comme ceux-ci, pour pouvoir effectuer des prévisions sur les points de données futurs. Par exemple pour identifier de nouvelles photos avec le bon animal ou affecter des prix de vente corrects à des véhicules d’occasion. Il s’agit d’un type d’apprentissage automatique utile et apprécié. Tous les modules d’Azure Machine Learning sont supervisés, mis à part l’algorithme des [k-moyennes][k-means-clustering].

  * Dans l’**apprentissage non supervisé**, les points de données n’ont aucune étiquette associée. En effet, l’objectif d’un algorithme d’apprentissage non supervisé est d’organiser les données d’une certaine façon ou de décrire sa structure. Cela peut signifier un regroupement en clusters, comme le fait l’algorithme des k-moyennes ou la recherche de différentes manières de visualiser des données complexes afin d’en simplifier l’affichage.

  * Dans l’**apprentissage par renforcement**, l’algorithme choisit une action en réponse à chaque point de données. Il s’agit d’une approche courante en robotique, où le jeu des lectures des capteurs à un moment donné est un point de données et où l’algorithme doit choisir l’action suivante du robot. Il est également adapté aux applications d’Internet des objets. L’algorithme d’apprentissage reçoit également un signal de récompense quelques instants plus tard, qui indique la qualité de la décision. En fonction de ce signal, l’algorithme modifie sa stratégie pour atteindre la récompense la plus élevée. Il n’existe actuellement aucun module d’apprentissage de renforcement dans Azure ML.

* **Les méthodes bayésiennes** effectuent des hypothèses de points de données statistiquement indépendants. Cela signifie que la variabilité sans modèle dans un point de données n’est pas corrélée avec d’autres : autrement dit, elle n’est pas prévisible. Par exemple, si les données enregistrées sont le nombre de minutes jusqu’à l’arrivée du métro suivant, deux mesures effectuées deux jours différents sont statistiquement indépendantes. Toutefois, deux mesures avec une différence d’une minute ne sont pas statistiquement indépendantes : la valeur de l’une est hautement prédictive de la valeur de l’autre.

* **La régression de l’arbre de décision optimisé** tire parti du chevauchement de fonctionnalités ou de l’interaction entre les fonctionnalités. Cela signifie que, dans tout point de données spécifique, la valeur d’une fonctionnalité est prédictive de la valeur d’une autre. Par exemple, pour les données de températures minimales et maximales quotidiennes, connaître la température minimale du jour vous permet de faire une estimation raisonnable de la température maximale. Les informations contenues dans ces deux fonctionnalités sont quelque peu redondantes.

* La classification des données en plus de deux catégories peut être effectuée en utilisant un classifieur à plusieurs classes par nature ou en combinant un ensemble de classifieurs à deux classes dans un **ensemble**. Dans l’approche de l’ensemble, il existe un classifieur à deux classes distinct pour chaque classe, chacun d’entre eux séparant les données en deux catégories : « cette classe » et « pas cette classe ». Puis ces classifieurs votent pour l’affectation correcte du point de données. C’est le principe de fonctionnement sur lequel repose le modèle [à plusieurs classes de un contre tous][one-vs-all-multiclass].

* Plusieurs méthodes, notamment la régression logistique et l’ordinateur de point de Bayes, supposent l’existence de **limites de classe linéaire**. Autrement dit, que les limites entre les classes sont plus ou moins des lignes droites (ou des hyperplans dans le cas le plus général). Il s’agit souvent d’une caractéristique des données que vous ne connaissez pas avant d’avoir essayé de les séparer. Toutefois, vous pouvez généralement la découvrir avec une visualisation au préalable. Si les limites de la classe semblent très irrégulières, utilisez des arbres de décision, des jungles de décision, des machines à vecteurs de support ou des réseaux neuronaux.

* Les réseaux neuronaux peuvent être utilisés avec des variables de catégorie en créant une **variable factice** pour chaque catégorie, en la définissant sur 1 dans les cas où la catégorie s’applique, sur 0 quand ce n’est pas le cas.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir une infographie téléchargeable décrivant les algorithmes et accompagnée d’exemples, consultez la page [Infographie téléchargeable : Principes de base de l’apprentissage automatique avec exemples d’algorithmes](basics-infographic-with-algorithm-examples.md).

* Pour obtenir une liste par catégorie de tous les algorithmes d’apprentissage automatique disponibles dans Machine Learning Studio, consultez [Initialiser le modèle][initialize-model] dans Machine Learning Studio : aide sur les algorithmes et les modules.

* Pour obtenir la liste alphabétique complète des algorithmes et des modules de Machine Learning Studio, consultez [Liste alphabétique des modules de Machine Learning Studio][a-z-list] dans Machine Learning Studio : aide sur les algorithmes et les modules.

* Pour télécharger et imprimer un diagramme offrant une vue d’ensemble des fonctionnalités de Machine Learning Studio, consultez [Diagramme de vue d’ensemble des fonctionnalités d’Azure Machine Learning Studio](studio-overview-diagram.md).



<!-- Module References -->
[a-z-list]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/a-z-module-list
[initialize-model]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/machine-learning-initialize-model
[k-means-clustering]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/k-means-clustering
[one-vs-all-multiclass]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/one-vs-all-multiclass
