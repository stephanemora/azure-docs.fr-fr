---
title: 'Nettoyage des données manquantes : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module de nettoyage des données manquantes dans le service Azure Machine Learning pour supprimer, remplacer ou déduire les valeurs manquantes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: de81204219a102734f1820258a3c32e59a64c685
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028784"
---
# <a name="clean-missing-data-module"></a>Propre module de données manquantes

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module à supprimer, remplacer ou déduire les valeurs manquantes. 

Les scientifiques des données souvent vérifient les données pour les valeurs manquantes et ensuite effectuent diverses opérations pour corriger les données ou d’insérer de nouvelles valeurs. L’objectif de ces opérations de nettoyage consiste à éviter les problèmes causés par des données manquantes qui peuvent survenir lors de l’apprentissage d’un modèle. 

Ce module prend en charge le type de plusieurs des opérations de « nettoyage » des valeurs manquantes, y compris :

+ Remplacement des valeurs manquantes par un espace réservé, moyenne ou une autre valeur
+ Suppression totale des lignes et colonnes qui ont des valeurs manquantes
+ Déduction de valeurs basées sur des méthodes statistiques


À l’aide de ce module ne modifie pas votre jeu de données source. Au lieu de cela, il crée un nouveau jeu de données dans votre espace de travail que vous pouvez utiliser dans le flux de travail suivant. Vous pouvez également enregistrer le nouveau jeu de données nettoyé pour une réutilisation.

Ce module génère également une définition de la transformation utilisée pour nettoyer les valeurs manquantes. Vous pouvez réutiliser cette transformation sur les autres jeux de données qui ont le même schéma, à l’aide de la [Apply Transformation](./apply-transformation.md) module.  

## <a name="how-to-use-clean-missing-data"></a>Comment utiliser le nettoyage des données manquantes

Ce module vous permet de définir une opération de nettoyage. Vous pouvez également enregistrer l’opération de nettoyage afin que vous pouvez l’appliquer ultérieurement aux nouvelles données. Consultez les liens suivants pour obtenir une description de la procédure créer et enregistrer un processus de nettoyage : 
 
+ Pour remplacer les valeurs manquantes
  
+ Pour appliquer une transformation de nettoyage à de nouvelles données
 
> [!IMPORTANT]
> La méthode de nettoyage que vous utilisez pour la gestion des valeurs manquantes peut affecter considérablement vos résultats. Nous vous recommandons de tester avec différentes méthodes. Prendre en compte les deux la justification pour une utilisation d’une méthode particulière et la qualité des résultats.

### <a name="replace-missing-values"></a>Remplacer les valeurs manquantes  

Chaque fois que vous appliquez le [Clean Missing Data](./clean-missing-data.md) module à un jeu de données, la même opération de nettoyage est appliqué à toutes les colonnes que vous sélectionnez. Par conséquent, si vous avez besoin des colonnes différentes à l’aide de différentes méthodes de nettoyage, utilisez des instances distinctes du module.

1.  Ajouter le [Clean Missing Data](./clean-missing-data.md) module à votre expérience et connectez le jeu de données qui a des valeurs manquantes.  
  
2.  Pour **colonnes à nettoyer**, choisissez les colonnes qui contiennent les valeurs manquantes, vous souhaitez modifier. Vous pouvez choisir plusieurs colonnes, mais vous devez utiliser la même méthode de remplacement dans toutes les colonnes sélectionnées. Par conséquent, en général, vous devez nettoyer les colonnes de type chaîne et des colonnes numériques séparément.

    Par exemple, pour rechercher les valeurs manquantes dans toutes les colonnes numériques :

    1. Ouvrez le sélecteur de colonne, puis sélectionnez **avec règles**.
    2. Pour **commencer par**, sélectionnez **non colonnes**.

        Vous pouvez également commencer avec toutes les colonnes et exclure des colonnes. Initialement, les règles ne sont pas affichés si vous cliquez d’abord sur **toutes les colonnes**, mais vous pouvez cliquer sur **les colonnes non** puis cliquez sur **toutes les colonnes** à nouveau pour démarrer avec toutes les colonnes et filtrer les colonnes (exclure) en fonction du nom, type de données, soit les index de colonnes.

    3. Pour **Include**, sélectionnez **type de colonne** dans la liste déroulante, puis sélectionnez **numérique**, ou un type numérique plus spécifique. 
  
    N’importe quelle méthode de nettoyage ou de remplacement que vous choisissez doit être applicable à **tous les** colonnes de la sélection. Si les données dans n’importe quelle colonne sont incompatibles avec l’opération spécifiée, le module renvoie une erreur et arrête l’expérience.
  
