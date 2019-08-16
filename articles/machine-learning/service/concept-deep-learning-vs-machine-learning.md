---
title: Deep Learning et Machine Learning
titleSuffix: Azure Machine Learning service
description: Apprenez-en davantage sur la relation entre le Deep Learning (apprentissage profond) et le Machine Learning (apprentissage automatique) et la manière dont ils s’inscrivent dans la catégorie de l’intelligence artificielle. Cet article explique comment le Deep Learning permet d’élaborer des scénarios de détection de fraude, de reconnaissances vocale et faciale, d’analyse des sentiments et de prévision de série chronologique.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 08/07/2019
ms.openlocfilehash: 4b66d015e8a182e305a36dacaa2e082288a3e19e
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840542"
---
# <a name="introduction-to-deep-learning-vs-machine-learning"></a>Deep Learning et Machine Learning

Cet article vous aide à comprendre la relation entre le Deep Learning et le Machine Learning. Il compare les deux concepts et explique comment ils s’inscrivent dans la catégorie plus large de l’intelligence artificielle. Enfin, il explique comment le Deep Learning peut s’appliquer à des scénarios du monde réel, tels que la détection de fraude, les reconnaissances vocale et faciale, l’analyse des sentiments et la prévision de série chronologique.

## <a name="how-do-deep-learning-machine-learning-and-ai-relate-to-one-another"></a>Quelle est la relation entre le Deep Learning, le Machine Learning et l’IA ?

