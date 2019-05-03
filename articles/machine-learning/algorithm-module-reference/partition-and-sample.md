---
title: 'Partition and Sample : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Partition and Sample dans le service Azure Machine Learning pour effectuer un échantillonnage sur un jeu de données ou pour créer des partitions à partir de votre jeu de données.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 72f9e11e3582d804eecc7479ea079276564bd12f
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029279"
---
# <a name="partition-and-sample-module"></a>Module partition and Sample

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour effectuer un échantillonnage sur un jeu de données ou pour créer des partitions à partir de votre jeu de données.

L’échantillonnage est un outil important dans l’apprentissage automatique, car il permet de réduire la taille d’un jeu de données tout en conservant le même rapport des valeurs. Ce module prend en charge plusieurs tâches associées qui sont importantes dans machine learning : 

- Diviser vos données en plusieurs sous-sections de la même taille. 

    Vous pouvez utiliser les partitions pour la validation croisée, ou d’affecter des cas à des groupes aléatoires.

- La séparation des données dans des groupes et ensuite utiliser des données à partir d’un groupe spécifique. 

    Après avoir affecté aléatoirement les cas à différents groupes, vous devrez peut-être modifier les fonctionnalités qui sont associées à qu’un seul groupe.

- Échantillonnage. 

    Vous pouvez extraire un pourcentage des données, appliquer un échantillonnage aléatoire ou choisir une colonne à utiliser pour l’équilibrage du jeu de données et effectuer un échantillonnage stratifié sur ses valeurs.

- Création d’un plus petit jeu de données pour le test. 

    Si vous avez une grande quantité de données, vous souhaiterez utiliser uniquement la première *n* lignes lors de la configuration de l’expérience, puis cliquez sur à l’aide du jeu de données complet lorsque vous créez votre modèle. Vous pouvez également utiliser l’échantillonnage pour créer des s plus petit jeu de données pour une utilisation dans le développement.

## <a name="configure-partition-and-sample"></a>Configurer la Partition and Sample

Ce module prend en charge plusieurs méthodes permettant de diviser vos données en partitions, ou pour l’échantillonnage. Choisissez la méthode tout d’abord, puis définir des options supplémentaires requis par la méthode.

- Nœud principal
- échantillonnage
- Affecter à des plis
- Choisir le repli

### <a name="get-top-n-rows-from-a-dataset"></a>Obtenir les N premières lignes d’un dataset

Utilisez ce mode pour obtenir uniquement la première *n* lignes. Cette option est utile si vous souhaitez tester une expérience sur un petit nombre de lignes et que vous n’avez pas besoin de données à être à charge équilibrée ou échantillonnées en aucune façon.

1. Ajouter le **Partition and Sample** module à votre expérience dans l’interface et connectez le jeu de données.  

2. **Mode de partitionnement ou exemple**: Définissez cette option sur **Head**.

3. **Nombre de lignes à sélectionner**: Tapez le nombre de lignes à retourner.

    Le nombre de lignes que vous spécifiez doit être un entier non négatif. Si le nombre de lignes sélectionnées est supérieur au nombre de lignes dans le jeu de données, le jeu de données entière est retournée.

4. Exécutez l’expérience.

Le module génère un jeu de données unique contenant uniquement le nombre spécifié de lignes. Les lignes sont toujours lus à partir du haut du jeu de données.

### <a name="create-a-sample-of-data"></a>Créer un échantillon de données

Cette option prend en charge un échantillonnage aléatoire simple ou l’échantillonnage aléatoire stratifié. Cela est utile si vous souhaitez créer un jeu de données plus petit échantillon représentatif de test.

1. Ajouter le **Partition and Sample** module à votre expérience, connectez le jeu de données.

2. **Mode de partitionnement ou exemple**: Affectez la valeur **échantillonnage**.

3. **Taux d’échantillonnage**: Tapez une valeur comprise entre 0 et 1. Cette valeur spécifie le pourcentage de lignes du jeu de données source qui doivent être inclus dans le jeu de données de sortie.

    Par exemple, si vous ne souhaitez que la moitié du jeu de données d’origine, tapez `0.5` pour indiquer que le taux d’échantillonnage doit être de 50 %.

    Les lignes du jeu de données d’entrée sont mélangées et placées sélectivement dans le jeu de données de sortie, en fonction du rapport spécifié.

4. **Valeur initiale aléatoire pour l’échantillonnage**: Si vous le souhaitez, tapez un entier à utiliser comme une valeur de départ.

    Cette option est importante si vous souhaitez que les lignes à diviser la même façon à chaque fois. La valeur par défaut est 0, ce qui signifie qu’une valeur de départ initiale est générée en fonction de l’horloge système. Cela peut entraîner des résultats légèrement différents chaque fois que vous exécutez l’expérience.

5. **Fractionnement stratifié pour l’échantillonnage**: Sélectionnez cette option s’il est important que les lignes du jeu de données doivent être divisées uniformément par une colonne clé avant d’échantillonnage.

    Pour **colonne clé de Stratification pour l’échantillonnage**, sélectionnez un seul *colonne de couches* à utiliser lors de la division du jeu de données. Les lignes dans le jeu de données sont divisées comme suit :

    1. Toutes les lignes d’entrée sont regroupées (stratifiées) par les valeurs dans la colonne de couches spécifiée.

    2. Les lignes sont mélangées au sein de chaque groupe.

    3. Chaque groupe est ajouté sélectivement au jeu de données de sortie à respecter le rapport spécifié.


6. Exécutez l’expérience.

    Avec cette option, le module génère un jeu de données unique contenant un échantillon représentatif des données. La partie restante, échantillonnée du jeu de données n’est pas sortie. 