3.  Pour **Minimum rapport des valeurs manquantes**, spécifiez le nombre minimal de valeurs manquantes requis pour l’opération à effectuer.  
  
    Vous utilisez cette option conjointement avec **Maximum rapport des valeurs manquantes** pour définir les conditions sous lesquelles une opération de nettoyage est effectuée sur le jeu de données. S’il existe trop ou trop peu de lignes qui sont des valeurs manquantes, l’opération ne peut pas être effectuée. 
  
    Le nombre que vous entrez représente le **ratio** des valeurs manquantes pour toutes les valeurs dans la colonne. Par défaut, le **rapport minimal des valeurs manquantes** propriété est définie sur 0. Cela signifie que les valeurs manquantes sont nettoyées même s’il en existe qu’une seule valeur manquante. 

    > [!WARNING]
    > Cette condition doit être remplie par chaque colonne dans l’ordre pour l’opération spécifiée à appliquer. Par exemple, supposons vous trois colonnes sélectionnées et le rapport minimal des valeurs manquantes à.2 (20 %), mais une seule colonne a réellement 20 % de valeurs manquantes. Dans ce cas, l’opération de nettoyage appliquera uniquement à la colonne avec des valeurs manquantes, est plus de 20 %. Par conséquent, les autres colonnes serait inchangés.
    > 
    > Si vous avez un doute indique si les valeurs manquantes ont été modifiées, sélectionnez l’option, **générer la colonne d’indicateur de valeur manquant**. Une colonne est ajoutée au jeu de données pour indiquer si chaque colonne répond aux critères spécifiés pour les plages minimales et maximales.  
  
4. Pour **Maximum rapport des valeurs manquantes**, spécifiez le nombre maximal de valeurs manquantes qui peuvent être présents pour l’opération à effectuer.   
  
    Par exemple, vous souhaiterez peut-être effectuer la substitution de valeurs manquantes uniquement si 30 % ou moins de lignes contiennent des valeurs manquantes, mais laissez les valeurs en tant que-est si plus de 30 % des lignes ont des valeurs manquantes.  
  
    Vous définissez le nombre en tant que le rapport des valeurs manquantes pour toutes les valeurs dans la colonne. Par défaut, le **Maximum rapport des valeurs manquantes** est défini sur 1. Cela signifie que les valeurs manquantes sont nettoyées même si 100 % des valeurs dans la colonne sont manquants.  
  
   
  
5. Pour **Mode de nettoyage**, sélectionnez une des options suivantes pour le remplacement ou suppression manquant de valeurs :  
  
  
    + **Valeur de substitution personnalisée**: Utilisez cette option pour spécifier une valeur d’espace réservé (par exemple, la valeur 0 ou NA) qui s’applique à toutes les valeurs manquantes. La valeur que vous spécifiez comme un remplacement doit être compatible avec le type de données de la colonne.
  
    + **Remplacer par la moyenne**: Calcule la moyenne de la colonne et utilise la moyenne comme valeur de remplacement pour chaque valeur manquante dans la colonne.  
  
        S’applique uniquement aux colonnes qui ont entier, Double ou les types de données Boolean.  
  
    + **Remplacez par la valeur médiane**: Calcule la valeur médiane de colonne et utilise la valeur médiane comme valeur de remplacement pour toute valeur manquante dans la colonne.  
  
        S’applique uniquement aux colonnes qui ont des types de données entier ou Double. 
  
    + **Remplacez par le mode**: Calcule le mode de la colonne et utilise le mode comme valeur de remplacement pour chaque valeur manquante dans la colonne.  
  
        S’applique aux colonnes qui ont des types de données entier, Double, booléen ou une catégorie. 
  
    + **Supprimer toute la ligne**: Supprime complètement n’importe quelle ligne dans le jeu de données qui comporte un ou plusieurs des valeurs manquantes. Cela est utile si la valeur manquante peut être considérée comme manquante de façon aléatoire.  
  
    + **Supprimer la colonne entière**: Supprime complètement n’importe quelle colonne dans le jeu de données qui comporte un ou plusieurs des valeurs manquantes.  
  
    
  
