---
title: 'Clustering K-Means : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module de clustering K-means dans le service Azure Machine Learning pour l’apprentissage des modèles de clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 29aeb460581655190b7c3f4256647059f4642d3e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029699"
---
# <a name="k-means-clustering"></a>Clustering K-Means

Cet article décrit comment utiliser le **Clustering K-Means** module dans Azure Machine Learning Studio pour créer un modèle de clustering K-means non formé. 
 
K-means est une des plus simples et les plus connues *non supervisé* algorithmes d’apprentissage et peut être utilisé pour un large éventail de tâches d’apprentissage automatique, tel que [détection de données anormales](https://msdn.microsoft.com/magazine/jj891054.aspx), clustering de texte documents et l’analyse d’un jeu de données avant d’utiliser d’autres méthodes de classification ou de régression. Pour créer un modèle de clustering, ajouter ce module à votre expérience, connectez un jeu de données et définir des paramètres tels que le nombre de clusters que vous vous y attendre, la mesure de la distance à utiliser pour créer les clusters et ainsi de suite. 
  
Une fois que vous avez configuré les hyperparamètres de module, connectez le modèle non formé à la [Train Clustering Model](train-clustering-model.md) , car l’algorithme K-means est une méthode d’apprentissage non supervisé, une colonne d’étiquette est facultative. 

+ Si vos données incluent une étiquette, vous pouvez utiliser les valeurs d’étiquette pour guider la sélection des clusters et optimiser le modèle. 

+ Si vos données n’ont aucune étiquette, l’algorithme crée des clusters qui représente les catégories possibles, en fonction des données.  
  

  
##  <a name="understanding-k-means-clustering"></a>Présentation clustering k-means
 
En règle générale, le clustering utilise des techniques itératives pour grouper les cas dans un jeu de données dans des clusters qui présentent des caractéristiques similaires. Ces regroupements sont utiles pour l’exploration de données, en identifiant les anomalies dans les données et finalement pour élaborer des prédictions. Modèles de clustering peuvent également vous aider à identifier les relations dans un jeu de données que vous ne pourriez pas forcément déduire par l’observation de navigation ou simple. Pour ces raisons, clustering est souvent utilisé dans les premières phases de tâches, d’apprentissage pour Explorer les données et découvrir des corrélations inattendues.  
  
 Lorsque vous configurez un modèle de clustering à l’aide de la méthode k-means, vous devez spécifier un nombre cible *k* indiquant le nombre de *centroïdes* souhaitées dans le modèle. Le centroïde est un point qui est représentatif de chaque cluster. L’algorithme K-means attribue chaque point de données entrantes à l’un des clusters en minimisant la somme des carrés au sein du cluster de. 
 
Lors du traitement des données d’apprentissage, l’algorithme K-means commence par un ensemble initial de randomnly choisi centroïdes, qui servent de points de départ pour chaque cluster et appliquent l’algorithme de Lloyd pour affiner de manière itérative les emplacements des centroïdes. L’algorithme K-means cesse de créer et affiner des clusters lorsqu’il répond à un ou plusieurs de ces conditions :  
  
-   Les centroïdes sont stabilisés, ce qui signifie que les affectations de cluster pour différents points de ne plus modifier et l’algorithme a convergé vers une solution.  
  
-   L’algorithme a terminé l’exécution du nombre spécifié d’itérations.  
  
 À l’issue de la phase de formation, vous utilisez le [affecter les données aux Clusters](assign-data-to-clusters.md) module pour affecter de nouveaux cas à un des clusters qui a été trouvé par l’algorithme k-means. Affectation de cluster est effectuée en calculant la distance entre le nouveau cas et le centroïde de chaque cluster. Chaque nouveau cas est affecté au cluster avec le centroïde le plus proche.  

## <a name="how-to-configure-k-means-clustering"></a>Comment configurer le Clustering K-Means
  
1.  Ajouter le **Clustering K-Means** module à votre expérience.  
  
2.  Spécifiez comment vous souhaitez que le modèle formé, en définissant le **créer un mode d’entraînement** option.  
  
    -   **L’unique paramètre**: Si vous connaissez les paramètres exacts que vous souhaitez utiliser dans le modèle de clustering, vous pouvez fournir un ensemble spécifique de valeurs comme arguments.  
  
   
  
3.  Pour **nombre de centroïdes**, tapez le nombre de clusters que vous voulez que l’algorithme commence par.  
  
     Le modèle n’est pas garanti pour produire exactement ce nombre de clusters. L’algorithn commence par ce nombre de points de données et effectue une itération pour trouver la configuration optimale.  
  
     
  
4.  Les propriétés **initialisation** est utilisé pour spécifier l’algorithme qui est utilisé pour définir la configuration initiale du cluster.  
  
    -   **N premiers**: Certains nombre initial de points de données est choisi dans le jeu de données et utilisée comme moyennes initiales.  
  
         Également appelé le *méthode Forgy*.  
  
    -   **Aléatoire**: L’algorithme place au hasard un point de données dans un cluster et puis calcule la moyenne initiale, qui est le centroïde du cluster façon aléatoire des points affectés.  
  
         Également appelé le *partition aléatoire* (méthode).  
  
    -   **K-Means ++**: Il s’agit de la méthode par défaut pour l’initialisation de clusters.  
  
         Le **K-means ++** algorithme a été proposé en 2007 par David Arthur et l’algorithme k-means Sergei Vassilvitskii afin d’éviter un clustering pauvre par la norme. **K-means ++** améliore K-means standard à l’aide d’une autre méthode permettant de choisir les centres de cluster initiale.  
  
    
5.  Pour **valeur initiale de nombre aléatoire**, vous pouvez éventuellement taper une valeur à utiliser comme valeur initiale pour l’initialisation de cluster. Cette valeur peut avoir un effet significatif sur la sélection du cluster.  
  
    
  
6.  Pour **métrique**, choisissez la fonction à utiliser pour mesurer la distance entre des vecteurs de cluster, ou entre les nouveaux points de données et le centroïde choisi au hasard. Azure Machine Learning prend en charge les mesures de distance de cluster suivantes :  
  
    -   **Euclidien**: La distance EUCLIDIENNE est couramment utilisée comme une mesure de nuage de points de cluster pour le clustering K-means. Cette mesure est privilégiée, car il réduit la distance moyenne entre les points et les centroïdes.
  

  
7.  Pour **itérations**, tapez le nombre de fois où l’algorithme doit effectuer une itération sur les données d’apprentissage avant la finalisation de la sélection des centroïdes.  
  
     Vous pouvez ajuster ce paramètre de précision au solde par rapport au temps de formation.  
  
8.  Pour **affecter le mode de label**, choisissez une option qui spécifie comment une colonne d’étiquette, s’il est présent dans le jeu de données, doit être gérée.  
  
     Clustering K-means étant une méthode d’apprentissage non supervisé, les étiquettes sont facultatives. Toutefois, si votre jeu de données a déjà une colonne d’étiquette, vous pouvez utiliser ces valeurs pour guider la sélection des clusters, ou vous pouvez spécifier que les valeurs ignorées.  
  
    -   **Ignorer la colonne d’étiquette**: Les valeurs dans la colonne d’étiquette sont ignorés et ne sont pas utilisés pour générer le modèle.
  
    -   **Remplir les valeurs manquantes**: Les valeurs de colonne d’étiquette sont utilisés en tant que fonctionnalités pour aider à créer les clusters. Si toutes les lignes manque une étiquette, la valeur est imputée à l’aide d’autres fonctionnalités.  
  
    -   **Remplacer le plus proche au centre**: Les valeurs de colonne d’étiquette sont remplacés par les valeurs d’étiquette prédite, à l’aide de l’étiquette du point le plus proche du Centroïde en cours.  

8.  Sélectionnez l’option **normaliser les fonctionnalités**, si vous souhaitez normaliser les fonctionnalités avant l’apprentissage.
  
     Si vous appliquez la normalisation, avant l’apprentissage, les points de données sont normalisées à `[0,1]` par MinMaxNormalizer.

10. Apprentissage du modèle.  
  
    -   Si vous définissez **créer un mode d’entraînement** à **seul paramètre**, ajoutez un jeu de données avec balises et former le modèle à l’aide de la [former le modèle de Clustering](train-clustering-model.md) module.  
  

### <a name="results"></a>Résultats

Une fois que vous avez terminé la configuration et l’apprentissage du modèle, vous disposez d’un modèle que vous pouvez utiliser pour générer des scores. Toutefois, il existe plusieurs façons pour former le modèle, ainsi que plusieurs méthodes pour afficher et utiliser les résultats : 

#### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>Capturer un instantané du modèle dans votre espace de travail

+ Si vous avez utilisé le [Train Clustering Model](train-clustering-model.md) module
    1. Cliquez sur le [Train Clustering Model](train-clustering-model.md) module.
    2. Sélectionnez **ajouté pour l’apprentissage modèle** puis cliquez sur **enregistrer en tant que modèle formé**.


Le modèle enregistré représente les données d’apprentissage au moment où que vous avez enregistré le modèle. Si plus tard, vous mettez à jour les données d’apprentissage utilisées dans l’expérience, il ne met pas à jour le modèle enregistré. 



#### <a name="see-the-clustering-result-dataset"></a>Consultez le jeu de données de résultat mise en cluster 

+ Si vous avez utilisé le [Train Clustering Model](train-clustering-model.md) module
    1. Cliquez sur le [Train Clustering Model](train-clustering-model.md) module.
    2. Sélectionnez **jeu de données de résultats** puis cliquez sur **visualiser**.


### <a name="tips-for-generating-the-best-clustering-model"></a>Conseils pour la génération du meilleur modèle de clustering  

Nous savons que la **amorçage** processus utilisés pendant le clustering peut affecter de manière significative le modèle. L’amorçage signifie que le placement initial de points dans potental centroïdes.
 
Par exemple, si le dataset contient de nombreuses valeurs hors norme, et une valeur hors norme est choisie pour amorcer les clusters, aucun autre point de données n’est compatible avec ce cluster et le cluster peut être un singleton : autrement dit, un cluster avec un seul point.  
  
Il existe différentes façons pour éviter ce problème :  
  
-   Modifier le nombre de centroïdes et essayez de plusieurs valeurs de départ.  
  
-   Créer plusieurs modèles, faire varier la mesure ou l’itération bien plus encore.  
  
  
En général, avec modèles de clustering, il est possible qu’une configuration donnée entraîne un ensemble localement optimisé de clusters. En d’autres termes, l’ensemble des clusters retournés par le modèle adapté à uniquement les points de données actuelle et n’est pas généralisable à d’autres données. Si vous avez utilisé une autre configuration initiale, la méthode K-means peut trouver une configuration différente, voire supérieure. 