## <a name="split-data-into-partitions"></a>Fractionner les données en partitions

Utilisez cette option lorsque vous souhaitez diviser le jeu de données en sous-ensembles de données. Cette option est également utile lorsque vous souhaitez créer un nombre de plis pour la validation croisée ou de fractionner les lignes en plusieurs groupes.

1. Ajouter le **Partition and Sample** module à votre expérience, connectez le jeu de données.

2. Pour **Partition ou mode échantillon**, sélectionnez **affecter à des plis**.

3. **Utiliser le remplacement dans le partitionnement**: Sélectionnez cette option si vous souhaitez que la ligne échantillonnée est replacé dans le pool de lignes pour une réutilisation potentielle. Par conséquent, la même ligne peut être attribuée à plusieurs plis.

    Si vous n’utilisez pas de remplacement (l’option par défaut), la ligne échantillonnée n'est pas remise dans le pool de lignes pour une réutilisation potentielle. Par conséquent, chaque ligne peut être affecté à uniquement un pli.

4. **Fractionnement aléatoire**:  Sélectionnez cette option si vous souhaitez que les lignes d’être affectés de façon aléatoire à des plis.

    Si vous ne sélectionnez pas cette option, les lignes sont affectées à des plis en utilisant la méthode tourniquet (round-robin).

5. **Valeur de départ aléatoire**: Si vous le souhaitez, tapez un entier à utiliser comme la valeur de départ. Cette option est importante si vous souhaitez que les lignes à diviser la même façon à chaque fois. Sinon, la valeur par défaut 0 signifie qu’aléatoire départ sera utilisé.

6. **Spécifiez la méthode du partitionneur**: Indiquer comment les données à être réparties entre chaque partition, à l’aide de ces options :

    - **Partitionner uniformément**: Utilisez cette option pour placer un nombre égal de lignes dans chaque partition. Pour spécifier le nombre de partitions de sortie, tapez un nombre entier dans le **spécifier le nombre de plis pour le fractionnement uniforme** zone de texte.

    - **Partition dans des proportions personnalisées**: Utilisez cette option pour spécifier la taille de chaque partition comme une liste séparée par des virgules.

        Par exemple, si vous souhaitez créer trois partitions, avec la première partition contenant 50 % des données et deux partitions restantes chacun 25 % des données, cliquez sur le **liste des proportions séparées par des virgules** zone de texte, et tapez ces numéros : `.5, .25, .25`

        La somme de toutes les tailles de partition doit ajouter jusqu'à 1 exactement.

        - Si vous entrez des nombres s’ajoutent en **inférieur à 1**, une partition supplémentaire est créée pour contenir les lignes restantes. Par exemple, si vous tapez la partition de valeurs.2 et.3, un tiers est créée qui contient les 50 pour cent de toutes les lignes restantes.

        - Si vous entrez des nombres s’ajoutent en **plus de 1**, une erreur est générée lorsque vous exécutez l’expérience.

7. **Fractionnement stratifié**: Sélectionnez cette option si vous souhaitez que les lignes doivent être stratifiées lors du fractionnement, puis choisissez le _colonne de couches_.

8. Exécutez l’expérience.

    Avec cette option, le module génère plusieurs jeux de données, partitionnées à l’aide des règles que vous avez spécifié.

### <a name="use-data-from-a-predefined-partition"></a>Utiliser les données d’une partition prédéfinie  

Cette option est utilisée lorsque vous avez divisé un jeu de données en plusieurs partitions et que vous voulez charger chaque partition à son tour pour traitement ou d’une analyse plus approfondie.

1. Ajouter le **Partition and Sample** module à l’expérience.

2. Se connecter à la sortie d’une instance précédente de **Partition and Sample**. Cette instance doit avoir utilisé le **affecter à des plis** option pour générer un certain nombre de partitions.

3. **Mode de partitionnement ou exemple**: Sélectionnez **choisir le repli**.

4. **Spécifier le pli à échantillonner à partir de**: Sélectionnez une partition à utiliser en tapant son index. Index de partition est en base 1. Par exemple, si vous avez divisé le jeu de données en trois parties, les partitions aurait l’index 1, 2 et 3.

    Si vous tapez une valeur d’index non valide, une erreur au moment du design est déclenchée : « Erreur 0018 : Jeu de données contient des données non valides. »

    En plus de regroupement du jeu de données en plis, vous pouvez séparer le jeu de données en deux groupes : un pli cible et tout le reste. Pour ce faire, tapez l’index d’un pli unique, puis sélectionnez l’option, **sélectionner le complément du pli sélectionné**, afin d’obtenir uniquement les données du pli spécifié.

5. Si vous travaillez avec plusieurs partitions, vous devez ajouter des instances supplémentaires de la **Partition and Sample** module pour gérer chaque partition.

    Par exemple, supposons que patients précédemment partitionnés en quatre REPLIS à l’aide d’âge. Pour travailler avec chaque repli individuel, vous avez besoin de quatre copies de la **Partition and Sample** module, et dans chaque cas, vous sélectionnez un pli différents, comme indiqué ci-dessous. Il n’est pas correct à utiliser le **affecter à des plis** directement la sortie.  

    [![Partition et échantillon](./media/partition-and-sample/partition-and-sample.png)](./media/partition-and-sample/partition-and-sample-lg.png#lightbox)

5. Exécutez l’expérience.

    Avec cette option, le module génère un jeu de données unique contenant uniquement les lignes affectées à ce pli.

> [!NOTE]
>  Vous ne pouvez pas afficher les désignations de pli directement ; ils sont présents uniquement dans les métadonnées.

## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 