6. L’option **valeur de remplacement** est disponible si vous avez sélectionné l’option, **valeur de substitution personnalisée**. Tapez une nouvelle valeur à utiliser comme valeur de remplacement pour toutes les valeurs manquantes dans la colonne.  
  
    Notez que vous pouvez utiliser cette option uniquement dans les colonnes ayant les types de données entier, Double, booléen ou Date. Pour les colonnes de date, la valeur de remplacement peut également être entrée en tant que le nombre de graduations de 100 nanosecondes depuis le 1/1/0001 12:00 a.m.  
  
7. **Générer la colonne d’indicateur de valeur manquant**: Sélectionnez cette option si vous souhaitez générer des indications si les valeurs dans la colonne aux critères de nettoyage de valeurs manquantes. Cette option est particulièrement utile lorsque vous installez une nouvelle opération de nettoyage et souhaitez vous assurer qu’il fonctionne comme prévu.
  
8. Exécutez l’expérience.

### <a name="results"></a>Résultats

Le module renvoie deux sorties :  

-   **Jeu de données nettoyé**: Un dataset composé des colonnes sélectionnées, avec des valeurs manquantes sont gérées comme spécifié, ainsi que d’une colonne d’indicateur, si vous avez sélectionné cette option.  

    Colonnes non sélectionnées pour le nettoyage sont également « transmis ».  
  
-  **Transformation de nettoyage**: Une transformation de données utilisée pour le nettoyage, qui peut être enregistrée dans votre espace de travail et appliquée aux nouvelles données ultérieurement.

### <a name="apply-a-saved-cleaning-operation-to-new-data"></a>Appliquer une opération de nettoyage enregistrée aux nouvelles données  

Si vous devez répéter les opérations de nettoyage souvent, nous vous recommandons d’enregistrer votre Recipe (Recette) pour le nettoyage des données comme un *transformer*, réutiliser avec le même jeu de données. L’enregistrement d’une transformation de nettoyage est particulièrement utile si vous devez réimporter fréquemment et puis nettoyer les données qui a le même schéma.  
      
1.  Ajouter le [Apply Transformation](./apply-transformation.md) module à votre expérience.  
  
2.  Ajoutez le jeu de données que vous souhaitez nettoyer et connectez le jeu de données au port d’entrée droite.  
  
3.  Développez le **transforme** groupe dans le volet gauche de l’interface. Recherchez la transformation et la faire glisser vers l’expérience.  
  
4.  Connectez la transformation enregistrée pour le port d’entrée gauche [Apply Transformation](./apply-transformation.md). 

    Lorsque vous appliquez une transformation enregistrée, vous ne pouvez pas sélectionner les colonnes auxquelles la transformation sont appliqués. C’est parce que la transformation a déjà été définie et s’applique automatiquement aux colonnes spécifiées dans l’opération d’origine.

    Toutefois, supposons que vous avez créé une transformation sur un sous-ensemble de colonnes numériques. Vous pouvez appliquer cette transformation pour un jeu de données des types de colonne mixte sans déclencher d’erreur, car les valeurs manquantes sont modifiés uniquement dans les colonnes numériques correspondantes.

6.  Exécutez l’expérience.  

## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 