![Diagramme des relations : Intelligence artificielle, Machine Learning et Deep Learning](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Pour comprendre la différence entre le Deep Learning, le Machine Learning et l’intelligence artificielle, découvrez chacun des domaines d’étude suivants :

- Le **Deep Learning**, ou apprentissage profond, est un sous-ensemble du Machine Learning, ou apprentissage automatique, basé sur des réseaux neuronaux artificiels permettant à une machine d’apprendre par elle-même. En l’occurrence, le _processus d’apprentissage_ est qualifié de _profond_ parce que la structure des réseaux neuronaux artificiels se compose de plusieurs couches d’entrée, de sortie et masquées. Chaque couche contient des unités qui transforment les données d’entrée en informations que la couche suivante peut utiliser pour effectuer une tâche prédictive spécifique. Grâce à cette structure, une machine est capable d’apprendre via son propre traitement de données.

- Le **Machine Learning** est un sous-ensemble de l’intelligence artificielle englobant des techniques (telles que le Deep Learning), qui permettent aux machines de s’améliorer à mesure qu’elles exécutent des tâches. En l’occurrence, le _processus d’apprentissage_ repose sur les étapes suivantes :

    1. Alimenter un algorithme en données en lui fournissant des informations (par exemple, en effectuant une extraction de caractéristiques).
    2. Utilisez ces données pour entraîner un modèle.
    3. Tester et déployer le modèle.
    4. Utiliser le modèle déployé pour effectuer une tâche prédictive automatisée.

- L’**intelligence artificielle** est une technique qui permet aux ordinateurs d’imiter l’intelligence humaine. Cette technique inclut le Machine Learning. Il est important de comprendre les relations entre _l’intelligence artificielle, le Machine Learning et le Deep Learning_ : Le Machine Learning contribue à l’intelligence artificielle. Les techniques d’apprentissage automatique et d’apprentissage profond vous permettent de construire des systèmes informatiques et applications capables d’exécuter des tâches couramment associées à l’intelligence humaine, telles que la perception optique, la reconnaissance vocale, la prise de décision et traduction entre langues.

## <a name="comparison-of-deep-learning-vs-machine-learning"></a>Comparaison entre Deep Learning et Machine Learning

À présent que vous comprenez les concepts de Machine Learning et de Deep Learning, comparons les deux techniques. Dans le cas du Machine Learning, l’algorithme a besoin d’informations pour apprendre à effectuer une prédiction précise. Dans le cas du Deep Learning, l’algorithme est capable d’apprendre part lui-même, via son propre traitement de données, grâce à sa structure de réseau neuronal artificiel.

Le tableau ci-dessous compare les deux techniques de manière plus détaillée :

| |Machine Learning dans sa globalité |Uniquement le Deep Learning|
|---|---|---|
|  **Nombre de points de données** | Peut utiliser de petites quantités de données fournies par les utilisateurs | Requiert une grande quantité de données d’entraînement pour en tirer des conclusions concises |
|  **Dépendances matérielles** | Peut fonctionner sur des machines bas de gamme. Ne nécessite pas beaucoup de puissance de calcul. | Nécessite des machines haut de gamme. Effectue fondamentalement un grand nombre d’opérations de multiplication de matrices. Ces opérations peuvent être efficacement optimisées à l’aide d’un processeur GPU. |
|  **Processus de personnalisation** | Nécessite que les fonctionnalités soient identifiées et créées avec précision par les utilisateurs | Apprend des caractéristiques de haut niveau à partir de données, et crée de nouvelles caractéristiques de façon autonome. |
|  **Approche d’apprentissage** | Fractionne les tâches en petits morceaux, puis combine les résultats reçus dans une conclusion. | Résout le problème de bout en bout. |
|  **Temps d’exécution** | Nécessite peu de temps pour apprendre, de quelques secondes à quelques heures. | Nécessite un temps conséquent pour apprendre, car l’algorithme intègre de nombreux paramètres. |
|  **Sortie** | La sortie est généralement une valeur numérique, telle qu’un score ou une classification. | La sortie peut être un score, un texte, un élément ou un son. |

## <a name="deep-learning-use-cases-what-problems-does-it-solve"></a>Cas d’utilisation du Deep Learning : Quels problèmes résout-il ?

En raison de sa structure de réseau neuronal artificiel, le Deep Learning excelle dans l’identification de modèles dans des données non structurées telles que des images, du son, de la vidéo et du texte. C’est pourquoi, il se répand rapidement dans de nombreux secteurs, tels que ceux de la santé, de l’énergie, des finances ou des transports, qui l’utilisent pour repenser leurs processus métier traditionnels. Certaines applications courantes du Deep Learning sont décrites dans les paragraphes suivants.

### <a name="named-entity-recognition"></a>Reconnaissance d’entité nommée

L’une des utilisations des réseaux de Deep Learning est la reconnaissance d’entité nommée, qui permet d’extraire de données non structurées et non étiquetées certains types d’informations telles que des personnes, des lieux, des entreprises ou des objets. Ces informations peuvent ensuite être stockées dans un schéma structuré afin d’établir une liste d’adresses ou de servir de référence pour un moteur de validation d’identité.

### <a name="object-detection"></a>Détection d’objets

le Deep Learning est souvent utilisé pour la détection d’objets. La détection d’objet est un processus qui s’opère en deux temps : classification d’images, puis localisation d’images. La classification d’images consiste à déterminer la nature des objets figurant dans des images : voiture, personne, , etc. La localisation d’images consiste à fournir l’emplacement spécifique de ces objets. La détection d’objets est déjà utilisée dans les secteurs du jeu, de la vente au détail, du tourisme et des véhicules autonomes.

### <a name="image-caption-generation"></a>Génération de légende d’image

À l’instar de la reconnaissance d’image, le légendage d’image consiste à générer, pour une image donnée, une légende décrivant le contenu de celle-ci. Lorsque vous pouvez détecter des objets sur des photographies et générer des étiquettes pour ces objets, l’étape suivante consiste à transformer ces étiquettes en descriptions cohérentes. Généralement, les systèmes impliquent l’utilisation de très grands réseaux neuronaux convolutifs pour la détection d’objets sur des photographies, puis d’un réseau de neurones récurrents (RNN) pour convertir les étiquettes en énoncés cohérents.

### <a name="machine-translation"></a>Traduction automatique

La traduction automatique consiste à traduire des mots, des phrases ou des expressions d’une langue dans une autre langue. La traduction automatique existe depuis longtemps, et le Deep Learning produit des résultats exceptionnels dans deux domaines spécifiques : la traduction automatique de texte (et la synthèse vocale) et la traduction automatique d’images. Avec une conversion appropriée des données, un réseau neuronal profond est capable de comprendre du texte, du son et des signaux visuels. La traduction automatique permet d’identifier des extraits de son dans des fichiers audio volumineux, ainsi que de transcrire de la parole ou des images en texte.

### <a name="text-analytics"></a>Analyse de texte

L’une des tâches importantes que le Deep Learning permet d’accomplir est la découverte électronique. Des entreprises utilisent des analyses de texte basées sur le Deep Learning pour la détection de délits d’initiés et en lien avec la conformité aux réglementations gouvernementales. Des fonds spéculatifs utilisent l’analyse de texte pour explorer de vastes référentiels de documents afin de recueillir des informations sur les performances futures de leurs investissements et les tendances du marché. Le cas d’utilisation typique de l’analyse de texte basée sur le Deep Learning consiste à exploiter sa capacité à traiter des quantités considérables de données textuelles ainsi qu’à effectuer des agrégations.

## <a name="what-are-artificial-neural-networks"></a>Que sont les réseaux neuronaux artificiels ?

Un réseau neuronal artificiel est un modèle de Deep Learning inspiré de la manière dont les réseaux neuronaux biologiques du cerveau humain traitent les informations. Plusieurs types de réseaux neuronaux artificiels sont actuellement utilisés. Les typologies de réseaux neuronaux artificiels les plus populaires sont décrites ci-dessous.

### <a name="feedforward-neural-network"></a>Réseau neuronal feedforward

Le réseau neuronal à feedforward (à propagation avant) est le type le plus fondamental de réseau neuronal artificiel, dans lequel les informations ne circulent que dans une seule direction, de la couche d’entrée à la couche de sortie. Les réseaux neuronaux feedforward transforment une entrée en la faisant passer par une série de couches masquées. Chaque couche est constituée d’un ensemble de neurones, et entièrement connectée à tous les neurones de la couche précédente. Enfin, il existe une dernière couche entièrement connectée (la couche de sortie) qui représente les prédictions générées.

### <a name="recurrent-neural-network"></a>Réseau de neurones récurrents

Les réseaux de neurones récurrents constituent un type de réseau neuronal artificiel plus largement utilisé, qui fonctionne selon le principe de l’enregistrement de la sortie d’une couche et de son renvoi à la couche d’entrée pour aider à prédire le résultat de la sortie. Ces réseaux neuronaux possèdent de plus grandes capacités d’apprentissage et sont largement utilisés pour des tâches complexes, telles que l’apprentissage de l’écriture manuscrite ou la reconnaissance de la parole.

### <a name="convolutional-neural-networks"></a>Réseaux neuronaux convolutifs

Un réseau neuronal convolutif est un type particulièrement efficace de réseau neuronal artificiel, qui présente une architecture unique. Premièrement, les couches sont organisées en trois dimensions : largeur, hauteur et profondeur. Ensuite, les neurones d’une couche ne sont pas connectés à tous les neurones de la couche suivante, mais uniquement à une petite région de celle-ci. Enfin, le résultat final est réduit à un seul vecteur de scores de probabilité, organisés dans la dimension de la profondeur. Ces réseaux neuronaux sont utilisés dans des domaines tels que la reconnaissance et la classification d’images.

## <a name="next-steps"></a>Étapes suivantes

Les articles suivants expliquent comment utiliser la technologie de Deep Learning sur [Azure Machine Learning service](/azure/machine-learning/service/) :

- [Classifier des chiffres manuscrits avec un modèle TensorFlow](how-to-train-tensorflow.md)
- [Classifier des chiffres manuscrits avec un estimateur TensorFlow et Keras](how-to-train-keras.md)
- [Classifier des images avec un modèle Pytorch](how-to-train-pytorch.md)
- [Classifier des chiffres manuscrits avec un modèle Chainer](how-to-train-pytorch.md)
